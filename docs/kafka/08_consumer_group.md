---
title: Consumer Group
parent: Kafka
nav_order: 8
---

# Consumer Group

A consumer group is a set of consumers working together.

## Key Rule

One partition → one consumer (within same group)


### Example

Topic: order.created.v1 (3 partitions)

Consumer group: order-service-group

Consumers: Consumer 1 → Partition 0\
Consumer 2 → Partition 1\
Consumer 3 → Partition 2


## Why Consumer Groups Exist

-   load balancing
-   parallel processing
-   scalability

------------------------------------------------------------------------

## Multiple Consumer Groups

Topic: order.created.v1

Group 1: notification-service\
Group 2: audit-service

Both groups receive all messages independently.

------------------------------------------------------------------------

# 4. Putting It All Together


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
