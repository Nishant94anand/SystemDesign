# Functional Requirements, User Journeys, Scope and Non-Goals

- **Status:** Completed
- **Phase:** 0 — Foundations and problem framing
- **Last updated:** 2026-10-13
- **Assessment:** 4/5 for this discussion
- **Scenario:** A customer-facing online Puja Samagri shop serving Gaya, Bihar
- **Related topics:** [NFRs and measurable targets](../../../progress/PROGRESS.md), [Syllabus V2](../../../curriculum/SYLLABUS_V2.md), [Daily Log](../../../progress/DAILY_LOG.md)

## Learning objectives

By the end of this chapter, you should be able to:

- Distinguish functional requirements (FRs), non-functional requirements (NFRs), assumptions, non-goals, and implementation decisions.
- Clarify user journeys and scope before choosing architecture.
- Capture exclusions without accidentally excluding required customer-facing behavior or integrations.
- Make requirements specific enough to test while keeping unresolved business questions visible.
- Explain why a design should follow requirements and workload rather than lead them.

## 1. Why start with requirements?

Imagine a local Gaya shop wants customers to order Puja Samagri online. Before choosing a database, cache, queue, or hosting topology, we need to agree what customers must be able to do, what the business will support, and what is deliberately excluded.

If one person assumes pickup is supported while another assumes home delivery only, the team can build a technically sound but incorrect system. Requirements reduce that risk by making the expected behavior explicit.

**System-design sequence:** clarify the problem → identify users and journeys → define scope and non-goals → define NFRs and measurable targets → estimate workload → design and validate.

The sequence is a guide, not a claim that requirements never change. New evidence can send us back to earlier steps.

## 2. Core distinctions

### Functional requirement (FR)

An FR describes a capability or behavior the system must provide. It should be possible to derive tests or acceptance criteria from it.

Examples:
- A customer can browse products and see prices and available quantities.
- A customer can add products to a cart and change quantities.
- A customer can submit an order using online payment or cash on delivery, subject to the business rules.

### Non-functional requirement (NFR)

An NFR describes a quality attribute or constraint on the system, ideally in measurable terms.

Examples:
- Product-list API latency shall meet an agreed percentile target under an agreed workload.
- Confirmed orders shall not be lost after a supported failure.
- The customer-facing application shall meet a defined monthly availability target.

“Fast,” “scalable,” and “highly available” are useful goals but are not complete, testable NFRs until their measurement boundary, workload, and target are specified.

### Scope and non-goals

Scope states what the release is responsible for. Non-goals state what it will not build or support.

An exclusion must be precise. “We will not implement the payment gateway” does **not** mean “the customer cannot pay online.” It means the application integrates with an existing provider rather than building payment processing itself.

### Assumption, business rule, and design decision

- **Assumption:** a provisional input used because evidence is not yet available. Example: 5,000 products as a planning baseline.
- **Business rule:** a policy the system must enforce. Example: orders may be placed only during shop operating hours.
- **Design decision:** a chosen implementation. Example: use a message queue for a particular workflow.

Keep these categories separate. An assumption should be validated; a business rule should be approved by the business; a design decision should be justified against alternatives.

## 3. Scenario scope

### In scope for V1

- One physical outlet in Gaya with one shared inventory.
- India-only service, initially Gaya and the shop's serviceable delivery areas.
- Responsive customer-facing website; no native mobile app in V1.
- Home delivery only; no pickup flow.
- Product catalogue, product details, prices and available quantities.
- Cart creation and quantity changes for both registered customers and guests.
- Address entry, saved addresses for registered customers, and serviceability validation.
- Checkout, order placement, online-payment integration, and cash on delivery.
- Submission of a placed order to the existing delivery system.
- Order confirmation, order details, and order history for registered customers.
- A guest order-lookup journey with suitable verification.

### Explicit non-goals for V1

- Admin login and admin interface.
- Building the customer-authentication implementation.
- Building a payment gateway.
- Building delivery logistics or live delivery tracking.
- Native mobile applications.
- Customer back-in-stock alerts.
- Multiple active carts per customer/session.
- Pickup orders.

