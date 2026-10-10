---
title: Consistency and Durability
parent: System Design
nav_order: 7
---

# Consistency and Durability

> **Consistency** describes what readers can observe after data changes.
> **Durability** describes whether acknowledged data survives failures.

They are different guarantees. A write may be durable while a read from a lagging
replica temporarily returns an older value. Conversely, a value may be visible
from one application instance but not durably saved, so it disappears after a
restart.

---

## Learning outcomes

After this topic, you should be able to:

- Explain consistency and durability separately.
- Recognize strong, eventual, read-your-writes, monotonic-read, and causal guarantees.
- Choose consistency requirements based on the consequence of stale or conflicting data.
- Explain replication lag and stale reads.
- Define what “accepted” or “committed” means to a caller.
- Understand durability in databases and message brokers.
- Explain the dual-write problem and the transactional outbox pattern.
- Apply these concepts to a Notification Service.

---

## 1. Consistency

Consistency describes the rules that determine what a read can observe after a
write. The guarantee may depend on where the data is read, how recent the write
was, whether a replica is used, and whether a cache is involved.

Suppose a user changes their notification preference from SMS to email:

```text
Write: preferred channel = EMAIL
Read immediately afterward: preferred channel = SMS
```

If a notification worker reads the old preference, it may send an unexpected
SMS. The design must define how fresh that read needs to be.

### Common consistency guarantees

| Model                    | What the caller may observe                                                                                     | Example use                                                |
|--------------------------|-----------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| **Strong consistency**   | Once a write is confirmed, subsequent reads behave as if they observe it in a single well-defined order.        | Payment status or inventory reservation                    |
| **Eventual consistency** | A read may briefly return older data, but replicas should converge after updates stop and replication succeeds. | Click analytics or recommendation counts                   |
| **Read-your-writes**     | A user sees their own update after making it.                                                                   | Profile or preference update                               |
| **Monotonic reads**      | A user does not move backward to an older version during a session.                                             | Order-status tracking                                      |
| **Causal consistency**   | Related operations are observed in their causal order.                                                          | A message should not appear before its conversation exists |

The exact meaning of “strong” depends on the storage system and operation. Be
clear about the read/write scope and failure assumptions instead of treating
these labels as magic guarantees.

---

## 2. Why replicas can return stale data

A common setup sends writes to a primary database and reads to replicas:

```text
Write → Primary database
             ↓ replication
Read  → Replica
```

Replication is often asynchronous. Immediately after a write, a replica may not
yet have received it. This delay is called **replication lag**.

Example:

1. A preference is updated to `EMAIL` on the primary.
2. The application confirms success.
3. A notification worker reads from a replica.
4. The replica still says `SMS`.
5. The worker sends through SMS.

Possible mitigations include:

- Read critical data from the primary.
- Use read-your-writes routing for a period after a change.
- Wait until a replica reaches a known replication position.
- Store versions and reject stale versions.
- Make the business operation safe despite temporary staleness.

Each option has costs. Reading from the primary can increase load and reduce
read-scaling benefits.

---

## 3. Consistency is not one global setting

Different operations need different guarantees. Do not require strong
consistency everywhere by default: stronger guarantees may increase latency,
reduce availability during network partitions, or limit scale.

| Data or operation                | Possible consistency need                                                   | Why                                                        |
|----------------------------------|-----------------------------------------------------------------------------|------------------------------------------------------------|
| Opt-out / consent change         | Strong or sufficiently fresh read-your-writes                               | Avoid sending after a user opts out                        |
| Payment or order state           | Strong enough for decisions that could cause financial or fulfilment errors | Avoid shipping an unpaid order or reporting a false status |
| Promotional recommendation count | Eventual                                                                    | A small reporting delay is usually acceptable              |
| Delivery analytics               | Eventual                                                                    | Reports can lag behind delivery events                     |
| Deduplication record             | Atomic/strong enough for concurrent attempts                                | Two retries must not both cause a send                     |
| Delivery status dashboard        | Often eventual                                                              | A status update can take time to propagate                 |

An order-confirmation message may be sent asynchronously, but the underlying
order event must not be lost. The delivery can be delayed; whether its content
may reflect stale order state depends on the message’s meaning.

---

## 4. Consistency in distributed systems

When data is copied across machines or regions, systems must handle communication
delay and network failure. A distributed write may be:

- Applied on one node but not yet copied to others.
- Accepted by one service while another is unavailable.
- Retried after a timeout even though the original write succeeded.
- Processed more than once by an at-least-once message consumer.

