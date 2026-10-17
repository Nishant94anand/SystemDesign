# Progress — System Design Learning Lab

Syllabus: [Syllabus V2](../curriculum/SYLLABUS_V2.md) · [Competency Matrix](../curriculum/COMPETENCY_MATRIX.md) · [Daily Log](DAILY_LOG.md)

Allowed statuses: `NOT STARTED`, `STARTED`, `COMPLETED`. Rating stays `—` until completion. On completion, rate discussion/performance out of 5 and link the topic name to its artifact folder. Do not infer progress from repository setup or reading alone.

## Topics — ordered learning path

| Phase | Topic | Status | Rating |
|---|---|---|---|
| 0 | [Functional requirements, user journeys, scope and non-goals](../concepts/foundations/functional-requirements/) | COMPLETED | 4/5 |
| 0 | Non-functional requirements and measurable targets | STARTED | — |
| 0 | Requirement conflicts and prioritization | NOT STARTED | — |
| 0 | Latency, throughput, bandwidth, concurrency and tail latency | NOT STARTED | — |
| 0 | Back-of-the-envelope capacity estimation | NOT STARTED | — |
| 0 | Availability, reliability, durability, resilience and fault domains | NOT STARTED | — |
| 0 | SLIs, SLOs, SLAs and error budgets | NOT STARTED | — |
| 0 | Networking/request lifecycle: DNS, HTTP, TCP, TLS, connections, timeouts | NOT STARTED | — |
| 0 | System design workflow: clarify, estimate, model, design, validate | NOT STARTED | — |
| 1 | API contracts: REST/RPC, pagination, versioning, compatibility, auth boundaries | NOT STARTED | — |
| 1 | Traffic management: gateways, load balancing, health checks, service discovery | NOT STARTED | — |
| 1 | Stateless/stateful services, scaling and capacity bottlenecks | NOT STARTED | — |
| 1 | Rate limiting and admission control | NOT STARTED | — |
| 1 | Caching patterns, TTL, eviction, invalidation, freshness and stale reads | NOT STARTED | — |
| 1 | Cache failure modes: stampedes, hot keys, penetration, negative caching | NOT STARTED | — |
| 1 | Cache consistency, memory limits, hit rate and invalidation cost | NOT STARTED | — |
| 1 | Relational/non-relational models and access-pattern-driven choice | NOT STARTED | — |
| 1 | Data modelling, indexes, query plans, transactions, isolation and concurrency anomalies | NOT STARTED | — |
| 1 | Read/write paths, connection pools, replication, partitioning and schema evolution | NOT STARTED | — |
| 1 | Data lifecycle: retention, deletion, backup, restore and integrity | NOT STARTED | — |
| 1 | Managed databases vs self-managed storage | NOT STARTED | — |
| 1 | Queues vs streams, workers, delivery, retries, DLQs, replay and ordering | NOT STARTED | — |
| 1 | Idempotent consumers, deduplication, schema compatibility and transactional outbox | NOT STARTED | — |
| 1 | Sagas, compensation and reconciliation | NOT STARTED | — |
| 1 | Object storage, metadata, CDN, search/indexing, batch and lifecycle policies | NOT STARTED | — |
| 1 | Multi-tenant fundamentals: tenant boundaries, isolation models, quotas and noisy neighbors | NOT STARTED | — |
| 2 | Replication, sharding, partition keys, hotspots, skew and rebalancing | NOT STARTED | — |
| 2 | Consistent hashing and virtual nodes | NOT STARTED | — |
| 2 | CAP theorem and network partition scenarios | NOT STARTED | — |
| 2 | Consistency models, stale reads, read-your-writes and monotonicity | NOT STARTED | — |
| 2 | Quorums, leader/follower, leader election and consensus | NOT STARTED | — |
| 2 | Clocks, logical ordering, causality and event ordering | NOT STARTED | — |
| 2 | Distributed locks/leases, fencing and expiry | NOT STARTED | — |
| 2 | Transactions and coordination across service/data boundaries | NOT STARTED | — |
| 2 | Timeouts, deadlines, retries, backoff/jitter, retry budgets and idempotency | NOT STARTED | — |
| 2 | At-most-once, at-least-once and scoped exactly-once claims | NOT STARTED | — |
| 2 | Duplicate requests, ambiguous outcomes, cancellation and reconciliation | NOT STARTED | — |
| 2 | Circuit breakers, bulkheads, backpressure, bounded queues, load shedding and degradation | NOT STARTED | — |
| 2 | Cascading failures, correlated dependencies, recovery storms and blast-radius containment | NOT STARTED | — |
| 3 — Labs | LAB — URL Shortener | NOT STARTED | — |
| 3 — Labs | LAB — Rate Limiter | NOT STARTED | — |
| 3 — Labs | LAB — Distributed Key-Value Store | NOT STARTED | — |
| 3 — Labs | LAB — Notification Service | NOT STARTED | — |
| 3 — Labs | LAB — File/Object Storage | NOT STARTED | — |
| 3 — Labs | LAB — News Feed | NOT STARTED | — |
| 3 — Labs | LAB — Chat/Messaging | NOT STARTED | — |
| 3 — Labs | LAB — Ticket Booking / Limited Inventory | NOT STARTED | — |
| 3 — Labs | LAB — Payment Processing and Reconciliation | NOT STARTED | — |
| 3 — Labs | LAB — Telemetry/Event Ingestion | NOT STARTED | — |
| 3 — Labs | LAB — Multi-Tenant SaaS Platform | NOT STARTED | — |
| 4 | Reliability engineering, fault isolation, degradation and recovery | NOT STARTED | — |
| 4 | Observability: metrics, logs, traces, dashboards, alerts, runbooks | NOT STARTED | — |
| 4 | Incident response, post-incident learning, capacity/load testing and chaos experiments | NOT STARTED | — |
| 4 | Operational readiness, ownership, on-call and support | NOT STARTED | — |
| 4 | Threat modelling, trust boundaries, attack surfaces and risk prioritization | NOT STARTED | — |
| 4 | Identity, authentication, authorization, least privilege, secrets, encryption and network boundaries | NOT STARTED | — |
| 4 | Data classification, retention/deletion, auditability, privacy and abuse prevention | NOT STARTED | — |
| 4 | Multi-tenant production operations: tenant-aware observability, lifecycle, quotas and isolation tiers | NOT STARTED | — |
| 4 | Safe rollout, canary deployment, health gates, feature flags and rollback constraints | NOT STARTED | — |
| 4 | Expand-contract changes, dual reads/writes, backfills, validation and deprecation | NOT STARTED | — |
| 4 | Data migration, repartitioning, ownership transfer and staged cutover | NOT STARTED | — |
| 4 | Multi-region replication, failover/failback, split-brain and data-loss bounds | NOT STARTED | — |
| 4 | RPO/RTO, disaster recovery, restore tests and recovery drills | NOT STARTED | — |
| 4 | Tail latency, saturation, bottleneck diagnosis, headroom and performance testing | NOT STARTED | — |
| 4 | Cost drivers, utilization, storage/egress, toil and cost/reliability trade-offs | NOT STARTED | — |
| 5 | Architecture reviews and design docs: assumptions, options and risks | NOT STARTED | — |
| 5 | Ambiguous requirements, stakeholder alignment and scope reduction | NOT STARTED | — |
| 5 | ADRs: context, alternatives, consequences, risks, reversibility and review triggers | NOT STARTED | — |
| 5 | Evolutionary architecture, migration sequencing, compatibility and deprecation | NOT STARTED | — |
| 5 | Cross-team boundaries, service/data ownership, interfaces and coordination costs | NOT STARTED | — |
| 5 | Build vs buy: lifecycle cost, reliability, security, lock-in and team capacity | NOT STARTED | — |
| 5 | Technical strategy, prioritization, opportunity cost and measurable outcomes | NOT STARTED | — |
| 5 | Operational ownership, on-call burden, blast radius, toil and support model | NOT STARTED | — |
| 5 | Technical disagreement, influence without authority and decision rationale | NOT STARTED | — |
| 5 | Evidence-driven decisions, experiments, leading indicators and decision triggers | NOT STARTED | — |
| 5 | Knowing when not to add abstractions, services, databases or coordination | NOT STARTED | — |
## Recurring practice (not a sequential topic)

