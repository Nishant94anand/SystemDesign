# Exercise: Payment Processing and Reconciliation

Design a payment workflow involving an external provider. Clarify state transitions, idempotency scope, ambiguous outcomes, audit, refunds and reconciliation. An HTTP timeout is not proof that payment failed.

**First task:** draw the state machine and define what happens when the provider response is lost.

**Later constraints:** concurrent retries; internal/provider records disagree; old unresolved payment.
