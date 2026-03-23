---
title: Message
parent: Kafka
nav_order: 2
---

## Messaging

Every messaging platform (Kafka, RabbitMQ, ActiveMQ, etc.) uses the same
fundamental components.

Core messaging architecture:

> Producer → Broker → Consumer

The **message** is the data flowing through the system.

------------------------------------------------------------------------

## Message

A **message** is a piece of information sent from one system to another
through a messaging platform.

It usually represents **an event or business data that needs to be
processed**.

Example:

``` json
{
  "eventType": "OrderCreated",
  "orderId": "ORD123",
  "amount": 500
}
```

------------------------------------------------------------------------

## Why Messages Exist

Messages allow systems to communicate **asynchronously** and **loosely
coupled**.

Instead of direct service calls:

> Order Service → HTTP → Payment Service

We send a message:

> Order Service → Message → Broker → Payment Service

Benefits:

-   services don't depend on each other availability
-   better scalability
-   asynchronous processing
-   improved reliability

------------------------------------------------------------------------

## Message Structure (Best Practice)

Good event messages normally contain:

| Field     | Purpose             |
| --------- | ------------------- |
| eventType | What happened       |
| eventId   | Unique ID           |
| timestamp | When event happened |
| payload   | Business data       |


Example:

``` json
{
  "eventId": "12345",
  "eventType": "CustomerCreated",
  "timestamp": "2026-03-14T10:10:00Z",
  "payload": {
    "customerId": "C001",
    "name": "John"
  }
}
```

------------------------------------------------------------------------

## Where Messages Are Used

### Banking systems

CustomerCreated\
AccountCreated\
MoneyTransferred\
TransactionCompleted

### E‑commerce systems

OrderCreated\
PaymentCompleted\
OrderShipped

------------------------------------------------------------------------

## Message Design Best Practices

### 1. Use meaningful event names

Good:

**order.created\
payment.completed\
customer.registered**

Avoid:

event1\
data1

------------------------------------------------------------------------

### 2. Include event version

Example:

**customer.created.v1**

This prevents breaking changes when event structure evolves.

------------------------------------------------------------------------

### 3. Keep messages small

Messaging systems are designed for **event metadata**, not large
payloads.

Bad example:

> Sending entire PDF document

Better:

> Send documentId 
> Store document in external storage.

------------------------------------------------------------------------

## Where Messages Are Stored (Kafka Preview)

Kafka stores messages as **log files on disk**.

- based on `log.dirs` in `server.properties` file under config. 

Example directory:

> /var/lib/kafka/data


Each partition is stored as an **append‑only log**.

------------------------------------------------------------------------



