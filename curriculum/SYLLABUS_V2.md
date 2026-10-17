# System Design Learning Lab — Syllabus V2

- **Status:** Finalized grounding baseline (living curriculum)
- **Purpose:** Ground future System Design Learning Lab conversations, learning sessions, exercises, and progress reviews.
- **Audience:** Software engineer targeting strong Staff/Principal-level system design capability.
- **Learning target:** Balanced interview readiness, production architecture judgment, and technical leadership.
- **Cloud stance:** Vendor-neutral core, with deeper Azure, AWS, and Google Cloud comparisons where implementation semantics, operations, limits, security, or cost materially differ.
- **Companion guide:** [Detailed Playbook](../docs/LEARNING_LAB_PLAYBOOK.md)
- **Audit rationale:** [Syllabus Audit and Research](../CopilotInstructions/SYLLABUS_AUDIT_AND_RESEARCH.md).
- **Authority:** This is the canonical, detailed syllabus. `SYLLABUS.md` is the short entry point linking here; approved curriculum changes belong here.

## 1. How to use this syllabus

This is a **living curriculum contract**, not a rigid checklist and not a promise to cover every topic exhaustively.

1. Follow the phases as a default progression; revisit prerequisites when evidence shows a gap.
2. Teach concepts from intuition to precise technical model, internals, trade-offs, failure modes, production use, and Principal-level judgment, following the Playbook.
3. Keep one canonical chapter per concept. Reuse it through links and exercises rather than duplicating long explanations.
4. Choose depth based on relevance, prerequisites, risk, and learning goals. Core competencies are mandatory; specialization topics are conditional.
5. Research current or disputed claims using authoritative sources. Distinguish **source-backed facts**, **design assumptions**, **simplifications**, **recommendations**, and **open questions**.
6. Do not treat reading, memorization, or drawing a diagram as mastery. Require explanation, application, failure analysis, and justified trade-offs.
7. Do not default to microservices, CQRS, event sourcing, distributed locks, active-active multi-region, or fashionable patterns. Add complexity only when requirements or evidence justify it.
8. Update progress only from demonstrated work. Do not claim GitHub, Drive, or other files changed unless the write succeeded.

## 2. Competency model

The curriculum develops six connected capabilities:

1. **Problem framing:** clarify users, workflows, constraints, exclusions, NFRs, and success measures.
2. **Quantitative reasoning:** estimate traffic, storage, bandwidth, latency, capacity, headroom, and cost; make assumptions visible and test sensitivity.
3. **Technical design:** choose APIs, data models, storage, caches, queues, partitioning, replication, and boundaries based on access patterns and guarantees.
4. **Distributed correctness and resilience:** reason about concurrency, consistency, ordering, partial failure, retries, overload, recovery, and state invariants.
5. **Production ownership:** design for security, observability, deployment, migration, incident response, disaster recovery, cost, and maintainability.
6. **Staff/Principal judgment:** compare alternatives, identify invalidating assumptions, manage cross-team boundaries, plan evolution, influence decisions, and know when not to add complexity.

## 3. Learning phases and exit evidence

| Phase | Focus | Minimum exit evidence |
|---|---|---|
| 0 | Problem framing, NFRs, quantitative foundations, networking and reliability fundamentals | Requirements brief with measurable targets, assumptions, workload estimate, and key trade-offs |
| 1 | Core building blocks: APIs, data, caching, queues, storage, compute and traffic management | Can trace request/data paths and justify component choices against alternatives and failure cases |
| 2 | Distributed systems and correctness | Can state invariants, reason through concurrency/partial failure, and explain consistency/recovery trade-offs |
| 3 | Applied system design exercises | Can derive a design from requirements, calculate capacity, analyse failures, and critique alternatives independently |
| 4 | Production architecture and evolution | Can produce testable security, observability, deployment, incident, cost, and recovery plans |
| 5 | Staff/Principal architecture and technical leadership | Can defend an ADR, migration strategy, ownership model, and decision criteria across teams and business constraints |
| Ongoing | Interview practice and spaced revision | Interview readiness scored separately from production/Principal-level evidence |

These are phase-level gates, not claims that one artefact proves universal competence. The Playbook's readiness labels remain authoritative.

## 4. Phase 0 — Foundations and problem framing

