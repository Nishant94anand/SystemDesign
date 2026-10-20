# Requirement Conflicts and Prioritization

- **Status:** Completed (learning discussion and reference artifact; not mastery)
- **Phase:** 0 — Foundations and problem framing
- **Last updated:** 2026-10-21
- **Discussion-coverage assessment:** **4/5** (see [evidence and gaps](#14-learning-evidence-and-assessment))
- **Case study:** Single-outlet Gaya Puja Samagri customer-facing web shop, with a Chhath festival demand spike
- **Prerequisites:** [Functional requirements](../functional-requirements/README.md) · [Non-functional requirements](../non-functional-requirements/README.md)
- **Curriculum:** [Syllabus V2](../../../curriculum/SYLLABUS_V2.md) · [Learning workflow](../../../CopilotInstructions/LEARNING_WORKFLOW.md) · [Daily Log](../../../progress/DAILY_LOG.md)

> **Status of decisions:** The shop, deadline, budget limits, performance targets, queuing policies, confirmation deadlines, and failure traces below were used as **hypothetical teaching scenarios or provisional proposals**, not signed business requirements, measured production results, or deployed architecture. The V1 scope established in the functional-requirements chapter remains authoritative. Do not implement proposed business-policy changes without stakeholder agreement.

## 1. Learning objectives and why this matters

After this chapter, you should be able to:

1. Detect contradictory requirements *before* choosing databases, queues, and services.
2. Distinguish **correctness invariants**, hard external constraints, quality objectives, preferences, hypotheses, business policy, and implementation decisions.
3. Identify which outcome matters to the customer and which measurable evidence would justify a target.
4. Compare credible alternatives across revenue, fairness, latency, cost, security, and operational burden.
5. Explain overload and partial failure without pretending timeouts, queueing, or a grace period provide absolute guarantees.
6. Write and defend a short **decision record** with options, rejected alternatives, risks, owners, tests, and revisit triggers.

**Why:** In an engineering review, the hard problem is often not "How do we make it fast?" but "What exactly are we promising, under which conditions, and which promise must yield when two conflict?"

## 2. Intuition: the Gaya shop during Chhath

A physical shop has a finite counter capacity, finite Puja Kit stock, and customers who arrive in bursts. The owner wants every shopper served instantly without increasing staff or stock. The engineer must ask whether "serve" means *admit to the shop*, *take a request*, *collect payment*, or *hand over a committed kit*.

| Physical-shop analogy | System-design meaning |
|---|---|
| Entry token / customer line | Waiting room / checkout admission policy |
| Counter can serve 60 customers per minute | Sustainable processing throughput under an assumed workload |
| Kit put aside for a buyer | Time-bounded inventory reservation (business claim) |
| Shopkeeper takes money before checking stock | Paid-but-unfulfilled business risk |
| Promise "your kit is ready" | Order confirmation requiring defined commitments |
| Customer leaves line | Abandonment / expiry / fairness policy |

**Where the analogy breaks:** Computers run concurrent operations, requests are duplicated, responses may be lost while actions succeed, payment providers are independent, and a physical shop's inventory ledger may lag actual stock. FIFO at one queue does not imply globally fair allocation across all channels.

Our actual V1 scope remains one Gaya outlet, responsive web, shared inventory with the physical store, browsing/cart, address/serviceability, checkout including online payment integration **and cash on delivery**, registered order history, verified guest lookup, and integration with existing fulfillment. **No new in-house gateway, native app, delivery implementation, admin UI, or back-in-stock alerts.** A hypothetical checkout-invitation message is a design-policy discussion, *not* approval for a general back-in-stock feature.

## 3. A precise vocabulary for negotiating requirements

| Class | Meaning | Case example | What validates it? |
|---|---|---|---|
| **Business constraint** | Bound imposed externally; may be fixed for the project | Chhath launch date; spending cap | Sponsor confirmation, dates, approved cost envelope |
| **Correctness invariant** | Must remain true for all successful states in the defined fault/ownership scope | Cannot confirm more kits than actually allocatable inventory | Concurrency/failure tests and reconciliation |
| **Business policy** | Stakeholder-owned eligibility/priority/outcome rule | FIFO checkout turns; reservation timeout | Written owner sign-off; observed customer impact |
| **Quality objective / SLO** | Target for quality measured over a population/window | 95% of eligible checkouts decided within 5 min | Defined timer, denominator, environment and instrumentation |
| **Preference / optimization** | Desirable if feasible; usually traded against another goal | Minimize avoidable technical rejection | Conversion/support metrics and marginal cost |
| **Assumption / hypothesis** | Input not yet established as fact | 3,000 read RPS at a new launch | Traffic forecast or measured/load-test workload evidence |
| **Implementation choice** | Mechanism chosen *after* outcome is agreed | Virtual waiting room, idempotency-key persistence | Comparison, design review and tests |

**Non-negotiable for correctness** is not the same as **commercially unchangeable**. A fixed budget limits the feasible solution set; no-overselling defines what successful confirmations must mean. Neither removes the need for a truthful availability and stock source.

**Absolute language requires scope.** "Zero overselling" can mean *no double allocation of one unit* under concurrently processed online orders, or may include simultaneous in-store sales, inventory recounts, damaged goods and supplier mistakes. A meaningful contract explicitly states the stock source and fault/consistency boundaries. Security/privacy and no false confirmations are also fundamental, even if a four-item stakeholder prompt omits them.

## 4. A repeatable decision process

1. **State the business outcome.** Chhath shoppers need timely, trustworthy purchase outcomes; shop needs sales without breaking the fixed budget.
2. **Separate facts from assumptions.** The 3,000 read-RPS estimate was a stress model, not a launch forecast; "1-second p95" needs business evidence.
3. **Define each promise precisely.** When does the order clock start? Which outcomes count? What is the eligible population? Who owns a third-party timeout?
4. **Find the real conflict.** Is existing capacity insufficient under an agreed load, or are we assuming conflict without evidence?
5. **Protect correctness/security.** Do not solve an overloaded checkout by confirming nonexistent stock or hiding a successful payment.
6. **Offer alternatives with consequences.** Relax a target, limit admissions, reduce scope, improve capacity/operations, or accept a clearly described delay/failure mode.
7. **Make a recommendation.** State why it meets the business objective *better than the alternatives*, not merely "the customer should choose."
8. **Record acceptance tests, owners and triggers.** Decisions are revisited when reality changes.

~~~mermaid
flowchart TD
    A["Stakeholder request"] --> B["Classify: constraint / invariant / target / assumption"]
    B --> C{"Conflict demonstrated by evidence?"}
    C -->|No| D["Keep target; validate workload and acceptance boundary"]
    C -->|Yes| E["Preserve invariants and security boundaries"]
    E --> F["Compare options: impact, cost, feasibility, risk"]
    D --> G["Document test, owner, and revisit trigger"]
    F --> G
    G --> H["Stakeholder decision and explicit sign-off"]
~~~

*Figure 1 — Requirement negotiation is a feedback loop, not a license to weaken guarantees prematurely. A demonstrated conflict includes a known constraint and evidence or explicit scenario assumptions.*

### A lightweight prioritization scheme

- **Must protect:** selected correctness, safety, security and contractual obligations, with explicit scope.
- **Must deliver within the release:** time and budget envelope; essential in-scope customer journey.
- **Target:** measurable experience and resolution objectives with clear conditions and exception handling.
- **Optimize:** rejected-checkout rate, waiting time and business conversion *inside* the guardrails.

Frameworks such as **MoSCoW** (Must/Should/Could/Won't) or a decision matrix can help, but labels do not prove feasibility. A "Must" with contradictory "Must" needs explicit escalation or scope/contract revision.

## 5. Core case — fixed budget, conflicting targets

The teaching scenario used these hypothetical demands:

| Request | Initial treatment | Why |
|---|---|---|
| Release in two weeks for Chhath | Fixed business constraint **if owner confirms** | Missing festival opportunity has real business consequences |
| Limited engineering budget | Fixed input in this exercise | Cannot simply purchase unlimited capacity |
| No overselling | Correctness guardrail | Misleading confirmations damage both buyers and seller |
| 1-second product-list p95 | **Provisional**, not automatically a hard requirement | Need user-impact evidence, request boundary and load |
| 3,000 read RPS | Unvalidated stress assumption | A new launch has no automatically established demand |
| 5-minute final resolution | Candidate target with *defined exceptions or reduced promise* | Third-party payment outcomes can be unknown beyond timeout |
| Zero rejected orders | **Not** an unconditional guarantee | Finite stock and unpredictable dependencies make it infeasible under some demand |
| Minimize avoidable rejection | Optimization objective | Real customer effort, refund friction and lost conversion matter |

Even if the business insists on "all orders eventually confirmed," indefinite stock scarcity makes that an indefinite promise. "Replenishment on time" does not imply sufficient sellable inventory for all pending buyers.

### A small, honest capacity calculation

Assume **for illustration only**: 100 new checkout requests per minute for a sustained peak, 60 completed requests/minute of sustainable capacity at the specified workload, and no abandonment, cancellation, or admission shedding.

~~~text
Arrival rate λ = 100 requests/min
Service rate μ = 60 requests/min
Backlog growth = λ - μ = 40 requests/min
After 5 min: 40 × 5 = 200 queued requests
After 20 min: 40 × 20 = 800 queued requests
~~~

A queue or async job can change **where customers wait**, but does **not** change the 60/minute capacity. The example shows **linear** backlog growth under constant rates, not exponential growth. In reality burstiness, bottlenecks, abandonment, rate limits and arrival variations change the shape. Stable long-run service requires arrivals below available processing capacity (with operational headroom) or a deliberate bounded-admission policy.

A separate earlier **3,000 read-RPS** scenario concerned catalog reads; it must not be confused with **100 checkout orders/minute**. Request types, units and load mix matter.

### What can business legitimately choose?

| Option | Outcome | Customer/operations cost | Evidence needed |
|---|---|---|---|
| Loosen waiting/resolution time | Process more backlog without extra capacity | Waiting and abandonment | Conversion vs delay; p95/p99 checkout times |
| Restrict checkout admission | Protect accepted attempts | Some customers cannot buy now | Rate rejected before payment; peak demand |
| Accept async intent, notify later | Customer need not stare at a spinner | State tracking, communication, later stock uncertainty | Pending age, pickup/return rate, customer feedback |
| Reduce nonessential V1 scope | More focus on checkout quality | Fewer launch features | Sponsor priority; effort estimate |
| Add justified capacity later | Higher feasible throughput if actual bottleneck scales | More cost and operations | Load-test bottleneck and marginal spend |

**A queue does not guarantee stock.** A waiting-room token may give earlier checkout *opportunity* without reserving a kit. Accepting a purchase request is different from committing that a kit will be fulfilled.

## 6. Order acceptance, inventory fairness and payment uncertainty

### Define the promise before the mechanism

| Customer-visible state | Meaning | Not a promise of |
|---|---|---|
| **Request received** | An incoming attempt was observed | Durable order acceptance or stock |
| **Accepted for processing / Pending** | Purchase intent durably tracked, subject to checks | Confirmed fulfillment |
| **Confirmed** | Required stock/payment/fulfillment commitments satisfied per agreed contract | Immediate physical delivery |
| **Rejected / Cancelled** | Fulfillment will not proceed under this attempt | Payment was necessarily unsuccessful |
| **Pending reconciliation** | External outcome unresolved; customer-visible state honest | A five-minute final decision |

**Order, payment and refund statuses must be modeled separately.** Order **Rejected** can coexist with Payment **Succeeded** and Refund **Pending**. A timed-out callback is not proof payment failed; a late callback is not proof a payment happened late. Refund **initiated** is not refund **settled to original source**. The external provider controls aspects of settlement timing.

Safety-oriented business invariants for future technical design:

- **No overselling:** confirmed allocation never exceeds valid allocatable stock (including the shared physical outlet's inventory changes within the contracted boundary).
- **No false confirmation:** only label a purchase confirmed after required commitments are made.
- **No silent lost money:** any discovered successful payment without a valid confirmed order has a durable, attributable resolution/refund obligation.
- **Retry same intent ≠ new purchase:** retrying one logical checkout should not create another business order or unintended duplicate charge; intentional new intent is separate.
- **Finite inventory holds:** holds expire by documented policy; a successful late payment does not silently resurrect an already-reassigned kit.
- **Privacy:** registered and guest status access is authorized; knowing an order ID alone is not enough.

### Fairness decisions

| Policy | Benefit | Cost/ambiguity |
|---|---|---|
| FIFO **checkout admission** | Earlier arrival receives earlier attempt opportunity | No guaranteed stock; slow/inactive customer may stall others |
| FIFO **inventory allocation** | Earlier eligible customer has priority claim | Requires precisely defined eligibility, timing, expiry and multi-channel coordination |
| First successful payment wins | Avoids stock held by inactive shoppers | Earlier queued customers may feel unfairly displaced |
| Short bounded reservation **at checkout** | Protects active buyer's chance | Idle holds deny stock to others until expiration |
| Reserve from waiting-room entry | Stronger earlier commitment | Abandonment hoards scarce kits; complex expiry/release |

A **working proposal**, not approved V1 policy: no stock reservation for *joining* the waiting room; admit checkout in FIFO order where possible; optionally hold eligible stock for a bounded time when active checkout begins; release on expiry; define stockout communication. Distinguish FIFO of **admissions**, not automatically FIFO of **confirmed orders**. Do not assume in-store sales stop while the web queue runs.

### Payment expiry failure timeline

~~~mermaid
sequenceDiagram
    participant C as Customer
    participant S as Shop checkout
    participant I as Shared inventory
    participant P as External payment provider
    C->>S: Start checkout
    S->>I: Request bounded stock hold
    I-->>S: Hold valid until 10:05:00
    C->>S: Initiate payment at 10:04:55
    S->>P: Payment attempt (external)
    Note over S,I: Hold expires at 10:05:00 unless policy extends it
    I-->>S: Expiry / release candidate
    P-->>S: Success reported at 10:05:08
    Note over S,P: External completion time may differ from callback receipt
    S-->>C: Truthful order/payment state
    Note over S,I: If stock already released, never over-confirm; reconcile/refund
~~~

*Figure 2 — Simplified, conceptual message ordering. Real implementation must coordinate concurrent expiry, payment, allocation and retries; diagram does not prove atomicity or mandate a specific protocol.*

**Strict expiry (A):** release stock at deadline; late verified payment success may require order rejection and refund. **Conditional grace (B):** if a *valid* attempt started before expiry, keep the hold for a capped additional time; potentially fewer unnecessary refunds at cost of more blocked inventory.

Neither policy can guarantee no late responses for **arbitrary external delays**. A grace period affects *frequency of a bad experience*, not fundamental correctness. If an owner demands both unconditional five-minute final resolution and no unknown outcome at five minutes, the requirements conflict with an arbitrary-latency external provider unless the external contract and supported fallback make the promise feasible. **Show Pending Reconciliation truthfully rather than fabricating success/failure**; explicitly negotiate the exception, and count it correctly when measuring a final-resolution SLO.

A payment/checkout hold is an **implementation-independent reservation contract** here. The exact database locking, transaction isolation, idempotency, race handling and payment flow belong to later design labs. Do not treat a session-side flag as sufficient protection against multi-device retries or missing acknowledgements.

## 7. The measurable requirements contract

Vague: "The website should never crash at Chhath"; "no customer should wait too long"; "reject almost nobody."

A proposed **measurement template**, not an approved SLA:

| Metric/guardrail | Proposed definition / unresolved acceptance decision |
|---|---|
| Confirmed-order stock integrity | Count of confirmations with insufficient allocatable stock should be **zero** in agreed fault/test scope; include physical-store sync cases |
| Time to terminal order decision | From **durable purchase-intent acceptance** to Confirmed/Rejected/Cancelled; agree percentile, evaluation window and eligible intents; Pending Reconciliation is **not** terminal |
| Checkout admission wait | From requesting admission to getting a usable checkout turn; measure percentiles and abandonment separately |
| Technical admission rejection | Due to overload / admitted attempts, over defined period; distinguish out-of-stock, payment/verification failure and voluntary cancellation |
| Paid-but-rejected ratio | Successfully paid attempts whose orders cannot be fulfilled / successfully paid attempts; associate refund progression |
| Reconciliation backlog | Number and oldest age of unresolved payment outcomes; owner escalation and external provider reconciliation |
| Refund completion | Separate initiation delay and confirmed settlement delay, tied to provider capability |
| Budget | Approved monthly/event envelope and staff/on-call cost, not just virtual-machine count |

**Illustrative threshold construction (do not treat as fact):**

- Target: at least 95% of eligible checkout attempts resolved terminally within 5 minutes **during an agreed peak test window**.
- Exclude or separately report unresolved third-party outcomes only if that exception has been explicitly approved. Otherwise those attempts miss final-resolution SLO.
- Trigger review if the resolution SLO misses its target over two designated event windows, or if paid-but-rejected attempts exceed an agreed threshold. The **threshold amount must be negotiated using baseline demand, loss magnitude, and acceptable customer harm**; no arbitrary production percent is claimed here.
- If physical stock is wrong in reconciliation, fix stock-source integrity before claiming no overselling.

**Acceptance test matrix (examples, not test results):**

| Setup | Required observation |
|---|---|
| Parallel buyers for last remaining kit | Only one valid confirmation; all other outcomes truthful |
| Hold expired, late payment reported success | No revived sold stock; refund/reconciliation tracked to resolution |
| Response lost after durable acceptance; client retries same intent | No unintended second order/charge; discoverable state |
| Payment outcome unknown after target deadline | Pending Reconciliation exposed; not misreported as final; escalation recorded |
| Simulated arrival 100/min with completion 60/min | Queue/admission policy's bounded delay and rejection behavior observed; no fabricated 100/min service capacity |
| Guest attempts to read another guest's purchase | No private order/payment info disclosed |
| In-store sale races web checkout | No false stock confirmation inside agreed inventory synchronization semantics |

**Security/operations/cost:** Reconciliation and refund access requires authorization and audit; do not log payment secrets or private customer information. Instrument tracing for checkout attempts, failed stock allocation, queue age, gateway outcome, refund debt, and event-day resource pressure. Define ownership between web team, shop inventory operators, gateway/support personnel and on-call team. A more complex queue or grace policy adds state transitions, recovery tests and operational toil; it needs demonstrated benefit.

## 8. Decision matrix and a sample engineering recommendation

| Business outcome | Candidate A: strict expiry | Candidate B: conditional payment grace | Unknown that can reverse choice |
|---|---|---|---|
| Reduce paid-but-rejected orders | More late-success exceptions | May reduce some if grace covers delay | Payment outcome and callback-delay distributions |
| Free stock for next shopper | Earlier | Later (bounded) | Stock scarcity / abandonment rate |
| Implementation simplicity | Fewer expiry rules | Eligibility and extra state transitions | Team capacity, tests, support burden |
| Correctness | Possible only with reconciliation and safe allocation | Possible only with reconciliation and safe allocation | Race cases, payment and inventory integration |

**Provisional recommendation:** protect no-overselling, no false confirmation, payment accountability and access boundaries. Under a confirmed fixed budget and release date, preserve essential V1 journeys; make five-minute terminal resolution an **explicitly bounded and measured target**, not an absolute guarantee against arbitrary third-party delays; optimize technical rejection with fair admission and honest pending states. Prefer simple, bounded holds and add grace **only if** observed late-result reduction justifies idle inventory blocking, extra operational complexity and refund exposure. Neither 30 seconds nor any other timeout is proven optimal.

**Do not claim** a waiting room, async checkout and five-minute SLA were signed off, or that all orders must be paid in advance. The actual FR scope supports cash on delivery and online payment; any new flow must preserve that scope unless revised by stakeholders.

### Mini decision record (source discussion vs refined memo)

| Field | Independent learner submission | Strengthening needed for production review |
|---|---|---|
| Decision | Hard: zero overselling; negotiable: 5-minute resolution; best effort: minimize rejection | Also state fixed **budget** and **release date** as explicit business constraints; scope integrity guarantee |
| Reason | Correctness and customer impact; not all causes of rejection are controllable | State eligibility, stock and gateway boundary, why alternatives not chosen |
| Risks | Rejections; resolution beyond five minutes | Give mitigation, customer communication, escalation/owner and fallback |
| Revisit | "Too many" rejections; time becoming "exponentially long" | Define denominator, percentile, window, threshold and action; queue grows linearly in the constant-rate example |

This last column is **review feedback**, not part of the learner's independent response.

## 9. Common misconceptions and corrections

1. **"A 3,000 RPS stress assumption is a production fact."** No; model it, label confidence and validate traffic sources.
2. **"Changing checkout to asynchronous removes capacity limits."** No; the backlog continues when arrivals exceed service rate.
3. **"Higher grace period guarantees late gateway results won't happen."** No finite grace bounds arbitrary external delay.
4. **"Payment succeeded, therefore order confirmed."** Not necessarily; payment/stock/order are separate commitments.
5. **"Returning an error means payment definitely failed."** No; a lost response/timeout leaves ambiguous outcome.
6. **"FIFO is always fair."** Fairness depends on which event gets priority and when the claim expires.
7. **"Zero technical rejects means zero stockouts."** Different denominators and failure categories.
8. **"A 5-minute p95 means every order resolves in 5 minutes."** No; p95 allows a slower tail. A strict deadline is a different contract.
9. **"Load test results prove actual festival demand."** A test measures behavior under a workload; it does not forecast demand.
10. **"Client/stakeholder must decide everything."** Engineering should recommend a reasoned option and bring choices for approval.

## 10. Principal-level review checklist

- Whose outcome is optimized? Which customers lose when someone else wins?
- Which invariant should hold regardless of traffic, retry, or crash?
- How are confirmed, rejected and pending distinguished? Which events are durable?
- Which promises rely on uncontrolled third parties or shared offline inventory?
- Is the performance limit measured at user experience, ingress, or internal API boundary?
- How costly are false positives/false confirmations vs truthful delay/rejection?
- What is the reversible, least-complex V1 alternative?
- Who handles unresolved money, stock mismatch and post-launch incident?
- How will release rollout, monitoring, support and rollback work on festival day?
- What evidence/threshold would force us to change admission, grace, inventory or budget policy?
- Is customer agreement documented, or are we unintentionally turning an engineering hypothesis into a contract?

## 11. Practice and graduated hints (unattempted unless explicitly marked)

**P1 — Independent negotiation:** A small regional store insists on 99.99% order availability and no increased cost. Offer two bounded alternatives, their user-visible consequences, and one piece of evidence that could change the choice.

<details><summary>Hint 1</summary>Separate an availability target from an inventory-supply guarantee, and check the current failure modes.</details>
<details><summary>Hint 2</summary>Define the eligible operation/window and the loss the owner is willing to accept. Do not assume additional servers are the only lever.</details>

**P2 — Numerical overload:** In a simplified constant-rate model, arrivals are 150 checkout attempts/min and sustained completions are 110/min. With no shedding/abandonment, calculate backlog at 3 and 15 minutes. Does making acceptance async fix this?

<details><summary>Hint 1</summary>Use arrivals minus completions with consistent units.</details>
<details><summary>Hint 2</summary>Separate when a request is acknowledged from when work is actually completed.</details>

**P3 — Boundary review:** A limited item is physically sold while a web buyer is paying; gateway response is delayed, and the user retries from another device. Define three invariants, two customer-visible states and test cases; do not choose a database yet.

<details><summary>Hint 1</summary>Distinguish order intent, allocation commitment, payment outcome and refund obligation.</details>
<details><summary>Hint 2</summary>Consider both cross-channel stock changes and same-intent duplicate requests.</details>

**P4 — Timed decision memo:** In 10 minutes, produce an ADR for a fixed-budget festival launch with a strict no-overselling rule and uncertain third-party payment. Include decision, rationale, two alternatives, failure fallbacks, measurement definitions, owner and revisit trigger.

<details><summary>Hint 1</summary>Use "must protect", "proposed SLO", "best effort" and "unverified assumption" labels.</details>
<details><summary>Hint 2</summary>Convert "too many failures" into a threshold, denominator, window, and responsible action.</details>

**Answer policy:** These additional problems are **not attempted** in the recorded session, so no model solution is published. The learner's *already attempted* memo and separate review are preserved in [Section 8](#8-decision-matrix-and-a-sample-engineering-recommendation).

## 12. Revision prompts and interview rehearsal

Try without notes, then revisit in 2–3 days and again after 1–2 weeks:

- In one minute, explain a hard correctness invariant versus a business deadline versus an SLO.
- Explain why a 30-second grace period is not a correctness guarantee.
- Diagnose the difference between "customer request accepted", "payment success", and "order confirmed".
- Explain what data you would require to justify 1-second instead of 2-second product-list p95 (actual architecture impact comes in the next latency and design chapters).
- Defend a bounded-inventory fairness policy for a festival peak, then reverse it if customer abandonment doubles.
- Write a single measurable trigger for revisiting the rejection and pending-order policy.

**Interview prompt:** "Your owner demands zero rejected orders, zero overselling, a five-minute resolution and a fixed launch budget; what do you do?" Model **reasoning structure only** (the user already attempted this specific scenario): establish infeasibility/conditions; preserve correctness and security; prioritize customer outcome; propose bounded alternatives; state assumptions and evidence; negotiate explicit exception semantics; log responsible owner and revisit condition. This is not a reusable memorized answer.

## 13. Quick revision and glossary

**Five principles:**

1. Validate alleged conflicts: do not trade off requirements without evidence.
2. Never weaken a correctness invariant to make a dashboard green.
3. A queue manages admission/waiting; it does not create throughput or inventory.
4. An honest unresolved state is preferable to a false terminal state, even if it violates an unqualified deadline promise.
5. A Staff/Principal decision specifies **why, boundaries, metrics, owners, and conditions for reversal**, not just an option.

**Glossary**

- **ADR:** Architecture Decision Record; context, options, selected choice, consequences and review triggers.
- **Invariant:** Property that remains true across allowed state transitions and failures within its defined scope.
- **SLO/SLI/SLA:** Objective / indicator / contractual service agreement, respectively; see [NFR chapter](../non-functional-requirements/README.md).
- **p95/p99:** Percentiles of a defined eligible sample, not guarantees on every operation.
- **Admission control:** Deciding which requests can start consuming limited resources.
- **Backpressure:** Mechanism/signaling that reduces incoming work as capacity is approached.
- **FIFO:** First In, First Out; define precisely what is ordered (arrival, admission, or inventory allocation).
- **Reservation / hold:** Temporary, bounded business claim on available stock; not necessarily a DB lock.
- **Idempotency:** Repeating the same intended operation does not create extra business effects within a well-defined scope.
- **Reconciliation:** Detecting and resolving differences between local and external payment/order state.
- **Tail latency:** Slow end of the latency distribution, often relevant for customer-visible deadlines.
- **RPS:** Requests per second; read RPS and order-commit rate are not interchangeable.

## 14. Learning evidence and assessment

**Discussion coverage: 4/5** — strong interactive coverage and independent prioritization attempt; not a certification of design mastery.

| Capability | Observed evidence | Confidence |
|---|---|---|
| Explain/classify | Challenged unvalidated 3,000 read RPS and 1-second goal; prioritized integrity over latency | Independent |
| Apply | Proposed a bounded checkout hold and FIFO fairness; identified abandonment and blocked stock cost | Independent with iterative feedback |
| Diagnose | Noticed **any finite payment grace** leaves late-result uncertainty; chose truthful pending reconciliation | Independent |
| Defend | Argued why a fast false promise or indefinite paid-pending state hurts trust; wrote a four-part decision memo | Attempted independently; reviewed with guidance |
| Validate | Requested performance/load-test evidence and reconsidered policies | Partially demonstrated; thresholds, denominators, operational ownership and exception measurement not specified independently |

**Independent memo (paraphrased accurately):** Hard constraint — zero overselling; negotiable — five-minute resolution; "uncommitted good-to-have" — minimize rejections. Reasoning: correctness and time-sensitive customer impact; cannot control all rejection factors. Risks: rejections and longer resolution. Revisit: unusually high rejection or very long resolution. Gaps: fixed release/budget were omitted in memo; "too many" and "exponentially long" weren't measurable; mitigation owners and eligibility/percentile boundaries missing.

- **Readiness:** Practicing, progressing toward Strong on requirements negotiation. **Interview readiness:** no unseen timed interview/independent defense yet; unassessed. **Production/Principal readiness:** not demonstrated; needs signed-off acceptance, ownership, controlled experiments, security/race tests and release/incident plan.
- **Important separation:** Finishing this chapter records agreed discussion coverage and a reference artifact; it does **not** imply independent mastery or that its illustrative business rules were approved.
- **Next reinforcement:** Re-attempt P4 under changed stock/payment/provider conditions, explicitly supply denominator, target percentile, window, exception policy, owner and reversal trigger. Pair with capacity/latency exercises.

## 15. Open decisions and constraints

- Validate Chhath launch timing, calendar deadlines, fixed budget magnitude and non-negotiable V1 scope.
- Obtain forecast of peak checkout writes vs catalog reads; traffic, conversion and in-store inventory change patterns; load-test with representative request mix.
- Decide shop-ordering hours/cutoffs, stock accuracy/physical synchronization, FIFO fairness, reservation expiration, payment initiation boundary and refunds, including COD differences.
- Agree what *final order resolution* means, which intents are eligible, which time starts the timer, observation windows, possible exceptions, and what a breach triggers.
- Decide reconciliation ownership, retry/cancellation rules, explicit refund initiation/settlement communications, verified guest lookup, customer data-retention boundaries.
- Distinguish a deliberately degraded experience, e.g., temporary admission denial, from an outage; define alerting and customer messaging.
- Validate that added complexity (waiting room, callback handling, grace, queue) has positive marginal business benefit given team size and fixed launch date.
- Later chapters supply algorithms, API semantics, database concurrency, exact capacity models, payment race proofs and threat-model validation.

## 16. References and related study

**Project references (authoritative for this learning record):**

- [Syllabus V2 — Phase 0](../../../curriculum/SYLLABUS_V2.md)
- [Functional requirements and V1 scope](../functional-requirements/README.md)
- [Non-functional requirements, SLI/SLO measurement and ambiguous checkout](../non-functional-requirements/README.md)
- [Learning Lab Playbook](../../../docs/LEARNING_LAB_PLAYBOOK.md)
- [Resource catalog](../../../resources/README.md)

**Optional external deep dives** (for independent further study; not evidence these were read):

- [Google SRE books](https://sre.google/books/) — reliability objectives, measurement and cost of failure.
- [AWS Builders' Library](https://aws.amazon.com/builders-library/) — timeout/retry and overload decision-making.
- [Understanding Distributed Systems — Roberto Vitillo](https://understandingdistributed.systems/) — distributed uncertainty, partial failures and system boundaries.

All prose, tables, diagrams and scenarios in this chapter are original learning notes. Numerical scenarios are labelled hypothetical; third-party content is linked rather than reproduced.
