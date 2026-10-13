# Competency-to-Exercise-to-Evidence Matrix

This matrix is a traceability aid, not a second progress tracker. Use [Syllabus V2](SYLLABUS_V2.md) for the curriculum order, [Progress](../progress/PROGRESS.md) for current status, and the [Playbook](../docs/LEARNING_LAB_PLAYBOOK.md) for teaching and scoring detail.

| Competency | Phase / ordered topics | Practice / application | Minimum observable evidence | Scorecard emphasis |
|---|---|---|---|---|
| Requirements and problem framing | Phase 0: requirements, scope, NFRs, prioritization | Every design lab; especially URL shortener | Requirements brief, explicit non-goals, measurable NFRs and trade-offs | Interview + Production/Principal |
| Quantitative reasoning and capacity | Phase 0 estimates; Phase 4 performance/cost | URL shortener, telemetry ingestion, capstones | Calculations with units, peak/average assumptions, headroom and sensitivity | Interview + Production/Principal |
| APIs, networking and service boundaries | Phase 0 networking; Phase 1 API/traffic | URL shortener, chat, notification service | Request lifecycle, API contracts, versioning/auth boundaries and failure behavior | Interview |
| Data modelling and lifecycle | Phase 1 databases/storage | File storage, ticket booking, payments | Access-pattern-driven model, indexes/transactions, invariants, retention and restore concerns | Interview + Production/Principal |
| Caching and performance | Phase 1 caching; Phase 4 tail latency | URL shortener, news feed | Freshness/invalidation decision, hot-key/stampede analysis and validation plan | Interview + Production/Principal |
| Asynchronous correctness | Phase 1 queues, outbox, sagas; Phase 2 delivery semantics | Notifications, payments, ticket booking | Failure matrix covering commit/publish/consume, duplicates, replay, poison messages and reconciliation | Interview + Production/Principal |
| Distributed data and consistency | Phase 2 replication, partitioning, consistency, clocks and consensus | Distributed key-value store, rate limiter | State invariants, failure/partition reasoning, guarantees and trade-offs | Interview |
| Overload and resilience | Phase 2 retries/backpressure; Phase 4 reliability | Rate limiter, telemetry ingestion, incident scenario | Deadlines, retry budget, load shedding/backpressure and recovery/blast-radius analysis | Interview + Production/Principal |
| Security and privacy | Phase 4 threat modelling, identity and data lifecycle; cross-cutting review | Every relevant lab; multi-tenant SaaS | Threat model with trust boundaries, abuse cases and testable controls | Production/Principal |
| Multi-tenant architecture | Phase 1 tenancy models; Phase 3 SaaS lab; Phase 4 tenant-aware operations | Multi-tenant SaaS platform | Isolation matrix, cross-tenant access controls, quotas/fairness, tenant telemetry and lifecycle/migration plan | Interview + Production/Principal |
| Observability and incident response | Phase 0 SLOs; Phase 4 observability/incidents | Telemetry ingestion and failure labs | SLIs, metrics/logs/traces, actionable alert/runbook and diagnosis | Production/Principal |
| Deployment and migration safety | Phase 4 rollout, compatibility, expand-contract, cutover | Ticket booking, payments, multi-tenant SaaS | Staged plan, validation, rollback limits, ownership and success criteria | Production/Principal |
| Multi-region and disaster recovery | Phase 4 regional design, RPO/RTO, restore drills | File storage and capstones | Dependency-aware recovery plan, data-loss bounds and drill that validates RPO/RTO | Production/Principal |
| Cost and operational trade-offs | Phase 0 estimates; Phase 4 cost; Phase 5 build-vs-buy | Any lab with explicit scale/cost constraint | Assumptions, cost drivers, operational burden and evidence that could change the choice | Interview + Production/Principal |
| Staff/Principal decision quality | Phase 5 ADRs, strategy, ownership, stakeholder alignment | Capstones and ADRs | Credible alternatives, rationale, risks, reversibility, ownership, migration and decision triggers | Production/Principal |

## How to use the matrix

- A row is demonstrated through artifacts and discussion, not by reading the topic.
- A lab may cover only a subset of competencies; do not force every concern into every design.
- Score interview readiness separately from production/Principal evidence.
- Add or revise a row only when the syllabus, exercise set, or target role reveals a real coverage gap.