### Core topics
- Functional requirements, user journeys, scope boundaries, explicit non-goals.
- Non-functional requirements: latency, throughput, availability, durability, consistency, privacy/security, scalability, maintainability, and cost.
- Requirement conflicts and prioritization; distinguish hard constraints from preferences.
- Latency vs throughput vs bandwidth vs concurrency; averages vs percentiles and tail latency.
- Back-of-the-envelope estimation: request rates, peak/average factors, data size, retention, growth, bandwidth, utilization, and headroom.
- Availability, reliability, durability, resilience, fault tolerance, and failure domains.
- SLIs, SLOs, SLAs, error budgets, and user-visible reliability.
- Basic request lifecycle and networking: DNS, HTTP, TCP, TLS, connection reuse, timeouts, and relevant protocol boundaries. Advanced protocol trade-offs belong to Phase 1.
- Design workflow: clarify → estimate → model APIs/data → sketch architecture → analyse correctness/failures → compare alternatives → define validation.

### Exit evidence
- Write a short requirements brief with exclusions and measurable NFR targets.
- Show calculations with units, assumptions, peak factors, and sensitivity to uncertain inputs.
- Explain what evidence could invalidate the initial estimates.
- Separate business SLA language from service-level objectives and actual measurement.

## 5. Phase 1 — Core building blocks

### APIs, traffic and compute
- API contracts, REST/RPC trade-offs, pagination, versioning, compatibility, authentication/authorization boundaries.
- Protocol and real-time communication choices: HTTP/1.1 vs HTTP/2 vs HTTP/3 where relevant, TLS/QUIC implications, gRPC, WebSockets, server-sent events (SSE), long polling and streaming/backpressure; choose by interaction and operational requirements.
- Identity and service trust: sessions/tokens, OAuth 2.0 and OpenID Connect at a design level, token validation and audience, service-to-service identities/mTLS and authorization boundaries. Treat authentication and tenant isolation separately.
- DNS, reverse proxies, API gateways, load balancing, health checks, connection management, and service discovery concepts.
- Stateless vs stateful components; vertical/horizontal scaling and capacity bottlenecks.
- Rate limiting and admission control fundamentals; distinguish per-process from distributed enforcement.

### Caching
- Cache-aside and other common patterns; TTL, eviction, invalidation, freshness and stale reads.
- Stampedes, hot keys, cache penetration, negative caching, and cache failure.
- Consistency, memory limits, hit-rate interpretation, and the cost of invalidation.

### Databases and data
- Relational and non-relational models; choose by access patterns, constraints, and query needs rather than labels.
- Data modelling, indexes, query plans, transactions, isolation levels, and common concurrency anomalies.
- Storage-engine mechanics at decision-making depth: B-tree vs LSM-tree access/write patterns, write-ahead logging (WAL), MVCC, compaction, crash recovery, durability/latency amplification and when implementation-specific details matter.
- Read/write paths, connection pools, replication basics, partitioning, schema evolution, data ownership.
- Data lifecycle: retention, deletion, backup/restore, and integrity when relevant to the workload.
- Operational trade-offs of managed databases versus self-managed storage.

### Asynchronous systems and storage
- Queues vs event streams, workers, batching, consumer groups, delivery semantics, retries, dead-letter/poison-message handling, replay, and ordering.
- Stream/broker internals for relevant workloads: append-only logs, partitions, offsets/checkpoints, consumer coordination/rebalancing, lag, retention, backpressure and implications for ordering and duplicate processing.
- Idempotent consumers, deduplication, schema compatibility, and transactional outbox when database state and event publication must stay coordinated.
- Sagas/compensation and reconciliation where multi-step workflows require them; understand complexity and limits.
- Object/blob storage, metadata, CDN, search/indexing, batch processing, and lifecycle policies.

### Multi-tenant architecture
- Define the tenant boundary for the product and distinguish tenants from individual users.
- Compare shared/pool, dedicated/silo, and hybrid isolation models across application, data, messaging, configuration, and infrastructure layers.
- Treat tenant isolation as a separate authorization/data-access boundary; authentication alone does not prove that cross-tenant access is prevented.
- Reason about tenant context propagation, per-tenant quotas/rate limits, fairness, noisy-neighbor risks, resource limits, and the cost/complexity of stronger isolation.
- Select isolation based on security, performance, reliability, compliance/data-residency needs, scale, and operational capability—not by defaulting to either fully shared or fully dedicated resources.

