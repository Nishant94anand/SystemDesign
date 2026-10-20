# Learning Resources — curated and selective

This is the **single canonical resource catalog**. Teach the **system problem and guarantees first**; recommend particular sections/videos when relevant, not an entire course by default. The aim is reasoning, implementation awareness and independent design—not completion of a reading list. Source details and editions must be checked before precise chapter/page recommendations.

## Default learning path (flexible)

1. **Intuition:** [Gaurav Sen](https://www.youtube.com/@gkcs) for accessible starting mental models, and [Jordan Has No Life — System Design 2.0](https://www.youtube.com/@jordanhasnolife5163/playlists) for concept/component depth (find the creator's **System Design 2.0** playlist under Playlists). Use the matching video, not a rigid sequence.
2. **Distributed systems bridge:** Roberto Vitillo, *Understanding Distributed Systems* ([official site](https://understandingdistributed.systems/)); use for foundational models around networking, state, reliability and consistency.
3. **Structured design practice:** Alex Xu, *System Design Interview*, **Vol 1** and **Vol 2**, plus [ByteByteGo](https://bytebytego.com/) / [ByteByteGo videos](https://www.youtube.com/@ByteByteGo). Attempt each exercise **before** inspecting worked solutions. Vol 2 is a follow-on, not an interchangeable duplicate.
4. **Deep technical foundation:** Martin Kleppmann, *Designing Data-Intensive Applications* (DDIA; [official site](https://dataintensive.net/)), selectively for storage engines, replication, partitioning, transactions, distributed correctness and stream processing. Verify edition because chapter numbering may vary.
5. **Evidence and production practice:** standards/RFCs, original research papers, official product docs, performance/reliability engineering guides, incident postmortems and engineering write-ups. These validate specific claims and help reason about operational limits.

## Additional useful references (pick by problem)

| Resource | Use when | Notes |
|---|---|---|
| [System Design Primer](https://github.com/donnemartin/system-design-primer) | Breadth, checklists, quick recall | Community reference, verify current product specifics |
| [Hello Interview — System Design](https://www.hellointerview.com/learn/system-design/in-a-hurry/introduction) | Interview flow, timed drills and feedback | Compare approaches after independent attempt |
| [Jordan Has No Life channel](https://www.youtube.com/@jordanhasnolife5163) | Deeper explanation of databases, streaming and distributed systems | Specifically use **System Design 2.0** playlist for this lab |
| [Arpit Bhayani](https://www.youtube.com/@AsliEngineering) | Implementation-facing internals and algorithms | Cross-check guarantees against official docs |
| [Hussein Nasser](https://www.youtube.com/@hnasr) | Networking, protocols, backend behavior | Topic-specific use |
| [Google SRE books](https://sre.google/books/) and [SRE Workbook](https://sre.google/workbook/table-of-contents/) | SLIs/SLOs, operations, reliability, incident response | Production evidence, not interview shortcuts |
| [AWS Builders' Library](https://aws.amazon.com/builders-library/) | Retries, timeouts, deployment and overload | Operational deep dives |
| [Martin Fowler — Patterns of Distributed Systems](https://martinfowler.com/articles/patterns-of-distributed-systems/) | Coordination, messaging and distributed data patterns | Evaluate assumptions and applicability |
| [OpenTelemetry docs](https://opentelemetry.io/docs/) | Instrumentation, tracing and metrics | Current implementation guidance |
| [MIT Distributed Systems course](https://pdos.csail.mit.edu/6.5840/) and [CMU Database Group](https://www.youtube.com/@CMUDatabaseGroup) | Advanced hands-on/DB engine depth | Optional, not mandatory core |
| [RFC Editor](https://www.rfc-editor.org/) and [NIST CSRC](https://csrc.nist.gov/) | Protocol and cryptographic/security claims | Cite the specific standard |
| [Azure](https://learn.microsoft.com/en-us/azure/architecture/), [AWS](https://docs.aws.amazon.com/wellarchitected/latest/framework/), [Google Cloud](https://cloud.google.com/architecture/framework) architecture guidance | Implementation comparisons, limits, security, disaster recovery, pricing assumptions | Vendor guidance is contextual, not universal proof |

### Existing repository reference materials

The repository contains filenames such as `System_Design_Interview_by_Alex_Xu_Vol_1.pdf`, `System_Design_Interview_by_Alex_Xu_Vol_2.pdf` and `Bytebytego_Big_Archive_System_Design_2025.pdf` (added 2026-10-18). These are **reference files, not licenses, citations or proof of chapter completion**. Their redistribution rights have **not been established by this catalog**. Before keeping third-party books, courses, PDFs or archives in a public GitHub repository, independently confirm explicit redistribution permission for each. If permission is absent or uncertain, remove public copies through an approved process and use official purchase/library links or permitted excerpts instead. Removing a file from the latest commit may not remove it from Git history.

## How tutors should use resources

- Pick one primary explanation source for the present learning goal; optionally one contrast/deep-dive source. Avoid mandatory watching of multiple near-identical videos.
- For a chapter's **optional deep-dive references**, choose only from this catalog, match the resource to the **specific concept** and explain **why it helps**. Prefer one primary and optionally one complementary reference; avoid mandatory reading lists. Use available official links; provide precise edition/chapter/section/video title only after verification, otherwise describe the relevant topic without invented page numbers or playlist IDs.
- **Teach the relevant concept and show a concrete example first**, then invite the learner to reason through one meaningful question. Repeat small explanation → example → learner attempt → feedback cycles; do not expect them to invent unfamiliar concepts. Keep full solution-bearing material gated during interactive learning until an attempt or explicit request; publish a separate complete model-answer section at authorized chapter completion per the Workflow/Playbook.
- Attribute research claims to the original technical paper, standard or official docs when possible rather than citing an interview summary as primary evidence.
- Compare service **guarantees** and costs, not just service names; validate volatile prices/quotas near the study date.
- Mark illustrative simplifications, unknowns, conflicting sources and historical designs. Use original explanations and diagrams.

## Multi-tenant architecture references

- [Azure tenancy models](https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/considerations/tenancy-models) — isolation spectrum and deployment trade-offs.
- [Azure noisy-neighbor antipattern](https://learn.microsoft.com/en-us/azure/architecture/antipatterns/noisy-neighbor) — shared-resource contention and quotas/throttling.
- [AWS SaaS tenant isolation strategies](https://docs.aws.amazon.com/whitepapers/latest/saas-tenant-isolation-strategies/saas-tenant-isolation-strategies.html) — pooled/siloed isolation patterns.

These are provider-specific examples; examine actual security boundaries and operational responsibilities.
