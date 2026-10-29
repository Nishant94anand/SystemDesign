# Latency, Throughput, Bandwidth, Concurrency and Tail Latency

- **Status:** COMPLETED discussion/reference chapter — **not** a mastery certification
- **Phase:** 0 (Foundations)
- **Last updated:** 2026-10-29
- **Discussion coverage:** **4/5** (evidence and gaps in Section 11)
- **Running example:** Gaya Puja Samagri web shop at Chhath peak
- **Navigate:** [Foundations](../) · [Syllabus V2](../../../curriculum/SYLLABUS_V2.md) · [Progress](../../../progress/PROGRESS.md) · [Daily Log](../../../progress/DAILY_LOG.md)

> This is an original learning reference, not a real production telemetry report. Numerical values are instructional assumptions. **Live exercises were attempt-first**; because the learner explicitly authorized chapter completion, Sections 13–15 now contain the complete, separate **model answer key** for *all* discussion checkpoints, new exercises and revision questions. Model-only answers are **not** learner evidence.

## 1. Why this chapter exists

A shop must answer catalogue requests quickly, even when Chhath creates bursts. "Fast" can mean short user-perceived wait, high requests/second, enough network transfer capacity or healthy slowest requests—four different things. Diagnose and make trade-offs rather than memorize magic thresholds.

**Learning outcomes:** define quantities/units and measured boundaries; relate average concurrency to throughput and duration with Little's Law; calculate backlog growth/draining; understand M/M/1 as a *model* and saturation/headroom; calculate percentiles, transfer times and fan-out risk; identify critical-path bottlenecks from traces; protect optional work; choose reversible operational mitigations backed by evidence.

**Scope boundary:** Detailed estimation/growth forecast comes in the **next** Phase 0 chapter; complete SLI/SLO/SLA/error-budget treatment, TCP/HTTP connection internals, DB indexing, distributed retries and Kubernetes operations each have dedicated later topics.

**Analogy:** Gaya shop's queue is analogous to pending requests; cashier speed to one server's service rate; customers simultaneously shopping to concurrent work. **Limitation:** software may use parallel processors and asynchronous I/O, retries, multiple networks, queues and databases. A one-cashier analogy is not a cluster performance model.

## 2. Definitions from scratch

| Term | Precise meaning | Unit |
|---|---|---|
| **Latency** | Elapsed time from *defined start to defined end* for one operation | milliseconds (ms), seconds |
| **Arrival rate** | New requests entering a boundary per unit time | requests/second (RPS) |
| **Throughput** | Completed useful work crossing a specified boundary per unit time | RPS, orders/minute |
| **Capacity** | Sustainable maximum completion rate *under a stated workload and objective* | RPS |
| **Bandwidth** | Provisioned/theoretical data-transfer capacity of a link/path | bits/second, Mbps |
| **Network throughput / goodput** | Actual transferred bits/time (goodput excludes non-useful wire bytes) | Mbps, MB/s |
| **Concurrency / in-flight count** | Number of operations inside a specified system boundary simultaneously | requests |
| **Queue depth** | Number waiting for service, *not* the count already running | requests |
| **Utilization** | Fraction of one resource's capacity currently consumed | % |
| **Headroom** | Unused capacity intended for spikes, failures and recovery | %, requests/s |

**Units:** 1 byte = 8 bits. Mbps = megabits/s; MB/s = megabytes/s. 100 Mbps / 8 = **12.5 MB/s theoretical**; real effective transfer can be lower. This chapter uses *decimal* MB (10^6 bytes), KB (10^3 bytes); MiB/KiB are binary. RPS = **Requests Per Second**; CPU = Central Processing Unit; API = Application Programming Interface; DB = Database; HTTP = Hypertext Transfer Protocol. More terms in Section 16.

Do not confuse **arrivals** with **completions**. With overload, they diverge: 800 arriving RPS and 600 completed RPS are not the same throughput.

## 3. Little's Law — average concurrency

For a stable system with consistent counting boundaries:

~~~text
L = λ × W

L = long-run average number in the system
λ = long-run completion rate (requests per second)
W = long-run mean time inside the system (seconds)
~~~

Example: 100 completed RPS × 0.2 seconds = **20 average in-flight requests**. Intuitively, the system is completing 100 requests every second while each spends one-fifth of a second in the measured boundary.

**Not a guarantee:** 20 is an average, not an instantaneous constant, maximum or thread count. Evenly spaced arrivals are *not* required: bursty arrivals can also satisfy the long-run relationship. But a queue growing without bound is **not** in the usual steady state; don't combine completed throughput with latency from a mismatched window or boundary.

