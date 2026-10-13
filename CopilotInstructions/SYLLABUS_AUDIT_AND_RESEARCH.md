# System Design Learning Lab — Syllabus Audit & Research Report

- **Status:** Historical audit baseline; syllabus subsequently finalized. Follow-up C15 (multi-tenant architecture) accepted and wired through the learning artifacts on 2026-10-13.
- **Prepared:** 2026-10-12
- **Baseline repository:** [Nishant94anand/SystemDesign](https://github.com/Nishant94anand/SystemDesign)
- **Baseline files reviewed:** `Context/curriculum/SYLLABUS.md`, `Context/docs/LEARNING_LAB_PLAYBOOK.md`, `Context/design-exercises/TEMPLATE.md`, `Context/progress/PROGRESS.md`, `Context/PROJECT_INSTRUCTIONS.md`
- **Agreed direction:** Balanced interview preparation + production architecture + Principal-level judgment; vendor-neutral core with deeper Azure/AWS/Google Cloud comparisons.
- **Change policy:** This report does not modify or replace the current syllabus. Proposed changes require review and approval.

## 1. Executive summary

The current syllabus has a sound high-level structure: fundamentals, building blocks, distributed systems, applied design exercises, advanced architecture, and Staff/Principal engineering. It already names many essential subjects, including NFRs, capacity estimation, SLOs, networking, databases, queues, replication, consistency, consensus, idempotency, multi-region, disaster recovery, observability, security, migrations, cost, and technical leadership.

The main concern is **not a lack of broad topic headings**. It is that several headings do not yet specify the depth, practice, and evidence needed to demonstrate mastery. The Playbook already establishes a stronger standard: explain internals, assumptions, costs, failure modes, correctness, security, observability, alternatives, and Principal-level implications. The syllabus should be audited for traceability to that standard rather than expanded indiscriminately.

### Preliminary conclusions

1. **Preserve the six-phase spine** unless prerequisite mapping shows a concrete reason to change it.
2. **Turn broad headings into assessable competencies**, particularly for data correctness, asynchronous workflows, overload, security, deployment safety, recovery, and operational ownership.
3. **Keep two independent scorecards:** interview readiness and production/Principal-level evidence.
4. **Use a vendor-neutral conceptual core**, then compare Azure, AWS, and Google Cloud implementations where service semantics, operational responsibilities, or costs materially differ.
5. **Avoid pattern collecting.** Microservices, CQRS, event sourcing, multi-region, and consensus are not default answers; each should be justified by requirements and trade-offs.
6. **Require practical evidence**, not just notes or diagrams: calculations, invariants, failure analysis, test plans, ADRs, migration plans, and operational exercises.

## 2. Baseline inventory

### 2.1 Current syllabus phases

| Phase | Current focus | Audit interpretation |
|---|---|---|
| 0 | Requirements, NFRs, capacity, latency, throughput, reliability, SLI/SLO | Good foundation; make requirement quality and quantitative assumptions assessable |
| 1 | Networking, APIs, load balancing, caching, databases, queues, storage, CDN | Broadly appropriate; explicitly assess protocol/data semantics where relevant |
| 2 | Replication, partitioning, consistent hashing, CAP, consistency, quorum, consensus, failure handling | Strong topic selection; needs explicit correctness and failure-model exit evidence |
| 3 | URL shortener, rate limiter, key-value store, notifications, object storage, feed, chat, reservation, payments, telemetry | Useful breadth; map each exercise to target competencies and increasing difficulty |
| 4 | Multi-region, resilience, migrations, observability, security, cost | Important production topics; strengthen recovery, deployment, threat modelling, and testing evidence |
| 5 | Architecture reviews, ambiguity, evolution, ownership, build-vs-buy, strategy, prioritization, communication | Correct Principal-level direction; needs review artefacts and evidence of organizational outcomes |

### 2.2 Existing learning and assessment mechanisms worth preserving

The Playbook already asks the learner to examine a concept's purpose, internals, assumptions, limitations, protocols/algorithms/equations, compute/storage/network/latency/operational costs, concurrency, partial failures, correctness, security, observability, alternatives, misconceptions, and Principal-level implications.

The exercise template already includes requirements, NFRs, capacity estimates, APIs/workflows, data model, architecture, decisions/trade-offs, failures/overload, security/observability, operations/evolution, risks, assumptions, and open questions.

**Recommendation:** keep these as canonical teaching and exercise standards. Do not copy their long checklists into every chapter. Instead, add traceability from syllabus competencies to the Playbook and exercise template.

## 3. Audit method and rating definitions

Each competency should be evaluated along four dimensions:

1. **Coverage:** Is it explicitly in the syllabus?
2. **Depth:** Are relevant internals, assumptions, algorithms/protocols, correctness properties, and failure modes specified?
3. **Application:** Is there an exercise or practical artefact that requires the learner to use it?
4. **Evidence:** Is there a clear, observable exit criterion?

Ratings in this report are about the *current written syllabus*, not Nishant's demonstrated skill.

- **Present:** Topic is explicitly listed.
- **Partial:** Topic exists, but depth/application/exit evidence is not explicit enough in the syllabus.
- **Not explicit:** A dedicated competency is not visible in the reviewed syllabus checklist. This does not prove that it is absent from all future learning material.
- **Keep:** Existing coverage is appropriate; no expansion is justified without further evidence.

## 4. Structured coverage audit

| Competency area | Current rating | Evidence in baseline | Audit finding / question to resolve |
|---|---|---|---|
| Requirements and NFRs | Present | Functional/NFR requirements, design workflow, SLI/SLO/SLA, error budgets | Preserve; exit criteria should require measurable targets, exclusions, and assumptions |
| Capacity and performance | Partial | Latency, throughput, bandwidth, concurrency, capacity estimates | Add explicit peak-vs-average reasoning, tail latency, bottleneck analysis, load-test interpretation, and cost-per-workload evidence where relevant |
| Networking and APIs | Present / partial depth | DNS, HTTP, TCP, TLS, REST/RPC, gateways, proxies | Preserve fundamentals; specify which protocol mechanics are necessary for design decisions (timeouts, connection reuse, TLS/auth boundaries, pagination/versioning) |
| Databases and data modelling | Partial | Relational/NoSQL, indexes, transactions, query patterns | Explicitly assess isolation anomalies, access-pattern-driven modelling, index/query-plan trade-offs, schema evolution, retention/deletion, and backup/restore where relevant |
| Distributed data and correctness | Partial | Replication, partitioning, consistent hashing, CAP, consistency, quorum, consensus, clocks | Require state invariants, stale-read consequences, leader/follower failure reasoning, ordering, rebalancing, recovery, and the limits of guarantees |
| Messaging and workflows | Partial | Queues, event streams, workers, notifications, payments | Explicitly cover delivery semantics, duplicate processing, replay, poison messages, schema compatibility, transactional outbox, workflow state, compensation/reconciliation when relevant |
| Timeouts, retries, idempotency, overload | Present / partial depth | Explicit in Phase 2 and Phase 4 failure topics | Keep one canonical conceptual chapter; apply repeatedly in exercises. Assess deadlines, retry budgets/jitter, load amplification, admission control, shedding, and recovery |
| Security and privacy | Partial | Security, privacy, abuse prevention | Add explicit threat-model exercise: assets, trust boundaries, identities, authorization, secrets, sensitive data, abuse cases, and validation of controls |
| Reliability and operations | Partial | Availability/reliability, SLOs, observability, incident response, fault isolation | Connect SLOs to signals, alerting, runbooks, incident diagnosis, error budgets, game days, and measurable recovery outcomes |
| Deployment and change safety | Partial / not explicit | Migrations, backward compatibility, deployment/ownership in exercise template | Assess canary/gradual rollout, rollback limitations, expand-contract migration, compatibility, feature flags, and operational readiness |
| Multi-region and disaster recovery | Partial | Multi-region, RPO/RTO, failover-related concepts | Require explicit failover/failback scenarios, regional dependency analysis, data-loss bounds, restore tests, and business-driven recovery targets |
| Cost and operational complexity | Partial | Cost modelling and operational complexity | Require rough cost drivers, utilization/headroom, cost-performance-reliability trade-offs, and operational toil; provider-specific estimates only when assumptions are clear |
| Technical leadership | Partial | Design docs, strategy, prioritization, cross-team ownership, build-vs-buy | Assess ADR quality, options considered, stakeholder constraints, ownership boundaries, migration strategy, decision reversibility, and what evidence would change the decision |
| Practical exercise progression | Present / partial | Ten system-design exercises | Map each exercise to prerequisites and competencies; make at least some exercises iterative (initial design → new constraint → failure/migration review) |
| Sustainability / regulatory context | Not explicit | Not a dedicated syllabus topic | Treat as contextual review dimensions where workload, region, data classification, compliance, or business requirements make them material—not necessarily a standalone core module |

## 5. Research findings and proposed changes

### Follow-up decision (2026-10-13)

**C15 — Multi-tenant architecture and tenant isolation: ACCEPTED.** Official Azure and AWS guidance supports treating tenant isolation as a deliberate design boundary and balancing security, performance, reliability, cost, and operational complexity. The topic is introduced in Phase 1, applied in a dedicated Phase 3 lab, and deepened in Phase 4 production concerns. The ordered Progress tracker, concept indexes, exercise catalog, revision glossary/flashcards, source register, and learning workflow have been updated to keep the change traceable. See [Syllabus V2](../curriculum/SYLLABUS_V2.md).

Priority meanings: **P0** = foundational competency or assessment gap to resolve; **P1** = important for production/Principal readiness; **P2** = conditional, workload-specific, or specialization-dependent.

| ID | Proposed action | Priority | Rationale | Source support | Suggested placement |
|---|---|---:|---|---|---|
| C01 | **Deepen database correctness and data lifecycle**: isolation anomalies, transaction boundaries, index/query plans, schema evolution, retention/deletion, backup/restore | P0 | “Transactions” alone does not demonstrate ability to reason about concurrency, data correctness, or operational recovery. | DDIA is the core reference for data systems and their trade-offs; cloud frameworks include reliability and operations in production design. [DDIA](https://dataintensive.net/), [Azure Well-Architected](https://learn.microsoft.com/en-us/azure/architecture/framework/) | Phase 1 with follow-through in Phase 3/4 |
| C02 | **Make asynchronous workflow correctness explicit**: delivery semantics, duplicate handling, replay, poison messages, schema compatibility, transactional outbox, saga/compensation, reconciliation | P0 | A database update plus event publication can fail as a dual write; duplicates and ordering need explicit handling. Sagas introduce their own compensation and debugging complexity, so should be conditional rather than default. | [AWS transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html), [AWS saga patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html) | Phase 1 foundations; apply to payments, reservations, notifications |
| C03 | **Deepen timeout/retry/overload reasoning**: deadlines, exponential backoff with jitter, retry budgets, admission control, load shedding, backpressure, cascading failure | P0 | Retries can amplify load; timeout policy must account for downstream latency and the operation's semantics. | [AWS Builders' Library: Timeouts, retries, and backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/) | Phase 2 canonical concept; revisit in every relevant exercise |
| C04 | **Add tail latency and empirical performance validation**: p50/p95/p99, queueing/bottlenecks, load profiles, headroom, cost per request/operation | P1 | Average latency alone can hide user-visible tail behavior; capacity estimates should be validated rather than treated as truth. | [Azure Well-Architected](https://learn.microsoft.com/en-us/azure/architecture/framework/), [Google Cloud Well-Architected](https://docs.cloud.google.com/architecture/framework) | Phase 0/1 foundations; validate in capstones |
| C05 | **Make threat modelling a required design activity**: assets, trust boundaries, identity/authz, secrets, sensitive data, abuse, secure development/testing | P0 | Security should influence architecture early, not appear only as a final checklist. The exact controls remain threat- and workload-dependent. | [NIST SSDF SP 800-218](https://csrc.nist.gov/pubs/sp/800/218/final), [Azure Well-Architected](https://learn.microsoft.com/en-us/azure/architecture/framework/) | Phase 0 lightweight introduction; deeper Phase 4 review |
| C06 | **Add deployment/change safety**: canary, staged rollout, rollback constraints, expand-contract migrations, compatibility, readiness gates | P1 | A design that works only on a clean deployment is incomplete for a live service with existing data and clients. | [Google Cloud Well-Architected](https://docs.cloud.google.com/architecture/framework), [Azure Well-Architected](https://learn.microsoft.com/en-us/azure/architecture/framework/) | Phase 4, with migration examples in Phase 3 |
| C07 | **Operationalize observability**: choose SLIs, correlate metrics/logs/traces, define alerts/runbooks, diagnose an incident, measure recovery | P0 | “Observability” is too broad to assess unless the learner produces telemetry and uses it to answer operational questions. | [OpenTelemetry documentation](https://opentelemetry.io/docs/), [Google SRE books](https://sre.google/books/) | Phase 0 SLO foundations; Phase 4 incident lab |
| C08 | **Make disaster recovery evidence-based**: business-derived RPO/RTO, dependency mapping, failover/failback, restore testing, data-loss analysis | P1 | Stating an RPO/RTO is not proof the architecture can meet it. Recovery paths need validation. | [Azure Well-Architected](https://learn.microsoft.com/en-us/azure/architecture/framework/), [Google Cloud Well-Architected](https://docs.cloud.google.com/architecture/framework) | Phase 4 |
| C09 | **Assess technical leadership through artefacts**: ADRs, options, ownership, migration sequencing, decision reversibility, stakeholder alignment, success measures | P1 | Principal-level work includes enabling teams to change and deploy independently, not just selecting technologies. | [DORA: Loosely coupled teams](https://dora.dev/capabilities/loosely-coupled-teams/), [StaffEng: Staff archetypes](https://staffeng.com/guides/staff-archetypes/) | Phase 5 |
| C10 | **Treat cost, performance, reliability, security and operations as recurring trade-offs** | P1 | These concerns interact. A provider checklist should inform review, not be copied blindly as a universal architecture. | [Azure Well-Architected](https://learn.microsoft.com/en-us/azure/architecture/framework/), [AWS Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html), [Google Cloud Well-Architected](https://docs.cloud.google.com/architecture/framework) | All phases; deeper Phase 4/5 |
| C11 | **Add a competency-to-exercise-to-exit-criteria matrix** | P0 | The syllabus currently has phase-level exit evidence but not a full traceability map from each competency to practical evidence. This is the most direct way to prevent “topic listed = mastered” confusion. | Derived from the existing Playbook assessment rubric and exercise template; this is a curriculum-design recommendation, not an external factual claim. | Syllabus metadata / audit matrix |
| C12 | **Maintain separate interview and production/Principal scorecards** | P0 | Timed communication and design synthesis are different evidence from production correctness, recovery validation, cost, migration, and cross-team leadership. One score can hide important gaps. | Derived recommendation based on the learning goal and the distinction between interview exercises and operational architecture frameworks. | Assessment model across all phases |
| C13 | **Keep cloud-specific comparisons after concepts are established** | P1 | Similar architectural goals can map to different provider services and responsibilities. Teaching the invariant first reduces service-name memorization and supports vendor-neutral reasoning. | [Azure framework](https://learn.microsoft.com/en-us/azure/architecture/framework/), [AWS framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html), [Google Cloud framework](https://docs.cloud.google.com/architecture/framework) | Add comparison labs to relevant modules |
| C14 | **Make specialization topics conditional**: deep Kubernetes internals, advanced consensus variants, ML serving, service mesh, event sourcing/CQRS | P2 | These topics can be valuable, but should enter the core only if a prerequisite, capstone, role target, or concrete design problem requires them. | Recommendation based on scope control and the Playbook principle of adding complexity only when justified. | Optional extensions / specialization tracks |

### Important caveat on sources

Cloud Well-Architected frameworks are authoritative provider guidance, but they are also provider-published frameworks. They are useful for systematic review and cloud implementation comparisons; they should not be treated as neutral experimental proof that one vendor's architecture is universally best. Foundational papers and DDIA support conceptual depth; NIST supports secure development practices; DORA supports organizational/technical coupling considerations. Recommendations that synthesize these sources are marked as recommendations, not as direct quotations or universal laws.

## 6. Interview preparation vs real-world Principal evidence

These should share technical foundations but have separate assessment criteria.

| Dimension | Interview readiness evidence | Production / Principal evidence |
|---|---|---|
| Requirements | Clarifies scope and identifies key NFRs in limited time | Resolves conflicting goals, constraints, stakeholders, and invalidating assumptions |
| Quantitative reasoning | Produces defensible rough estimates | Validates estimates against workload measurements, capacity headroom, and cost |
| Architecture | Draws coherent components and request/data flows | Defines ownership, boundaries, migration path, and operational responsibilities |
| Correctness | Explains consistency, concurrency, idempotency, and failure cases | Defines invariants and demonstrates recovery behavior with tests or experiments |
| Reliability | Identifies common failures and mitigations | Defines SLOs, tests failover/recovery, diagnoses incidents, and measures outcomes |
| Security | Names likely threats and controls | Produces a threat model and verifies controls in the deployment context |
| Performance | Identifies bottlenecks and scaling options | Uses load tests/telemetry to validate tail latency, throughput, and headroom |
| Cost | Gives rough cost drivers | Compares lifecycle cost, operational toil, and business value under explicit assumptions |
| Communication | Explains trade-offs and responds to follow-ups | Aligns teams, documents decisions, and handles disagreement/ownership boundaries |
| Evolution | Mentions future scaling or migration | Produces staged migration, compatibility, rollback, and decision-reversal criteria |

### Suggested scoring model (for review)

Use the Playbook's readiness labels, but score the two tracks independently:

- **Interview track:** problem framing, quantitative reasoning, architecture coherence, correctness/failure analysis, trade-off communication.
- **Production/Principal track:** invariants and recovery, operational evidence, security, performance/cost validation, safe evolution, ownership and technical influence.

Do not set a numerical pass threshold yet. A threshold should be calibrated after a few baseline exercises, otherwise it would be arbitrary. Reading notes alone should never promote a learner to “Strong”, “Interview Ready”, or “Principal-Level Evidence”.

## 7. Prerequisite and sequencing proposal

This is a proposal for dependency ordering, not an approved rewrite of the six phases.

1. **Foundation:** requirements, NFRs, basic quantitative reasoning, networking, data fundamentals.
2. **Core building blocks:** API contracts, data modelling, storage, caching, queues, concurrency and transaction basics.
3. **Distributed-systems depth:** replication, partitioning, consistency, ordering, clocks, consensus, failure semantics, retry/overload control.
4. **Applied designs:** start with simpler stateless/data-backed services; progress to messaging, reservation, payment/reconciliation, and telemetry.
5. **Production architecture:** security, observability, deployment safety, migration, disaster recovery, performance and cost validation.
6. **Principal capstones:** ambiguous constraints, multiple teams, build-vs-buy, staged migration, ownership, strategy and decision communication.

Across every stage, revisit correctness, reliability, security, operational burden, cost, and assumptions at an appropriate depth.

### Suggested exercise progression

| Exercise | Primary competency emphasis | Suggested extension |
|---|---|---|
| URL shortener | API/data modelling, cache, capacity | Hot key, abuse, data growth |
| Rate limiter | Algorithms, distributed state, atomicity | Multi-region policy and overload |
| Distributed key-value store | Replication, partitioning, consistency | Node loss, rebalancing, recovery |
| Notification service | Queues, retries, delivery semantics | Replay, poison message, provider failure |
| File/object storage | Metadata vs blob storage, access patterns | Integrity, lifecycle, deletion, DR |
| News feed | Read/write paths, fan-out, cache | Hot users, freshness, cost |
| Chat/messaging | Ordering, connection management, delivery state | Reconnect, duplicate delivery, retention |
| Ticket/inventory reservation | Concurrency, invariants, expiry | Oversell prevention, hot inventory, compensation |
| Payments/reconciliation | Idempotency, workflow state, auditability | Ambiguous provider outcome, reconciliation, outbox |
| Telemetry ingestion | Throughput, batching, partitioning, retention | Backpressure, sampling, cost, schema evolution |

The extensions are proposals. Not every exercise should cover every dimension; map each to a deliberately selected subset so the workload remains sustainable.

## 8. Vendor-neutral core + Azure/AWS/Google Cloud comparison model

Teach the **architectural problem and guarantees first**, then compare provider options. Do not teach a service-name mapping as if products were semantically identical.

For each relevant concept, compare:

1. Service responsibility and failure boundary.
2. Consistency/durability/delivery semantics that the service actually promises.
3. Scaling limits, quotas, partitioning model, and hot-spot risks.
4. Identity, network, encryption, and security integration.
5. Monitoring, failover, backup/restore, and operational responsibilities.
6. Cost drivers and workload assumptions.
7. Lock-in, portability, and migration implications.

Example comparison labs (service choices to be researched when the module is taught):

- Relational database and managed transactional storage.
- Object storage and CDN.
- Queue vs event-stream services.
- Managed cache and distributed rate limiting.
- Identity/secrets/key management.
- Telemetry, tracing, metrics and alerting.
- Deployment strategies and infrastructure automation.
- Regional recovery and data replication.

Use Azure Well-Architected, AWS Well-Architected, and Google Cloud Well-Architected as review frameworks. For exact product guarantees, limits, pricing, and feature availability, consult current product documentation at the time of the module; those details change and should not be frozen into a generic syllabus.

## 9. Duplication and scope-control policy

### Keep one canonical concept chapter; revisit it in exercises

- **Retries, timeouts, idempotency:** teach once deeply; apply in notification, reservation, and payment workflows.
- **SLOs and observability:** establish SLIs/SLOs in fundamentals; use them in production incident and capstone exercises.
- **Security and privacy:** teach threat modelling as a method; apply workload-specific threats in each design.
- **Migration and compatibility:** maintain a canonical migration concept; apply expand-contract and rollback reasoning to database/API changes.
- **Cost and performance:** teach estimation/testing methods once; use consistent assumptions and revisit in relevant designs.

Repeated application is desirable; repeated long-form explanations are not. Link to the canonical chapter instead of copying it.

### Do not add by default

- Microservices as the default architecture.
- CQRS, event sourcing, sagas, or distributed locks without a concrete need.
- Multi-region active-active as a default resilience strategy.
- Every cloud service or every consensus algorithm.
- Deep Kubernetes, service mesh, ML infrastructure, or provider-specific implementation detail without an explicit learning objective.

## 10. Proposed measurable exit criteria

These are candidate criteria for review, not industry-mandated numerical standards.

A competency should have evidence appropriate to its type:

- **Explain:** learner can define the concept and its assumptions without notes.
- **Apply:** learner can use it in a new design, calculation, trace, or decision.
- **Diagnose:** learner can interpret a failure scenario and identify plausible causes.
- **Defend:** learner can compare alternatives and explain what would change the decision.
- **Validate:** learner can propose a test, experiment, metric, or operational drill that would provide evidence.

Examples:

| Competency | Candidate exit evidence |
|---|---|
| Capacity | Written estimates with units, assumptions, peak factors, bottleneck and sensitivity analysis |
| Database correctness | Concurrency scenario with invariant, anomaly, chosen transaction/locking strategy and trade-offs |
| Messaging | Failure matrix for commit/publish/consume/replay, duplicate handling, ordering, and recovery |
| Resilience | Failure-mode table with detection, containment, degraded behavior, recovery and validation method |
| Security | Threat model mapping assets/trust boundaries to threats and testable controls |
| Observability | Proposed SLIs, metrics/logs/traces, actionable alerts, and a diagnosis exercise |
| Disaster recovery | Explicit RPO/RTO assumptions, dependencies, restore/failover procedure and validation plan |
| Principal decision | ADR with options, constraints, costs/risks, ownership, migration plan, reversibility and evidence that would change the decision |
| Interview design | Timed design followed by follow-ups, scored separately from production evidence |

Numerical pass thresholds should be calibrated after baseline exercises. The initial goal is consistency and actionable feedback, not false precision.

## 11. Source register

### Foundational distributed systems and data systems

1. Martin Kleppmann, *Designing Data-Intensive Applications* — [official book site](https://dataintensive.net/). Broad conceptual reference for data models, storage, replication, partitioning, transactions, distributed systems, and stream processing.
2. Google, *Spanner: Google's Globally-Distributed Database* — [publication](https://research.google/pubs/spanner-googles-globally-distributed-database-2/). A system paper for studying design choices and guarantees in a specific globally distributed database; not a universal blueprint.
3. Leslie Lamport, *Time, Clocks, and the Ordering of Events in a Distributed System* — [paper](https://lamport.azurewebsites.net/pubs/time-clocks.pdf). Foundational reasoning about event ordering and logical clocks.

### Reliability, operations, and architecture frameworks

4. Google SRE books — [SRE books](https://sre.google/books/). SLOs, monitoring, incident response, toil, and reliability engineering.
5. Google SRE Workbook — [workbook](https://sre.google/workbook/table-of-contents/). Practical SRE application and exercises.
6. AWS Well-Architected Framework — [framework pillars](https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html). Review framework across operational excellence, security, reliability, performance, cost, and sustainability.
7. AWS Builders' Library — [library](https://aws.amazon.com/builders-library/). Production engineering practices including timeouts, retries, backoff, and distributed systems operations.
8. Azure Well-Architected Framework — [framework](https://learn.microsoft.com/en-us/azure/architecture/framework/). Reliability, security, cost optimization, operational excellence, performance efficiency, review tools and workload guidance.
9. Google Cloud Well-Architected Framework — [framework](https://docs.cloud.google.com/architecture/framework). Cross-provider comparison input for reliability, security, performance, cost, operations, and sustainability.
10. OpenTelemetry — [documentation](https://opentelemetry.io/docs/). Vendor-neutral observability instrumentation and signals.

### Security, workflows, and technical leadership

11. NIST SP 800-218, Secure Software Development Framework — [publication](https://csrc.nist.gov/pubs/sp/800/218/final). Secure development practices integrated into software lifecycles.
12. AWS Prescriptive Guidance, Transactional Outbox — [pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html). Dual-write failure modes, duplicates, ordering, and idempotent consumers.
13. AWS Prescriptive Guidance, Saga Patterns — [pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html). Local transactions and compensating actions, with choreography/orchestration trade-offs.
14. DORA, Loosely Coupled Teams — [capability](https://dora.dev/capabilities/loosely-coupled-teams/). Technical and organizational coupling, independent change/deployment, and measurable outcomes.
15. StaffEng, Staff Engineer Archetypes — [guide](https://staffeng.com/guides/staff-archetypes/). A practitioner resource for thinking about different forms of Staff-level impact; not a universal promotion rubric.

### Interview-oriented learning resources

16. Alex Xu, *System Design Interview* — [author's site](https://www.alexxuniversity.com/). Structured system-design interview practice.
17. Gaurav Sen — [YouTube channel](https://www.youtube.com/@gkcs). Introductory intuition and system-design discussions.

### Source-selection caveats

- Vendor architecture frameworks are valuable checklists and trade-off guides, but they are published by vendors and should not be treated as neutral proof that one provider is universally best.
- Papers describe particular systems, assumptions, and historical designs; compare their guarantees carefully before generalizing.
- Books and interview resources are teaching aids, not proof of mastery.
- Exact cloud features, quotas, service guarantees, and pricing should be verified against current provider documentation when a module is created.

## 12. Open questions for review

1. Should consensus internals and formal safety/liveness reasoning remain in the shared core, or should advanced consensus details be an extension after the conceptual model?
2. Should the core require one complete architecture decision record and one migration plan, or should these be part of capstones only?
3. How much hands-on cloud implementation should be required versus paper design and provider-documentation comparison?
4. Should security threat modelling be a short recurring activity in every exercise, with one deeper dedicated lab?
5. Should sustainability and compliance remain conditional review dimensions rather than standalone core modules?
6. After a baseline design exercise, what sustainable weekly learning cadence and assessment thresholds make sense?

## 13. Approval workflow

1. Review this report and mark each proposal `Accept`, `Modify`, `Defer`, or `Reject`.
2. Resolve open questions and any prerequisite disagreements.
3. Update the competency-to-exercise-to-exit-criteria matrix.
4. Only after approval, produce the final syllabus and any modular chapter/assessment files.
5. Keep the existing `SYLLABUS.md` unchanged until the review is complete.

**Historical note:** The original audit made no repository changes. The accepted C15 follow-up above records a later implementation decision; the canonical current curriculum is `curriculum/SYLLABUS_V2.md`.
