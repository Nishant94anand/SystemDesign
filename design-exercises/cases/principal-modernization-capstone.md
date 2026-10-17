# Principal Capstone — Modernize a Legacy Multi-Tenant SaaS Platform

**Phase:** 5 — Staff/Principal architecture and technical leadership. **Type:** solution-free review, roadmap, and decision-defense exercise.

## Scenario

A mature B2B work-management SaaS platform serves thousands of tenants on an evolving mix of shared services, a legacy relational datastore and multiple integrations. Growth is uneven; some large customers demand isolation, region-specific data handling and stronger recovery objectives. Several product teams share deployment ownership, and customer-visible incidents during major changes are becoming costly.

The CTO asks for a **two-year technical strategy with an executable first quarter**. You must explain why the current architecture is or is not the limiting factor; do **not** default to microservices, active-active multi-region or replacing the database.

## First-round exercise

1. Identify product/business objectives, stakeholder conflicts, contractual/security obligations, success measures, explicit non-goals and what evidence is missing.
2. State existing-system assumptions to verify via code/telemetry/experiments. Draw current-state dependency, data and team ownership boundaries.
3. Propose at least two credible evolutionary strategies (including a simpler/minimal-change alternative); compare delivery risk, total lifecycle cost, reliability, correctness, security and reversibility.
4. For the selected approach, define tenant isolation and authorization guarantees, data/control-plane boundaries, SLIs/SLOs, fault isolation, regional dependencies and expected RPO/RTO.
5. Draft an ADR, sequencing plan and ownership/contract model; propose safe canaries, compatibility, cutover validation, backfill, rollback limits and incident/DR game days.
6. Plan cross-team coordination, escalation and decision gates. Explain how to gain agreement without claiming sole authority over every team.

Use [ADR template](../../architecture-decisions/TEMPLATE.md) and [exercise template](../TEMPLATE.md). Use original Mermaid roadmap/sequence/failure diagrams, risk tables and quantified assumptions where they communicate decisions better than prose.

## Progressive review-panel interventions

1. Finance cuts the infrastructure budget by 25%; what do you protect, simplify or postpone?
2. A high-value customer requires single-tenant isolation and regional residency six months earlier than planned.
3. During dual writes, a subset of events are duplicated and reconciler lag grows. Demonstrate data correctness and migration recovery.
4. Teams disagree about service ownership and API contracts; present the strongest argument for the option you reject.
5. A regional outage hits during a partial migration. Specify impact scope, failover/failback and what can actually be rolled back.
6. New evidence invalidates your original growth projection. Which ADR decision triggers fire, and how does your roadmap change?

## Required completion evidence

A decision-quality strategy memo / ADR; current and proposed data/dependency/team maps; objective measures; alternatives and rationale; staged migration/backward compatibility, ownership, incident readiness, security and cost analysis; validated assumptions and proposed experiments; risk register and review triggers. Demonstrate the ability to **change the decision** under evidence, not merely defend a preferred diagram. Assess interview readiness and real-world Principal judgment independently. Do not provide a worked solution in this case prompt.
