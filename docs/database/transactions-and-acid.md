---
title: Transaction Fundamentals & ACID Principles
parent: Database
nav_order: 3
---

# Transaction Fundamentals & ACID Principles

A **transaction** is a sequence of operations performed as a **single logical unit of work**.
Transactions ensure that a system remains **consistent and reliable**, even in the presence
of failures such as crashes, errors, or concurrent access.

---

## What Is a Transaction?

> A transaction is a group of database operations that must either **all succeed** or
**all fail**.

### Example 

Transfer ₹1000 from Account A to Account B:

1. Debit ₹1000 from Account A
2. Credit ₹1000 to Account B

✔ Both must succeed  
❌ Partial success is unacceptable    

---

## Why Transactions Are Needed

Without transactions:
- Data corruption can occur
- Partial updates may be persisted
- Concurrent users can overwrite data
- System consistency cannot be guaranteed

Transactions provide:
- Reliability
- Consistency
- Fault tolerance
- Safe concurrency

---

## Transaction Lifecycle

BEGIN   
→ Execute operations    
→ COMMIT (success)    
→ ROLLBACK (failure)     
END    

---

## ACID Principles

ACID defines **four fundamental properties** that every reliable transaction must satisfy:


A → Atomicity    
C → Consistency    
I → Isolation    
D → Durability      


---

## 1️⃣ Atomicity

### Definition
> A transaction must be **all-or-nothing**.

Either:
- All operations succeed → COMMIT
- Any operation fails → ROLLBACK


### Example

```text
Debit Account A ✔     
Credit Account B ❌
```

Without atomicity:
- Money disappears ❌

With atomicity:
- Debit is rolled back ✔

### Key Points

- Partial updates are never visible
- Rollback ensures safety
- Managed by transaction manager

---

## 2️⃣ Consistency

> A transaction must take the database from one valid state to another valid state.

### What Consistency Ensures

- Constraints are not violated
- Business rules are preserved
- Referential integrity is maintained

### Example

Rules:

- Balance cannot be negative
- Account must exist

After transaction:

- All rules must still hold

> Consistency is a responsibility of both the database AND the application logic.

---

## 3️⃣ Isolation

> Concurrent transactions must not interfere with each other.

Each transaction should behave as if it is running alone.    

### Problem Without Isolation

Two transactions read same balance:

- Both withdraw money
- Resulting balance becomes incorrect ❌

| Level            | Behavior                     |
| ---------------- | ---------------------------- |
| Read Uncommitted | Dirty reads allowed          |
| Read Committed   | No dirty reads               |
| Repeatable Read  | Same data remains consistent |
| Serializable     | Full isolation               |


### Trade-off

Strong isolation → lower performance   

Weak isolation → higher throughput

---

## 4️⃣ Durability

> Once a transaction is committed, the data must persist, even if the system crashes.

### Example

After:

> COMMIT;


Even if:

- Power failure
- Server crash
- Data must remain safe      

---

## ACID in Microservices Context (Preview)

- Single DB transaction → easy ACID

- Multiple services → ACID becomes hard

- Leads to:
  - Saga pattern
  - Eventual consistency
  - Compensating transactions