### Exit evidence
- Trace a request and a data change end-to-end.
- Explain why the chosen API/data/storage design fits the access pattern.
- Analyse a cache-staleness or database-concurrency scenario.
- Build a failure matrix for publish/consume/retry/replay where asynchronous processing is used.
- Compare pooled, siloed and hybrid tenancy at a conceptual level; identify the tenant boundary and explain why authenticated identity alone does not guarantee cross-tenant isolation.
- Explain one protocol/connection trade-off and one token/session/service-identity trust boundary; compare B-tree/LSM and WAL/MVCC behavior in a concrete storage problem; trace offsets/rebalancing in a stream-processing failure case when applicable.

## 6. Phase 2 — Distributed systems and correctness

### Scaling and distributed data
- Replication, partitioning/sharding, partition keys, hotspots, skew, rebalancing, and consistent hashing.
- CAP theorem with assumptions and partition scenarios; avoid simplistic “pick two” interpretations.
- Consistency models, stale reads, read/write paths, read-your-writes and monotonicity where relevant.
- Quorums, leader/follower behavior, leader election, consensus concepts, and the guarantees and costs they provide.
- Clocks, logical ordering, causality, event ordering, and why wall-clock timestamps do not automatically establish causal order.
- Distributed locks and leases: fencing, expiry, stale holders, and when a lock is unnecessary or unsafe.
- Transactions and coordination across service/data boundaries; use the simplest mechanism that preserves required invariants.

### Partial failure and overload
- Timeouts, deadlines, retries, exponential backoff/jitter, retry budgets, and idempotency.
- At-least-once/at-most-once delivery distinctions and why end-to-end “exactly once” claims require careful scope.
- Duplicate requests, ambiguous outcomes, cancellation, and reconciliation.
- Circuit breakers, bulkheads, backpressure, bounded queues, admission control, load shedding, and graceful degradation.
- Cascading failures, correlated dependencies, recovery storms, and blast-radius containment.

### Exit evidence
- State system invariants and what must remain true under concurrency.
- Walk through node loss, network partition, duplicate delivery, delayed response, and recovery.
- Explain guarantees and trade-offs without relying on vague claims such as “eventual consistency is fine.”
- Identify how retries change load and correctness, and specify evidence needed to validate the policy.

## 7. Phase 3 — Applied system design exercises

Exercises cover different competencies, not every pattern in every design. Start with the simplest viable design; introduce constraints progressively.

| Exercise | Core learning objectives | Extension / failure lab |
|---|---|---|
| URL shortener | API/data modelling, key generation, reads/writes, cache and capacity | Hot key, abuse, retention, analytics and growth |
| Rate limiter | Algorithms, atomicity, distributed state and trade-offs | Multi-region limits, clock assumptions, overload |
| Distributed key-value store | Partitioning, replication, consistency and recovery | Node loss, rebalancing, quorum trade-offs |
| Notification service | Queues, retries, delivery state and provider integration | Duplicate delivery, poison messages, replay, provider outage |
| File/object storage | Metadata/blob separation, access patterns, integrity | Lifecycle, deletion, restore and regional failure |
| News feed | Fan-out, ranking boundary, cache and read/write trade-offs | Hot users, freshness, skew and cost |
| Chat/messaging | Connections, ordering, delivery state and persistence | Reconnect, duplicate messages, offline delivery and retention |
| Ticket booking / limited inventory | Concurrency, reservation expiry and invariants | Overselling, hot inventory, payment timeout, compensation |
| Payment processing and reconciliation | Idempotency, workflow state, auditability and ambiguous outcomes | Outbox, provider disagreement, retries, reconciliation and recovery |
| Telemetry/event ingestion | Throughput, batching, partitioning, retention and observability | Backpressure, sampling, schema evolution and cost |
| Multi-tenant SaaS platform | Tenant boundary, isolation model, data access, quotas/fairness, tenancy-aware operations | Noisy neighbor, tenant migration, data residency, tenant-specific restore, shared-service outage |
| Integrated commerce and inventory capstone | Requirements → capacity → bounded inventory → payments/reconciliation → event-driven side effects → observability and operations | Festival surge, oversell race, payment ambiguity, downstream outage, design revision and migration |

