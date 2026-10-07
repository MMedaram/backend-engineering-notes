---
title: Assumptions and Constraints
parent: System Design
nav_order: 2
---

# Assumptions and Constraints

> A senior engineer does not hide uncertainty. They make it visible, assess the
> risk, validate the important parts, and design with clear boundaries.

No system design starts with complete information. At the beginning of a
project, traffic numbers, provider capabilities, business rules, budget limits,
and future changes may all be uncertain. Assumptions and constraints make those
unknowns explicit before architecture decisions become expensive to change.

---

## 1. Why this matters

Consider this request:

> Build a Notification Service that can send email, SMS, and push messages.

It is not yet possible to responsibly choose Kafka, Redis, a database, or a
microservice architecture. First, clarify questions such as:

- How many notifications per second must be handled?
- Must a notification reach the customer immediately?
- Can promotional messages be delayed?
- Which external providers are approved?
- Is customer data allowed to leave a specific region?
- Does the order system already publish events?
- How long must audit records be retained?
- Can the team operate Kafka and its on-call responsibilities?

When an answer is unknown, either validate it or state it as an assumption and
continue with a clear design boundary.

---

## 2. Requirement vs assumption vs constraint

| Term                       | Meaning                                                   | Can it change?                             | Example                                                |
|----------------------------|-----------------------------------------------------------|--------------------------------------------|--------------------------------------------------------|
| Functional requirement     | A capability the system must provide                      | Usually stable for the scope               | “Send an order-shipped notification.”                  |
| Non-functional requirement | A quality target or operational rule                      | Can evolve                                 | “Submit 95% of order notifications within 30 seconds.” |
| Assumption                 | Something unconfirmed that is temporarily treated as true | Yes; it must be validated                  | “The order system can publish reliable order events.”  |
| Constraint                 | A mandatory boundary the solution must obey               | Usually no, unless the business changes it | “Customer PII must remain in India.”                   |

The core difference is simple:

- **Assumption:** “We believe this is true.”
- **Constraint:** “We must operate within this boundary.”

---

## 3. Assumptions

An assumption fills a knowledge gap so design work can proceed.

Example:

> Assume that the email provider supports an idempotency key.

This is not a fact until validated. If it is false, duplicate prevention may
need to happen entirely in the application database before calling the provider.

### Good assumptions

Good assumptions are:

- Explicit
- Specific
- Testable
- Relevant to a design decision
- Revisited before implementation or release

### Weak assumptions

Weak assumptions are:

- Hidden
- Vague
- Never validated
- Used only to justify a preferred technology

Bad:

> The system will not have much traffic.

Better:

> Assume peak traffic is 500 notification requests per second until product
> confirms the campaign forecast. Size the queue and workers for 2,000 requests
> per second to allow four times headroom.

---

## 4. Types of assumptions

### 4.1 Business assumptions

These concern users, product behaviour, and business priorities.

- Most users have one preferred notification channel.
- Order-status notifications are more important than marketing messages.
- Promotions can be delayed by several minutes during peak load.
- The product is expected to grow 30% annually.
- Customers will tolerate a limited number of reminders.

### 4.2 Technical assumptions

These concern integrations, infrastructure, and platform capabilities.

- The Order Service publishes an `OrderShipped` event.
- The SMS provider supports delivery-status callbacks.
- The existing identity provider issues JWTs.
- Kubernetes is available as the deployment platform.
- Redis is available as a managed service.

### 4.3 Data assumptions

These concern volume, ownership, retention, and consistency.

- An order event has a globally unique event ID.
- Notification history is retained for 90 days.
- A user's notification preferences change infrequently.
- Eventual consistency is acceptable for promotional subscriptions.
- Payment events require stronger auditability than product-release events.

### 4.4 Operational assumptions

These concern people, ownership, support, and delivery capability.

- The team can support on-call alerts for this service.
- A platform team manages Kafka clusters.
- The service can use the existing CI/CD pipeline.
- Operations can rotate provider credentials safely.
- The team has enough knowledge to operate a multi-region deployment.

