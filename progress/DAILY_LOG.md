# Daily Log

One dated section per day. Keep the summary concise and record what was covered, where we stopped, and the next step.

## Entry template

## YYYY-MM-DD — Topic
- Type: Concept / Lab / Ad-hoc
- Covered:
- Key conclusions / design decisions:
- Stopped at / remaining questions:
- Next step:

## 2026-10-13 — Learning workflow, functional requirements, and initial NFR discussion
- Type: Workflow setup + interactive concept lesson
- Covered:
  - Set up the learning workflow: Syllabus V2 is canonical, Progress is the single tracker, and Daily Log records dated session summaries.
  - Completed the functional-requirements discussion for a customer-facing online Puja Samagri shop in Gaya: scope/non-goals, user journeys, guest vs registered customers, product catalogue and exact available quantities, cart, addresses/serviceability, checkout/payment integration, order confirmation, order history, guest order lookup, and receipt generation.
  - Clarified that excluding implementation of admin UI, authentication, payment gateway, or logistics does not exclude relevant customer-facing behavior or integrations.
  - Began NFR and capacity discussion: illustrative MAU/DAU/concurrency assumptions; 5× festival concurrency; an aggressive 3,000 read-RPS scenario; 500 festival orders per assumed 12-hour order window; product/media, order, and customer storage estimates; and the distinction between customer-perceived checkout duration and server latency.
  - Discussed availability, correctness/durability, read performance, RPO and RTO. Proposed working targets include 99.9% monthly availability, RPO=0 for confirmed orders, RTO=15 minutes, product-list API p95 ≤1 second, and order-history p95 ≤3 seconds. These remain provisional and require agreed measurement boundaries and workload validation.
- Key conclusions / design decisions:
  - FR scope: one physical outlet in Gaya, shared inventory, responsive web only, home delivery only; no pickup, native app, admin UI, in-house payment gateway, delivery-logistics implementation, live delivery tracking, or back-in-stock alerts in V1.
  - Registered and guest customers can browse and use a cart; registered customers can save multiple addresses and view account order history. Guest order lookup requires appropriate verification; order ID alone is not authorization.
  - Product catalogue displays exact currently available quantity, but checkout must validate current availability. Stock reservation and payment-failure mechanics remain design-stage topics.
  - Receipt PDFs are expected to be generated on demand, not retained as files, subject to audit/record-retention requirements.
  - Workload numbers are teaching assumptions, not measured business facts. 3,000 read RPS assumes 1,000 festival-concurrent users each generate 3 read requests/second; this is a stress scenario, not a validated forecast. Order count/day is not write RPS.
  - The preliminary 10 GB storage idea is not a guaranteed four-year capacity figure because indexes, backups, replication, logs, media variants and other retained data have not all been included.
  - FR topic rated 4/5: good clarification and business-scope reasoning; improvement area is keeping requirements, assumptions, and implementation choices separate.
- Stopped at / remaining questions:
  - FR artifact is complete. Open FR assumptions include exact shop hours/cutoff, delivery-serviceability rule, real product/media sizes, actual traffic and conversion patterns, and retention obligations.
  - NFR topic is partially complete and remains STARTED. Availability and read-latency targets are provisional; write-latency/peak write rate, measurement boundaries, durability/recovery scope, security/privacy, maintainability/cost, and validation of workload assumptions remain to be completed. SLIs/SLOs/SLAs/error budgets have not yet been covered.
- Next step: Continue NFRs without selecting implementation technologies; define measurable targets and their measurement boundaries, validate assumptions, then cover requirement conflicts/prioritization and quantitative foundations in syllabus order.


## 2026-10-18 — Symmetric and asymmetric encryption
- Type: Ad-hoc
- Covered:
  - Distinguished plaintext, ciphertext, encryption, decryption, and cryptographic keys.
  - Compared symmetric encryption (one shared secret; efficient for bulk data; key-distribution and compromise risks) with asymmetric cryptography (public/private key pair; public-key distribution; private-key protection and authenticity concerns).
  - Introduced AES, RSA, and ECC while distinguishing encryption, key agreement (for example ECDH), and digital signatures (for example ECDSA).
  - Walked through a simplified HTTPS/TLS flow: certificate/identity validation, ephemeral key agreement in modern TLS, derivation of symmetric traffic keys, and bulk data protection with AEAD such as AES-GCM or ChaCha20-Poly1305.
  - Discussed why encryption alone is not the whole security story, the man-in-the-middle risk of unverified public keys, key lifecycle, and pairwise key-management growth.
