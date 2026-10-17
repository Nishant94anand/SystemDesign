# System Design Glossary

Add terms as they arise in a chapter or exercise. Keep definitions concise and link to canonical explanations.

| Term | Working definition | Related area |
|---|---|---|
| SLI | Quantitative measure of a service aspect from the user's perspective | [Phase 0](../curriculum/SYLLABUS_V2.md) |
| SLO | Target value/range for an SLI over a defined window | [Phase 0](../curriculum/SYLLABUS_V2.md) |
| SLA | Agreement defining service commitments and consequences | [Phase 0](../curriculum/SYLLABUS_V2.md) |
| RPO | Maximum tolerable data loss measured in time | [Phase 4](../curriculum/SYLLABUS_V2.md) |
| RTO | Target time to restore a service after disruption | [Phase 4](../curriculum/SYLLABUS_V2.md) |
| Tenant | A customer, organization, workspace, or other isolation boundary defined by a product; it is not automatically the same as an individual user | [Multi-tenant architecture](../curriculum/SYLLABUS_V2.md) |
| Tenant isolation | Controls that prevent a tenant from accessing another tenant's data/resources and constrain cross-tenant impact | [Multi-tenant architecture](../curriculum/SYLLABUS_V2.md) |
| Noisy neighbor | A tenant's resource consumption degrades another tenant's performance or availability | [Multi-tenant architecture](../curriculum/SYLLABUS_V2.md) |

| Plaintext | Original readable data before encryption | [Symmetric and asymmetric encryption](../concepts/production-architecture/security/symmetric-asymmetric-encryption/) |
| Ciphertext | Output of encryption; intended to be unintelligible without the required key | [Symmetric and asymmetric encryption](../concepts/production-architecture/security/symmetric-asymmetric-encryption/) |
| Symmetric encryption | Encryption and decryption use the same shared secret key | [Symmetric and asymmetric encryption](../concepts/production-architecture/security/symmetric-asymmetric-encryption/) |
| Asymmetric cryptography | Cryptographic mechanisms use a mathematically related public/private key pair; purposes include key agreement, signatures and, in some schemes, encryption | [Symmetric and asymmetric encryption](../concepts/production-architecture/security/symmetric-asymmetric-encryption/) |
| AEAD | Authenticated Encryption with Associated Data; provides confidentiality and integrity/authenticity for encrypted data and associated unencrypted data | [Symmetric and asymmetric encryption](../concepts/production-architecture/security/symmetric-asymmetric-encryption/) |
| Key agreement | Protocol by which parties derive shared secret material, e.g. ECDH; it is not itself the bulk-data encryption algorithm | [Symmetric and asymmetric encryption](../concepts/production-architecture/security/symmetric-asymmetric-encryption/) |
| Digital signature | Cryptographic operation used to authenticate a signed message and detect changes; verification uses the corresponding public key | [Symmetric and asymmetric encryption](../concepts/production-architecture/security/symmetric-asymmetric-encryption/) |

These seed entries are not evidence of mastery.