If **50 slots** can be occupied concurrently and each request occupies a slot on average for **one second**, maximum sustainable slot throughput is about **50 RPS** if all slots are efficiently used. That is a *conditional ceiling*, not a rule that 50 threads always serve 50 RPS. Waiting outside the slot must **not** be mixed into slot occupation time. An asynchronous in-flight request may be waiting for I/O without tying up a dedicated thread.

## 4. Overload, backlog and saturation

An ideal unbounded queue with no dropped/cancelled requests grows at:

~~~text
backlog growth = arrivals per second − completions per second
               (while arrivals exceed processing rate)
~~~

**Worked case:** 100 arriving RPS, 50 served RPS for 30 s: (100−50)×30 = **1,500 waiting requests**. When arrivals stop, at 50 RPS the waiting queue takes **30 additional seconds** to drain, excluding the remainder of already-active work.

~~~mermaid
flowchart LR
  A["Arrivals 100 RPS"] --> Q["Waiting queue grows by 50/s"]
  Q --> S["Server 50 completed RPS"]
  S --> U["Responses"]
  Q -. "full finite queue" .-> R["Reject / shed work"]
~~~

A queue absorbs a **temporary burst**; it does **not** increase processing capacity. In production, finite buffers, waiting-time deadlines, cancellation and retry amplification matter. 100 arrivals vs 50 completions sustained indefinitely cannot produce a stable queue.

### M/M/1, explained

M/M/1 is a simplified **queueing theory model**, not a Kubernetes architecture:

- First **M** = *Markovian arrivals*: requests arrive according to a Poisson process (exponential interarrival times).
- Second **M** = *Markovian service time*: exponential service-time distribution.
- **1** = one server works on one request at a time.
- Standard assumptions: independent arrivals/services, a first-come-first-served unbounded queue and **λ < μ**.

Here λ = arrival rate, μ = mean service capacity (RPS), W = average total queue **plus** service time; ρ = utilization of *this model's single server*.

~~~text
W = 1 / (μ − λ) seconds, if λ < μ
ρ = λ / μ
~~~

For μ = 100 RPS:

| Arrivals λ | Model server utilization ρ | Mean total time W |
|---:|---:|---:|
| 50 RPS | 50% | 20 ms |
| 80 RPS | 80% | 50 ms |
| 90 RPS | 90% | 100 ms |
| 95 RPS | 95% | 200 ms |
| 99 RPS | 99% | 1,000 ms |

Latency rises **nonlinearly** near saturation. At λ ≥ μ there is **no finite steady-state mean W** in this ideal model. For sustained 120 arrivals vs 100 possible completions, backlog rises **20 requests/s**, assuming no rejection or cancellation.

**Critical distinction:** ρ is **not automatically measured CPU utilization**. An API can be blocked on connections, storage, locks or downstream services with low CPU. There is **no universal 80% CPU operating target**: choose headroom using measured workloads, tail latency, failover constraints, cost and autoscaling reaction time. Real multiworker services need load testing, not naive M/M/1 predictions.

## 5. Bandwidth and full-response latency

End-to-end duration may include setup/DNS/TLS, network travel, proxy wait, server-side admission queue, application CPU, DB dependency wait/execution, serialization, response transfer and browser rendering. Stages can overlap; keep measurement boundaries explicit.

~~~mermaid
sequenceDiagram
  participant B as Browser
  participant A as API
  participant D as Database
  B->>A: HTTP request (network/setup as applicable)
  Note over A: Admission/worker/connection waiting may occur
  A->>D: Query
  D-->>A: Result
  Note over A: Process and serialize
  A-->>B: Stream/transfer response bytes
  Note over B: Parse and render
~~~

**Time to first byte (TTFB)** is not **time to last byte**. Backend-only processing latency is not browser-visible full-response duration, nor necessarily page-ready time. A tracer's server p99 cannot simply be subtracted from a separate DB p99.

### Worked transfer example

4 MB × 8 bits/byte = 32 megabits; at an **effective** rate of 8 Mbps, the payload takes **4 seconds** in a fixed-rate simplification.

| Stage | Before | Backend only optimized |
|---|---:|---:|
| App processing | 100 ms | 50 ms |
| Other network overhead (assumed) | 200 ms | 200 ms |
| Transfer | 4,000 ms | 4,000 ms |
| **Total, assumed non-overlapping** | **4,300 ms** | **4,250 ms** |

