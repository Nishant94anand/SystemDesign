# Active Recall and Flashcards

Attempt answers before reopening the canonical chapter. Prefer scenarios over definitions.

1. How do latency, throughput, bandwidth and concurrency differ?
2. Why can average latency hide a tail-latency problem?
3. What distinguishes an SLI, SLO and SLA?
4. Why can retrying a timed-out write be unsafe?
5. What invariant must limited-inventory booking preserve?
6. Why might a distributed lock need fencing?
7. What does an RPO/RTO target fail to prove without a restore drill?
8. What evidence would justify simplifying an architecture instead of adding a distributed component?
9. Why is authenticated access not sufficient to prove tenant isolation?
10. Compare pooled, siloed and hybrid tenancy models. What requirements would justify the added cost of stronger isolation?
11. How would you detect and contain a noisy neighbor without creating unbounded telemetry cardinality?

12. Why is symmetric encryption usually used for bulk application data even when asymmetric cryptography is involved in establishing a secure connection?
13. In TLS 1.3, what are the distinct roles of a certificate signature, ephemeral key agreement (such as ECDHE), and symmetric traffic encryption?
14. Why does sharing a public key not automatically prove whose key it is, and how can an unverified key enable a man-in-the-middle attack?
15. What security properties does AEAD provide beyond confidentiality, and what does it not prove about the real-world identity of the peer?