- Key conclusions / design decisions:
  - Symmetric encryption is generally the efficient choice for bulk data; asymmetric mechanisms help establish secrets and/or authenticate parties, depending on the algorithm and protocol.
  - Do not describe TLS 1.3 as using RSA key transport: key agreement and certificate signatures are separate roles.
  - Added as an out-of-syllabus Ad-hoc topic, not as a change to the canonical Syllabus V2.
  - Marked the session artifact COMPLETED at the learner's request. Rating 3/5 reflects introductory discussion coverage only; the application checkpoint was not attempted, so this is not evidence of independent mastery or interview/Principal readiness.
- Stopped at / remaining questions:
  - The learner acknowledged the explanation; no explain-back or scenario answer was recorded.
  - Future reinforcement: explain why a TLS connection normally uses symmetric traffic encryption after a key-agreement handshake, and distinguish confidentiality from authentication and integrity.
- Next step: Resume the ordered STARTED NFR topic when continuing the core curriculum; revisit this concept during the networking/TLS topic or chat/messaging design lab.

## 2026-10-19 — Completed non-functional requirements and measurable targets
- Type: Concept — Phase 0
- Covered:
  - Completed interactive NFR discussion using the Gaya Puja Samagri web shop: measurable versus vague NFRs, performance/availability trade-offs, peak read and average order-rate assumptions, p95 and CPU/thread-pool saturation hypothesis, durability/reliability/availability, RPO/RTO, SLI/SLO/SLA/error budgets, security/privacy, permission-plus-resource-scope authorization, maintainability/operability, cost and conflicting constraints.
  - Applied time-based 99.9% monthly SLO to 43.2 allowed unavailable minutes over 30 days; 80% spent in 10 days leaves 8.64 minutes. Clarified that request-based error budgets have request units.
  - Discussed uncertain checkout completion: durable commit and client acknowledgement differ; gateway refunds are not automatic; safe retries of the same checkout intent and an intentional second order are distinct business outcomes.
  - Published and verified the original [NFR chapter](../concepts/foundations/non-functional-requirements/README.md) with working requirements brief, Mermaid failure timeline, acceptance matrix, worked calculations, exercises with hints, open assumptions, references and assessment; updated [Foundations index](../concepts/foundations/README.md).
- Learner evidence and corrections:
  - Independent: identified durability as the primary missing-committed-order failure; selected reliability mitigation over risky releases when error budget heavily consumed; spotted security + privacy exposure; proposed manager permissions scoped to a store and global-admin override; prioritized correctness over an unvalidated 1-second checkout latency target; chose business-testable maintainability NFR over SOLID wording.
  - Guided/explained: translating security/maintainability goals into fully testable requirements; 401 vs 403 vs 400/500; role/action/resource scoping; safe checkout retry semantics.
  - Open misconception to revisit: initially interpreted missing acknowledgement as proof of no commit and assumed automatic gateway refund; proposed duplicate orders + customer cancellation as default. Need independent retest of ambiguous outcome and same-intent retry invariant.
- Assessment:
  - NFR **COMPLETED, 4/5 discussion coverage**; this does **not** imply interview or production/Principal mastery.
  - Interview readiness: not tested with an independent timed requirements brief; Production/Principal readiness: not demonstrated. Full NFR test plan, failure-boundary sign-off and quantitative assumptions remain unassessed.
- Open decisions:
  - 99.9% availability, product-list p95 <=1s, order-history p95 <=3s, confirmed-order RPO=0 (single DB-server failure) and RTO<=15m are **provisional**, not agreed SLAs. Verify eligible request population, time windows, peak write RPS, data durability boundaries, incident recovery, business cancellation/refund policy, privacy retention and cost budget.
  - Future two-store inventory authorization was a hypothetical extension; V1 remains single Gaya outlet and existing admin/inventory tool is out of scope for implementation.
- Stopped at / remaining questions:
  - User explicitly requested chapter completion; reference artifact is published; no additional NFR question pending.
  - Reinforce later with an **independent** five-NFR brief, error-budget arithmetic, and post-commit lost-acknowledgement/payment reconciliation thought experiment.
- Next step: In a new session, start the next eligible Phase 0 topic **Requirement conflicts and prioritization**; do not change its NOT STARTED status until that lesson starts.

### Follow-up — Requirement conflicts and prioritization (STARTED)
- Type: Concept — Phase 0
- Covered: Introduced the new topic after confirming that Functional Requirements and NFRs were completed. Reused the single-outlet Gaya Puja Samagri web-shop scenario; framed hard business/correctness constraints versus provisional performance and capacity assumptions.
- Current checkpoint: Diagnostic scenario — shop owner wants V1 live within two weeks with a small fixed budget, while requesting no lost confirmed orders, product-list p95 <=1 second, and readiness for an unvalidated 3,000 read-RPS festival stress scenario. Ask which demand to challenge or renegotiate first, and what evidence to request before committing.
- Stopped at / remaining questions: Learner has not answered this prioritization scenario yet. No solution or ranking has been supplied.
- Next step: Evaluate learner's proposed priority and evidence; then cover hard constraints versus preferences, business impact, trade-off documentation and validation without prematurely selecting technology.