### Required exercise workflow
Follow the [Design Exercise Template](../design-exercises/TEMPLATE.md): scope/NFRs → estimates → APIs/workflows → data/access patterns → architecture → decisions/trade-offs → failure/overload → security/observability → operations/evolution → review/open risks.

At least some exercises should be iterative: initial design, then a new scale or correctness constraint, then a production incident or migration scenario. Include an integrated [commerce capstone](../design-exercises/cases/integrated-commerce-capstone.md) after the component labs, and a separate [Principal modernization capstone](../design-exercises/cases/principal-modernization-capstone.md) during Phase 5. Assess the ability to revise designs under new constraints, not just draw a polished final diagram. Do not provide the full solution before the learner attempts it unless explicitly requested.

## 8. Phase 4 — Production architecture and evolution

### Reliability and operations
- Fault domains, isolation, graceful degradation, overload controls, dependency failure, and recovery sequencing.
- Observability: metrics, logs, traces, context propagation, useful dashboards, alert quality, runbooks, and incident diagnosis.
- Connect SLIs/SLOs to user outcomes, alerting, error budgets, and operational decisions.
- Incident response, post-incident learning, capacity planning, load testing, chaos experiments/game days when justified.
- Operational readiness: ownership, on-call burden, dependency maps, support procedures, and failure communication.

### Security and privacy
- Threat modelling: assets, actors, trust boundaries, attack surfaces, abuse cases, and risk prioritization.
- For multi-tenant systems, explicitly validate tenant-context propagation and cross-tenant authorization/data isolation across APIs, background jobs, caches, search indexes, logs, exports, and support/admin paths.
- Identity/authentication, authorization, least privilege, service identities, secrets/key management, encryption and network boundaries.
- Sensitive data classification, retention/deletion, auditability, privacy, abuse prevention, and security validation.
- Security should influence design from the start and be revisited in each relevant exercise; this phase deepens the method.

### Multi-tenant production concerns
- Tenant-aware observability and metering: per-tenant health, latency, errors, consumption, cost, throttling, and quota alerts without unbounded metric cardinality.
- Tenant lifecycle: onboarding/provisioning, configuration, upgrades, offboarding, data export/deletion, retention, and restore/migration procedures.
- Control-plane versus tenant-workload/data-plane boundaries; prevent one tenant's workload or control-plane activity from exhausting shared capacity.
- Residency, customer-managed keys, backup/restore, availability and isolation tiers where contractual or regulatory requirements demand them; compare the cost and operational burden of dedicated resources.

### Deployment, migration and compatibility
- Safe rollout, canary/gradual deployment, health gates, rollback constraints, feature flags, and operational readiness.
- Expand-contract database/API changes, dual-read/write risks, backfills, validation, compatibility and deprecation.
- Data migration, re-partitioning, ownership transfer, and staged cutover plans.
- Distinguish code rollback from data rollback; define recovery options before high-risk changes.

### Multi-region, disaster recovery and cost
- Regional dependency analysis, replication choices, failover/failback, split-brain risks and data-loss bounds.
- Business-derived RPO/RTO; restore tests and recovery drills rather than paper-only targets.
- Tail latency, load profiles, saturation, bottleneck diagnosis, headroom, and performance-test interpretation.
- Cost drivers, utilization, storage/egress, managed-service pricing assumptions, operational toil, and cost-performance-reliability trade-offs.
- Sustainability/compliance are contextual review dimensions when workload, region, data classification, or business obligations make them material.

### Exit evidence
- Produce a threat model with testable controls for a selected design.
- Define useful SLIs and telemetry, then diagnose a supplied failure scenario.
- Write a staged migration/rollout plan including compatibility, validation, rollback limits, and ownership.
- Document RPO/RTO assumptions, dependencies, recovery steps, and how a drill would prove readiness.
- State cost assumptions and identify measurements that could change the architecture decision.
- For a multi-tenant design, justify the isolation model and show how cross-tenant access is prevented, noisy-neighbor risk is controlled, tenant-level health/consumption is measured, and tenant lifecycle operations are performed.

## 9. Phase 5 — Staff/Principal architecture and technical leadership

