---
title: Offset
parent: Kafka
nav_order: 9
---


# Kafka Offsets

---

# 1. What is an Offset?

> An offset is the position of a message inside a partition.


## Example

Partition 0:

Offset 0 → Order A  
Offset 1 → Order B  
Offset 2 → Order C  
Offset 3 → Order D  

Offset is a sequence number assigned by Kafka.

---

## Key Points

- Offset is per partition  
- Offsets are sequential  
- Kafka assigns offsets automatically  

---

# 2. What Does “Processing a Message” Mean?

Kafka does NOT know business logic.

Processing means:

1. Consumer reads message  
2. Business logic executes (DB/API/etc.)  
3. Offset is committed  

---

# 3. What is Offset Commit?

Offset commit = telling Kafka:

"I have successfully processed messages up to this offset"

---

## Example

Offset 0 → A  
Offset 1 → B  
Offset 2 → C  
Offset 3 → D  

Consumer processed till Offset 2  

Commit offset = 2  

---

## Where Kafka Stores This

__consumer_offsets (internal topic)

---

## Mapping

Consumer Group → Topic → Partition → Offset

---

# 4. Why Offset is Critical

Offsets enable:

- Resume after crash  
- Replay messages  
- Fault tolerance  
- Consumer scaling  

---

# 5. Auto Commit vs Manual Commit

---

## Auto Commit

### Config

```yaml
spring:
  kafka:
    consumer:
      enable-auto-commit: true
      auto-commit-interval: 5000
```

---

## Problem with Auto Commit

Message received → offset committed → THEN processing

If crash happens:

Message lost (not processed but marked done)

---

## Manual Commit (Recommended)

### Flow

1. Read message  
2. Process message  
3. Commit offset  

---

### Spring Kafka Example

```java
@KafkaListener(topics = "order.created.v1")
public void consume(String message, Acknowledgment ack) {

    process(message);

    ack.acknowledge();
}
```

---

### Config

```yaml
spring:
  kafka:
    listener:
      ack-mode: manual
```

---

## Why Manual is Better

- Crash before commit → message reprocessed  
- Crash after commit → message safe  

---

# 6. Consumer Crash Scenarios

## Before Commit

Offset not committed → message reprocessed

---

## After Commit

Offset committed → message NOT reprocessed

---

# 7. Replay (Reprocessing)

Replay = re-reading old messages

---

## Example

Processed till offset 100  
Replay from offset 50  

---

## CLI

```
kafka-consumer-groups.sh   --bootstrap-server localhost:9092   --group order-group   --topic order.created.v1   --reset-offsets --to-offset 50 --execute
```

---

## Use Cases

- Bug fix  
- Audit  
- Recovery  
- Data correction  

---

# 8. Offset Reset Behavior

## Config

```yaml
spring:
  kafka:
    consumer:
      auto-offset-reset: earliest
```

---

## Options

- earliest → read from beginning  
- latest → read only new messages  

---

## Important

If no offset exists:

- earliest → read all data  
- latest → skip old data  

---

# 9. Relationship with Lag

Lag = latest offset - consumer offset

- Offset increases → lag decreases  
- Offset stuck → lag increases  

---

# 10. Multi-Consumer Scenario

Each consumer group has its own offsets.

Example:

Group A → offset = 100  
Group B → offset = 50  

---

# 11. Failure Scenario

Consumer crashes during processing:

- Read message  
- Partial processing  
- Crash before commit  

After restart:

Message is reprocessed  

---

# 12. Important Kafka Behavior

Kafka NEVER deletes messages after consumption.

It only tracks offsets.

Same message can be read multiple times.

---

# 13. Offset Retention

## Config

```properties
offsets.retention.minutes=10080
```

(7 days)

---

## After Expiry

Kafka forgets offset → auto-offset-reset applies

---

# 14. End-to-End Flow

Producer → Kafka → Consumer

Consumer flow:

Poll message  
Process message  
Commit offset  

---

# 15. Best Practices

- Use manual commit  
- Commit after successful processing  
- Make consumers idempotent  
- Monitor lag  
- Use replay carefully  

---

# Final Understanding

Offsets answer:

> "What has been processed?"

Lag answers:

> "What is pending?"
