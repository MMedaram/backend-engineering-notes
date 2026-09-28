---
title: Functional vs Non-Functional Requirements
parent: System Design
nav_order: 1
---

# Functional vs Non-Functional Requirements

> Before choosing Java, Spring Boot, Kafka, Redis, databases, or microservices,
> first define **what the system must do** and **how well it must do it**.

This distinction is the foundation of high-level design (HLD). A technically
elegant architecture is still the wrong architecture if it does not satisfy the
real business need and operational constraints.

---

## 1. Functional requirements (FR)

Functional requirements describe the system’s **features and business behaviour**.

They answer:

> **What should the system do?**

### Examples: e-commerce system

- A customer can browse products.
- A customer can add products to a cart.
- A customer can place an order.
- The system can process a payment.
- The system sends an order-confirmation notification.
- An administrator can update product prices.

Functional requirements guide:

- APIs and user flows
- Database entities and domain models
- Business rules and validations
- Service boundaries
- Acceptance tests

---

## 2. Non-functional requirements (NFR)

Non-functional requirements describe the system’s **quality attributes and constraints**.

They answer:

> **How well must the system do it?**

### Examples: the same e-commerce system

- Product pages should respond within **200 ms** for 95% of requests.
- The checkout flow should provide **99.99% availability**.
- The system must support **10,000 requests per second** during a sale.
- Payment processing must not create duplicate charges.
- Customer payment data must be encrypted and access-controlled.
- Order records must be retained for seven years.
- Confirmed orders must survive an application-server failure.

Non-functional requirements influence the **architecture, data stores, deployment
model, resilience strategy, security controls, and operational cost.**

---

## 3. The simplest way to remember the difference

| Type                       | Meaning                                      | Example                                                                                  |
|----------------------------|----------------------------------------------|------------------------------------------------------------------------------------------|
| Functional requirement     | A capability the system provides             | “A customer can place an order.”                                                         |
| Non-functional requirement | The quality or constraint of that capability | “Order placement must complete within two seconds and must not create duplicate orders.” |

A feature without NFRs is incomplete. “Send a notification” sounds simple, but
the solution changes considerably when we add these constraints:

- Send within 30 seconds.
- Handle one million notifications per hour.
- Avoid duplicate customer-visible notifications.
- Retry temporary delivery failures three times.
- Support email, SMS, and push notifications.
- Retain an audit trail for 90 days.

---

## 4. How NFRs change the architecture

| Requirement                | Architecture implications                                   |
|----------------------------|-------------------------------------------------------------|
| Low latency                | Redis cache, database indexes, CDN, efficient queries       |
| High availability          | Multiple instances, load balancer, replicas, health checks  |
| High write volume          | Partitioning, queues, asynchronous processing, batching     |
| No duplicate payment       | Idempotency key, unique constraints, audit records          |
| Strong security            | OAuth2/JWT, encryption, access controls, secrets management |
| Reliable asynchronous work | Kafka/RabbitMQ, retries, DLQ, transactional outbox          |

The technology is not the starting point. The requirement is. For example, do
not say “we need Kafka” first; say “analytics can be processed asynchronously
without slowing down a customer redirect,” then evaluate Kafka or another
appropriate queue.

---

## 5. Make requirements measurable

Words such as *fast*, *scalable*, *secure*, and *reliable* are not enough.
Replace them with a measurable target or a concrete rule.

| Vague statement                            | Better requirement                                                                            |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
| The application should be fast.            | Product search should complete within 300 ms at p95 latency.                                  |
| The system should scale.                   | The system should support 50,000 requests/second during peak traffic.                         |
| The application should be secure.          | Only authenticated users can access orders; payment data must never be logged.                |
| The system should be reliable.             | Confirmed orders must not be lost if an application instance fails.                           |
| Notifications should be delivered quickly. | Push notifications should be submitted to the provider within 10 seconds for 95% of requests. |

### What does p95 mean?

**p95 latency** means 95 out of 100 requests complete within the target time.
It is more useful than average latency because a good average can hide a poor
experience for a meaningful group of users.

For example, an average latency of 100 ms could still hide some requests taking
many seconds. A p95 target exposes that slow tail.

---

## 6. A practical requirement-discovery process

Use this sequence in a system-design interview, design review, or project kickoff:

1. **Identify users and the core user journey.** Who uses the system and what outcome do they need?
2. **List the main functional requirements.** What actions, workflows, and outputs are essential?
3. **Identify scale.** How many users, requests per second, events, and bytes of data are expected?
4. **Establish NFRs.** What are the latency, availability, consistency, security, compliance, and cost expectations?
5. **Define exclusions.** What is explicitly out of scope for this version?
6. **Write assumptions.** State what you are assuming when information is missing.

This avoids premature design. It is better to ask “Can analytics be delayed by
one minute?” than to introduce a stream-processing platform without knowing if
asynchronous processing is acceptable.

---

## 7. Worked example: URL shortener

### Functional requirements

- A user can submit a long URL and receive a short URL.
- A user can open a short URL and be redirected to the original URL.
- A user may create a custom alias.
- The system records click analytics.
- A user can optionally set an expiry time for a link.

### Non-functional requirements

- Redirects should complete in under 100 ms for most requests.
- The system must handle 100 million redirects per day.
- Short URLs must be globally unique.
- Redirects should remain available if an application server fails.
- Click analytics may be eventually consistent.
- Expired or deleted links must not redirect users.
- The system should prevent malicious URL abuse and excessive API requests.

### Design insight