Backend becomes twice as fast, but customer elapsed time improves only **50/4300 ≈ 1.16%**. If 4 MB can validly be reduced to 1 MB at the same transfer rate, transfer becomes ~1 s and total ~1.3 s.

Potential tests: pagination, eliminate unused fields, compress suitable text, serve smaller image variants, lazy-load images. Actual benefit depends on client link, wire bytes, cache, compression, connection reuse, overlap and user expectations. As another units check: 200 KB per response × 600 completed RPS = **120 MB/s ≈ 960 Mbps of application payload outbound**, if all responses have that size. A displayed "network utilization 30%" cannot alone identify which path or measuring point has headroom.

## 6. Percentiles, SLOs and tail latency

A **mean** = sum(latencies)/count. A **percentile** describes the distribution, not the worst individual request. For examples we use **nearest-rank**: sort N durations; p95 is rank ceil(0.95N). Real tools can implement interpolation/histogram estimates—record the convention and aggregation window.

### Example A: 100 completed requests

90 requests took 100 ms, 9 took 500 ms, and 1 took 10,000 ms.

Mean = (90×100 + 9×500 + 1×10,000)/100 = **235 ms**. p50=**100 ms**, p95=**500 ms**, p99=**500 ms**, maximum=**10,000 ms**. The mean and even p99 can conceal a single extreme outlier. A 10-second outlier deserves investigation, **not automatic release blocking** without frequency/criticality/SLO context.

### Example B: 10,000 completed requests

| Requests | Latency |
|---:|---:|
| 9,500 | 100 ms |
| 400 | 400 ms |
| 100 | 5,000 ms |

Mean = (950,000 + 160,000 + 500,000)/10,000 = **161 ms**; p95=**100 ms**, p99=**400 ms**, p99.9=**5,000 ms**. A provisional Product Listing target **p95 ≤1 second** would pass for **this sample**, but 1% still waited 5 seconds. Compliance over the intended window and eligible populations remains unproven.

**SLO** = Service Level Objective (target), **SLI** = Service Level Indicator (actual measurement), **SLA** = Service Level Agreement (service commitment and possible consequences). A response-time SLO must state request population, client/server boundary, success/error/timeout treatment, time window and threshold. Successful-completion-only latency can hide timed-out and still-pending users.

**User-session effect:** if each request has an *independent* 1% slow probability, across 20 requests P(at least one slow) = 1−0.99^20 ≈ **18.21%**. Real sessions, request mixes and correlated failures may differ.

### Fan-out amplifies the tail

Suppose the Product Listing API requires **all** n parallel downstream calls. Each independently has *exactly* 99% probability of finishing within 500 ms.

~~~text
P(all n fast) = 0.99^n
P(at least one slow) = 1 − 0.99^n

n=3  -> ~2.97% have at least one slow
n=20 -> ~18.21% have at least one slow
~~~

For 20 required calls only ~81.79% have *every* dependency ≤500 ms; user-facing p99 cannot be ≤500 ms in this idealized wait-for-all model with nonnegative overhead. **Independence is an assumption**; shared DB/network degradation creates correlated delays, so do not apply the numeric formula blindly. Nor does this compute the API's precise p99 *duration*: the distribution of slow results remains unknown.

## 7. Designing around a slow optional dependency

Customer-critical: **Product Listing**. Optional: **"You may also like" Recommendations**. Learner proposed a separate suggestion API, independent UI population, a dedicated pod and cached precomputed suggestions; a practical direction with important resource and freshness caveats.

~~~mermaid
flowchart TD
  UI["Browser"]
  UI --> P["Required Product Listing API"]
  UI --> R["Optional Recommendations API"]
  P --> DB["Product data"]
  R --> H{"Usable cache entry?"}
  H -->|"yes"| RES["Prompt suggestion response"]
  H -->|"no"| FB["Prompt fallback, no open wait"]
  H -. "if admitted" .-> JOB["Bounded background job"]
  JOB --> C["Populate recommendation cache"]
  C -. "future requests" .-> H
~~~

**Critical-path isolation**: render products irrespective of recommendation completion/failure. An open HTTP request awaiting async I/O may not occupy a dedicated thread but *still* costs request state, connections, memory and deadlines.

**Resource isolation**: different endpoints or Kubernetes pods do **not** guarantee independent nodes, DB pools, CPU, network or quotas. Consider scoped concurrency limits, worker budgets, per-dependency deadlines and error containment. Escalate to fully independent infrastructure only with evidence and cost/ownership justification.