For important operations, define:

- Which component owns the authoritative data?
- Which write is authoritative?
- What order must operations follow?
- Can updates conflict?
- Are duplicates possible?
- How are stale reads handled?
- What does the client see if the outcome is unknown?

---

## 5. Durability

Durability means that once data is acknowledged as committed, it should survive
the failures covered by the system’s design.

For example:

> If the Notification Service responds “accepted,” the notification request
> should not disappear after a process restart or host failure.

Durability depends on:

- Database commit behavior
- Write-ahead logs
- Replication and acknowledgement policy
- Broker persistence settings
- Disk and storage reliability
- Backups and restore procedures
- Disaster-recovery architecture

Define what “saved” means. Did the request reach application memory, local disk,
a database primary, replicated storage, or a durable queue? The response must
not promise more than the system guarantees.

---

## 6. Durability in queues and messaging

A producer may consider a message accepted at different stages:

1. It is placed in local application memory.
2. A broker receives it in memory.
3. The broker writes it to disk.
4. One or more replicas confirm it.

These stages offer different latency and durability trade-offs. If an API says
“accepted” before the message is durably stored, a process or broker failure
could lose accepted work.

A safer shape for important notifications is:

```text
Request
  → validate
  → durably persist job or publish to durable broker
  → acknowledge acceptance
  → worker processes it
```

The exact guarantee depends on broker and database configuration. A durable
queue alone does not guarantee end-to-end delivery: consumers, providers,
retries, and status tracking still matter.

---

## 7. The dual-write problem

Suppose an Order Service writes an order to its database and then publishes an
`OrderCreated` event:

```text
1. Save order in database
2. Publish event to broker
```

If the service crashes between the steps, the order exists but the event is not
published. Downstream notifications may never be created.

Reversing the order is also unsafe:

```text
1. Publish event
2. Save order in database
```

If the event is published but the database write fails, other services may act
on an order that does not exist.

This is the **dual-write problem**: the database and broker are separate systems,
so a simple application flow cannot atomically commit both.

### Transactional outbox pattern

Write the business record and an outbox record in the same database transaction:

```text
Single database transaction:
  save order
  save OrderCreated event in outbox
```

A separate publisher reads the outbox and publishes events to the broker. After
broker acknowledgement, it marks the event as published. Publication may happen
more than once, so consumers still need idempotency.

The outbox makes the database change and the intent to publish atomic. It does
not make the entire end-to-end workflow exactly once.

---

## 8. Durability is not the same as backup

- **Replication** can make data available on another node quickly, but accidental
  deletion or corruption may be replicated too.
- **Backups** provide recovery points, but restoring can take time and may lose
  changes since the last backup.
- **Durable commits** protect acknowledged writes against specified failures,
  depending on configuration.
- **Disaster recovery** addresses larger failures such as a full-region outage.

A mature design defines how these mechanisms fit together and regularly tests
restoration.

---

## 9. Consistency and durability trade-offs

| Choice                                   | Benefit                                | Cost or risk                                                             |
|------------------------------------------|----------------------------------------|--------------------------------------------------------------------------|
| Wait for replicas to acknowledge a write | Better protection against node failure | Higher write latency; reduced availability if replicas are unreachable   |
| Read from primary                        | Fresh reads                            | More primary load; fewer read-scaling benefits                           |
| Read from replicas                       | More read capacity                     | Possible stale data due to replication lag                               |
| Asynchronous event processing            | Decoupling and burst absorption        | Eventual consistency and delayed results                                 |
| Synchronous cross-service call           | Immediate response from dependency     | Higher latency and dependency availability coupling                      |
| Stronger consistency across regions      | More current shared view               | Higher latency and reduced ability to operate through network partitions |

These are design choices, not universally right or wrong answers. Apply
stronger guarantees where stale or lost data has serious consequences.

---

## 10. Notification Service examples

### Example A: User opts out

A customer opts out of promotional notifications. If the update is written to
the primary but a worker reads stale preferences from a replica, the system
might still send a promotion.

Possible design:

- Treat consent and opt-out checks as critical.
- Read current consent from an authoritative source at send time, or ensure the
  chosen design provides sufficiently fresh data.
- Make preference updates visible immediately to the user.
- Record a preference version and ensure workers do not use older versions.
- Do not log or replay a promotion after consent has been revoked.

### Example B: Provider timeout

A worker submits an SMS and then times out before receiving the provider’s
response. The provider may have accepted the message even though the worker did
not see the acknowledgement.

If the worker retries blindly, the customer may receive a duplicate. Use
provider idempotency where supported and internal deduplication. Exactly-once
external delivery may not be achievable if the provider offers no way to identify
the original request.

### Example C: Accepted notification disappears

The API returns success after placing a job only in application memory. The
process restarts and loses that job. The service should acknowledge only after
durable persistence or broker confirmation that meets the requirement.

---

## 11. Questions to ask in an HLD discussion

For every important piece of data, ask:

1. Who owns the authoritative copy?
2. How soon must updates be visible to other users or services?
3. Is a stale read acceptable? For how long?
4. Can concurrent updates conflict?
5. Can operations be retried or processed more than once?
6. What does “accepted” or “committed” mean to the caller?
7. Which failures must acknowledged data survive?
8. What are the backup, restore, RPO, and RTO requirements?

These questions turn vague phrases like “data must be consistent” or “never
lose data” into useful requirements.

---

## 12. Common mistakes

- Treating consistency as a synonym for validation or database constraints.
- Assuming every read immediately sees a write when replicas or caches are involved.
- Making every workflow strongly consistent even when temporary staleness is acceptable.
- Acknowledging work before it is durably stored.
- Assuming a durable broker guarantees final delivery to a customer.
- Publishing events and updating the database as unrelated writes without handling the dual-write problem.
- Assuming replication replaces backups.
- Promising exactly-once delivery without defining the boundary and failure semantics.

---

## 13. Design checklist

- [ ] Identify the authoritative owner for each data item.
- [ ] Choose the consistency guarantee required per operation.
- [ ] Define acceptable staleness and replication lag.
- [ ] Define when the system may acknowledge a write.
- [ ] Specify which failures acknowledged data must survive.
- [ ] Make retries safe through idempotency or deduplication.
- [ ] Handle the database-and-event dual-write problem.
- [ ] Separate replication, backups, and disaster recovery.
- [ ] Test recovery and message replay procedures.
- [ ] Explain where eventual consistency is visible to users or operators.

---

## Practice exercise

For the Notification Service, answer:

1. A user opts out of promotional messages. Which consistency guarantee should
   the preference update have, and why?
2. The API returns “notification accepted.” What must have happened before that response?
3. An Order Service saves an order but crashes before publishing `OrderCreated`.
   How could the system avoid losing the event?
4. A worker times out after sending an SMS request to a provider. Why can blindly
   retrying create a duplicate, and how can the design reduce this risk?
5. Which data in the service could be eventually consistent, and which data
   needs stronger guarantees? Give examples.

### Reference answers

#### 1. Opt-out preference consistency

Use a guarantee that makes the opt-out visible immediately to the user
(read-your-writes) and sufficiently fresh to workers before a promotional send.
Otherwise, a worker might read stale consent data and send a message after the
user opted out. The system can achieve this through an authoritative read,
versioning, or another design with equivalent freshness guarantees.

#### 2. Meaning of “notification accepted”

Before returning “accepted,” the service should validate the request and
durably store the notification job, or receive a durable acknowledgement from
the broker, according to the promised failure guarantees. Storing only in
application memory is not enough: a process restart could lose the request.

If both a database record and a broker event are written, account for the
dual-write problem; a transactional outbox is one common solution.

#### 3. Avoid losing the event after saving an order

Use the transactional outbox pattern. Save the order and an `OrderCreated`
outbox record in the same database transaction. A separate publisher sends
outbox records to the broker and marks them published after acknowledgement.
The publisher may send more than once, so consumers must be idempotent.

#### 4. Timeout after an SMS request

A timeout means the worker does not know whether the provider accepted the SMS.
Blind retrying may submit the same notification again. Reduce this risk by:

- Sending a stable idempotency key if the provider supports one.
- Recording internal deduplication state keyed by event, user, type, and channel.
- Checking provider status or reconciling outcomes where supported.
- Keeping the operation safe to retry and recognizing that exactly-once external
  delivery cannot always be guaranteed.

#### 5. Eventual vs stronger consistency

**Potentially eventual:** click analytics, delivery dashboards, and
recommendation counts may tolerate short delays.

**Stronger/freshness-sensitive:** payment/order state used for business
decisions, consent and opt-out checks before sending promotions, and atomic
deduplication decisions need stronger guarantees because stale or conflicting
data can cause financial, fulfilment, privacy, or duplicate-send problems.

An order-confirmation notification may be delivered asynchronously, but the
order event must not be lost. Delivery can be delayed; the message content must
still reflect the intended order state.

