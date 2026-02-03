---
title: HashMap vs Hashtable
parent: Collections
nav_order: 4
---

# HashMap vs Hashtable

- **HashMap** is **not synchronized** and is **faster**
- **Hashtable** is **synchronized** and is **slower**

---

## Core Difference 

| Point | HashMap | Hashtable |
|-----|--------|-----------|
| Synchronization | ❌ Not synchronized | ✅ Synchronized |
| Thread-safe | ❌ No | ✅ Yes |
| Performance | Faster | Slower |
| Allows null key | ✅ One null key | ❌ Not allowed |
| Allows null values | ✅ Yes | ❌ Not allowed |
| Introduced in | Java 1.2 | Java 1.0 (Legacy) |
| Iterator type | Fail-fast | Fail-safe (Enumeration) |

---

## HashMap

A **HashMap** stores key–value pairs using a **hash table**, without synchronization.

### Characteristics
- Not thread-safe
- Allows **one null key** and **multiple null values**
- Better performance in single-threaded or controlled multi-threaded environments

### When to use
- Single-threaded applications
- Multi-threaded apps with external synchronization
- Performance is important


---

## Hashtable

A **Hashtable** is a **synchronized map**, meaning all its methods are thread-safe.

### Characteristics
- Thread-safe by default
- Does **not allow null keys or values**
- Slower due to synchronization overhead
- Considered **legacy**

### When to use
- Legacy code only
- Rarely used in modern applications

---

> **HashMap is a non-synchronized, high-performance map that allows nulls, whereas Hashtable is a synchronized legacy map that does not allow null keys or values.**

---
