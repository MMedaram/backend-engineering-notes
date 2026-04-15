---
title: Retry, Backoff & DLT
parent: Kafka
nav_order: 12
---

# Retry, Backoff & Dead Letter Topic (DLT)

---

## 1. Big Picture (End-to-End Flow)

```
Producer  
↓ (producer retry + backoff)  
Kafka Topic  
↓  
Consumer  
↓ (consumer retry + backoff)  
Success ✅ [ OR ] DLT ❌
```

---

## 2. Retry — Two Types 

---

### 2.1 Producer Retry

Retry when sending message to Kafka fails.

### When
`Producer → Kafka fails (broker down / network issue / timeout)`

### Config (Spring Boot)

```yaml
spring:
  kafka:
    producer:
      acks: all
      retries: 3
      properties:
        retry.backoff.ms: 1000
      enable-idempotence: true
```

### Flow

`Send → fail → wait → retry → success`

### Key Points
- Happens BEFORE message enters Kafka
- Controlled by Kafka client
- Risk: duplicate messages (if idempotence disabled)

---

## 2.2 Consumer Retry 

Retry when message processing fails.

### When
`Kafka → Consumer → DB/API fails`

### Flow
`Read → process → fail → retry → success OR DLT`

---

# 3. Backoff — Delay Between Retries

---

## 3.1 Producer Backoff

### Config

```yaml
spring:
  kafka:
    producer:
      properties:
        retry.backoff.ms: 1000
```

### Purpose
- Avoid hammering Kafka
- Give broker time to recover

---

## 3.2 Consumer Backoff (Critical)

### Example (Spring Kafka)

```java
@RetryableTopic(
    attempts = "3",
    backoff = @Backoff(
        delay = 5000,
        multiplier = 2
    )
)
@KafkaListener(topics = "account.transaction.completed.v1")
public void consume(AccountTransactionCompletedEvent event) {
    process(event);
}
```

### Behavior
1st fail → wait 5s  
2nd fail → wait 10s  
3rd fail → wait 20s  
→ DLT

---

## Types of Backoff

| Type | Behavior |
|------|--------|
| Fixed | 5s → 5s → 5s |
| Exponential (Recommended) | 5s → 10s → 20s |

---

## Why Backoff is Critical

**Without backoff:**

Immediate retry → CPU spike → DB overload ❌

**With backoff:**

Retry with delay → system stabilizes ✅

---

# 4. Retry Strategies

## Immediate Retry (Bad)
`Fail → retry instantly → infinite loop ❌`

## Backoff Retry (Better)
`Fail → wait → retry`

## Retry Topics (Best Practice)

Main Topic  
↓  
Retry Topic 1 (5s)  
↓  
Retry Topic 2 (30s)  
↓  
DLT

---

# 5. Dead Letter Topic (DLT)

Kafka topic for permanently failed messages.

## Important
- Just another Kafka topic
- Not offset related
- Not automatic (handled by consumer logic)

---

## Flow
`Message fails → retries → still fails`    
→ send to DLT  
→ commit offset

---

## Spring Kafka DLT Handler

```java
@DltHandler
public void handleDlt(AccountTransactionCompletedEvent event) {
    log.error("DLT Event: " + event.getTransactionId());
    // store in DB / alert / manual fix
}
```

---

# 6. Offset + DLT Relationship

After sending to DLT:
Consumer commits offset

Why?
To avoid infinite reprocessing loop

---

## Key Difference

| Concept | Meaning |
|--------|--------|
| Offset Commit | Processing completed |
| DLT | Processing failed permanently |

---

# 7. Full Flow (Correct Design)

1. Read message
2. Process
3. Fail
4. Retry with backoff
5. Still fail
6. Send to DLT
7. Commit offset

---

# 8. Banking Example (Real)

## Scenario
Transaction Event → Ledger Service

### Case 1 — DB Down (Temporary)
Fail → retry → success

### Case 2 — Invalid Data (Permanent)
Fail → retry → fail → DLT

### Case 3 — Kafka Down
Producer retry handles it

---

# 9. application.yaml (Consumer)

```yaml
spring:
  kafka:
    consumer:
      enable-auto-commit: false
    listener:
      ack-mode: manual
```

---

# 10. Best Practices

## Producer
- acks=all
- retries enabled
- idempotence enabled
- small backoff

## Consumer
- use @RetryableTopic
- use exponential backoff
- always use DLT
- commit offset after handling
- implement idempotency

---

# 11. Common Mistakes

- No DLT ❌
- Infinite retry ❌
- No backoff ❌
- Blocking retry ❌
- Mixing commit + retry ❌

---

# 12. Final Summary

| Concept | Side | Purpose |
|--------|------|--------|
| Producer Retry | Producer | Ensure delivery |
| Producer Backoff | Producer | Retry delay |
| Consumer Retry | Consumer | Ensure processing |
| Consumer Backoff | Consumer | Retry delay |
| DLT | Consumer | Store failed messages |

---

# Final Insight

Kafka ensures delivery  
You ensure correctness