Interview practice and spaced revision recur throughout the curriculum. Record each session and the next prompt in the [Daily Log](DAILY_LOG.md), and assess interview readiness separately from production/Principal evidence. Do not mark this recurring activity as a completed curriculum topic.

## Optional specializations

Optional specialization topics are listed in [Syllabus V2](../curriculum/SYLLABUS_V2.md). When one is selected, add a specific row to this table with its status and rating before studying it; do not track all optional topics as one catch-all row.

## Misc / Ad-hoc

Out-of-syllabus topics are tracked here separately from the ordered core curriculum. A completed status records that the requested session/artifact is complete; the rating reflects the discussion coverage, not proof of independent mastery.

| Type | Topic | Status | Rating |
|---|---|---|---|
| Ad-hoc | [Symmetric and asymmetric encryption](../concepts/production-architecture/security/symmetric-asymmetric-encryption/) | COMPLETED | 3/5 |

## Continue / specific-topic rules
- **CONTINUE:** If a topic is STARTED, resume from its last relevant Daily Log entry. Otherwise start the next NOT STARTED topic in the ordered table, respecting prerequisite order.
- **SPECIFIC TOPIC:** Study that topic regardless of sequence and update only its status. If outside Syllabus V2, add it under `Misc / Ad-hoc` before studying.
- Keep this table as the roadmap; labs are first-class rows in learning order.
