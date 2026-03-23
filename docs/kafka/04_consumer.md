---
title: Consumer
parent: Kafka
nav_order: 4
---

# Consumer

A **consumer** reads messages from the messaging system and processes them.

Example:

> `Payment Service` consumes `OrderCreated event`.

------------------------------------------------------------------------

## How Consumers Work

Consumers pull messages from broker.

Flow:

> Consumer → request messages → Broker

Broker sends available messages.

Kafka is **pull-based** (not push).

------------------------------------------------------------------------

## Example Consumer (Spring Kafka)

### Configuration

``` yaml
spring:
  kafka:
    consumer:
      group-id: order-group
```

### Consumer Code

``` java
@KafkaListener(topics = "order-created")
public void consume(String message) {
    System.out.println(message);
}
```

------------------------------------------------------------------------

## Consumer Example in Microservices

Event:

`OrderCreated`

Consumers:

```
Payment Service
Inventory Service
Email Service
```

Each service processes the event independently.

------------------------------------------------------------------------

## What Happens If Consumer Crashes

Kafka guarantees **at‑least‑once delivery**.

If a consumer crashes before completing processing:

Message may be reprocessed.

Therefore consumers must be **idempotent**.

------------------------------------------------------------------------

## Consumer Scaling

Consumers can scale horizontally.

Example topic partitions:

```
Partition 0
Partition 1
Partition 2
```

Consumers:

```
Consumer 1 → Partition 0
Consumer 2 → Partition 1
Consumer 3 → Partition 2
```

This is handled through **consumer groups**.

------------------------------------------------------------------------
