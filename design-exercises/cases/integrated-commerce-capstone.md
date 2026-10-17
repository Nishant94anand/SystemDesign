# Capstone — Festival-Scale Commerce, Inventory and Payments

**Phase:** 3 — Applied system design, after foundational component labs. **Type:** solution-free first-attempt design with successive constraints.

## Problem

A regional Puja Samagri retailer based in Gaya is expanding its online ordering to multiple fulfillment locations. Orders spike sharply before major festivals. Customers need accurate product availability, reliable checkout and payment outcomes, order-status access, and operational communication. The business cannot tolerate confirmed orders silently disappearing or the same scarce inventory being sold twice.

First derive the functional/non-functional requirements rather than assuming every behavior is in scope. Distinguish requirements, assumptions and implementation decisions. Existing [Functional Requirements](../../concepts/foundations/functional-requirements/) notes are a **learning reference**, not binding capstone requirements: challenge anything that does not fit this new multi-location scenario.

## First attempt — no model architecture provided

1. Define actor journeys, scope/non-goals, business constraints and measurable NFRs.
2. Estimate normal/festival concurrency, read/write operations, inventory hot spots, retention, media/storage, network and headroom. Show units and uncertainty.
3. State invariants (inventory, order confirmation, payment/refund state) and API/workflow contracts.
4. Choose a simple viable data model, transaction ownership and architecture; explain what must be synchronous vs asynchronous.
5. Trace browse → cart → reserve/checkout → payment outcome → fulfillment with normal and failed request paths.
6. Explain authorization, privacy and fraud/abuse boundaries; propose SLIs, alerts and an operational response plan.
7. Document alternatives, costs, assumptions that could invalidate the choice, and a migration path.

Use the [exercise template](../TEMPLATE.md). Create original Mermaid sequence/flow/state diagrams only after thinking through the workflow, not as a ready-made solution.

## Progressive constraints — reveal one at a time

1. Chhath festival promotion drives a burst 20x above your expected checkout arrival rate; three products become extremely hot.
2. A client retries an order after a timeout and the payment provider sends duplicate or delayed webhooks.
3. Reservation expiry races with checkout completion and a worker crashes after committing order state but before emitting an event.
4. One fulfillment location goes offline while customers still see aggregated available quantity. Specify the consistency guarantee and recovery behavior.
5. Product/event ingestion and order-history reads create capacity pressure. Explore cache policy, hot-key control, backpressure, telemetry and cost.
6. The business must roll out a new reservation implementation without losing in-flight orders. Produce an expand-contract/migration-and-reconciliation plan.
7. A security incident exposes another customer's order metadata. Provide containment, evidence, remediation and verification steps.

## Required completion evidence

Requirements brief; capacity math and assumptions; state invariants; data model and access paths; diagrammed happy/failure paths; idempotency and recovery policy; actionable SLIs and threat model; alternatives and operational/cost trade-offs; an updated design after new constraints. Separate **interview performance** from **production/Principal evidence** and record unresolved risks. Do not write a model solution in this case prompt.
