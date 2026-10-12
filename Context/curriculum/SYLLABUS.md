# System Design Learning Lab — Syllabus

See [the Detailed Playbook](../docs/LEARNING_LAB_PLAYBOOK.md) for teaching, exercise, assessment, and publishing rules.

| Phase | Focus | Exit evidence |
|---|---|---|
| 0 | Requirements, NFRs, capacity, latency, throughput, reliability, SLI/SLO | Can clarify a problem and estimate a basic workload |
| 1 | Networking, APIs, load balancing, caching, databases, queues, storage, CDN | Can explain core building blocks and trade-offs |
| 2 | Replication, partitioning, consistent hashing, CAP, consistency, quorum, consensus, failure handling | Can reason about distributed data and partial failure |
| 3 | End-to-end design exercises | Can derive a design from requirements and critique alternatives |
| 4 | Multi-region, resilience, migrations, observability, security, cost | Can reason about production operations and evolution |
| 5 | Staff/Principal architecture, strategy, ownership, cross-team boundaries | Can defend decisions across technical and organizational dimensions |

## Phase 0 — Fundamentals
- [ ] Functional and non-functional requirements
- [ ] Latency, throughput, bandwidth, concurrency
- [ ] Capacity estimation and peak-load assumptions
- [ ] Availability, reliability, durability, resilience
- [ ] SLIs, SLOs, SLAs, error budgets
- [ ] System design workflow

## Phase 1 — Building blocks
- [ ] DNS, HTTP, TCP, TLS
- [ ] API design: REST and RPC
- [ ] Load balancers, reverse proxies, gateways
- [ ] Caching, TTL, eviction, invalidation, stampedes
- [ ] Relational databases, NoSQL, indexes, transactions
- [ ] Queues, event streams, background workers
- [ ] Object storage, CDN, search, batch processing

## Phase 2 — Distributed systems
- [ ] Vertical vs horizontal scaling
- [ ] Replication and partitioning
- [ ] Consistent hashing and rebalancing
- [ ] CAP theorem
- [ ] Consistency models and stale reads
- [ ] Quorum and consensus
- [ ] Leader election and distributed locks
- [ ] Idempotency, retries, timeouts, duplicate processing
- [ ] Ordering, clocks, partial failure
- [ ] Rate limiting, backpressure, circuit breakers

## Phase 3 — Design exercises
- [ ] URL shortener
- [ ] Rate limiter
- [ ] Distributed key-value store
- [ ] Notification service
- [ ] File/object storage
- [ ] News feed
- [ ] Chat/messaging
- [ ] Ticket booking / limited inventory
- [ ] Payment processing and reconciliation
- [ ] Telemetry/event ingestion

## Phase 4 — Advanced architecture
- [ ] Hot keys and hotspots
- [ ] Multi-region systems
- [ ] Disaster recovery: RPO/RTO
- [ ] Fault isolation and graceful degradation
- [ ] Data migration and backward compatibility
- [ ] Observability and incident response
- [ ] Security, privacy, abuse prevention
- [ ] Cost and operational complexity

## Phase 5 — Staff/Principal
- [ ] Architecture review and design docs
- [ ] Ambiguous and conflicting requirements
- [ ] Evolutionary architecture and migration
- [ ] Cross-team interfaces and ownership
- [ ] Build versus buy
- [ ] Technical strategy and prioritization
- [ ] Operational burden and blast radius
- [ ] Communicating trade-offs and resolving disagreement
