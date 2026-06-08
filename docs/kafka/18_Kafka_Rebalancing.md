---
title: Kafka Rebalancing
parent: Kafka
nav_order: 18
---

# Kafka Rebalancing

## What is Rebalancing?

Rebalancing is the process where Kafka redistributes partition ownership among consumers within the same consumer group whenever group membership or partition assignments change.

Kafka automatically manages rebalancing through the Consumer Group Coordinator.

---

## Why Rebalancing Exists

Kafka partitions are used for parallel processing.

Example:

Topic: Orders

Partitions:

* P0
* P1
* P2
* P3

Consumer Group:

* Consumer-1
* Consumer-2

Kafka distributes partitions among consumers:

Consumer-1:

* P0
* P1

Consumer-2:

* P2
* P3

This allows workload sharing and horizontal scaling.

---

## Events That Trigger Rebalancing

### 1. New Consumer Joins

Before:

Consumer-1 -> P0, P1, P2, P3

After Consumer-2 joins:

- Consumer-1 -> P0, P1
- Consumer-2 -> P2, P3

---

### 2. Consumer Leaves or Crashes

Before:

- Consumer-1 -> P0, P1
- Consumer-2 -> P2, P3

Consumer-2 crashes

After Rebalance:

Consumer-1 -> P0, P1, P2, P3

---

### 3. Partitions Increased

Before:

P0, P1, P2

After:

P0, P1, P2, P3, P4

Kafka redistributes partitions.

---

### 4. Consumer Restart

When consumers restart, Kafka may rebalance ownership.

---

## Who Performs Rebalancing?

Not the developer.

Kafka uses a special broker called the Group Coordinator.

Responsibilities:

* Consumer registration
* Heartbeat monitoring
* Partition assignment
* Offset tracking
* Rebalancing

---

## Heartbeat Mechanism

Consumers continuously send heartbeats.

Consumer -> Heartbeat
Consumer -> Heartbeat

**If Kafka does not receive heartbeats within:**

session.timeout.ms

**Kafka assumes the consumer is dead and triggers a rebalance.** 

---

## Important Rule

Within a Consumer Group:

One partition can be assigned to only one consumer.

Example:

Topic:

* P0
* P1
* P2

Group:

Consumer-1 -> P0
Consumer-2 -> P1
Consumer-3 -> P2

No partition is shared between consumers in the same group.

---

## Consumer Design Rule

All consumers in the same group must be capable of processing any partition.

Bad Design:

Consumer-1 -> Savings Accounts
Consumer-2 -> Current Accounts

Kafka can move partitions during rebalance, causing failures.

Good Design:

All consumer instances run identical logic and can process any assigned partition.

---

## Rebalancing and Capacity

Kafka does not consider:

* CPU
* Memory
* Server size

during partition assignment.

Kafka only considers:

* Number of consumers
* Number of partitions
* Assignment strategy

---

## Handling Higher Capacity Servers

Common Production Approach:

Large Server:

* Consumer-A
* Consumer-B
* Consumer-C

Small Server:

* Consumer-D

Kafka sees 4 consumers and naturally assigns more partitions to the larger server.

This is the preferred scaling strategy.

---

## Partition Assignment Strategies

Kafka supports:

* RangeAssignor
* RoundRobinAssignor
* StickyAssignor
* CooperativeStickyAssignor

Recommended:

CooperativeStickyAssignor

Benefits:

* Fewer partition movements
* Reduced downtime
* Smoother rebalances

---

## Rebalancing Impact

During rebalance:

* Consumption pauses temporarily
* Partitions are revoked
* New assignments are calculated
* Consumption resumes

Frequent rebalancing can reduce throughput.

---

## Consumer Groups vs Partitions

Partitions divide workload.

Consumer Groups duplicate consumption.

Example:

Topic: account-events

Message stored in Partition-1

Consumer Groups:

* NotificationGroup
* AuditGroup
* ReportingGroup

All three groups receive the same event.

Each group maintains its own offsets.

---

## Offset Storage

Offsets are stored per:

(Group, Topic, Partition)

Example:

NotificationGroup:

* P0 -> Offset 100

AuditGroup:

* P0 -> Offset 50

Same partition.
Different consumer groups.
Different offsets.

---

## Interview Summary

Rebalancing is the automatic process by which Kafka redistributes partitions among consumers within the same consumer group when consumers join, leave, fail, or partitions change. 

The **Group Coordinator** manages this process using heartbeats and partition assignment strategies. Consumers within a group must be stateless and capable of processing any assigned partition because ownership can change at any time during a rebalance.
