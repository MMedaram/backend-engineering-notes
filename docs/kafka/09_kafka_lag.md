---
title: Kafka Lag
parent: Kafka
nav_order: 9
---

# Kafka Lag 

------------------------------------------------------------------------

## 1. What is Lag?

> Lag = difference between produced messages and consumed messages

------------------------------------------------------------------------

## Formula

> Lag = Latest Offset (in partition) - Consumer Offset

------------------------------------------------------------------------

## Example

Partition 0:

Offset 0 → Event A\
Offset 1 → Event B\
Offset 2 → Event C\
Offset 3 → Event D\
Offset 4 → Event E

Consumer processed till:

Offset 2

Lag = 4 - 2 = 2

Pending messages: D, E

------------------------------------------------------------------------

# 2. Consumer Lag (Actual Kafka Concept)

Consumer lag means:

How many messages are waiting to be processed by a consumer


### Visualization

Producer → writes till offset 10\
Consumer → processed till offset 6

Lag = 10 - 6 = 4


### Real Meaning

-   Consumer is behind
-   Messages are waiting
-   System may be slow or overloaded


## Where Lag is Stored

Kafka tracks:

-   Latest offset → in partition\
-   Consumer offset → in \_\_consumer_offsets

------------------------------------------------------------------------

## Kafka CLI --- Check Lag

```
kafka-consumer-groups.sh   --bootstrap-server localhost:9092   --describe   --group order-group
```

### Output Example

TOPIC PARTITION CURRENT-OFFSET LOG-END-OFFSET LAG\
order.created.v1 0 5 10 5

### Meaning

-   CURRENT-OFFSET → Consumer processed till\
-   LOG-END-OFFSET → Latest message in Kafka\
-   LAG → Pending messages

------------------------------------------------------------------------

# 3. Why Consumer Lag Happens

----

## 1. Slow Consumer

-   Heavy processing
-   DB calls
-   External APIs

## 2. High Producer Rate

Producer sending 1000 msgs/sec\
Consumer processing 100 msgs/sec

Lag increases continuously

## 3. Consumer Down

Producer continues\
Consumer not running

Lag keeps growing

## 4. Errors / Retries

-   retries
-   DLT flow
-   failures slow down consumption

------------------------------------------------------------------------

# 4. Is Lag Always Bad?

## Not Always

-   Small lag → Normal
-   Temporary spike → Acceptable
-   Continuously increasing → Problem


## Healthy System

Lag increases → decreases → stable

## Problem System

Lag continuously increasing

------------------------------------------------------------------------

# 5. Producer Lag (Clarification)

Kafka does NOT officially define "producer lag"

## What People Mean by Producer Lag

### Case 1 --- Producer Send Delay

-   network delay
-   batching delay

### Case 2 --- Waiting for ACK

acks=all → producer waits for replicas

### Case 3 --- Buffer Full

buffer.memory full → producer blocks


### Important Point

Producer lag is not a Kafka metric
Consumer lag is the real metric

------------------------------------------------------------------------

# 6. Real Production Example

- Order Service → produces 1000 events/sec
- Payment Service → consumes 200 events/sec

Lag increases by 800/sec

After 10 seconds: Lag = 8000

------------------------------------------------------------------------

# 7. How to Reduce Lag

## 1. Scale Consumers

More instances → parallel processing

## 2. Increase Partitions

More partitions → more parallelism

## 3. Optimize Processing

-   reduce DB calls
-   async processing
-   batch processing

## 4. Efficient Serialization

-   avoid heavy payloads
-   use JSON/Avro efficiently

## 5. Retry Optimization

-   non-blocking retries
-   retry topics

------------------------------------------------------------------------

# 8. Lag in Multi-Partition Scenario

Partition 0 → Lag = 5\
Partition 1 → Lag = 2\
Partition 2 → Lag = 8

Total lag = 15

------------------------------------------------------------------------

# 9. Monitoring Lag

Tools:

-   Kafka CLI
-   Prometheus + Grafana
-   Kafka UI tools

------------------------------------------------------------------------

## Why Lag Monitoring is Critical

Lag shows:

-   system health
-   consumer performance
-   bottlenecks
-   scaling needs

------------------------------------------------------------------------

# 10. Real Failure Scenario

Consumer down for 1 hour

Producer continues

After restart:

Consumer processes backlog\
Lag reduces gradually

------------------------------------------------------------------------

# 11. Key Takeaways

## Consumer Lag

-   Real Kafka metric
-   Measures backlog
-   Must monitor

------------------------------------------------------------------------

## Producer Lag

-   Not official
-   Means delay in sending

------------------------------------------------------------------------

## Important Rule

Kafka does not lose data because of lag\
It only delays processing

------------------------------------------------------------------------

# Final Understanding

Lag answers:

"How far behind is my consumer?"