- Architecture reviews and design documents that make assumptions, options, risks, and decisions explicit.
- Ambiguous/conflicting requirements, stakeholder alignment, and principled scope reduction.
- Architecture Decision Records (ADRs): context, constraints, alternatives, decision, consequences, risks, reversibility, and review triggers.
- Evolutionary architecture, migration sequencing, compatibility, deprecation, and decision reversibility.
- Cross-team boundaries, service/data ownership, interfaces, coordination costs, and independent delivery.
- Build-versus-buy analysis: strategic differentiation, total lifecycle cost, reliability, security, lock-in, and team capacity.
- Technical strategy and prioritization: business outcome, opportunity cost, risk reduction, sequencing, and measurable success.
- Operational ownership, on-call burden, blast radius, toil, and support model.
- Communicating trade-offs, resolving technical disagreement, influencing without authority, and recording dissent/decision rationale.
- Evidence-driven decision making: state assumptions, design experiments, identify leading indicators, and explain what evidence would change the decision.
- Know when *not* to introduce a new abstraction, service, database, platform, or coordination mechanism.

### Exit evidence
- Produce an ADR comparing credible alternatives and documenting what would change the decision.
- Produce a migration plan with milestones, owners, compatibility strategy, rollback/recovery limits, and success measures.
- Present an architecture review that considers business value, correctness, security, reliability, cost, operations, and team boundaries.
- Explain a technical disagreement fairly, including the strongest argument for the alternative.
- Complete a cross-team modernization capstone: argue build-vs-buy, tenancy/security and ownership boundaries, compatibility and migration sequencing, rollout/rollback limits, cost/reliability trade-offs, incident response and measurable decision triggers. Support the recommendation with an ADR and review.

## 10. Vendor-neutral core and cloud comparisons

Learn the problem, guarantees, and constraints first; then compare provider-specific implementations. A service-name mapping is not enough.

For each relevant concept, compare:
1. Responsibility and failure boundary.
2. Consistency, durability, ordering, and delivery guarantees.
3. Scaling model, quotas/limits, partitioning, and hotspot risks.
4. Identity, network, encryption, and security integration.
5. Monitoring, backup/restore, failover, and operational responsibility.
6. Cost drivers and workload assumptions.
7. Lock-in, portability, and migration implications.

Use deeper Azure, AWS, and Google Cloud comparisons for relevant modules, such as managed relational/transactional databases; object storage and CDN; queue vs event-stream services; cache and distributed rate limiting; identity, secrets and key management; telemetry and alerting; deployment automation; and regional replication/disaster recovery.

Cloud implementations are examples, not the conceptual syllabus. Verify exact product guarantees, quotas, feature availability, and pricing against current official documentation when teaching the module. Do not assume similarly named services have identical semantics.

## 11. Optional specialization topics

Introduce these only when a learning goal, role target, prerequisite, or capstone justifies them:
- Deep consensus algorithms and formal verification.
- Kubernetes internals, service mesh and platform engineering.
- CQRS/event sourcing beyond conceptual trade-offs.
- ML serving/data-intensive ML infrastructure.
- Specialized databases, search internals, graph systems, and stream processing.
- Advanced multi-region active-active designs.
- Sustainability and compliance deep dives.

Optional does not mean unimportant; it means the core remains coherent and adaptable rather than attempting exhaustive coverage of all engineering subfields.

## 12. Assessment model: two independent scorecards

### A. Interview readiness
Assess problem framing and requirement clarification; quantitative estimates and assumptions; architecture coherence and data/API flow; correctness, bottlenecks and failure analysis; trade-off reasoning and clear communication within time constraints.

**Evidence:** timed designs, follow-up questions, estimates, and rubric-based reviews.

### B. Production / Principal-level evidence
Assess state invariants and recovery correctness; operational reliability, observability and security; performance and cost validation; safe migration, compatibility and deployment; ownership, cross-team interfaces and technical strategy; ADR quality, evidence-driven decisions, and ability to simplify.

**Evidence:** design artefacts, experiments/test plans, failure labs, threat models, ADRs, migration plans, recovery drills, and review feedback.

Use the Playbook readiness labels: Not Started, Learning, Practicing, Strong, Interview Ready, Principal-Level Evidence. Reading alone is not evidence. Do not invent universal numerical pass thresholds before baseline exercises establish a useful calibration.

## 13. Cross-cutting review checklist

