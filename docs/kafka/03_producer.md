---
title: Producer
parent: Kafka
nav_order: 3
---

# Producer

-----

A **producer** is an application that sends messages to the messaging system.

Example:

> Order Service → sends OrderCreated event

Producer does **not know who consumes the event**.

> **A producer is responsible for sending events to Kafka topics.**

### Why Producers Exist

Producers allow services to publish events **without knowing downstream
services**.

### Producer Example

```
Transaction Service
↓
AccountTransactionCompletedEvent
↓
Kafka Topic: account.transaction.completed.v1
```

Consumers:

- Notification Service
- Audit Service
- Fraud Detection Service

#### Flow

`Producer → Kafka Broker → Partition → Consumer`

------------------------------------------------------------------------

### How Producer Works

Basic flow:

```
Application
↓
Producer
↓
Broker
```

- Producer sends event to broker.
- Broker stores it.
- Consumers read later.

------------------------------------------------------------------------

## Example Banking Event

### Event Class

```java
public class AccountTransactionCompletedEvent {

    private String transactionId;
    private String accountId;
    private double amount;
    private String type; // DEBIT / CREDIT
    private String status;
    private LocalDateTime timestamp;
}
```

### Configuration

application.yaml

``` yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092

    producer:
      acks: all
      retries: 3
      batch-size: 16384
      linger-ms: 10
      buffer-memory: 33554432
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
```


#### What Each Config Means

**acks: all**

- Wait for leader + replicas
- Ensures no data loss

**retries: 3**

- Retry sending if failure occurs

**batch-size: 16384**

- Producer groups messages into batches
- Improves performance

**linger-ms: 10**

- Wait up to 10ms to fill batch
- Trade-off: latency vs throughput

**buffer-memory: 33554432**

- Memory buffer for unsent messages

----

### Kafka Producer Service

``` java
@Service
public class TransactionEventProducer {

    @Autowired
    private KafkaTemplate<String, AccountTransactionCompletedEvent> kafkaTemplate;

    public void publishTransactionEvent(AccountTransactionCompletedEvent event) {

        kafkaTemplate.send(
            "account.transaction.completed.v1",
            event.getAccountId(), // key
            event
        );
    }
}
```

### Why Key = accountId?

`Same account → same partition → ordering maintained`

## Without Key

> kafkaTemplate.send("topic", event);

- Kafka distributes randomly
- No ordering guarantee


### Send with CallBack

> Even though Kafka handles ACK internally, you can capture result.

```java
public void publishTransactionEvent(AccountTransactionCompletedEvent event) {

    kafkaTemplate.send(
        "account.transaction.completed.v1",
        event.getAccountId(),
        event
    ).addCallback(
        success -> System.out.println("Message sent"),
        failure -> System.out.println("Failed: " + failure.getMessage())
    );
}
```

### Why Callback Matters

- detect failures
- log events
- trigger retry logic (if needed)



------------------------------------------------------------------------

## Failure Scenario

### 1. Kafka Down

If Kafka becomes unavailable:

| Case           | Result                                                       |
| -------------- |--------------------------------------------------------------|
| retry enabled  | producer retries : If retries exhausted --> Exception thrown |
| retry disabled | message lost                                                 |
| broker cluster | another broker handles                                       |

Producer configuration controls behavior.

Example:

``` yaml
spring.kafka.producer.retries: 3
```

### Handling 

```
try {
    kafkaTemplate.send(...);
} catch (Exception e) {
    // log / store in DB (outbox pattern)
}
```


### 2. Broker Slow

- Messages accumulate in buffer
- batching increases
- delay increases 
- If buffer full
  - Producer blocks OR fails

### 3. Network Issue
- retries triggered
- possible duplicate sends

----

## Idempotent Producer 

Retries can cause duplicates:

`Message sent → timeout → retry → duplicate`

### Enable idempotence:

```yaml
spring:
  kafka:
    producer:
      enable-idempotence: true
```

- Kafka ensures no duplicate writes
- Exactly-once per partition (producer side)

--------------

## Delivery Guarantees

##### At Most Once

> acks=0

- fast
- data loss possible

##### At Least Once (Most Common)

> acks=all + retries

- no loss
- duplicates possible


##### Exactly Once (Partial)

- idempotent producer
- no duplicates from producer

------------------------------------------------------------------------


## Where Message Is Stored

Kafka stores message in:

> /var/lib/kafka/data/account.transaction.completed.v1-0/

File:

> 00000000000000000000.log

------


## Producer Best Practices

- Use acks=all
- Enable retries
- Use idempotent producer
- Use proper keys (like accountId)
- Avoid large payloads
- Handle failures 

------------------------------------------------------------------------