**8,000 missing keys example:** cap heavy jobs at 50 running plus 200 pending. Admit at most **250 distinct jobs** initially; 7,750 cannot immediately trigger expensive computation. Return usable stale/generic suggestions or omit them, without promising eventual personalized results. **Singleflight/coalescing** deduplicates *identical keys*, not 8,000 unique per-user computations. Precompute popular/trending segments when useful; background queues must be bounded, observable, cancellable and safely retryable.

**Correctness/privacy:** cache keys must respect user/tenant identity; recommendation access must be authorized. Set sensible TTL/freshness and deletion/retention rules. Recommendations must **not** substitute for authoritative live inventory at checkout. Background computation, broker, cache and extra pods increase operational and egress costs.

## 8. Bottleneck diagnosis: trace first, not "scale first"

A system has an end-to-end critical path composed of work and waits. A **distributed trace** associates spans (timed units of work) with the *same logical request* across components. Compare corresponding requests rather than subtracting unrelated aggregates.

~~~mermaid
flowchart TD
  A["API latency / errors rising"] --> B["Confirm traffic, window, queue and user impact"]
  B --> C["Inspect correlated slow-request traces"]
  C --> D{"Where is time spent?"}
  D -->|"Connection acquisition"| E["Pool waits, leaks, hold duration"]
  D -->|"Query execution"| F["Query plan, locks, indexes, DB I/O"]
  D -->|"App/worker queue"| G["Admission, thread contention, CPU / GC"]
  D -->|"Network transfer"| H["Payload, client link, egress"]
  E --> V["Test a targeted reversible change"]
  F --> V
  G --> V
  H --> V
  V --> J["Verify end-to-end p95/p99, errors, throughput and rollback"]
~~~

Illustrative sequential **single request**: auth 30 ms + application 50 ms + waiting for DB connection 900 ms + DB query 700 ms + serialization 20 ms = **1,700 ms**. Optimizing only the SQL leaves the 900-ms pool wait, unless the change also affects wait indirectly.

### Final interactive scenario — actual learner exercise

| Metric | Supplied observation |
|---|---:|
| Incoming | 800 RPS |
| Completed | 600 RPS |
| Mean | 800 ms |
| p99 | 4 seconds |
| CPU | 45% |
| DB connection pool usage | 100% |
| Network usage | 30% |
| Response payload | 200 KB |

Assumptions: same observation interval, requests accumulate upstream, no drops/cancellations, constant rates.

**Backlog:** 800−600 = **200 requests/s**, reaching **12,000 waiting after 60 s** from an empty queue. At 600 completed/s, that backlog alone represents ~20 s processing work after arrivals stop, excluding active requests. A mean 800-ms latency does **not** establish whether p95 ≤1 s passes; percentiles among *completed* requests may conceal still-waiting ones.

**DB pool at 100% is a serious lead, not a diagnosis.** Investigate separately:
- **Connection acquisition wait:** unable to check out a connection.
- **Connection hold duration:** how long the application holds the checked-out connection; long transactions, I/O while holding, leaks, missing cancellation.
- **Query execution duration:** query plan/index suitability, DB disk I/O, locks, transaction contention, DB-specific limits.
- **Workload amplification:** retries, repeated queries, unnecessary fan-out, cache misses, excessive per-request queries.
- **Other resource signals:** per-core CPU, memory/GC, network location, backlog admission, downstream limits.

**Why not automatically double pool size 100→200 when DB CPU is 25%?** CPU alone cannot prove spare DB processing capacity: locks, I/O and connection/memory overhead may limit effective throughput. Investigate both app and DB layers. Test a *reversible*, monitored increase only if acquisition waits are high, connections are healthy, and downstream capacity has been verified. During serious incidents, reversible mitigations may precede complete root-cause investigation—protect users while diagnosing.

## 9. Operations, security, cost and validation

**Incident sequence:** (1) confirm customer impact, backlog growth, timeouts/errors; (2) bound admission/queue and shed optional work or apply reversible mitigation; (3) collect slow *and* normal correlated traces and pool/DB/queue/CPU/network metrics; (4) test alternatives; (5) validate and roll back if tail/errors/DB contention worsen.

**Important**: retries under overload can amplify traffic. Use bounded attempts, deadlines, jitter/backoff and safe cancellation; an HTTP timeout does not necessarily stop server-side work. Critical payment/inventory APIs need separate correctness guarantees (idempotency/reservation policy), not merely shorter timeouts.