> A technically possible design can still be a poor choice if the organisation
> cannot operate it safely.

---

## 5. Constraints

Constraints narrow the set of acceptable designs. They are not preferences.

### 5.1 Business constraints

- Launch must happen in three months.
- The solution must stay within a fixed budget.
- Existing customers cannot be forced to reinstall the mobile app.
- The service must support a specific market before global expansion.

### 5.2 Technical constraints

- Must integrate with an existing Oracle database or legacy system.
- Must use the company-approved identity provider.
- Must deploy on the existing Kubernetes platform.
- Cannot modify a vendor-controlled upstream system.
- Must support existing REST APIs while newer APIs are introduced.

### 5.3 Legal, compliance, and security constraints

- PII must remain in a specific geographic region.
- Audit data must be retained for a specified period.
- Data deletion requests must be supported.
- Only approved providers can process SMS or email data.
- Payment-card data must not pass through the Notification Service.
- Encryption in transit and at rest is mandatory.

### 5.4 Operational constraints

- The service must be supported by a small team.
- On-call engineers cannot manually reconcile millions of failures.
- Production releases may happen only in an approved maintenance window.
- Existing monitoring and incident-management tooling must be used.

### 5.5 Cost constraints

- Monthly provider spend cannot exceed a budget.
- High-cost SMS should be reserved for high-priority messages.
- Infrastructure should scale down outside peak periods.
- Data retention must balance audit needs against storage cost.

---

## 6. Constraints drive trade-offs

| Constraint                    | Likely design effect                                                              |
|-------------------------------|-----------------------------------------------------------------------------------|
| Launch in three months        | Prefer a modular monolith or managed services over a large microservice platform. |
| Must use an existing database | Design integration boundaries and avoid an unnecessary migration in version one.  |
| PII must stay in India        | Choose region-compliant providers and restrict cross-region replication.          |
| Small operations team         | Prefer managed services or reduce infrastructure complexity.                      |
| Strict audit retention        | Store immutable audit records and define archival/purge jobs.                     |
| SMS budget is limited         | Use channel-priority rules and fall back to email/push where appropriate.         |

The right architecture is not the newest one. It is the one that satisfies the
requirements **within the confirmed constraints**.

---

## 7. Identify risky assumptions first

Not all assumptions deserve equal attention. Assess each one using:

- **Impact:** How badly does the design suffer if this is false?
- **Uncertainty:** How likely is it that the assumption is wrong?

| Impact | Uncertainty | Action                                             |
|--------|-------------|----------------------------------------------------|
| High   | High        | Validate immediately before finalising the design. |
| High   | Low         | Document it and create a fallback plan.            |
| Low    | High        | Track it; do not over-engineer around it.          |
| Low    | Low         | Record briefly and proceed.                        |

### Example risk classification

| Assumption                                         | Impact if false                            | Uncertainty | Action                                                        |
|----------------------------------------------------|--------------------------------------------|-------------|---------------------------------------------------------------|
| The Order Service publishes reliable events.       | High: notifications may be missed.         | Medium      | Validate the event contract; consider a transactional outbox. |
| The SMS provider supports idempotency keys.        | High: duplicates may reach users.          | Medium      | Review provider documentation; add internal deduplication.    |
| Promotional messages can be delayed.               | Medium: lower user engagement.             | Low         | Use a lower-priority queue.                                   |
| Each user has one preferred channel.               | Low: more preference logic is needed.      | Medium      | Model preferences per category and channel.                   |
| One million notifications/hour is the actual peak. | High: workers/providers may be overloaded. | High        | Validate with product forecast; design burst headroom.        |

High-impact, high-uncertainty assumptions should be resolved before the design
becomes expensive to change.

---

## 8. Assumption log

Keep a lightweight assumption log in the design document or an Architecture
Decision Record (ADR).

