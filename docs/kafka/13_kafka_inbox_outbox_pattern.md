---
title: Inbox and Outbox Pattern
parent: Kafka
nav_order: 13
---

# Inbox / Outbox Pattern (Banking-Grade Design)

---

## 1. Scenario (Without Pattern)

### Without Outbox Pattern

```
Transaction Service  
    ↓  
Save to DB ✅  
    ↓  
Send event to Kafka ❌ (Kafka down)
```

---

## Result

- DB updated  
- Kafka event missing  
- System inconsistent ❌  

Examples:
- Ledger updated but notification missing  
- Audit trail incomplete  

---

## 2. Outbox Pattern (Producer Side)

Store event in DB first, then publish to Kafka reliably.

---

## Key Idea

DB = Source of truth  
Kafka = eventual delivery  

---

## Flow

```
1. Business transaction starts  
2. Save business data  
3. Save event in outbox table (same transaction)  
4. Background job reads outbox  
5. Publish to Kafka  
6. Mark as SENT 

```

---

## Diagram

```
Application  
 ↓  
DB Transaction  
 ├── Business Table  
 └── Outbox Table  
 ↓  
Outbox Processor  
 ↓  
Kafka  
```

---

# 3. Outbox Table Design

```sql
CREATE TABLE outbox_events (
    id BIGINT PRIMARY KEY,
    event_type VARCHAR(100),
    payload JSON,
    status VARCHAR(20),
    created_at TIMESTAMP
);
```

---

## Status Flow

`NEW → SENT → FAILED`

---

## 4. Spring Boot Example (Producer)

## Save Data + Outbox

```java
@Transactional
public void completeTransaction(Transaction txn) {

    transactionRepository.save(txn);

    OutboxEvent event = new OutboxEvent(
        "account.transaction.completed.v1",
        txn.getId(),
        toJson(txn)
    );

    outboxRepository.save(event);
}
```

---

## Outbox Processor

```java
@Scheduled(fixedDelay = 5000)
public void publishOutboxEvents() {

    List<OutboxEvent> events = outboxRepository.findTop10ByStatus("NEW");

    for (OutboxEvent event : events) {

        try {
            kafkaTemplate.send(event.getEventType(), event.getPayload());
            event.setStatus("SENT");

        } catch (Exception e) {
            event.setStatus("FAILED");
        }

        outboxRepository.save(event);
    }
}
```

---

## Key Benefit

Even if Kafka is down:
→ Event stored safely in DB  
→ Will be retried later  

---

## 5. Inbox Pattern (Consumer Side)

Ensure each message is processed only once.

---

## Problem Solved

- Duplicate messages due to retries  
- Rebalancing  
- Consumer crashes  

---

## Flow

```
1. Consumer receives event  
2. Check inbox table  
3. If already processed → skip  
4. Else → process  
5. Store event in inbox  
6. Commit offset  
```

---

## 6. Inbox Table Design

```sql
CREATE TABLE inbox_events (
    event_id VARCHAR(100) PRIMARY KEY,
    processed_at TIMESTAMP
);
```

---

## 7. Consumer Code Example

```java
@KafkaListener(topics = "account.transaction.completed.v1")
public void consume(AccountTransactionCompletedEvent event,
                    Acknowledgment ack) {

    if (inboxRepository.existsById(event.getTransactionId())) {
        ack.acknowledge();
        return;
    }

    processTransaction(event);

    inboxRepository.save(new InboxEvent(event.getTransactionId()));

    ack.acknowledge();
}
```

---

# 8. Combined Flow

```
[ PRODUCER SIDE ]

DB Transaction  
→ Save business data  
→ Save outbox event  

Outbox Processor  
→ Kafka  
```

```
[ CONSUMER SIDE ]

Kafka  
→ Inbox check  
→ Process  
→ Save inbox  
→ Commit offset  
```
---

## 9. Banking Example

Money Transfer Flow:

1. Debit account  
2. Save outbox event  
3. Outbox publishes event  
4. Consumer processes  
5. Inbox ensures no duplicate  

---

## 10. Guarantees

| Problem | Solution |
|--------|--------|
| Kafka down | Outbox stores event |
| Duplicate processing | Inbox prevents |
| Retry issues | Safe handling |
| Exactly-once | Achieved logically |

---

## 11. Important Insight

Kafka alone cannot guarantee DB + event consistency.

Outbox + Inbox ensures end-to-end reliability.

---

## 12. Best Practices

- Use Outbox for critical flows  
- Use Inbox for idempotency  
- Use DB constraints  
- Monitor outbox failures  
- Use scheduler or CDC  

---

## 13. Common Mistakes

- Direct Kafka call inside DB transaction ❌  
- No retry for outbox ❌  
- No inbox check ❌  
- Using non-unique key ❌  

---

# Final Understanding

Outbox = Don’t lose events  
Inbox = Don’t process twice  

