---
title: End-to-End Kafka + Spring Boot
parent: Kafka
nav_order: 17
---


# End-to-End Kafka + Spring Boot

---

## 1. System Overview

```
Transaction Service
    ↓
(DB + Outbox)
    ↓
Kafka (account.transaction.completed.v1)
    ↓
Ledger Service
    ↓
(Inbox + Idempotent Processing)
```

### Services We Will Build

#### 1. Transaction Service (Producer)

Responsibility:

- Accept transaction
- Save DB
- Save outbox event
- Publish to Kafka (async)

#### 2. Ledger Service (Consumer)

Responsibility:

- Consume event
- Check inbox (idempotency)
- Process transaction
- Retry + DLT
- Commit offset

---

## 2. Common Event Model

```java
public class AccountTransactionCompletedEvent {
    private String transactionId;
    private String accountId;
    private double amount;
    private String type;
    private String status;
    private LocalDateTime timestamp;
}
```

---

## 3. Topic Creation (Spring @Bean)

```java
@Configuration
public class KafkaTopicConfig {

    @Bean
    public NewTopic transactionTopic() {
        return TopicBuilder.name("account.transaction.completed.v1")
                .partitions(3)
                .replicas(1)
                .build();
    }
}
```

---

## 4. Transaction Service (Producer + Outbox)

### application.yaml

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    producer:
      acks: all
      retries: 3
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
      properties:
        enable.idempotence: true
        retry.backoff.ms: 1000
```

---

### DB Tables

```sql
CREATE TABLE transactions (
    id VARCHAR(50) PRIMARY KEY,
    account_id VARCHAR(50),
    amount DECIMAL,
    type VARCHAR(10),
    status VARCHAR(20),
    created_at TIMESTAMP
);

CREATE TABLE outbox_events (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    event_type VARCHAR(100),
    aggregate_id VARCHAR(50),
    payload JSON,
    status VARCHAR(20),
    created_at TIMESTAMP
);
```

---

### Service Logic

```java
@Transactional
public void processTransaction(Transaction txn) {
    transactionRepository.save(txn);

    OutboxEvent event = new OutboxEvent(
        "account.transaction.completed.v1",
        txn.getId(),
        toJson(txn),
        "NEW"
    );

    outboxRepository.save(event);
}
```

---

## Outbox Publisher

```java
@Scheduled(fixedDelay = 5000)
public void publishOutboxEvents() {

    List<OutboxEvent> events = outboxRepository.findByStatus("NEW");

    for (OutboxEvent event : events) {
        try {
            kafkaTemplate.send(
                event.getEventType(),
                event.getAggregateId(),
                event.getPayload()
            );
            event.setStatus("SENT");
        } catch (Exception e) {
            event.setStatus("FAILED");
        }

        outboxRepository.save(event);
    }
}
```

---

## 5. Ledger Service (Consumer + Inbox)

## application.yaml

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: ledger-group
      enable-auto-commit: false
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.JsonDeserializer
      properties:
        spring.json.trusted.packages: "*"
    listener:
      ack-mode: manual
      concurrency: 3
```

---

## Inbox Table

```sql
CREATE TABLE inbox_events (
    event_id VARCHAR(50) PRIMARY KEY,
    processed_at TIMESTAMP
);
```

---

## Consumer Logic

```java
@RetryableTopic(
    attempts = "3",
    backoff = @Backoff(delay = 5000, multiplier = 2)
)
@KafkaListener(topics = "account.transaction.completed.v1")
public void consume(AccountTransactionCompletedEvent event,
                    Acknowledgment ack) {

    if (inboxRepository.existsById(event.getTransactionId())) {
        ack.acknowledge();
        return;
    }

    processLedger(event);

    inboxRepository.save(new InboxEvent(event.getTransactionId()));

    ack.acknowledge();
}
```

---

## DLT Handler

```java
@DltHandler
public void handleDlt(AccountTransactionCompletedEvent event) {
    log.error("DLT Event: {}", event.getTransactionId());
}
```

---

## 6. End-to-End Flow

1. User triggers transaction  
2. Transaction saved in DB  
3. Outbox event saved  
4. Outbox publisher sends to Kafka  
5. Consumer receives  
6. Inbox check  
7. Process  
8. Commit offset  

---

## 7. Failure Handling

- Kafka Down → Outbox retries  
- Consumer Crash → Offset reprocessed  
- Duplicate → Inbox prevents  
- Invalid Data → DLT  

---

# Final Understanding

Outbox → No event loss  
Inbox → No duplicate processing  
Kafka → Reliable event streaming

    