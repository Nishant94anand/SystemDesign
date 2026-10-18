# Non-Functional Requirements and Measurable Targets

- **Status:** Completed discussion/reference chapter (not a mastery certification)
- **Phase:** 0 — Foundations and problem framing
- **Last updated:** 2026-10-19
- **Discussion coverage:** 4/5; interview and production/Principal readiness assessed separately below
- **Scenario:** Gaya Puja Samagri online shop, building on [functional requirements](../functional-requirements/)
- **Syllabus:** [Phase 0](../../../curriculum/SYLLABUS_V2.md) · [Progress](../../../progress/PROGRESS.md) · [Daily Log](../../../progress/DAILY_LOG.md)
- **Adjacent topics, not completed by this chapter:** [requirements conflicts](../../../progress/PROGRESS.md), quantitative latency/load, availability/reliability, dedicated SLO/error-budget treatment, and distributed retries/idempotency.
- **Abbreviations:** See the [Glossary and abbreviation expansions](#17-glossary) for full forms (including SLI, SLO, SLA, RPO and RTO). Important terms are also expanded where they are introduced.

## 1. Learning outcomes and boundaries

By the end of this discussion, the learner should be able to:
1. Transform vague quality adjectives into **specific, testable, context-bound requirements**.
2. Separate a **business requirement**, an **assumption**, an **architectural solution**, and **verification evidence**.
3. State quality attributes: performance, capacity/scalability, availability, reliability, durability, security, privacy, maintainability, operability, and cost.
4. Explain SLI vs SLO vs SLA and a basic error-budget calculation.
5. Identify customer-visible correctness guarantees, including ambiguous checkout outcomes.
6. Defend trade-offs without assuming that faster, more redundant, or more abstract is automatically better.
7. Identify open assumptions and measurements required before design sign-off.

**Boundary:** This chapter is about **framing and measuring non-functional requirements (NFRs)**, not choosing a database (DB), committing to replication topology, implementing idempotency, or completing later dedicated syllabus topics.

## 2. Why NFRs exist: Chhath shop analogy

A Gaya shop promises that people can browse Puja Samagri, order during working hours, pay, and get a durable confirmation. Those are customer-facing capabilities (functional requirements). But business success also depends on whether customers can complete them during Chhath traffic, whether private addresses leak, whether confirmed orders survive failures, and whether a small team can operate the site economically.

**Mapping:** shop opening/ordering service -> availability boundary; checkout line length -> user-perceived latency; correct receipt/fulfilled order -> reliability and correctness; preserved ledger -> durability; staff access to records -> authorization; onboarding a second delivery partner -> maintainability.

**Analogy limits:** software concurrency is not a physical queue; a DB commit, a payment capture, and a customer acknowledgement are distinct asynchronous events. Real guarantees need stated failure models and explicit measurement boundaries.

## 3. Write a measurable NFR, not a solution

Bad: "Website must be fast, secure, highly available and scalable."

A useful NFR includes:

| Field | Question |
|---|---|
| **Quality / outcome** | Which customer journey or invariant matters? |
| **Boundary** | Which route/operation, region, user population, and hours? |
| **Target** | What measurable threshold or binary guarantee applies? |
| **Load / failure model** | At what traffic level, dependency condition or failure scope? |
| **Window** | Per request, five-minute rolling window, or calendar month? |
| **Measurement** | Where does timing begin/end? What counts as eligible, successful or failed? |
| **Validation** | Load test, fault injection, access test, code review, incident evidence? |
| **Status** | Confirmed business decision, proposed target, or assumption? |

**Distinguish four layers:**

- **Business requirement:** "A retry of the same checkout attempt must not cause an additional order or charge."
- **Design option:** client operation ID/idempotency coordination, reconciliation, durable transaction boundary. These are *alternatives to evaluate later*, not the business requirement.
- **Verification:** attempt the same checkout twice after a lost response; inspect orders and charges.
- **Assumption:** "Traffic peaks 5x during festivals" is a planning hypothesis until measured.

Some NFRs are **numeric** (p95 under load); others are **invariants** (unauthorized inventory changes never succeed). Testability does not require every sentence to contain a percentage.

## 4. Working scenario, scale and measurement boundaries

V1 scope from the [functional requirements (FR) chapter](../functional-requirements/): one Gaya outlet/shared inventory, responsive web, home delivery, catalog/cart/checkout, existing customer auth and payment/delivery integrations, online payment or cash on delivery (COD), registered history and verified guest lookup. The existing admin/inventory management UI is **not being built in V1**. The two-store inventory example later in this chapter is a *hypothetical future extension*, not a scope change.

### Illustrative inputs (unverified)

| Parameter | Teaching value | Interpretation |
|---|---:|---|
| MAU (Monthly Active Users) / DAU (Daily Active Users) | 10,000 / 1,000 | Planning estimates, not telemetry |
| Normal / festival peak concurrency | 200 / 1,000 | 5x concurrent-user assumption |
| Festival read activity | 3 requests/user/second | Aggressive stress-model assumption |
| Normal / festival successful orders | 50 / 500 per day | Orders, not DB write operations |
| Provisional ordering window | 12 hours/day | Actual hours/cutoffs unknown |
| Catalog | 5,000 products | Initial estimate |
| Product images | 3 x 300 KB/product | Media planning assumption |
| Order logical size | 10 KB/order | Excludes indexes, audit/logs, replicas, backups |
| Online order history | 2 years | Subject to retention obligations |

Illustrative festival **read** stress:

    1,000 concurrently active users x 3 read requests/second
    = 3,000 read requests/second.

The assumption may overstate active per-user request frequency and must be calibrated against traces or load tests. **Concurrency is not automatically throughput.**

Illustrative average **successful order rate**:

    500 successful orders / (12 x 3,600 seconds)
    = 0.0116 orders/second
    ≈ 1 successful order per 86.4 seconds (uniform-average assumption).

That is **not** peak checkout arrival rate or database write requests per second (RPS). One checkout could trigger multiple state changes, payment callbacks and retries. Need arrival distribution, per-order operations and burst factors.

Simplified logical data examples:

    5,000 products x (3 x 300 KB) ≈ 4.5 GB of product media
    500 orders/day x 365 days x 2 years x 10 KB ≈ 3.65 GB of orders.

Do not treat these as provisioned storage or full TCO. Add variants, thumbnails, indexes, WAL/logs, backup/replicas, metadata, retention, growth, egress and cache costs during estimation.

## 5. Performance, scale and tail latency

Latency is the **elapsed duration for a defined operation**. A 5-minute customer checkout journey is not the same as a 5-minute server response. Distinguish client-observed elapsed time from server-side handler time and dependency time.

- **p95 (95th-percentile) latency:** 95% of the sampled eligible observations are at or below the reported value; the slowest 5% may still be substantially worse. The average alone can hide tail pain.
- **Throughput:** operations completed per unit time (RPS, orders/minute, bytes/sec) at an explicitly defined mix.
- **Concurrency:** in-flight/active operations or users at a point in time.
- **Saturation:** CPU, thread pools, queue length, connections and dependency utilization can cause nonlinear latency growth near capacity.

Worked diagnostic from discussion: average ~300 ms, p95 ~2.5 s; festival load 5x, unchanged cache hit ratio (~95%), CPU ~90%, DB latency normal. Learner hypothesized CPU saturation and request/thread-pool wait; **plausible hypothesis, not proven**. Test it with queue wait, CPU throttling, per-stage traces, thread pool metrics, error rates and request mix.

Compare plausible alternatives: 500-ms p95 with 99.5% availability versus 900-ms p95 with 99.99% availability. In a 30-day month, approximate time-based downtime budgets are **3 h 36 min** versus **4.32 min**. The learner preferred the more available option because a 400-ms latency penalty may cost less than hours of unavailability. This is context-dependent: verify the price, workload, checkout outcomes, and customer impact.

### Provisional performance targets

| Proposed target | Missing contract detail |
|---|---|
| Product listing p95 <= 1 s | Client/server boundary, sample scope, image loading included?, region/device/network, peak RPS, error/timeout exclusion |
| Order-history p95 <= 3 s | Authenticated user population, history size, page size, test mix, time window |
| Checkout latency target | **Not yet agreed**; user hypothesized even 2 s could be acceptable if durability is protected |
| Festival load | Model with validated arrival patterns, RPS, read/write mix and headroom; do not accept 3,000 RPS as a fact |

Avoid "95% of successful requests are fast" as the only user-experience measure when failed requests/timeouts may be excluded: pair latency with success/availability SLIs and define denominator explicitly.

## 6. Availability, reliability, durability, resilience and recovery

| Attribute | Precise focus | Illustrative failure |
|---|---|---|
| **Availability** | Can a user perform the specified operation when needed? | Checkout endpoint inaccessible during shop hours |
| **Reliability** | Does the system consistently behave according to its contract? | Duplicate charge or wrong order state |
| **Durability** | Does durably committed data survive defined failures? | A previously committed confirmed order disappears after DB restart |
| **Resilience** | Can service withstand/degrade/recover from disturbances within agreed limits? | Dependency overload causes cascading failures |
| **RPO (Recovery Point Objective)** | Maximum acceptable lost committed data measured as a recovery-point lag (defined scope) | Provisional **RPO = 0 for confirmed orders** after a single DB-server failure |
| **RTO (Recovery Time Objective)** | Time to restore a defined service after a specified incident | Provisional **RTO = 15 minutes**, failure and operation scope unconfirmed |

**RPO=0 is not universal magic.** Define exactly when an order is accepted, which node/zone/region failures count, whether acknowledgement implies durable commit, and whether asynchronous dependencies carry separate state. RTO needs measured recovery drills and scope. Avoid claiming zero data loss across region-wide or correlated disasters without a validated design and evidence.

### Ambiguous outcome: response is not the commit

~~~mermaid
sequenceDiagram
    autonumber
    participant U as Customer
    participant A as Checkout service
    participant D as Durable order store
    U->>A: Place order (attempt X)
    A->>D: Record order (attempt X)
    D-->>A: Commit acknowledged
    Note over A,U: Failure or timeout before response is delivered
    A--xU: Confirmation response not received
    Note over U,D: Customer sees unknown outcome, but a committed order may exist
~~~

This is a *possible failure timeline*, not a complete payment workflow or implementation blueprint. If the store crashed **before** durable commit, the order may not exist. No visible confirmation does **not** prove the order failed, and a successful payment does **not** necessarily auto-refund. Payment and order states may diverge and need later verification/reconciliation.

**Business-level invariant (proposed):** Retry of the **same checkout attempt** after an uncertain outcome must not create an additional order or charge. A genuinely new checkout attempt with identical items may be a legitimate second purchase. Cancellation windows and refunds are independent policies, not a default substitute for safe retries. Mechanisms belong in later [partial failure and idempotency](../../../progress/PROGRESS.md) lessons.

## 7. SLIs, SLOs, SLAs and error budgets

- **SLI — Service Level Indicator:** actual measured indicator, such as the proportion of eligible checkout requests meeting a success criterion.
- **SLO — Service Level Objective:** desired target for that indicator over a named measurement window.
- **SLA — Service Level Agreement:** formal customer-facing agreement that may include commitments and consequences/remedies; not every internal SLO is a contractual SLA.

**Memory aid:** SLI = *what we measure*; SLO = *target we aim for*; SLA = *formal agreement*. Full forms for other abbreviations appear in the [glossary](#17-glossary).
- **Error budget:** tolerated unreliability under an SLO, in **the same units and measurement model** as the SLI.

**Time-based availability model**, 99.9% per 30-day month:

    30 x 24 x 60 = 43,200 total minutes
    43,200 x (1 - 0.999) = 43.2 permitted unavailable minutes.

After spending **80% in 10 days**:

    Consumed = 34.56 minutes
    Remaining = 8.64 minutes for the remaining 20 days.

Learner chose to investigate reliability and temporarily control risky releases instead of ignoring the budget or stopping *all* deployments forever. A mature policy checks root cause, impact, release risk and recovery evidence; critical mitigations may still ship.

**Request-based model:** if 1,000,000 eligible requests are measured with a 99.9% success SLO, error allowance = 1,000 requests. This does not directly translate to 43.2 minutes. Define whether bad requests, intentional rejections, third-party failure and timeouts count; and whether the window is calendar month or rolling.

Provisional SLOs are **not signed SLAs**. Measurement must include customer-visible impact during the shop's ordering hours; a browse-only overnight period may require a different availability SLI from checkout.

## 8. Security and privacy as testable outcomes

Security is broader protection against unauthorized reading, modification or misuse; privacy addresses appropriate handling of information about identifiable individuals. A leaked order can affect both. **Even an items-only purchase list can be personal data when linked to an identifiable customer**, potentially revealing preferences or sensitive inferences. Without a link to a person, privacy implications depend on context.

### Testable examples (working policy proposals)

| ID | Requirement/invariant | Validation |
|---|---|---|
| SEC-ORD-001 | Customer A must not read, edit, cancel or enumerate Customer B's private orders, regardless of guessed order IDs; verified guest access needs its own authorization proof | Cross-account API and guest-lookup tests; check data non-disclosure |
| SEC-INV-001 | Only authenticated principals with **inventory.update** and relevant resource scope may change inventory; denial must leave state unchanged | API auth tests and post-state verification |
| SEC-LOG-001 | Customer address/phone and payment secrets must not appear in ordinary application logs; authorize any exceptional access explicitly | Synthetic personally identifiable information (PII) log scanning, access reviews and sampling |
| SEC-ABUSE-001 | Rate/attempt policies for login and guest-order lookup must be defined and enforced against a threat model, minimizing collateral customer lockouts | Negative/abuse tests, legitimate-user tests, alert review |

**HTTP semantics:** missing/invalid credentials typically receive **401** (authentication required); authenticated but insufficient privilege gets **403**; **400** is malformed/invalid request; **500** is unexpected server error. A "500 Bad Request" code does not exist. UI disabled/hidden states are usability, not an authorization boundary.

### Future multi-store scope example (not V1)

A Gaya manager with **inventory.update** scoped to **store Gaya** must not mutate Patna inventory by changing an API argument. The check is **authenticated AND has action permission AND resource belongs to authorized scope**. Backend resolves the *actual target resource* and checks its scope; do not trust a client-supplied store ID as proof. A Global Admin may have explicit cross-store authorization with appropriate audit. If a shared Product exists in multiple stores, inventory quantities often need a separate Product x Store inventory relation; this is a *possible model*, not a V1 requirement.

Security requirements should state **asset, threat, allowed/forbidden behavior, verification**. Additional choices (Role-Based Access Control [RBAC] / Attribute-Based Access Control [ABAC], encryption configuration, rate threshold) depend on risk, compliance and architecture.

## 9. Maintainability, operability, and cost

Vague: "Easy to extend." Testable proposal:

**MAINT-001:** After agreeing on the integration scope and prerequisites, onboarding a comparable delivery provider should require no more than an agreed engineering-effort budget (illustratively five working days) and critical checkout/order regression tests must pass without unnecessary changes to core payment/order-state logic.

- **Measure** elapsed team effort, files/modules touched, and regression results over actual comparable integrations.
- **Don't confuse solution with requirement:** "Use SOLID" (a mnemonic for five object-oriented design principles) is a design guideline, not a sufficient business-quality target.
- **Be careful with absolutes:** some partner integrations *legitimately* require cross-cutting changes, legal reviews and security work. Agree what is included in the effort target.
- **Operability proposal:** checkout failures are traceable using correlation IDs, service and failure categories without leaking PII or secrets; validate through an injected-failure test.
- **Cost proposal:** establish monthly spend envelope and spending units (compute, DB/replicas, storage, images/egress, vendor charges, support/toil); set performance targets alongside the budget.

Example trade-off: double operating cost for only 100 ms improved browsing p95 may not be justified when both options meet user experience targets. For checkout, 100 ms alone also does not justify doubling cost; **materially fewer timeouts/failed orders** at festival peak could change the business case. Compare lost-order/abandonment impact against total cost, not a blanket "critical journey = pay anything" rule.

Principal judgment asks: what customer outcome is improved, what is the marginal benefit and cost, who owns operations, what evidence changes the decision, and **when not to add abstraction**?

## 10. Proposed consolidated NFR brief for Gaya shop

**This is a working requirements brief, not a signed-off product contract.** Mark all unvalidated targets clearly rather than treating discussion numbers as fact.

| Area | Proposed/working target | Status and missing decision |
|---|---|---|
| Customer availability | 99.9% monthly | **Provisional**; define time/request model, browse vs checkout and operating hours |
| Product-list latency | p95 <= 1 s | **Provisional**; client boundary, geography, request mix, load, period |
| Order-history latency | p95 <= 3 s | **Provisional**; user/data mix and page size |
| Order placement | No approved p95 target yet | **Open**; 1s vs 2s example was a trade-off hypothesis |
| Read peak | Test 3,000 read RPS | **Stress scenario**, not measured forecast |
| Checkout write peak | No validated peak or write amplification | **Open**; order/day does not establish write RPS |
| Confirmed-order durability | No lost durably accepted orders after a single DB-server crash | **Working correctness priority**; specify acknowledgement and fault scope |
| Recovery | RPO=0 confirmed orders; RTO <=15m | **Provisional**; incident scope and drill evidence needed |
| Checkout retry | Same attempt must not create another order/charge; new intent may | **Proposed** business invariant; verify stakeholder agreement |
| Order privacy | Cross-customer private-order access prohibited | **Working security invariant**, test at server |
| Inventory access | Permission + authorized store scope for future multi-store case | **Illustrative extension**, not currently in-scope admin UI |
| Data protection | PII/secrets excluded from ordinary logs; least privilege, appropriate encryption | **Policy specifics open**; audit, retention and regulatory review |
| Maintainability | Comparable partner/payment integration within agreed effort and safe regression | **Illustrative target**; baseline effort, partner dependency and scope open |
| Operability | Failures diagnosable by safe correlation information | **Proposed**; audit actual alert/trace completeness |
| Cost | Fit approved monthly and peak-event cost envelope | **Open**; no budget or vendor pricing validated |

### Acceptance/validation matrix (design-independent)

| Validation | Setup | Pass evidence |
|---|---|---|
| Peak read latency | Load test realistic request mix at agreed festival RPS with defined client boundary | p95 target AND success target jointly met, with resource headroom |
| Durable accepted order | Inject supported DB-server crash *after* durable acceptance | Accepted order recoverable; no missing committed order |
| Ambiguous checkout outcome | Lose response after commit; re-attempt **same intent** | No extra order/charge; final state discoverable |
| Order ID enumeration | Authenticated A requests B's order; guest tries unverified lookup | No private data exposure |
| Scoped stock mutation | Authenticated manager attempts out-of-scope inventory update | Denied and stock unchanged |
| Diagnostics | Inject checkout timeout with synthetic identity | Correlation path exists; sensitive fields absent |
| Extension effort | Integrate representative new provider after baseline is agreed | Effort within target; critical flow regression evidence |

Do not confuse load-test synthetic success with a proven production SLO, or integration tests with independent threat-model verification.

## 11. Requirement conflicts, decisions and evidence

During discussion, learner prioritized durability over shaving order-confirmation latency (1s vs 2s), **subject to customer validation**. A Principal Engineer should first ask if both can meet the workload and budget; do not negotiate away a requirement before demonstrating genuine tension.

Suggested business clarification questions:
- What counts as "confirmed": durable commit, API acceptance, payment success or user-visible acknowledgement?
- Does "zero loss" apply to process crash, one DB server, zone loss, or entire region? Which orders and time range?
- During what hours and which user/device/geography should checkout meet its p95?
- What is tolerable failed checkout rate, not just latency?
- What customer harm follows failure: duplicate payment, wrong stock, lost revenue, support burden?
- Which targets are **hard safety/correctness invariants**, which are negotiable experience goals?
- Who approves cost, legal/privacy retention, operational ownership and exception handling?

A requirements review should end with **owner, decision, validation evidence and open assumption**, not an unjustified technology choice.

## 12. Failure/operational review

| Failure case | Quality impact | Open design/operations follow-up |
|---|---|---|
| CPU/thread pool saturation at festival spike | Tail latency, eventual timeouts | Queue-wait traces, load tests, admission/backpressure evaluation |
| DB server loss after durable commit | Durability/recovery correctness | Define fault model, restore/failover test and acceptance boundary |
| Checkout response lost after commit | Ambiguous outcome, retries, duplicate charge risk | Business invariant for same-attempt retry; later idempotency/reconciliation design |
| Payment captured without visible order | Reliability and financial correctness | Define verification, reconciliation, support and refund/void policies |
| Cross-customer order access | Security and privacy | Server-side object-level authorization, negative tests, auditing |
| Retry storms / dependency failure | Throughput, cost, resilience | Dedicated retry, timeout and failure-lab coverage later |
| Diagnostic logs expose PII | Privacy/security while debugging | Redaction, least privilege and production log scanning |
| High-cost performance optimization | Cost and maintenance | Compare marginal user/business benefit with lifecycle cost |

## 13. Common misconceptions corrected during discussion

1. "Higher availability always means faster." No; quality dimensions can trade off.
2. "Average latency is enough." No; p95 and failure rates expose tail pain.
3. "Concurrency equals RPS." No; activity rate and operation duration matter.
4. "500 orders/day implies 500 DB writes/day." No; one order has multiple operations/retries.
5. "No confirmation screen means order was not committed." No; acknowledgement can be lost *after* commit.
6. "Gateway automatically refunds after server crash." Not a universal guarantee; payment and order need independent status resolution.
7. "Same items mean duplicate order." Not necessarily: distinguish repeated **intent** from a newly initiated purchase.
8. "Unauthorized inventory request should return 500 Bad Request." No; normally **403** after valid authentication, **401** without it.
9. "Privacy breach requires address or phone disclosure." Not always; identifiable purchase history can qualify.
10. "Frontend hidden controls protect data." No; the backend must enforce access.
11. "SOLID is a measurable maintainability NFR." It is an engineering practice; quantify safe change effort and blast radius.
12. "RPO=0 means zero loss under any catastrophe." No; explicitly declare the durability/failure scope.

## 14. Practice and spaced revision (solutions intentionally withheld)

These exercises are **unassessed** unless the learner attempts them. Hints are progressive; do not read further until trying.

### Exercise A — Produce the NFR brief independently
A festival client expects 5x normal browse traffic, online payments, COD, and limited delivery radius. Write **five measurable NFRs**: performance, durability, authorization, operations and cost. Label confirmed versus proposed and define evidence.

- Hint 1: What operation does each adjective refer to?
- Hint 2: Specify measurement boundary, workload, time window and acceptance test.
- Hint 3: Label every unverified numerical target as a negotiation proposal.

### Exercise B — Compute two kinds of error budget
Given monthly 99.95% **time-based** availability over 30 days, calculate downtime allowance. Separately calculate allowed failed requests for 2 million eligible requests at 99.95% **request-based** success.

- Hint 1: The unavailable fraction is (1 - 0.9995).
- Hint 2: Choose the correct units; the two budgets cannot be interchanged.

### Exercise C — Ambiguous checkout
Payment callback arrives, order state is uncertain, customer retries the same checkout. Write only business-level invariants, observable states and the acceptance tests. Do **not** select Redis locks, database topology or messaging.

- Hint 1: What is known by the customer, payment provider and order system respectively?
- Hint 2: Separate committed order, displayed acknowledgement and paid/authorized/refunded states.

### Exercise D — Principal trade-off
Option A costs ₹X/month and meets p95 checkout 2s with 99.99% success at festival peak. Option B costs 2X and improves p95 to 1.9s, same success rate. What evidence would reverse a default preference for simpler/cheaper A?

- Hint 1: Link latency delta to checkout conversion and revenue.
- Hint 2: Include lifecycle operations, change risk and contractual commitments.

### Exercise E — Scoped authorization
A staff member can edit Gaya stock but attempts to change Patna stock using an object ID while lying about store ID. Write the **server-side invariant** and both positive and negative test cases.

- Hint: Determine the real resource scope on the trusted side, not from a client assertion.

## 15. Honest learner evidence and readiness

**Discussion coverage: 4/5.** Broad, interactive exposure to major NFR categories, numerical examples and production-relevant trade-offs. This **does not** measure mastery. The learner correctly reasoned about durability as the primary failure, selected a reliability-oriented release policy after error budget consumption, distinguished authorization and privacy exposure, identified role + store-scoped permissions, and prioritized correctness based on customer impact. The learner correctly noted that requirements clarification precedes SOLID/interface design.

**Guided or explained rather than independently demonstrated:**
- Security/maintainability NFRs were initially listed as mechanisms; concrete testable formulations required explanation and examples.
- Ambiguous checkout outcome was first treated as definitively unconfirmed and assumed to trigger an automatic refund. Learner proposed duplicate orders plus cancellation; guided review surfaced same-attempt retry versus new intent, but **independent explain-back of the refined invariant was not assessed**.
- NFR brief, measurement boundary, failure envelope, scale forecast, error-budget arithmetic and load-test plan were not all independently authored under assessment conditions.

**Capability evidence (topic-limited):**

| Skill | Current evidence |
|---|---|
| Explain | Good emerging conceptual distinctions; some concepts explained by tutor |
| Apply | Strong qualitative trade-off and store-scope reasoning; numeric NFR drafting guided |
| Diagnose | Plausible CPU/thread-pool bottleneck hypothesis; no independent measured incident trace |
| Defend | Sensible business prioritization and cost/risk questions; requires quantified evidence |
| Validate | Acceptance-test thinking present, but a full independently authored validation plan is pending |

- **Interview readiness:** Not assessed in timed independent design; cannot call interview-ready from this chapter alone.
- **Production/Principal readiness:** Not demonstrated; requires written NFR sign-off, validation tests, incident analysis, recovery and cross-team ownership evidence.
- **Suggested retest:** Write five measurable NFRs unaided, classify ambiguous post-commit timeout/payment states, then evaluate a cost/availability trade-off with real units and a signed-off failure model.

## 16. Quick revision / flashcards

- **FR vs NFR:** Capability vs quality/constraint of delivery.
- **Requirement vs mechanism:** What is guaranteed vs how implementation achieves it.
- **SLI / SLO / SLA:** Observed indicator / objective / formal agreement.
- **Error budget:** Permitted unreliability in the SLI's units over its stated window.
- **p95:** 95th-percentile measured latency; pair with error rate and workload.
- **Durability / availability / reliability:** Preserved committed state / usable service / behavior meeting contract.
- **RPO / RTO:** Acceptable recovery-point loss / restoration time; declare failure scope.
- **Security / privacy:** Prevent unauthorized actions/disclosure / appropriate handling of identifiable personal information.
- **Permission + resource scope:** Action entitlement alone does not authorize every record.
- **Retry vs new order:** Same checkout intent should not cause duplicate side effects; new intent can.
- **Principal lens:** User outcome, hard invariants, quantitative evidence, cost, ownership, and simplifying assumptions.

## 17. Glossary

| Term / abbreviation | Full form / definition |
|---|---|
| **FR** | **Functional Requirement** — capability or behavior the system must provide |
| **NFR** | **Non-Functional Requirement** — quality attribute or constraint governing how a capability must perform |
| **SLI** | **Service Level Indicator** — measured service behavior |
| **SLO** | **Service Level Objective** — target for an SLI over a stated window |
| **SLA** | **Service Level Agreement** — formal service commitment to a customer, potentially with remedies |
| **RPO** | **Recovery Point Objective** — maximum acceptable data-loss/recovery-point lag under a defined failure model |
| **RTO** | **Recovery Time Objective** — maximum acceptable service restoration duration for a defined disruption |
| **MAU / DAU** | **Monthly Active Users / Daily Active Users** — unique active users within each respective period |
| **RPS** | **Requests Per Second** — throughput; specify eligible request types and traffic distribution |
| **p95** | **95th percentile** — 95% of eligible observations are at or below this value |
| **PII** | **Personally Identifiable Information** — information that can identify a person; privacy obligations may cover broader personal data too |
| **RBAC / ABAC** | **Role-Based Access Control / Attribute-Based Access Control** — authorization models using assigned roles or evaluated attributes/policies |
| **COD** | **Cash on Delivery** — payment method where payment is collected on delivery |
| **API / HTTP / UI** | **Application Programming Interface / Hypertext Transfer Protocol / User Interface** |
| **DB / CPU** | **Database / Central Processing Unit** |
| **TCO** | **Total Cost of Ownership** — upfront, vendor/infrastructure and ongoing operating costs |
| **WAL** | **Write-Ahead Log** — a database recovery/logging technique (detailed mechanics deferred to storage internals) |
| **SOLID** | **Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion** — object-oriented design principles; not a measurable NFR by themselves |
| **KB / GB** | **Kilobyte / Gigabyte** — data size units; distinguish decimal (1 GB = 10^9 bytes) and binary (1 GiB = 2^30 bytes) when capacity precision matters |
| **ms / s / h** | **Milliseconds / seconds / hours** — latency and time units |
| Quality attribute | Performance, security, durability, maintainability or another non-functional property |
| Invariant | Property that must remain true across relevant operations/failures |
| Measurement boundary | Exact start/end and inclusion rules for an observation |
| Error budget | Allowed failures/unavailability implied by an SLO |
| Resource-scoped authorization | Access depends on action entitlement **and** which resource is targeted |
| Ambiguous outcome | Caller cannot tell whether an operation committed after a timeout/lost response |
| Idempotent retry (business intent) | Repeating the same logical attempt does not multiply business side effects |
| Regression surface | Existing behavior potentially affected by a change |

## 18. References and next chapters

- [Functional requirements chapter](../functional-requirements/) — fixed V1 scope, provisional workloads and earlier decisions.
- [Canonical syllabus](../../../curriculum/SYLLABUS_V2.md) — ordered Phase 0 chapters; later deeper coverage is intentionally deferred.
- [Learning Lab Playbook](../../../docs/LEARNING_LAB_PLAYBOOK.md) — evidence and publishing conventions.
- [Google SRE Book: Service Level Objectives](https://sre.google/sre-book/service-level-objectives/) — SLI/SLO framing and operational consequences.
- [Google SRE Workbook: Implementing SLOs](https://sre.google/workbook/implementing-slos/) — practical SLO process.
- [OWASP API Security Top 10 2023: Broken Object Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/) — authorization failures involving object IDs.
- [RFC 9110 HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110) — definitions of HTTP status codes.
- [AWS Builders' Library](https://aws.amazon.com/builders-library/) — production treatment of retries, timeouts, overload (choose specific article during the later dedicated topic).

**Next eligible syllabus topic:** **Requirement conflicts and prioritization**. Revisit NFR assumptions there; do not pre-mark any later topic as completed just because it appeared in this discussion.

## 19. Open questions to confirm before real design approval

1. Business-defined shop ordering hours, holiday handling and browse-only availability.
2. Actual concurrency, RPS, request mix, device/network population and festival spike traces.
3. Client versus server latency boundary, sample size/window, error eligibility and measurement ownership.
4. Checkout success and payment/order status semantics; confirmation/commit boundary.
5. Covered durability failure classes (server, zone, region), recovery test evidence and acceptable cost.
6. Cancellation, refunds, reconciliation, support procedures and duplicate-intent policy.
7. SLO stakeholder sign-off and whether any externally enforceable SLA is required.
8. Personal data retention, audit requirements, authentication responsibilities and security policies.
9. Integration change frequency, realistic engineering effort baseline and support ownership.
10. Cloud/vendor pricing, operational overhead, egress and budget ceiling.

**Chapter completion:** the *agreed teaching discussion and reference artifact* are complete, while these design decisions and independent practical assessments remain open.