**Measure:** arrival and completion RPS, request durations p50/p95/p99/p99.9, error/timeout and rejected rates, in-flight count, queue depth/age, per-operation SLO, CPU per core, DB pool active/wait/hold, DB query/lock/I/O, bytes transferred, cache hit rate, background jobs in-flight and dropped, user journey latency.

**Security:** don't put private user IDs/order content into unprotected metrics/logs/traces; keep recommendation cache keys tenant/user-safe, authorization scoped, sampling and retention reviewed. **Cost:** node/pod reservations, DB capacity, egress bandwidth, cache memory, extra traces, broker/queue operations and on-call toil compete with the benefit of lower latency.

**Validate with evidence:** representative Chhath read/write/payload mix; warm vs cold cache; burst/soak tests, slow downstream injection, finite-queue recovery, safe retries; compare same boundary/population and both successful/failed requests. Record change owner, threshold, experiment duration, risk and rollback trigger. **No universal 80% CPU utilization cutoff.**

## 10. Misconceptions to retain

| Claim | Correction |
|---|---|
| "100 RPS means 100 concurrent" | Rate differs from in-flight count; averages relate through L=λW. |
| "Little's Law requires uniform arrivals" | No; long-run stable averages and consistent boundaries suffice. |
| "A full queue gives more capacity" | It stores work; sustained excess demand keeps growing. |
| "p95 passing means everyone fast" | It allows a tail; account for user journeys, failed and timed-out requests. |
| "One 10-second call always blocks release" | Severity, repeatability, impact, risk and SLO decide. |
| "Parallel dependency p99 equals API p99" | Wait-for-all fan-out amplifies slow calls; correlations and overhead matter. |
| "Async open HTTP consumes a thread" | Not necessarily, but it uses other resources. |
| "Separate pods guarantee isolation" | Shared nodes, DB, network and budgets can still propagate overload. |
| "Full DB pool proves SQL is slow" | Could be acquisition wait, long held connections, leaks, retries or DB execution. |
| "DB CPU 25% = 75% spare DB throughput" | DB may be I/O/lock/connection bound. |
| "Adding CPU always fixes slow APIs" | Find actual end-to-end bottleneck first. |
| "Two different p95 spans can be subtracted" | Percentile values need not represent the same request. |

## 11. Evidence, coverage rating and readiness

**Final rating: 4/5 discussion coverage**, for good independent quantitative reasoning, defensible architectures and diagnostic judgment, with identifiable gaps. **COMPLETED ≠ interview-ready or production/Principal-ready.**

| Skill | Actual learner evidence | Assessment |
|---|---|---|
| Explain/concurrency | Independently calculated 20 in flight at 100 RPS × 200 ms, and correctly distinguished averages from instantaneous guaranteed counts. | Demonstrated; nuance: evenly spaced arrivals not necessary |
| Apply queue math | Independently solved 50-slot/1-s ceiling, 1,500 backlog and 30-s drain. | Demonstrated |
| Tail latency | Flagged 10-s outlier investigation; predicted 20-way fan-out issue; correctly said sample p95 can pass while UX is unhealthy. | Qualitative independent; some percentile calculation tutor-led |
| Architectural trade-offs | Proposed independent recommendation endpoint/pod/precomputation/cache and fallback async path. | Good; shared-resource isolation and job admission explained later |
| Network vs compute | Recognized that 4-MB/8-Mbps payload dominates 100→50 ms CPU optimization. | Independent decision, tutor supplied numerical percentage |
| DB vs API diagnosis | Flagged independent p95 boundaries, checked app and DB; final lab independently found 200 RPS backlog and 100% DB pool; investigated pool cause rather than blindly doubling. | Good independent diagnostic judgment |
| Overload and validation | Favored headroom over 95% utilization and capacity changes only when justified. | Partly demonstrated; 80% not universal, no independent 120-vs-100 overload answer |
| Production instrumentation | Asked to correlate API/DB slow requests, suggested query plans, indexes, long-held connections, leaks and retries. | Partial; monitoring plan, experiment ownership, measured rollback unassessed |

**Taught/model-only, not independently attempted:** 8,000-cache-miss overload solution (requested explanation), 120-RPS arrival vs 100-RPS capacity analysis (requested explanation), precise percentile arithmetic and detailed SLI instrumentation. Do not upgrade readiness because answers now appear below.

**Interview readiness:** No unseen, timed interview-style problem assessed; **unassessed**. **Production/Principal readiness:** No production measurements, fault-injection, load-test evidence, SLO sign-off or rollback plan independently produced; **not demonstrated**.