| ID   | Assumption                                | Why it matters                | Risk if false                                  | Owner          | Validation plan                 | Status |
|------|-------------------------------------------|-------------------------------|------------------------------------------------|----------------|---------------------------------|--------|
| A-01 | Order events have unique event IDs.       | Enables idempotency.          | Duplicate or missed notifications.             | Order team     | Review event schema.            | Open   |
| A-02 | SMS provider supports 1,000 requests/sec. | Determines throughput design. | Queue backlog and delay.                       | Provider owner | Confirm contract and load test. | Open   |
| A-03 | Promotions may be delayed by 10 minutes.  | Enables priority queues.      | More expensive infrastructure may be required. | Product        | Confirm with product owner.     | Open   |

---

## 9. Design for changing assumptions

A good design does not blindly trust assumptions. It contains reasonable escape
routes.

### Example: provider throughput

Assumption:

> The SMS provider accepts 1,000 requests per second.

Risk-aware response:

- Place notifications into a queue before calling the provider.
- Apply rate limiting in the provider adapter.
- Track backlog and delivery latency.
- Keep throughput limits configurable.
- Keep provider integration behind an adapter interface.
- Add a secondary provider only when the business value justifies its cost and complexity.

If the provider limit later decreases, configuration and worker scaling can
change without redesigning the entire system.

### Example: event availability

Assumption:

> The Order Service reliably emits order-status events.

Risk-aware response:

- Use a versioned event contract.
- Include a unique event ID and event timestamp.
- Use an outbox pattern when the Order Service owns the database transaction.
- Track event-consumption lag and failed messages.
- Define reconciliation for critical notifications if required.

---

## 10. How to state assumptions in an HLD interview

Do not wait for perfect information. State assumptions early and invite
correction.

> I will assume this is a notification platform for order updates and
> promotional alerts. Transactional notifications are higher priority, and the
> service supports one million requests per hour. I assume order events contain
> a unique event ID, promotional notifications can be delayed up to ten minutes,
> and the service integrates with approved external providers. Please let me
> know if any of these assumptions should change.

This shows structured thinking and prevents designing against an imaginary
problem.

Avoid turning assumptions into excuses.

Weak:

> I assume traffic is low, so one application instance is enough.

Better:

> Traffic is not yet confirmed. For version one, use stateless instances behind
> a load balancer so the service can scale horizontally when measured load
> requires it.

---

## 11. Common mistakes

- Treating an assumption as a confirmed fact.
- Hiding assumptions inside an architecture diagram.
- Confusing a preference with a constraint.
- Ignoring organisational constraints, such as team skills or on-call capacity.
- Validating high-risk assumptions too late.
- Over-engineering for every uncertainty.
- Never revisiting initial traffic forecasts, provider limits, or product priorities.

---

## 12. Practice exercise — answered

The following is a reference answer for the Notification Service practice
exercise. It is not the only correct answer; the important part is making risk
and validation explicit.

### 12.1 Business assumptions

| ID   | Assumption                                                        | Why it matters                                            | If false                                         | Validation                                            | Risk response                                      |
|------|-------------------------------------------------------------------|-----------------------------------------------------------|--------------------------------------------------|-------------------------------------------------------|----------------------------------------------------|
| B-01 | Transactional notifications have higher priority than promotions. | Determines queue priority and retry policy.               | Customers may miss time-sensitive order updates. | Confirm with product and support teams.               | Separate transactional and promotional queues.     |
| B-02 | Promotions may be delayed by up to 10 minutes.                    | Allows capacity to be reserved for transactional traffic. | More real-time capacity is required.             | Confirm campaign requirements with product.           | Make queue priority and delay target configurable. |
| B-03 | Users accept at most three promotional notifications per week.    | Prevents notification fatigue and controls cost.          | Opt-outs and provider cost may increase.         | Confirm marketing policy and analyse engagement data. | Add frequency caps per user/category.              |

### 12.2 Technical assumptions