## 2026-10-21 — Requirement conflicts and prioritization — completed chapter and independent decision review
- Type: Concept — Phase 0
- Covered: Negotiating fixed Chhath launch/budget and provisional latency/read-RPS; no-overselling vs zero rejection/5-minute decision; overload/admission, bounded inventory holds, FIFO fairness, delayed payment, grace limitations, payment reconciliation/refund obligations, truthful Pending Reconciliation and business-level acceptance criteria.
- Independent learner evidence: Challenged unvalidated 3,000 read RPS and 1-second read target; prioritized no overselling; proposed inventory hold expiry, FIFO checkout, payment reconciliation/refunds; independently identified finite grace cannot guarantee gateway response; produced a four-part decision record (constraint, target, risk, revisit criteria).
- Corrections / open gaps: Distinguish waiting room admission from inventory allocation and COD from online payment; queue does not increase capacity; linear backlog in constant 100 arrival/min vs 60 completed/min model; payment successful need not imply confirmed order. Independent ADR omitted fixed budget/release deadline and lacked measurable SLO population/window/percentile, threshold-based review, risks' mitigation owners.
- Assessment: **COMPLETED, 4/5 discussion coverage** (good reasoning and independent applied memo, gaps in formal measurable decision quality). Interview readiness unassessed by unseen timed case; production/Principal-level sign-off not demonstrated; no claim of real stakeholder approval.
- Artifact: [Requirement conflicts and prioritization chapter](../concepts/foundations/requirement-conflicts-and-prioritization/README.md); includes business/invariant/target taxonomy, decision loop, Mermaid decision flow and payment race timeline, illustrative numbers, alternatives, trade-offs, audit/validation matrix, separate unattempted practice prompts, learning evidence, references and open questions. [Foundations index](../concepts/foundations/README.md) updated.
- Open decisions: Business agreement for final-resolution eligibility and exceptions, checkout admission/inventory hold policy, COD versus online-payment behavior, refund/reconciliation owner, threshold values, physical/web inventory integrity and validated traffic forecast.
- Next step: Start first remaining Phase 0 **Latency, throughput, bandwidth, concurrency and tail latency** as NOT STARTED when user resumes. Revisit changed-scenario ADR with measurable thresholds and payment uncertainty; cover 1-second vs 2-second latency architecture implications there and in design labs.

## 2026-10-21 — Completed chapter correction: model answers and Mermaid rendering
- Type: Post-completion learning artifact repair (no status/rating changes)
- Reported: Payment expiry sequence Mermaid diagram failed to render due to punctuation in its final Note; chapter lacked model answers for attempted discussion checkpoints and unattempted P1–P4 exercises.
- Fixed and verified on GitHub: Replaced offending semicolon in Mermaid note; added a separate substantial model-answer section with P1–P4 fully worked reference solutions, the independently attempted ADR versus refined model, ten discussion checkpoint reference outcomes and all six revision-prompt answer keys. Kept unattempted and model-only material clearly excluded from learner evidence.
- Future policy: Updated Project Instructions, Learning Workflow and Learning Lab Playbook to require complete separate worked model answers at explicit chapter completion while keeping interactive sessions attempt-first.
- Assessment unchanged: Requirement Conflicts and Prioritization remains COMPLETED at 4/5 discussion coverage; no additional independent mastery demonstrated by publishing reference answers.
- Next: Start Latency, throughput, bandwidth, concurrency and tail latency when user requests. Optionally map Roberto Vitillo 2nd-edition gaps (e.g., CRDT/CALM, control/data-plane separation) to syllabus explicitly before claiming book-complete coverage.

## 2026-10-21 — Refined tutoring workflow and resource attribution
- Type: Learning workflow refinement; no topic status/rating change.
- Requested: Teach a short concept and concrete example before posing one progressive interactive question, then evaluate/reteach/extend rather than running question-only hypothetical sequences. Use topic-matched optional deep-dive references from the canonical `resources/README.md` in relevant final chapters, with a reason and verified links/sections only.
- Applied: Updated Project Instructions, Learning Workflow, Learning Lab Playbook, and Resources catalog guidance; preserved attempt-first live learning and separate complete model solutions in authorized completed chapters.
- Next step: Continue Phase 0 with latency, throughput, bandwidth, concurrency and tail latency when requested; use incremental teach → demonstrate → ask → review loops.