**Revisit:** distinguish mean vs p95, response time vs queue/capacity boundary, M/M/1 server utilization vs CPU, and user-impact containment vs permanent DB pool optimization.

## 12. New practice — unattempted; progressive hints

**P1. Average concurrency under changing latency.** Stable 250 completed RPS with mean time 80 ms; later 240 ms at same completed rate. Calculate both mean in-flight counts. What do they *not* tell you about CPU?  
*Hint:* seconds first; use L=λW.

**P2. Queue burst and drain.** From empty, 180 arriving RPS / 120 completed RPS for 45 s; arrivals then stop. Find backlog and time to drain the waiting backlog, ignoring already-active work. Name two real-world complications.  
*Hint:* difference × time; then divide by completions/s.

**P3. Payload or backend optimization?** 6 MB over effective 12 Mbps, app 150 ms and other overhead 100 ms. Compare total improvement from (a) halving app time and (b) reducing transferred payload to 1.5 MB. State simplifying assumptions.  
*Hint:* 1 MB = 8 megabits here.

**P4. Percentile trap.** 1,000 completed requests: 940 take 80 ms, 50 take 400 ms, 10 take 6 s. Find mean, p95, p99 and p99.9 by nearest rank. Is a p95 ≤500 ms sample target met?  
*Hint:* ranks 1–940 / 941–990 / 991–1000.

**P5. Fan-out probability.** 10 independent required dependencies; each ≤300 ms with exactly 98% probability. Calculate probability of any slow dependency, and state whether actual numeric API p99 can be calculated.  
*Hint:* 1 − 0.98^10; threshold probability is not full distribution.

**P6. Production decision memo.** 450 arrive, 400 complete RPS; CPU 30%, DB pool full, p99=3 s. State (i) immediate risk/mitigation, (ii) traces/metrics needed, (iii) when pool increase might help, (iv) rollback/validation.  
*Hint:* contain harm separately from root-cause repair.

---

## 13. Reference answers for **every live checkpoint** (answer key)

All entries explicitly distinguish learner attempts from tutor-provided/model-only outcomes.

**C1 — 100 RPS and 200 ms, mean concurrency.** 100 × 0.2=**20 average in flight**. Stable system, same counting boundary. No uniform arrivals needed and instantaneous count is not fixed. *Learner attempted correctly; distribution nuance clarified.*

**C2 — 50 occupied slots, 1 s average per slot; can 100 RPS continue?** Ceiling ≈50/1=**50 RPS**, if the slot stays occupied for the specified second. Upstream wait must not be mixed into this slot duration. *Independently solved.*

**C3 — 100 arrive, 50 complete for 30 s, stop arrivals.** Growth=50/s, backlog=**1,500**, drain at 50/s=**30 additional seconds** (queued work, ideal no retries/timeouts). *Independently solved.*

**C4 — mean 235 ms and one 10-s call; release?** Trace the outlier; check criticality, reproducibility, load sensitivity, incident risk, SLO and mitigations. Isolated outlier alone is not automatic release veto. *Learner identified investigation needs; blanket release block refined through feedback.*

**C5 — 20 independent dependencies, 99% ≤500 ms each, API waits for all.** P(all fast)=0.99^20≈**81.79%**; P(any slow)=**18.21%**, so API p99 exceeds 500 ms under stated independence and nonnegative-overhead model. *Learner predicted outcome; math tutor-provided.*

**C6 — optional recommendations sometimes take 3 s.** Separate the optional request/UI render from critical Product Listing; return product data promptly regardless. *Learner independently proposed separate suggestion API.*

**C7 — shared app pool of 100 slots, recommendations occupy 90.** Isolate priority/service capacity using separate worker budgets/pools/limits, optional timeouts and fallback; pods alone do not isolate node, DB and network bottlenecks. *Learner proposed pods, cached async suggestions; limits explained by tutor.*

**C8 — 8,000 recommendation cache misses burst.** Bound concurrent and pending computations, deduplicate identical-key jobs, precompute popular variants, return fallback immediately, shed work if capacity exhausted. If 50 active/200 pending slots: at most **250** unique jobs admitted, **7,750** miss calculations deferred/skipped initially; no eventual guarantee. *Tutor explained at learner's request; **unattempted**.*

**C9 — hold HTTP open (A) vs fallback+background job (B).** **B**, because recommendations are optional. Bounded jobs reduce request wait/connection-state costs, while async waiting does not necessarily occupy threads. *Learner independently chose B.*

