# Research Source Register

Track sources that materially influence the syllabus, a concept chapter or an architecture decision; this is not a reading-completion tracker.

| Topic / claim | Source | Type | Published/updated | Checked on | Supports | Caveats / open questions |
|---|---|---|---|---|---|---|
| Syllabus rationale | [Syllabus audit and research](../CopilotInstructions/SYLLABUS_AUDIT_AND_RESEARCH.md) | Research synthesis | See source | 2026-10-12 | Rationale behind curriculum baseline | Re-check source links and currency before relying on time-sensitive claims |
| Multi-tenant isolation and tenancy models | [Azure tenancy models](https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/considerations/tenancy-models) | Official cloud architecture guidance | Living documentation | 2026-10-13 | Isolation is a spectrum; compare security, performance, reliability, cost, and operational trade-offs by tier | Azure-specific guidance; use as a decision framework, not a universal deployment prescription |
| Multi-tenant noisy-neighbor controls | [Azure noisy-neighbor antipattern](https://learn.microsoft.com/en-us/azure/architecture/antipatterns/noisy-neighbor) | Official cloud architecture guidance | Living documentation | 2026-10-13 | Shared-resource contention, quotas, throttling, and tenant impact | Validate the applicable service limits and controls against current product docs |
| SaaS tenant isolation | [AWS SaaS tenant isolation strategies](https://docs.aws.amazon.com/whitepapers/latest/saas-tenant-isolation-strategies/saas-tenant-isolation-strategies.html) | Official cloud whitepaper | Published 2020-08-01; living documentation page | 2026-10-13 | Tenant isolation as an explicit security boundary and trade-offs between pooled and siloed resources | Older whitepaper; verify service-specific details against current AWS documentation |

Prefer primary papers, official cloud documentation and detailed postmortems. A source supports a claim in context, not a universal law. Mark disputed claims and assumptions explicitly.
