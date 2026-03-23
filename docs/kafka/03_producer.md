---
title: Producer
parent: Kafka
nav_order: 3
---

# Producer

A **producer** is an application that sends messages to the messaging
system.

Example:

> Order Service → sends OrderCreated event

Producer does **not know who consumes the event**.

------------------------------------------------------------------------

## Why Producers Exist

Producers allow services to publish events **without knowing downstream
services**.

Example event:

> OrderCreated event

Consumers might be:

```
Payment Service
Inventory Service
Notification Service
```

Producer stays independent.

------------------------------------------------------------------------

## How Producer Works

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

## Example Producer (Spring Boot + Kafka)

### Configuration

``` yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
```

### Producer Code

``` java
@Autowired
private KafkaTemplate<String, String> kafkaTemplate;

public void sendEvent(String message) {
    kafkaTemplate.send("order-created", message);
}
```

------------------------------------------------------------------------

## Real Producer Example

Banking system:

Transaction Service publishes:

> account.transaction.completed

------------------------------------------------------------------------

## Failure Scenario

### Kafka Down

If Kafka becomes unavailable:

| Case           | Result                 |
| -------------- | ---------------------- |
| retry enabled  | producer retries       |
| retry disabled | message lost           |
| broker cluster | another broker handles |

Producer configuration controls behavior.

Example:

``` yaml
spring.kafka.producer.retries: 3
```

------------------------------------------------------------------------

## Producer Best Practices

Enable reliable producer settings.

Example:

``` yaml
spring.kafka.producer.acks: all
```

Recommendations:

-   enable retries
-   use idempotent producers
-   use `acks=all` for strong durability

------------------------------------------------------------------------
