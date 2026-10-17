# Research Source Register

Track sources that materially influence the syllabus, a concept chapter or an architecture decision; this is not a reading-completion tracker.

| Topic / claim | Source | Type | Published/updated | Checked on | Supports | Caveats / open questions |
|---|---|---|---|---|---|---|
| Syllabus rationale | [Syllabus audit and research](../CopilotInstructions/SYLLABUS_AUDIT_AND_RESEARCH.md) | Research synthesis | See source | 2026-10-12 | Rationale behind curriculum baseline | Re-check source links and currency before relying on time-sensitive claims |
| Multi-tenant isolation and tenancy models | [Azure tenancy models](https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/considerations/tenancy-models) | Official cloud architecture guidance | Living documentation | 2026-10-13 | Isolation is a spectrum; compare security, performance, reliability, cost, and operational trade-offs by tier | Azure-specific guidance; use as a decision framework, not a universal deployment prescription |
| Multi-tenant noisy-neighbor controls | [Azure noisy-neighbor antipattern](https://learn.microsoft.com/en-us/azure/architecture/antipatterns/noisy-neighbor) | Official cloud architecture guidance | Living documentation | 2026-10-13 | Shared-resource contention, quotas, throttling, and tenant impact | Validate the applicable service limits and controls against current product docs |
| SaaS tenant isolation | [AWS SaaS tenant isolation strategies](https://docs.aws.amazon.com/whitepapers/latest/saas-tenant-isolation-strategies/saas-tenant-isolation-strategies.html) | Official cloud whitepaper | Published 2020-08-01; living documentation page | 2026-10-13 | Tenant isolation as an explicit security boundary and trade-offs between pooled and siloed resources | Older whitepaper; verify service-specific details against current AWS documentation |

Prefer primary papers, official cloud documentation and detailed postmortems. A source supports a claim in context, not a universal law. Mark disputed claims and assumptions explicitly.

| Symmetric encryption / AES | [NIST FIPS 197 — Advanced Encryption Standard (AES)](https://csrc.nist.gov/pubs/fips/197/final) | Official cryptographic standard | Updated 2023-05-09 | 2026-10-18 | AES is a standardized symmetric block cipher | AES alone does not specify an entire secure message protocol; mode and nonce requirements matter |
| TLS 1.3 handshake and traffic protection | [RFC 8446 — The Transport Layer Security (TLS) Protocol Version 1.3](https://www.rfc-editor.org/rfc/rfc8446) | IETF Standards Track RFC | 2018-08 | 2026-10-18 | TLS 1.3 handshake, key agreement, authentication and symmetric traffic protection roles | This chapter provides a simplified overview; consult the RFC for exact protocol behavior and cipher-suite details |
| Cryptographic key lifecycle | [NIST SP 800-57 Part 1 Rev. 5 — Recommendation for Key Management](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final) | Official NIST guidance | 2020-05 | 2026-10-18 | Key protection, lifecycle and management considerations | General guidance; implementation requirements depend on system and compliance context |
