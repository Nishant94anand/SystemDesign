# Symmetric and Asymmetric Encryption

- **Status:** Completed Ad-hoc introductory session
- **Type:** Cross-cutting security concept
- **Last updated:** 2026-10-18
- **Scope:** Core mental model, algorithm roles, HTTPS/TLS connection to system design
- **Assessment note:** The session explanation was completed at the learner's request. The learner acknowledged it, but the application checkpoint was not attempted. The progress rating (3/5) measures discussion coverage, not independent mastery.

## Learning objectives

By the end of a revision, you should be able to:
- Explain plaintext, ciphertext, encryption, decryption and keys.
- Compare symmetric encryption with asymmetric cryptographic mechanisms.
- Distinguish public-key encryption, key agreement and digital signatures.
- Explain at a high level why modern TLS combines asymmetric mechanisms with symmetric traffic encryption.
- Identify key-distribution, identity-verification, key-compromise and protocol-design risks.

## 1. Why encryption exists

When a client sends sensitive data over a network, an attacker may be able to observe a copy of the traffic. Encryption aims to protect the confidentiality of data even when ciphertext is exposed.

- **Plaintext:** original data in readable form.
- **Ciphertext:** encrypted output.
- **Encryption:** transforms plaintext using an algorithm and key.
- **Decryption:** recovers the plaintext using the required key or key material.

Conceptually:

\[
C = E_K(P), \qquad P = D_K(C)
\]

Here, \(P\) is plaintext, \(C\) is ciphertext and \(K\) denotes the relevant key material. This notation is a simplified model; real protocols also define encodings, modes, nonces, authentication and error handling.

Encryption does not automatically authenticate the sender, prove the real-world identity of a server, prevent replay, or make an application secure by itself. Those properties depend on the scheme and surrounding protocol.

## 2. Symmetric encryption

Symmetric encryption uses the same shared secret key for encryption and decryption.

\[
C = E_K(P), \qquad P = D_K(C)
\]

**Examples:** AES is a widely used symmetric block cipher. In practice, data is normally protected using an appropriate mode/construction, often an authenticated-encryption scheme such as AES-GCM or ChaCha20-Poly1305.

### Why it is useful

- Efficient for large volumes of data.
- Suitable for ongoing application traffic, files and stored data.
- The same secret must be protected by every party or component that needs to use it.

### Main risks and trade-offs

- The parties need a secure way to establish or provision the shared secret.
- A key compromise can expose data protected by that key, subject to the scheme and key/nonce usage.
- Key reuse, nonce misuse, poor randomness, insecure storage and weak rotation practices can undermine security.
- A bare encryption mode that lacks authentication may allow undetected tampering.

**Intuition:** two people who already share a secret key can use it to protect and recover a message. The analogy helps explain shared-key distribution, but real cryptographic security depends on standardized algorithms and correct protocol use, not on secrecy of the algorithm.

## 3. Asymmetric cryptography

Asymmetric cryptography uses a mathematically related public/private key pair. The public key can be distributed; the private key must remain protected.

The term covers several distinct operations:

- **Public-key encryption:** in suitable schemes, a sender encrypts for a recipient using the recipient's public key, and the corresponding private key is used to decrypt.
- **Key agreement:** parties derive shared secret material using a protocol such as ECDH. This is not the same operation as encrypting the entire application message with a public key.
- **Digital signatures:** a signer uses a private key to produce a signature; others use the corresponding public key to verify it. A signature is not simply “encryption with the private key.”

**Examples:** RSA can be used in standardized signature and encryption schemes; ECDH is used for key agreement; ECDSA is a signature algorithm. Do not assume every asymmetric algorithm supports every operation.

### Benefits

- A public key can be distributed without revealing the private key.
- Key agreement can establish shared secrets without sending those secrets directly over the network.
- Digital signatures can support authentication and integrity checks when the public key is reliably bound to the claimed identity.

### Trade-offs and risks

- Asymmetric operations are generally more computationally expensive than symmetric bulk encryption.
- A public key by itself does not prove who owns it. Certificates, trust anchors, hostname checks, and protocol validation help bind keys to identities.
- If an attacker substitutes an unverified public key, a man-in-the-middle attack may be possible.
- Private-key storage, rotation, revocation, access control and recovery remain operational responsibilities.

## 4. Comparison

| Property | Symmetric encryption | Asymmetric cryptography |
|---|---|---|
| Key model | One shared secret for a communicating group/context | Public/private key pair |
| Common role | Bulk data encryption | Key agreement, signatures, and selected encryption schemes |
| Performance | Generally efficient for bulk data | Generally more expensive per operation |
| Distribution challenge | Share/provision the secret securely | Distribute public keys and establish trust in their identity |
| Compromise concern | Exposure of a shared key affects data protected by it | Private-key compromise affects the operations and identities that depend on it |
| Examples | AES-based authenticated encryption | RSA, ECDH, ECDSA (different purposes) |

Neither category is universally “more secure.” Security depends on the algorithm, parameters, protocol, implementation, key management and threat model.

## 5. How modern HTTPS/TLS uses both

