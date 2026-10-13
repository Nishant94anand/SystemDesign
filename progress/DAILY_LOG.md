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
