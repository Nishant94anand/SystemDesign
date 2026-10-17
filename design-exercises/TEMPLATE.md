# Design Exercise: [System Name]

## Status and evidence
- Syllabus phase:
- Iteration: Initial design / New constraint / Failure lab / Evolution
- Date:
- Readiness: Not assessed
- Related concept chapters:

## 1. Problem framing
### Users and primary workflows
### Functional requirements
### Explicit non-goals / out of scope
### Business constraints and success measures

## 2. Non-functional requirements
- Availability / reliability:
- Latency targets (percentiles where relevant):
- Throughput / peak load:
- Durability and correctness:
- Consistency and ordering:
- Security / privacy / abuse:
- Recovery objectives (RPO/RTO, if relevant):
- Cost constraints:
- Conflicting priorities:

## 3. Assumptions and capacity estimates
Show calculations with units. Estimate average/peak requests, concurrency, data size, retention, bandwidth, growth, headroom and cost drivers as relevant. Include sensitivity to uncertain inputs.

## 4. APIs and workflows
Document contracts, key request/response fields, errors, pagination/versioning, authentication boundaries and synchronous/asynchronous workflows.

## 5. Data model, invariants and access patterns
Describe entities, ownership, indexes, read/write patterns, transactions and lifecycle. State invariants under concurrency, retries and partial failure.

## 6. High-level architecture
Prefer clear editable Mermaid architecture/sequence/flow/state diagrams where they add understanding; supplement with original images, equations, comparison tables, traces or restrained emojis under the [Playbook visual-assets policy](../docs/LEARNING_LAB_PLAYBOOK.md#chapter-visuals-diagrams-and-generated-assets). Explain diagrams in prose, label critical boundaries/assumptions, and validate paths and rendering. Trace a request and a data change end-to-end; mark trust boundaries, dependencies and failure domains.

## 7. Key decisions and alternatives
For each decision: chosen approach, requirements satisfied, credible alternatives, correctness/performance implications, operational burden/cost/lock-in, assumptions that could invalidate it, and evidence that would change the choice.

## 8. Data correctness and distributed behaviour
Where relevant, analyse concurrency, transactions, partitioning, replication, ordering, idempotency, retries, duplicate delivery, ambiguous outcomes, reconciliation and recovery.

## 9. Failure, overload and recovery
Consider timeouts, dependency/node/region failure, hot keys, queue growth, retry storms, load shedding, graceful degradation, restore/failover and blast radius. State expected behaviour.

## 10. Security and observability
Cover trust boundaries, identity, authorization, least privilege, secrets, encryption, retention/deletion and abuse controls as relevant. Define metrics, logs, traces, SLIs/SLOs, alerts and diagnostic signals.

## 11. Operations, deployment and evolution
Cover rollout/rollback, compatibility, migrations/backfills, validation, ownership/on-call, runbooks, recovery drills, capacity and scaling path. Distinguish code rollback from data rollback.

## 12. Cloud comparison (when relevant)
Keep the core vendor-neutral. Compare Azure, AWS and Google Cloud only where semantics, limits, operations, security or cost materially differ. Cite current official documentation for product-specific claims.

## 13. Review and open risks
- Strongest part:
- Biggest unresolved risks:
- Assumptions to validate:
- What would change the design:
- Simplest viable alternative:
- Staff/Principal considerations:
- Follow-up experiment/test:

## 14. Assessment
- Requirements and estimation:
- Architecture and data/API flow:
- Correctness and failure reasoning:
- Trade-off communication:
- Discussion coverage rating (separate from mastery):
- Independently demonstrated vs guided vs unassessed:
- Interview readiness evidence:
- Production / Principal-level evidence:
- Remaining gaps:
- Next action:
