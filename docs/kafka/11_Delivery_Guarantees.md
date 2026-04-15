---
title: Delivery Guarantees
parent: Kafka
nav_order: 11
---

# Delivery Guarantees

-----

**Delivery guarantee defines how reliably a message is delivered from `producer → Kafka → consumer`**


## Types
- At Most Once
- At Least Once
- Exactly Once (practical understanding)


---

## End-to-End Flow

`Producer → Kafka → Consumer → Offset Commit`

👉 Delivery guarantee depends on:

- Producer config
- Kafka behavior
- Consumer offset handling

---


## At Most Once

Message is delivered at most once (may be lost)

`Producer sends → Offset committed → THEN processing`

If crash happens:

- ❌ Message lost

**Producer Config**

> acks: 0

**Consumer Config**

> enable-auto-commit: true

### Problem
- No retries
- No guarantee
- Data loss possible

`Money debited → event lost → audit system not updated`

Not acceptable

---

## At Least Once

Message is delivered at least once

👉 No data loss     
👉 But duplicates possible       


Flow

`Producer → Kafka → Consumer → Process → Commit offset`

If crash before commit:

`Message reprocessed → duplicate`

**Producer Config**

> acks: all
> retries: 3

**Consumer Config**

> enable-auto-commit: false
> ack-mode: manual


### Consumer Code

```java
@KafkaListener(topics = "account.transaction.completed.v1")
public void consume(Event event, Acknowledgment ack) {

    process(event);

    ack.acknowledge();
}
```

`Debit processed twice → duplicate transaction`

👉 Must handle using idempotency

-----


## Exactly Once (Realistic Understanding)

Kafka cannot guarantee full exactly-once across systems easily

But we can achieve it practically

> Message is processed only once logically


## How to Achieve

1. Idempotent Producer

> enable-idempotence: true

2. Idempotent Consumer

Check before Processing

```
if (alreadyProcessed(event.getTransactionId())) {
    return;
}
```

3. Database Constraint

> UNIQUE(transaction_id)


----


| Guarantee     | Data Loss  | Duplicates  | Use Case                   |
| ------------- | ---------- | ----------- | -------------------------- |
| At Most Once  | ❌ Possible | ❌ No        | logs, metrics              |
| At Least Once | ✅ No       | ⚠️ Possible | most systems               |
| Exactly Once  | ✅ No       | ✅ No        | banking (with idempotency) |


----


## Where Problems Happen

### Case 1 — Producer Retry

`Send → timeout → retry → duplicate`

### Case 2 — Consumer Crash

`Processed → crash before commit → reprocess`

### Case 3 — Network Issues

- duplicates
- delayed delivery

--------------


```
Producer (Transaction Service)
      ↓
Kafka
      ↓
Consumer (Ledger Service)
      ↓
Check transactionId (idempotency)
      ↓
Insert into DB (unique constraint)
      ↓
Commit offset
```