Redirect correctness and low latency matter to the customer. Click analytics is
valuable but can tolerate a small delay. This suggests a fast lookup path—often
with caching—for redirects, and asynchronous event processing for analytics.

---

## 8. Worked example: notification service

### Functional requirements

1. **Order-status notifications**  
   Notify customers when an order is placed, shipped, delivered, cancelled, or refunded.

2. **Back-in-stock and product notifications**  
   Allow customers to subscribe to an out-of-stock product and notify them when it becomes available. Optionally allow opt-in notifications for similar products or new-product releases.

3. **Channel preferences**  
   Allow customers to choose email, SMS, push notification, WhatsApp, or other supported channels.

4. **Notification preferences**  
   Allow customers to enable or disable categories such as order updates, promotions, back-in-stock alerts, and product recommendations.

5. **Delivery tracking**  
   Record each notification as requested, queued, sent to provider, delivered, failed, retried, or permanently failed.

6. **Retry handling**  
   Retry temporary failures, such as provider timeouts, with a controlled retry policy.

7. **Templates and personalization**  
   Support reusable templates with variables such as customer name, order ID, product name, tracking link, and delivery date.

### Non-functional requirements

1. **Throughput**  
   Handle one million notification requests per hour and tolerate bursts during major sales or campaigns.

2. **Delivery latency**  
   For transactional notifications—such as order placed or shipped—submit the notification to the selected provider within 30 seconds for 95% of requests.

3. **Duplicate prevention**  
   Do not intentionally send duplicate notifications for the same event, customer, notification type, and channel.

4. **Reliability**  
   Process every accepted notification request at least once unless it is cancelled or rejected.

5. **Audit retention**  
   Retain notification audit records for 90 days, including event, customer, channel, template version, provider, timestamps, status, and failure reason.

6. **Availability**  
   Accept notification requests even when an email, SMS, or push provider is temporarily unavailable.

7. **Security and privacy**  
   Protect customer contact details, restrict access to notification history, and avoid logging sensitive content or credentials.

8. **Observability**  
   Expose metrics for volume, latency, retries, failures, provider errors, and duplicate-prevention events.

### Assumptions

- Order, inventory, and product systems publish events when relevant business state changes.
- Email, SMS, and push providers are external systems and can fail or rate-limit requests.
- A customer may have multiple enabled channels and category-specific preferences.
- Promotional notifications can tolerate more delay than order-status notifications.

### Out of scope

- Building the underlying email, SMS, or push-delivery networks; the service integrates with external providers.
- Advanced recommendation logic for deciding which similar products a customer should receive.

---

## 9. Important distributed-systems concept: at-least-once and duplicates

Two requirements often appear together:

- “Never send duplicate notifications.”
- “Every accepted notification must be processed at least once.”

They require careful wording.

### At-least-once processing

At-least-once processing means the system retries work so that accepted work is
not silently lost. A retry can produce more than one delivery attempt.

### Why absolute exactly-once delivery is difficult

After an external SMS or email provider receives a request, a network timeout
may leave the notification service uncertain whether the provider accepted it.
Retrying can cause a duplicate; not retrying can lose the notification. The
notification service cannot always prove the final customer-visible outcome.

### Practical requirement

> Process accepted notification requests at least once and prevent duplicate
> sends using a deduplication key based on `eventId + userId + notificationType + channel`.

This is commonly described as **effectively-once customer impact**. Internally,
the system may retry; externally, it uses idempotency and deduplication so the
customer should not receive a duplicate notification.

---

## 10. Transactional vs promotional notifications

This distinction changes the system design.

| Type          | Examples                                                | Typical priority | Customer opt-out                                           |
|---------------|---------------------------------------------------------|------------------|------------------------------------------------------------|
| Transactional | Order placed, shipped, delivered, password reset        | High             | Usually limited; some messages are operationally essential |
| Promotional   | Similar products, product launches, marketing campaigns | Lower            | Required and must be respected                             |

Later, this distinction will influence priority queues, retry policy, rate
limits, templates, consent handling, and provider selection.

---

## 11. Common mistakes

- Starting with technologies: “We will use Kafka and microservices” before requirements are understood.
- Treating “fast,” “secure,” “scalable,” or “reliable” as complete requirements.
- Forgetting failure cases: retries, duplicate requests, partial failure, and data loss.
- Mixing a business requirement with an implementation choice.
- Assuming every feature needs strong consistency or a microservice.
- Missing explicit out-of-scope items, which leads to uncontrolled scope growth.

---

## 12. Requirement checklist

Before beginning architecture, verify that you can answer these questions:

- [ ] Who are the users and what is their core journey?
- [ ] What are the essential functional requirements?
- [ ] Which requirements are explicitly out of scope?
- [ ] What scale must the system support now and in the future?
- [ ] What latency and availability targets matter?
- [ ] Which data must be strongly consistent, and which can be eventually consistent?
- [ ] What data is sensitive and how must it be protected?
- [ ] What can fail, and what should happen when it does?
- [ ] Which assumptions still need validation?

---

## Interview and design-review summary

When starting an HLD discussion:

1. State the core functional requirements.
2. Clarify the highest-impact NFRs.
3. State assumptions and out-of-scope items.
4. Only then propose the architecture.

> **Functional requirements define the destination. Non-functional requirements define the engineering constraints of reaching it.**

---

## Practice exercise

Write a short requirement brief for a notification service that supports email,
SMS, and push notifications. Include:

1. Five functional requirements
2. Five non-functional requirements
3. Three assumptions
4. Two explicit out-of-scope items

Then classify each notification as transactional or promotional and explain what
changes in its priority, retry policy, and opt-out rules.