**C10 — 95% at 100 ms, 4% at 400 ms, 1% at 5 s; provisional p95 ≤1 s.** Sample p95=**100 ms**, so it passes, but 1% very slow can impair journeys; verify population/window and errors. *Learner independently distinguished SLO pass from healthy experience.*

**C11 — 95% CPU with Chhath spike.** Prefer evidence-driven headroom and latency/failure guards, not universally "never above 80%"; CPU percentage differs from M/M/1 ρ. *Learner chose headroom; threshold nuance guided.*

**C12 — 120 arrivals vs 100 RPS capacity.** Backlog rises **20/s** while overload persists; queueing delay can explode near saturation. M/M/1 finite steady-time formula does not apply at λ≥μ. *Learner asked for explanation; **unattempted**.*

**C13 — 4 MB over 8 Mbps, 100→50 ms backend processing.** Transfer=**4 s**; total 4.3→4.25 s, only **1.16%** reduction. Payload/network is the stronger initial hypothesis, subject to measured bytes/path. *Learner independently rejected backend-only improvement; arithmetic tutor-led.*

**C14 — API p95=2 s, DB p95=1.7 s, low CPU.** Trace the same slow requests and measure DB pool acquisition vs execution vs app time. Aggregated p95 values cannot be subtracted. Do not blindly scale app or inflate timeout. *Learner independently highlighted correlation concern and selected DB investigation.*

**C15 — 800 arriving/600 completed, mean 800 ms, p99 4 s, DB pool 100%.** Backlog **+200/s**, acute if sustained. Full DB pool is a strong hypothesis; instrument connection acquisition, holding, SQL and app behavior; mean latency does not tell p95. *Learner independently calculated queue growth and chose DB/app-layer investigation.*

**C16 — pool 100 busy, DB CPU 25%; double pool?** Not without evidence: measure waiting-to-acquire, hold times, DB query/I/O/locks, leaks/retries and spare connection/memory capacity. Controlled pool increase is appropriate if tests verify benefit and rollback guardrails. *Learner independently rejected blind increase and was open to measured capacity growth.*

## 14. Fully worked **P1–P6 answers** (model-only; learner has not attempted these)

**P1:** L1=250×0.08=**20**; L2=250×0.24=**60** average in-flight. Neither figure proves CPU utilization tripled: more requests could be waiting for DB/network; both require stable comparable boundaries.

**P2:** Growth 180−120=**60/s**; backlog 60×45=**2,700**. Drain 2700/120=**22.5 s** once arrivals cease, ignoring already-active work. Cancellation, retry, changing service rate, finite queues and timeouts invalidate ideal results.

**P3:** 6 MB=48 megabits; at 12 Mbps transfer=**4 s**. Baseline total 4+.15+.1=**4.25 s**. (a) Half app time to 75 ms: total **4.175 s** or ~**1.76%** improvement. (b) Payload 1.5 MB=12 megabits -> transfer **1 s**, total **1.25 s** or ~**70.59%** improvement. Assumes unchanged goodput, semantic validity of compression, no overlapping stages and no other cost change.

**P4:** Sorted ranks 1–940 at 80 ms, 941–990 at 400 ms, 991–1000 at 6000 ms. Mean=(940×80+50×400+10×6000)/1000=(75,200+20,000+60,000)/1000=**155.2 ms**. p95 rank 950=**400 ms**; p99 rank 990=**400 ms**; p99.9 rank 999=**6000 ms**. Sample p95 ≤500 ms **passes**, 10 requests nevertheless take 6 seconds.

**P5:** P(all fast)=0.98^10≈**81.71%**. P(any slow)=1−0.98^10≈**18.29%**. All-required API is slower than 300 ms whenever any required dependency is slow, under model assumptions. Its actual **numeric** p99 duration cannot be calculated without the slow-time distribution, overhead and actual correlation.

**P6:** At fixed rates backlog grows (450−400)=**50/s** or **3,000/min**. Triage: define impact/timeouts/queue age, bound admission and prioritize critical work, avoid retry storms, apply reversible mitigations if urgent. Correlate slow traces, DB acquire wait, connection hold, query execution, locks/I/O, DB limits, app retries and request fan-out. Pool increase only after demonstrated excessive acquire wait and verified spare DB capacity. Run bounded trial, compare p95/p99, errors, DB lock/I/O/CPU, queue-depth recovery; roll back when thresholds worsen. No invented root cause or universal pool size.

## 15. Revision prompts **with complete answer key** (model-only)