**Scope nuance:** the admin interface is out of scope, but the customer-facing catalogue still needs product and inventory information from the existing management system. Payment-gateway implementation is out of scope, but payment integration and handling its outcome remain in scope. Delivery logistics are out of scope, but submitting an order to the existing delivery system remains in scope.

## 4. User journeys and functional requirements

### Journey A — Browse products

1. Customer opens the responsive website.
2. Customer browses the catalogue and product details.
3. Each product displays its price and exact currently available quantity.
4. Customer can choose to include unavailable products in the catalogue view.
5. Customer can choose a quantity up to the currently displayed available quantity.

The quantity displayed during browsing is an informational snapshot. The requirement does not imply that stock is guaranteed until order placement; final availability must be checked as part of checkout. The reservation and concurrency mechanism is a design-stage concern.

### Journey B — Manage cart

1. A registered customer or guest can add products to a cart.
2. They can remove products and change quantities.
3. The system supports one active cart per customer/session; multiple independent carts are not required.
4. Cart totals are shown for review.

### Journey C — Provide a delivery address

1. A registered customer can select a saved address or enter a new one.
2. A registered customer can save multiple addresses.
3. A guest enters an address manually.
4. The system validates whether the address is within the supported delivery area.
5. The solution may use an external geolocation/distance service; building such a service is not a requirement.

The exact serviceability rule (including radius or road-distance policy) remains a business question to resolve before implementation.

### Journey D — Checkout and place an order

1. Customer reviews cart contents and the final amount.
2. Customer selects or enters a delivery address.
3. The system validates delivery serviceability and current product availability.
4. Customer chooses online payment or cash on delivery.
5. Online payment uses an existing payment provider; the shop does not build a gateway.
6. The customer receives an order ID and confirmation when the order is successfully placed.
7. The order is submitted to the existing delivery system.

Order placement is restricted to shop operating hours. The website remains accessible outside those hours for browsing; it should communicate that ordering is closed and indicate the next opening time. Exact opening/closing times and any cutoff policy remain to be supplied by the business.

### Journey E — Review orders

- Registered customers can view order history and order details.
- Order details include items, quantities, amounts, payment status, receipt access/generation, and the order status that is available from the existing workflow.
- Guests can look up an order after appropriate verification.
- An order ID alone must not be treated as authorization to view private order information.
- Live delivery tracking is not required.

Receipt PDFs are expected to be generated on demand and downloaded rather than retained as stored files. This is a working requirement/assumption and should be validated against any audit or record-retention obligations.

## 5. User and workload assumptions captured during discussion

These are **illustrative planning assumptions, not measured business facts**:

| Input | Working assumption | Notes |
|---|---:|---|
| Monthly active users (MAU) | 10,000 | Unique active users over a 30-day period |
| Daily active users (DAU) | 1,000 | Unique active users per day |
| Normal peak concurrency | 200 | Simultaneously active users |
| Festival concurrency | 1,000 | 5× normal peak assumption |
| Normal daily successful orders | 50 | Derived from 1,000 DAU × 5% conversion |
| Festival daily orders | 500 | Stress/planning assumption: 10× normal orders |
| Shop order-taking window | 12 hours/day | Provisional assumption for order-rate calculation |
| Product catalogue | 5,000 products | Planning baseline; 10,000-product stress scenario considered |
| Product media | 3 images × ~300 KB | Approx. 900 KB media per product before variants/overhead |
| Product metadata | 2–5 KB/product | Logical data estimate, excluding index/engine overhead |
| Order record | ~10 KB/order | Approximate logical estimate including line items |
| Registered customers | 20,000 | Provisional total-account estimate; not inferred from MAU |
| Customer record | ~10 KB/customer | Approximate logical estimate including saved addresses |
| Order history | 2 years | Proposed online retention period, subject to policy/legal review |

### Read-load estimate discussed

The working peak-read scenario was:
- 200 normal peak concurrent users × 5 = 1,000 festival concurrent users.
- If each festival user generates 3 read requests per second, estimated read traffic is 1,000 × 3 = **3,000 read requests/second**.

This is arithmetic based on a deliberately aggressive behavior assumption, not an observed forecast. The 10× per-user activity multiplier needs validation; multiplying concurrent users by 5 and per-user activity by 10 yields 50× normal peak read traffic if the factors are independent.