A useful high-level model for a modern TLS 1.3 connection is:

1. The client connects to the server and negotiates protocol parameters.
2. The server presents a certificate chain. The client validates the chain and the requested hostname, along with applicable policy.
3. The handshake uses key agreement (commonly ephemeral Diffie–Hellman such as ECDHE) to establish shared secret material. Authentication uses signatures and the certificate's trust binding where applicable.
4. Both sides derive symmetric traffic keys from the handshake secrets using the TLS key schedule.
5. Application data is protected using negotiated symmetric authenticated-encryption algorithms, such as AES-GCM or ChaCha20-Poly1305.

The practical point is that asymmetric cryptographic mechanisms help establish and authenticate a secure connection; symmetric authenticated encryption efficiently protects the ongoing data stream.

**TLS 1.3 nuance:** RSA key transport was removed. An RSA certificate key may still be used for signatures, but that is distinct from key agreement. The actual handshake details depend on negotiated parameters; consult RFC 8446 for exact behavior.

See [the companion Mermaid flow](encryption-flows.mmd).

## 6. Failure modes and production considerations

- **Unverified public key:** the client encrypts to or establishes a secret with an attacker's key. Mitigation requires authenticated key binding and correct validation, not merely “using a public key.”
- **Leaked symmetric key:** attackers may decrypt captured ciphertext protected by that key. Scope, retention, rotation and forward secrecy affect impact.
- **Nonce misuse:** for constructions such as AES-GCM, violating nonce requirements can severely damage security. Follow the algorithm/protocol's exact requirements.
- **Encryption without integrity:** a message may be confidential yet tampered with undetected. Prefer established authenticated-encryption constructions or protocols.
- **Hard-coded or over-shared secrets:** compromise creates a large blast radius. Use managed key storage, least privilege, rotation and clear ownership.
- **False confidence from encryption:** application authorization, endpoint security, data retention, logging, backups and abuse controls still matter.

## 7. Key-management lifecycle

A production design should explicitly consider:
- Key generation and entropy.
- Secure storage and access boundaries.
- Distribution or agreement protocol.
- Intended scope and separation between keys.
- Rotation, expiry, revocation and replacement.
- Backup/recovery and disaster scenarios.
- Auditability, incident response and compromise blast radius.
- Data re-encryption or retention implications when keys change.

Do not design a bespoke cryptographic protocol. Prefer well-reviewed, standard protocols and maintained libraries, and follow platform-specific guidance.

## 8. Pairwise key growth: a scaling intuition

If every pair in a group of \(n\) participants had a distinct symmetric key, the number of pairwise keys would be:

\[
\frac{n(n-1)}{2}
\]

For one million participants this is approximately 500 billion possible pairs. This illustrates why pairwise key administration can become awkward. It does **not** mean every application needs a key for every pair; session keys, key-management services, group-key schemes and other architectures change the model.

## 9. System-design application

When reviewing a system that handles sensitive traffic, ask:

1. What exactly must be confidential, and against which attacker?
2. How are endpoints authenticated and keys established?
3. Which data is encrypted in transit and at rest?
4. Which protocol/library and authenticated-encryption construction is used?
5. Who owns key storage, access, rotation and incident response?
6. What is the blast radius of one compromised key?
7. Are secrets exposed in logs, traces, crash dumps, backups or support tooling?
8. How are configuration and protocol changes rolled out compatibly?

For a chat or messaging design, do not equate TLS with end-to-end encryption. TLS protects traffic between TLS endpoints; end-to-end encryption has a different key-ownership and threat model, especially when a server should not be able to read message contents.

No standalone design exercise was added for this short cross-cutting Ad-hoc session. Reinforce the concept during the core networking/TLS topic and the chat/messaging design lab.

## 10. Practice prompts

Try these before rereading the chapter:
1. Why is symmetric encryption preferred for bulk data after a secure session is established?
2. What is the difference between public-key encryption, ECDH key agreement and a digital signature?
3. How can a public key be intercepted or substituted even though it is not secret?
4. Why does encryption alone not guarantee message integrity or peer identity?
5. Why is TLS not the same as end-to-end encryption?

## Quick revision

- **Symmetric:** same shared secret key; generally efficient for bulk data.
- **Asymmetric:** public/private pair; different algorithms serve key agreement, signatures or selected encryption tasks.
- **Hybrid TLS:** authenticate and establish secrets through handshake mechanisms; derive symmetric traffic keys; protect application traffic with authenticated encryption.
- **Trust matters:** a public key must be bound to the intended identity.
- **Operations matter:** generation, storage, access, rotation and compromise recovery are part of the design.
- **Status:** Ad-hoc artifact completed; independent application and mastery remain unassessed.

## References

- [NIST FIPS 197 — Advanced Encryption Standard (AES)](https://csrc.nist.gov/pubs/fips/197/final)
- [RFC 8446 — The Transport Layer Security (TLS) Protocol Version 1.3](https://www.rfc-editor.org/rfc/rfc8446)
- [NIST SP 800-57 Part 1 Rev. 5 — Recommendation for Key Management](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final)