| Prompt (hide right side for self-test) | Reference answer |
|---|---|
| **R1.** RPS vs concurrency? | Rate of completions vs mean number simultaneously inside the system; L=λW for stable same boundary. |
| **R2.** Why no M/M/1 finite steady W at λ≥μ? | Arrivals equal/exceed a single server's sustained service capacity; backlog is not stably bounded in the unlimited-queue model. |
| **R3.** Can average improve while p99 worsens? | Yes: most requests get faster while slow outliers become worse; inspect distributions/time windows. |
| **R4.** 100 Mbps equals how many MB/s? | 12.5 theoretical MB/s before overhead, *not* 100 MB/s. |
| **R5.** Why doesn't 100% DB pool use prove slow query plans? | Could be long-held connections, acquisition queues, leaked handles, retries, locks or insufficient pool. Separate measurements. |
| **R6.** Why may separate pods still impact each other? | Shared node CPU/memory/network and downstream DB/queues/quotas; protect with explicit limits and fallback. |
| **R7.** What justifies increasing pool size? | Verified acquisition waiting + healthy query/hold times + downstream capacity, reversible test and measured improvement. |
| **R8.** Why include failures/timeouts in performance analysis? | Success-only p95 may hide pending, rejected and timed-out customer operations, misstating user impact. |

**Suggested revisit:** after 1–2 days attempt R1–R4; after a week solve P4/P5 plus R5–R8; after a month attempt an unseen overload-and-DB trace case. Log new evidence only after an actual independent attempt.

## 16. Glossary, sources and next steps

| Term | Meaning |
|---|---|
| SLI | Service Level Indicator — the actual measurement |
| SLO | Service Level Objective — target for an SLI |
| SLA | Service Level Agreement — contractual/service commitment, sometimes with consequences |
| p95, p99, p99.9 | 95th, 99th, 99.9th percentile of a specified measured population |
| TTFB | Time To First Byte |
| M/M/1 | Markovian arrivals / Markovian service duration / one server |
| TTL | Time To Live — e.g., cache expiration rule |
| GC | Garbage Collection |
| Bulkhead | Resource/failure isolation (not absolute where dependencies shared) |
| Singleflight | Coalescing duplicate concurrent work for the same key |
| Admission control | Deciding whether new work enters a constrained service |
| Load shedding | Intentionally declining excess/optional work to protect healthy service |

### Project grounding
- [Syllabus V2 — Phase 0](../../../curriculum/SYLLABUS_V2.md)
- [Earlier functional requirements and business scope](../functional-requirements/README.md)
- [Non-functional requirements and provisional p95 target](../non-functional-requirements/README.md)
- [Requirement conflicts and prioritization](../requirement-conflicts-and-prioritization/README.md)
- [Canonical resource catalog](../../../resources/README.md)
- [Learning Lab Playbook](../../../docs/LEARNING_LAB_PLAYBOOK.md)

### Sources for technical practice (linked, not copied)
- [Google SRE Book — Service Level Objectives](https://sre.google/sre-book/service-level-objectives/) — latency distributions, tail percentiles and consistent measurement boundaries.
- [OpenTelemetry — Traces](https://opentelemetry.io/docs/concepts/signals/traces/) — spans and cross-service request correlation.
- [AWS Builders' Library — Timeouts, retries and backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/) — how retries can exacerbate overload and consume limited resources.

### Optional *topic-matched* deep dives (only from the canonical resource catalog)
- **Primary: [Roberto Vitillo, Understanding Distributed Systems](https://understandingdistributed.systems/)** — connect networking/request boundaries, distributed uncertainty and latency trade-offs. Optional, not evidence of prior reading; no unverified edition/page/section claimed.
- **Complementary: [Google SRE books](https://sre.google/books/)** — deeper operational treatment of percentiles, service objectives and measurable production behavior. Optional; no precise section asserted.

**Next eligible topic:** **Back-of-the-envelope capacity estimation** ([Progress](../../../progress/PROGRESS.md)); **NOT STARTED** until explicitly begun. Reuse arrival/throughput/payload/concurrency models while estimating traffic, storage, peaks, growth, headroom and cost.

**Open assumptions to verify in any real system:** actual Chhath peak and mix, client network distributions, p95 SLO window/eligibility, error/timeout accounting, pool acquisition/hold metrics, node/DB I/O/locks, workload profile, operational owner and rollback plan.

---

**Completion scope:** Chapter publication is authorized and finished as a learning artifact. Rating records discussion coverage, not independent interview or production/Principal mastery.
