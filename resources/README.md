# Learning Resources

Resources support reasoning; they do not replace explanation, practice or mastery evidence.

- **Initial intuition:** Gaurav Sen videos selected for the current concept.
- **Distributed-systems concepts:** *Understanding Distributed Systems* by Roberto Vitillo, paired with independent resources and discussion.
- **Structured design practice:** Alex Xu's system design material, paired with independent attempts.
- **Distributed-systems depth:** selected *Designing Data-Intensive Applications* (Martin Kleppmann) chapters when relevant.
- **Primary evidence:** research papers, protocol specifications, official Azure/AWS/Google Cloud documentation and production postmortems.
- **Additional references:** reputable open-source repositories and other verifiable resources, selected for the topic at hand.

Recommend specific sections when useful; do not assume completion. Verify current product guarantees, quotas, pricing and feature availability. Do not reproduce copyrighted passages.

## Multi-tenant architecture references

- [Azure tenancy models](https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/considerations/tenancy-models) — isolation spectrum and deployment trade-offs.
- [Azure noisy-neighbor antipattern](https://learn.microsoft.com/en-us/azure/architecture/antipatterns/noisy-neighbor) — shared-resource contention and quotas/throttling.
- [AWS SaaS tenant isolation strategies](https://docs.aws.amazon.com/whitepapers/latest/saas-tenant-isolation-strategies/saas-tenant-isolation-strategies.html) — tenant isolation and pooled/siloed strategies.

Treat these as provider-specific examples; compare the guarantees and operational responsibilities that matter to the workload.

## Repository copies of learning materials

Some reference files are stored under `resources/`. Before sharing or keeping this repository public, verify that every bundled book, course, PDF or archive is licensed for redistribution. Prefer official purchase/library links or permitted excerpts when redistribution rights are unclear.