| ID   | Assumption                                                        | Why it matters                                       | If false                                                           | Validation                                       | Risk response                                                              |
|------|-------------------------------------------------------------------|------------------------------------------------------|--------------------------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------|
| T-01 | Upstream systems publish events with unique `eventId` values.     | Enables idempotency and tracing.                     | Duplicate or missing notifications become difficult to handle.     | Review event schema and producer behaviour.      | Store a deduplication record keyed by event/channel/user.                  |
| T-02 | SMS/email/push providers expose rate limits and status callbacks. | Determines throttling and delivery tracking.         | The service cannot safely control throughput or know final status. | Review provider contracts and test sandbox APIs. | Use provider adapters, rate limits, and provider-specific fallback states. |
| T-03 | A managed message broker is available.                            | Supports decoupled, retryable asynchronous delivery. | Direct synchronous calls could lose work during provider outages.  | Confirm with platform team.                      | Start with a supported queue; do not self-manage a broker unnecessarily.   |

### 12.3 Data assumptions

| ID   | Assumption                                                         | Why it matters                        | If false                                                       | Validation                       | Risk response                                                      |
|------|--------------------------------------------------------------------|---------------------------------------|----------------------------------------------------------------|----------------------------------|--------------------------------------------------------------------|
| D-01 | Notification audit records are retained for 90 days.               | Defines storage and cleanup strategy. | Storage cost or compliance behaviour changes.                  | Confirm legal/compliance policy. | Implement configurable retention and scheduled purge/archive jobs. |
| D-02 | A user may have preferences per channel and notification category. | Defines the preference data model.    | A simple single-channel preference model becomes insufficient. | Confirm product UX requirements. | Model preferences as user + category + channel records.            |

### 12.4 Operational assumptions

| ID   | Assumption                                                                                 | Why it matters                                                  | If false                                                | Validation                           | Risk response                                                       |
|------|--------------------------------------------------------------------------------------------|-----------------------------------------------------------------|---------------------------------------------------------|--------------------------------------|---------------------------------------------------------------------|
| O-01 | A platform team manages broker availability and upgrades.                                  | Determines whether the application team can rely on the broker. | The service team inherits substantial operational work. | Confirm ownership and support model. | Prefer a managed/approved broker and document operational runbooks. |
| O-02 | The team has on-call monitoring for provider failure, queue backlog, and delivery latency. | Failures need timely response.                                  | Notifications can remain delayed or failed unnoticed.   | Confirm alert routing and ownership. | Add dashboards, alerts, runbooks, and escalation paths.             |

### 12.5 Hard constraints

| ID   | Constraint                                                            | Design impact                                                                                      |
|------|-----------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| C-01 | The service must handle one million notification requests per hour.   | Use asynchronous queueing, horizontally scalable workers, provider throttling, and burst headroom. |
| C-02 | Customer contact details and notification content are sensitive data. | Encrypt data, restrict access, mask logs, and use approved providers.                              |
| C-03 | Audit history must be retained for 90 days.                           | Persist delivery attempts/status and implement retention/archival controls.                        |

---

## 13. Final checklist

Before finalising a high-level design, ask:

- [ ] What information am I assuming because it is not confirmed?
- [ ] Which assumptions have high impact if false?
- [ ] Who can validate each high-risk assumption?
- [ ] What constraints are mandatory?
- [ ] Which constraints are technical, legal, cost-related, or operational?
- [ ] Does the proposed architecture respect every confirmed constraint?
- [ ] Can the design evolve safely if a key assumption changes?
- [ ] Are assumptions, constraints, risks, and decisions documented?

---

## Interview and design-review summary

When starting an HLD discussion:

1. State the core functional requirements.
2. Clarify the highest-impact non-functional requirements.
3. State assumptions and out-of-scope items.
4. Identify mandatory constraints and risky unknowns.
5. Only then propose the architecture.

> **Assumptions make uncertainty visible. Constraints define the boundaries of a
> valid solution.**