### Order-rate estimate discussed

At 500 orders over a 12-hour operating window, the evenly distributed average is approximately 41.7 orders/hour, or 0.0116 orders/second. Actual peak order rate may be higher. Order count is not write RPS: one order can produce multiple writes and status updates.

### Storage estimate discussed

At approximately 5,000 products, 900 KB media/product gives about 4.5 GB of primary product media (rounded to ~5 GB), plus roughly 10–25 MB of logical product metadata. At 500 orders/day for two years, 365,000 orders × 10 KB/order gives about 3.65 GB of logical order data. These numbers are simplified and do not include all indexes, logs, backups, replicas, media variants, or other retained records. Therefore, 10 GB is a preliminary estimate, **not a proven four-year capacity guarantee**.

## 6. Common requirement mistakes

1. **Mixing a requirement with its solution:** “Use a queue” is not the same as “prevent overselling.”
2. **Treating an exclusion too broadly:** not building a payment gateway does not remove the payment integration requirement.
3. **Leaving adjectives unmeasurable:** “always available” needs a target, measurement window, and definition of availability.
4. **Treating estimates as facts:** user counts and festival multipliers must be labelled and validated.
5. **Confusing customer elapsed time with server latency:** a 5-minute checkout journey does not imply 5 minutes of server processing.
6. **Confusing orders/day with write RPS:** operation counts per order and arrival distribution matter.
7. **Ignoring authorization in guest lookup:** an order identifier by itself should not expose private order details.
8. **Calling a topic complete because a discussion happened:** completion needs a coherent artifact and an honest assessment of what remains unknown.

## 7. Open questions and assumptions to validate

- Exact shop opening/closing times and order cutoff behavior.
- Exact delivery-serviceability rule and how address distance is determined.
- Real catalogue size, media count/size, and expected product growth.
- Actual traffic profile, concurrent active behavior, and festival multipliers.
- Whether the assumed 5% conversion and 10× festival order volume match business expectations.
- Peak order arrival distribution and write operations per order.
- Online order-history retention versus legal/accounting record-retention requirements.
- Whether any receipt or audit artifact must be retained despite on-demand receipt generation.
- Required response-time targets, availability target, RPO/RTO, and how each metric will be measured.
- Exact guest-order verification policy.

Open questions do not invalidate the FR chapter; they must remain visible and be resolved before the relevant implementation or NFR commitment.

## 8. Principal-level review lens

Before architecture, ask:
- Which user journey is business-critical?
- What must always be true, especially around inventory, orders, and payments?
- Which scope exclusions could be misinterpreted by another team?
- Which estimates have the highest uncertainty, and what evidence would change them?
- Which requirements conflict, and who owns the decision?
- What can be simplified for V1 without compromising correctness or future evolution?

Do not select a database, cache, queue, or deployment topology solely from a single requirement. Requirements constrain the design; workload estimates quantify expected demand; component choices require comparing viable alternatives and their failure/operational costs.

## 9. Practice prompts

1. Explain the difference between “integrate an existing payment gateway” and “build a payment gateway.”
2. Is “use a message queue to prevent overselling” an FR, NFR, or design decision? Rewrite it as an outcome-based requirement.
3. Why do 10,000 MAU not directly tell us the required RPS?
4. Why is 500 orders/day not enough to calculate write RPS?
5. Name two assumptions in this scenario that could materially change the architecture.
6. A stakeholder asks for pickup orders one week before launch. What do you clarify before accepting the scope change?

## 10. Assessment and next step

**Discussion rating: 4/5.** The learner clarified the customer journeys and scope well, asked useful questions about workload assumptions, and demonstrated the core principle that requirements and estimates should precede technology selection. The main improvement area is keeping business requirements, assumptions, and design decisions separate—for example, treating queues and NoSQL as candidate solutions rather than NFRs.

**Not yet demonstrated:** an independently completed end-to-end requirements brief with every open question resolved, or a validated capacity forecast. The current artifact records provisional values transparently rather than claiming those validations are complete.

**Next step:** Continue the separate NFR topic. Finalize measurable targets and their measurement boundaries, then cover requirement conflicts/prioritization and the quantitative foundations in the syllabus.
