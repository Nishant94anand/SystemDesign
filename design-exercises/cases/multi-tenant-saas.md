# Exercise: Multi-Tenant SaaS Platform

## First-attempt prompt

Design a multi-tenant SaaS work-management platform used by hundreds of customer organizations. Each organization has multiple users, projects, files, integrations, and background jobs. Most tenants are small, a few are very large, and usage peaks overlap. Some customers require stronger data isolation, regional data residency, or tenant-specific restore options.

Do not assume that every component must be shared or every tenant needs a dedicated stack. Choose a model from requirements and justify it.

## Initial scope

- Define what a tenant is and how a request's tenant context is established and trusted.
- Identify functional requirements, explicit non-goals, and measurable NFRs.
- Compare pooled, siloed, and hybrid isolation options for application, data, cache, queues, configuration, and deployment.
- Propose data ownership and access patterns; explain how cross-tenant access is prevented across APIs, background workers, exports, search, caches, and administrative/support paths.
- Design tenant-aware quotas, rate limits, fair resource allocation, and noisy-neighbor containment.
- Define tenant-level telemetry for health, latency, errors, resource consumption, and cost.
- Cover onboarding, configuration, upgrades, tenant migration, offboarding, deletion, backup/restore, and auditability.
- Explain how residency, encryption/key requirements, availability tiers, and cost affect the isolation decision.

## Progressive follow-ups

1. One tenant generates 40% of background-job volume and causes queue lag for others. What do you measure and change?
2. A bug in an export endpoint may have returned another tenant's records. How do you contain the incident, establish scope, and verify the fix?
3. A large tenant must move from pooled storage to a dedicated database with minimal downtime. Design the migration and rollback boundaries.
4. A customer requires data to remain in a particular region and requests a tenant-specific restore. Which boundaries and processes must change?
5. Tenant count grows by 10x. Which architecture choices become bottlenecks, and what evidence would trigger a redesign?

## Required evidence

Use [the design exercise template](../TEMPLATE.md). Include:

- Requirements, assumptions, tenant definition, and NFRs.
- Isolation matrix by component and rationale for each choice.
- Threat model focused on cross-tenant access and privilege boundaries.
- Data model and authorization/invariant checks.
- Capacity estimates, quota/fairness policy, and noisy-neighbor failure analysis.
- Tenant-aware observability plan with bounded-cardinality metrics.
- Tenant lifecycle and migration/restore plan.
- Alternatives, costs, risks, and evidence that would change the decision.

Keep the first attempt solution-free. Ask for progressive hints after documenting your own initial design.
