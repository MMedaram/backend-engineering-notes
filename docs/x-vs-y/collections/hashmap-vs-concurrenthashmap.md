---
title: HashMap vs ConcurrentHashMap
parent: Collections
nav_order: 5
---

- **HashMap** is **not thread-safe**
- **ConcurrentHashMap** is **thread-safe and designed for concurrency**

---

## Core Difference

| Point | HashMap | ConcurrentHashMap |
|-----|--------|-------------------|
| Thread safety | ❌ Not thread-safe | ✅ Thread-safe |
| Synchronization | No synchronization | Internal fine-grained locking |
| Performance (multi-thread) | Poor / unsafe | High |
| Allows null key | ✅ One null key | ❌ Not allowed |
| Allows null values | ✅ Yes | ❌ Not allowed |
| Iterator type | Fail-fast | Weakly consistent |
| Introduced in | Java 1.2 | Java 5 |

---

## HashMap

A **HashMap** stores key–value pairs using a hash table and is **not synchronized**.

### Characteristics
- Fast in single-threaded scenarios
- Not safe in concurrent environments
- Can cause **data inconsistency** if modified by multiple threads

### When to use
- Single-threaded applications
- Read-heavy scenarios with no concurrent writes
- When performance is critical and thread safety is not needed


---

## ConcurrentHashMap

A **ConcurrentHashMap** is a thread-safe map designed for **high concurrency**.

### Characteristics
- Allows concurrent read and write operations
- Uses **internal locking (bucket-level / CAS)**
- No null keys or values (avoids ambiguity)
- Iterators do not throw `ConcurrentModificationException`

### When to use
- Multi-threaded applications
- Concurrent read and write operations
- High-performance, thread-safe scenarios

---
### How ConcurrentHashMap Actually Works
#### 1️⃣ Read operations

- NOT synchronized
- Multiple threads can read at the same time
- Very fast

#### Examples:

- get()
- containsKey()
- size() (approximate)

👉 Reads do not block writes.

#### 2️⃣ Write operations

- Partially synchronized
- Locks only a small portion (bucket/bin), not the whole map
- Uses CAS (Compare-And-Swap) internally

##### Examples:

- put()
- remove()
- compute()
- putIfAbsent()     

👉 One thread writing to key A does not block another thread writing to key B.

---

| Map Type          | Locking Style               |
| ----------------- | --------------------------- |
| Hashtable         | Entire map locked           |
| SynchronizedMap   | Entire map locked           |
| ConcurrentHashMap | Bucket-level / fine-grained |



---
> **HashMap is not thread-safe and suitable for single-threaded use, whereas ConcurrentHashMap is thread-safe and allows concurrent access with high performance.**