Apply relevant parts to every substantial design:
- Requirements, exclusions, invariants, and assumptions.
- Workload, peak traffic, growth, capacity, tail latency, and cost.
- Data ownership, access patterns, consistency and lifecycle.
- Security, privacy, abuse and trust boundaries; tenant boundaries and isolation where applicable.
- Concurrency, retries, partial failure, overload and recovery.
- Observability, SLOs, deployment, migration and operations.
- Alternatives, complexity, team ownership, and what evidence would change the design.

This is a review lens, not a demand that every design use every technology or cover every concern at equal depth.

## 14. Resource and research policy

- Use the [canonical Resources catalog](../resources/README.md) rather than maintaining a second reading list here: Gaurav Sen and Jordan Has No Life **System Design 2.0** for intuition/concept deep dives; Roberto Vitillo's *Understanding Distributed Systems* for bridging theory and practice; Alex Xu Vol 1/2 and ByteByteGo for **attempt-first** case studies; selective *Designing Data-Intensive Applications* (DDIA) for data/distributed-systems depth. Use current RFCs, standards, primary papers, engineering postmortems and cloud documentation as claim-level evidence. Books/videos are optional targeted aids, not proof of competence.
- Prefer primary papers, official technical documentation, recognized engineering handbooks, and production postmortems for claims that need evidence.
- For multi-tenant architecture, use provider guidance as comparative examples—not universal defaults—and validate current service guarantees, quotas, isolation properties, and limits against official documentation.
- Cite external claims in learning chapters when appropriate. Explain the source's context and limits; do not treat a specific paper or vendor framework as a universal law.
- Distinguish facts from assumptions, simplified models, recommendations, and unknowns.
- Keep cloud details current at the time of study. Avoid freezing changing limits/prices into this syllabus.
- Use original explanations and diagrams; do not reproduce copyrighted passages, proprietary course graphics or PDFs without permission. Verify redistribution rights for third-party files in this public repository.
- Concept chapters and lab documents should use purposeful **Mermaid sequence/flow/state/architecture diagrams, request/data traces, tables, carefully selected original images, explanatory flowcharts, equations and restrained emojis** where they improve understanding. Follow the [Playbook visual-assets policy](../docs/LEARNING_LAB_PLAYBOOK.md#chapter-visuals-diagrams-and-generated-assets); never replace technical explanations with decoration.

## 15. Change policy — keep the syllabus flexible

This syllabus is a stable grounding baseline, not a closed contract.

A future topic or change may be proposed when a design exercise reveals a repeated gap; new authoritative research changes the model; production experience/postmortem demonstrates a missing competency; a target role/project introduces a material constraint; or a cloud service/industry practice changes in a way that affects design choices.

For any material change, record:
1. **Proposal:** what should change and why.
2. **Evidence:** source, incident, exercise result, or requirement motivating it.
3. **Impact:** prerequisites, workload, duplication, and topics affected.
4. **Disposition:** Accept, Modify, Defer, or Reject.
5. **Follow-through:** update this syllabus only when the change is approved; update related chapters/exercises/progress as needed.

Do not add every new trend to the core. Prefer optional extensions when the competency is specialized or evidence is narrow. Preserve backward links and avoid duplicating canonical concept chapters.

## 16. Companion files and progress

- Teaching, exercise, assessment, and publishing rules: [Detailed Playbook](../docs/LEARNING_LAB_PLAYBOOK.md)
- Competency-to-exercise-to-evidence traceability: [Competency Matrix](COMPETENCY_MATRIX.md)
- Exercise structure: [Design Exercise Template](../design-exercises/TEMPLATE.md)
- Progress state: [Progress](../progress/PROGRESS.md)
- Audit and rationale: [Syllabus Audit and Research](../CopilotInstructions/SYLLABUS_AUDIT_AND_RESEARCH.md)
- Concepts index: [Concepts](../concepts/README.md)
- Research source register: [Source Register](../research/SOURCE_REGISTER.md)
- Learning resources: [Resources](../resources/README.md)

At the end of substantial sessions, record demonstrated strengths, gaps, exercises, readiness per scorecard, and the next step in the progress file. Never infer mastery from reading alone.

## 17. Initial next step

Calibrate current system-design experience with one focused question, agree on a sustainable learning cadence, and begin at the appropriate prerequisite. Do not start with an advanced topic merely because the target role is Principal-level.
