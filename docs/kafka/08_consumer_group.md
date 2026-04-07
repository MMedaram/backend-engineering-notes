---
title: Consumer Group
parent: Kafka
nav_order: 8
---

# Consumer Group

A consumer group is a set of consumers working together to consume data from a topic.

## Key Rule

One partition → one consumer (within same group)


### Example

Topic: order.created.v1 (3 partitions)

Consumer group: order-service-group

Consumers: 

- Consumer 1 → Partition 0
- Consumer 2 → Partition 1
- Consumer 3 → Partition 2


## Why Consumer Groups Exist

- load balancing
- parallel processing
- scalability 
- Avoid duplicate processing

---

# Consumer Group Behavior

## Case 1 — Equal Consumers & Partitions

> 3 partitions → 3 consumers

✔ Perfect distribution  
✔ Maximum parallelism

---

## Case 2 — More Consumers than Partitions

3 partitions → 5 consumers

Consumer 4 → idle  
Consumer 5 → idle

❌ Wasted resources

---

## Case 3 — Fewer Consumers

3 partitions → 2 consumers

Consumer 1 → Partition 0,1  
Consumer 2 → Partition 2

✔ Works  
❗ uneven load

------------------------------------------------------------------------

## Multiple Consumer Groups

Topic: order.created.v1

Group 1: notification-service\
Group 2: audit-service

Both groups receive all messages independently.

------------------------------------------------------------------------

# Putting It All Together


```
Producer
↓
Topic (order.created.v1)
↓
Partitions (0,1,2)
↓
Consumer Group
↓
Consumers process messages
```
------------------------------------------------------------------------

## Real Banking Example

```
Transaction Service
↓
account.transaction.v1 topic
↓
Partition 0 / 1 / 2
↓
Consumer Group: notification-service
↓
Consumers process events
```

------------------------------------------------------------------------

# Command

## Produce Message

``` 
kafka-console-producer.sh   --topic order.created.v1   --bootstrap-server localhost:9092
```

## Consume Message

```
kafka-console-consumer.sh   --topic order.created.v1   --from-beginning   --bootstrap-server localhost:9092
```

------------------------------------------------------------------------

# Failure Scenarios

## Consumer Crash

-   Kafka reassigns partition to another consumer
-   Messages reprocessed if not committed

------------------------------------------------------------------------

## More Consumers than Partitions

Example: 3 partitions, 5 consumers

Result:  2 consumers remain idle

------------------------------------------------------------------------

## Fewer Consumers than Partitions

Example: 3 partitions, 2 consumers

Result: - one consumer handles multiple partitions

------------------------------------------------------------------------


# Key

-   Topic = logical grouping
-   Partition = scalability + ordering unit
-   Consumer Group = parallel processing

------------------------------------------------------------------------




# What is Rebalancing?

Rebalancing = redistribution of partitions among consumers

---

## When Does Rebalancing Happen?

1. New Consumer Joins

```
Consumer 1,2 running  
Consumer 3 joins
```

➡ Kafka redistributes partitions

2. Consumer Leaves / Crashes

```
Consumer 2 crashes
```

➡ Kafka reassigns its partitions

3. Consumer Restart

```
Treated as leave + join
```

4. Topic Partition Change

```
partitions increased
```


## Rebalancing Flow

```
Consumers STOP processing
        ↓
Group coordinator triggers rebalance
        ↓
Partitions reassigned
        ↓
Consumers resume processing
```


## Impact

During rebalance:

❌ Consumers pause\
❌ No processing happens

---

# Real Problem

Consumers suddenly pause → likely rebalance.

---

# Group Coordinator

Kafka uses a group coordinator broker.

Responsibilities:

- Track consumers in group
- Assign partitions
- Manage offsets
- Trigger rebalancing

---

# Heartbeat Mechanism

Consumers send heartbeat to Kafka.

If missed → Kafka assumes consumer is dead.

---

## Config

```yaml
spring:
  kafka:
    consumer:
      heartbeat-interval: 3000
      session-timeout: 10000
```

---

# Rebalance Types

## Eager

- Full stop
- Full reassignment

## Cooperative

- Partial reassignment
- Less disruption

---

# Partition Assignment Strategies

Kafka decides:

Which partition → which consumer

## Strategies

### Range (default)

Partition 0,1 → Consumer 1\  
Partition 2,3 → Consumer 2

Partitions assigned in chunks

## Round Robin

Partition 0 → C1\
Partition 1 → C2\
Partition 2 → C1\
Partition 3 → C2

Partitions distributed evenly

---

## Config

```yaml
spring:
  kafka:
    consumer:
      properties:
        partition.assignment.strategy: org.apache.kafka.clients.consumer.RoundRobinAssignor
```

---

# Failure Scenario

Consumer crashes:

Partitions reassigned to others.

If offset not committed → message reprocessed.

---

# Rebalance + Offset

Rebalance reads from last committed offset.

Manual commit = safe  
Auto commit = risk

---

# Real System Behavior

Rebalance during processing → duplicate possible.

Solution: idempotent consumer

---

# Scaling Strategy

Rule:

#Consumers ≤ #Partitions

---

# Kafka CLI

```
kafka-consumer-groups.sh   --bootstrap-server localhost:9092   --describe   --group order-group
```

---

# Best Practices

- Consumers ≤ partitions
- Manual commit
- Idempotent consumers
- Monitor lag
- Avoid frequent restarts

---


> Consumer group → who processes data  
> Rebalancing → how work is redistributed
