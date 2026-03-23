---
title: Broker
parent: Kafka
nav_order: 5
---

# Broker

A **broker** is the server that stores and manages messages.

Example:

> Kafka Broker

Responsibilities:

-   Store messages
-   Handle producer writes
-   Serve consumer reads

------------------------------------------------------------------------

## Why Brokers Exist

Broker acts as the **central event store**.

Instead of services communicating directly:

> Service A → Service B

We use:

> Service A → Broker → Service B

Benefits:

-   decoupling
-   durability
-   scalability

------------------------------------------------------------------------

## Kafka Broker

A Kafka broker:

-   stores topic partitions
-   accepts producer writes
-   serves consumer reads

Kafka typically runs multiple brokers forming a **cluster**.

Example cluster:

```
Broker 1
Broker 2
Broker 3
```

------------------------------------------------------------------------

## Where Kafka Stores Data

Kafka stores data as **log segment files**.

Example location:

> /var/lib/kafka/data/topic-name/partition-0

Example file:

> 00000000000000000000.log

Kafka appends messages sequentially.

Kafka is essentially a **distributed commit log**.

------------------------------------------------------------------------

## Broker Failure Scenario

If a broker goes down:

> Kafka cluster continues operating.

Example:

> Topic partition replicated

Replica brokers take over.

This is called **replication**.

------------------------------------------------------------------------
