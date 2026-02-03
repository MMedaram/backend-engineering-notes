---
title: Microservices Transactional Boundaries
parent: Microservices Architecture
nav_order: 11
---

# Microservices Transactional Boundaries

In a microservices architecture, a **transactional boundary** defines
the scope within which a transaction is executed and guaranteed to be consistent.

> **Key rule:**  
> In microservices, a transaction boundary must NOT cross service boundaries.

---

## What Is a Transactional Boundary?

A transactional boundary determines:
- Where a transaction starts
- Where it ends
- Which data is guaranteed to be consistent

Inside the boundary:
- ACID guarantees apply

Outside the boundary:
- ACID guarantees do NOT apply

---

## Transaction Boundaries in Monolith vs Microservices

### Monolithic Architecture

``` text 
Service A
├── DB Table 1
├── DB Table 2
└── DB Table 3
```

✔ Single transaction     
✔ ACID across all operations     

---

### Microservices Architecture

Order Service → Order DB    
Payment Service → Payment DB     
Inventory Service → Inventory DB     


> ✔  Each service has its own transaction     
> ❌  No global transaction across services      

---

## Why Transactions Cannot Span Microservices

Distributed ACID transactions:
- Require 2-phase commit (2PC)
- Introduce tight coupling
- Cause performance bottlenecks
- Reduce availability
- Are hard to recover on failures

> **Microservices favor availability and scalability over global consistency.**

---

## Correct Transaction Boundary Rule 

> **One service = one database = one transaction boundary**

This rule ensures:
- Loose coupling
- Independent scalability
- Independent failure handling

---

## Example: Banking Transfer (Microservices)

### Scenario
Transfer money from Account A to Account B

### Services Involved
- Account Service
- Transaction Service
- Notification Service

---

### ❌ Wrong Approach (Distributed Transaction)

```text
Begin Transaction
  Debit Account Service
  Credit Account Service
  Send Notification
Commit Transaction
```

Problems:

- Network failures
- Partial commits
- Blocking locks
- Poor scalability

### ✅ Correct Microservices Approach

```text
1. Account Service:
   - Debit Account
   - Commit transaction

2. Publish Event: MoneyDebited

3. Transaction Service:
   - Record transaction
   - Commit transaction

4. Notification Service:
   - Send notification
```

✔ Each service has its own transaction     
✔ System becomes eventually consistent      

## Eventual Consistency (Key Concept)

Because transactions don’t span services:

- System becomes eventually consistent
- Temporary inconsistencies are allowed
- Final state converges

> This is a designed behavior, not a bug.

## Handling Failures Across Boundaries

Since rollback across services is not possible, we use:

- Saga Pattern
- Compensating transactions
- Retries
- Idempotency

Example:

- If credit fails → compensate debit


```java 
@Transactional
public void placeOrder() {
    callPaymentService();
    callInventoryService();
}
```

❌ This does NOT create a distributed transaction    
✔ It only applies to the local database     

---

## Best Practices (Enterprise Grade)

✔ Keep transactions short    
✔ Do not include remote calls in transactions    
✔ Commit before publishing events     
✔ Design APIs to be idempotent     
✔ Embrace eventual consistency     

> In microservices, transactional boundaries are limited to individual services. Each service manages its own transaction and database, and consistency across services is achieved using eventual consistency patterns like sagas instead of distributed ACID transactions.
