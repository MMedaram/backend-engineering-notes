---
title: Partition
parent: Kafka
nav_order: 7
---

# Partition

A partition is a subdivision of a topic.

Each partition is: 
- ordered 
- append-only
- independent

------------------------------------------------------------------------

## Example

Topic: order.created.v1

Partition 0\
Partition 1\
Partition 2

------------------------------------------------------------------------

## Why Partitions Exist

**1. Scalability**

Multiple consumers can process in parallel.

**2. Performance**

Data is distributed across brokers.

------------------------------------------------------------------------

## Ordering Rule

Kafka guarantees ordering only within a partition.

### Example

Partition 0: offset 0 → A\
offset 1 → B\
offset 2 → C

Order is guaranteed.

------------------------------------------------------------------------

Across partitions:

Partition 0 → A, B\
Partition 1 → C, D

Order is NOT guaranteed globally.

------------------------------------------------------------------------

## How Producer Decides Partition

### With Key

``` java
kafkaTemplate.send("orders", "user1", event);
```

Same key → same partition → ordering guaranteed

------------------------------------------------------------------------

### Without Key

Kafka distributes messages randomly.

------------------------------------------------------------------------

## Kafka CLI --- Describe Topic

```
kafka-topics.sh   --describe   --topic order.created.v1   --bootstrap-server localhost:9092
```

------------------------------------------------------------------------
