# Progress — System Design Learning Lab

Syllabus: [Syllabus V2](../curriculum/SYLLABUS_V2.md) · [Daily Log](DAILY_LOG.md)

Allowed statuses: `NOT STARTED`, `STARTED`, `COMPLETED`. Rating stays `—` until completion. At completion, rate the discussion/performance out of 5 and link the topic name to its artifact folder. Do not infer progress from repository setup or reading alone.

## Topics — ordered learning path

| Phase | Topic | Status | Rating |
|---|---|---|---|
| 0 | Functional requirements, user journeys, scope and non-goals | NOT STARTED | — |
| 0 | Non-functional requirements and measurable targets | NOT STARTED | — |
| 0 | Requirement conflicts and prioritization | NOT STARTED | — |
| 0 | Latency, throughput, bandwidth, concurrency and tail latency | NOT STARTED | — |
| 0 | Back-of-the-envelope capacity estimation | NOT STARTED | — |
| 0 | Availability, reliability, durability, resilience and fault domains | NOT STARTED | — |
| 0 | SLIs, SLOs, SLAs and error budgets | NOT STARTED | — |
| 0 | Request lifecycle and networking: DNS, HTTP, TCP, TLS, connections and timeouts | NOT STARTED | — |
| 0 | System design workflow: clarify, estimate, model, design and validate | NOT STARTED | — |
| 1 | API contracts: REST/RPC, pagination, versioning, compatibility and auth boundaries | NOT STARTED | — |
| 1 | DNS, proxies, API gateways, load balancing, health checks and service discovery | NOT STARTED | — |
| 1 | Stateless vs stateful services, vertical/horizontal scaling and bottlenecks | NOT STARTED | — |
| 1 | Rate limiting and admission control fundamentals | NOT STARTED | — |
| 1 | Caching patterns, TTL, eviction, invalidation, freshness and stale reads | NOT STARTED | — |
| 1 | Cache stampedes, hot keys, cache penetration, negative caching and cache failure | NOT STARTED | — |
| 1 | Cache consistency, memory limits, hit rate and invalidation cost | NOT STARTED | — |
| 1 | Relational vs non-relational data models and choosing by access patterns | NOT STARTED | — |
| 1 | Data modelling, indexes, query plans, transactions, isolation and concurrency anomalies | NOT STARTED | — |
| 1 | Read/write paths, connection pools, replication basics, partitioning and schema evolution | NOT STARTED | — |
| 1 | Data lifecycle: retention, deletion, backup, restore and integrity | NOT STARTED | — |
| 1 | Managed databases vs self-managed storage: operational trade-offs | NOT STARTED | — |
| 1 | Queues vs event streams, workers, batching, consumer groups, delivery, retries, DLQs, replay and ordering | NOT STARTED | — |
| 1 | Idempotent consumers, deduplication, schema compatibility and transactional outbox | NOT STARTED | — |
| 1 | Sagas, compensation and reconciliation | NOT STARTED | — |
| 1 | Object/blob storage, metadata, CDN, search/indexing, batch processing and lifecycle policies | NOT STARTED | — |
| 2 | Replication, partitioning/sharding, partition keys, hotspots, skew and rebalancing | NOT STARTED | — |
| 2 | Consistent hashing and virtual nodes | NOT STARTED | — |
| 2 | CAP theorem and network partition scenarios | NOT STARTED | — |
| 2 | Consistency models, stale reads, read-your-writes and monotonicity | NOT STARTED | — |
| 2 | Quorums, leader/follower, leader election and consensus concepts | NOT STARTED | — |
| 2 | Clocks, logical ordering, causality and event ordering | NOT STARTED | — |
| 2 | Distributed locks and leases, fencing and expiry | NOT STARTED | — |
| 2 | Transactions and coordination across service/data boundaries | NOT STARTED | — |
| 2 | Timeouts, deadlines, retries, backoff/jitter, retry budgets and idempotency | NOT STARTED | — |
| 2 | At-most-once, at-least-once and scoped exactly-once claims | NOT STARTED | — |
| 2 | Duplicate requests, ambiguous outcomes, cancellation and reconciliation | NOT STARTED | — |
| 2 | Circuit breakers, bulkheads, backpressure, bounded queues, load shedding and graceful degradation | NOT STARTED | — |
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
| 4 | Reliability engineering: fault isolation, graceful degradation and recovery | NOT STARTED | — |
| 4 | Observability: metrics, logs, traces, dashboards, alerts and runbooks | NOT STARTED | — |
| 4 | Incident response, post-incident learning, capacity planning, load testing and chaos experiments | NOT STARTED | — |
| 4 | Operational readiness, ownership, on-call and support procedures | NOT STARTED | — |
| 4 | Threat modelling: assets, actors, trust boundaries, attack surfaces and risks | NOT STARTED | — |
| 4 | Identity, authentication, authorization, least privilege, secrets, encryption and network boundaries | NOT STARTED | — |
| 4 | Data classification, retention/deletion, auditability, privacy and abuse prevention | NOT STARTED | — |
| 4 | Safe rollout, canary deployment, health gates, feature flags and rollback constraints | NOT STARTED | — |
| 4 | Expand-contract changes, dual reads/writes, backfills, validation and deprecation | NOT STARTED | — |
| 4 | Data migration, repartitioning, ownership transfer and staged cutover | NOT STARTED | — |
| 4 | Multi-region replication, failover/failback, split-brain and data-loss bounds | NOT STARTED | — |
| 4 | RPO/RTO, disaster recovery plans, restore tests and recovery drills | NOT STARTED | — |
| 4 | Tail latency, saturation, bottleneck diagnosis, headroom and performance testing | NOT STARTED | — |
| 4 | Cost drivers, utilization, storage/egress, operational toil and cost-performance-reliability trade-offs | NOT STARTED | — |
| 5 | Architecture reviews and design documents with explicit assumptions, options and risks | NOT STARTED | — |
| 5 | Ambiguous/conflicting requirements, stakeholder alignment and scope reduction | NOT STARTED | — |
| 5 | ADRs: context, alternatives, consequences, risks, reversibility and review triggers | NOT STARTED | — |
| 5 | Evolutionary architecture, migration sequencing, compatibility and deprecation | NOT STARTED | — |
| 5 | Cross-team boundaries, service/data ownership, interfaces and coordination costs | NOT STARTED | — |
| 5 | Build vs buy: lifecycle cost, reliability, security, lock-in and team capacity | NOT STARTED | — |
| 5 | Technical strategy, prioritization, opportunity cost, risk reduction and measurable outcomes | NOT STARTED | — |
| 5 | Operational ownership, on-call burden, blast radius, toil and support model | NOT STARTED | — |
| 5 | Technical disagreement, influence without authority and decision rationale | NOT STARTED | — |
| 5 | Evidence-driven decisions, experiments, leading indicators and decision triggers | NOT STARTED | — |
| 5 | Knowing when not to add abstractions, services, databases or coordination | NOT STARTED | — |
| Ongoing | Interview practice and spaced revision | NOT STARTED | — |
| Optional | Optional specializations: deep consensus/formal verification, Kubernetes/service mesh, CQRS/event sourcing, ML serving, specialized data systems, active-active multi-region, sustainability/compliance | NOT STARTED | — |

## Continue / specific-topic rules

- **CONTINUE:** If a topic is STARTED, resume from the last Daily Log entry for it. Otherwise start the first NOT STARTED topic after the last COMPLETED topic.
- **SPECIFIC TOPIC:** Study that row regardless of sequence and update only its status. If it is outside Syllabus V2, add it under `Misc / Ad-hoc` before studying.
- Keep all rows in syllabus order. Labs are first-class rows, interleaved into the learning path.
