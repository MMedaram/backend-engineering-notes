---
title: Vector vs ArrayList
parent: Collections
nav_order: 14
---

# Vector vs ArrayList

- **Vector** is **synchronized (thread-safe)** and **slower**
- **ArrayList** is **not synchronized** and **faster**

---

## Core Difference

| Point | Vector | ArrayList |
|-----|--------|-----------|
| Synchronization | ✅ Synchronized | ❌ Not synchronized |
| Thread-safe | ✅ Yes | ❌ No |
| Performance | Slower | Faster |
| Introduced in | Java 1.0 | Java 1.2 |
| Legacy | ✅ Yes | ❌ No |

---

## Vector

`Vector` is a **legacy dynamic array** where **all methods are synchronized**.

### Characteristics
- Thread-safe by default
- Slower due to synchronization overhead
- Rarely used in modern applications

### When to use
- Very rare
- Only in **legacy code**

---

## ArrayList

`ArrayList` is a **modern dynamic array** that is **not synchronized**.

### Characteristics
- Faster than Vector
- Not thread-safe
- Most commonly used List implementation

### When to use
- Single-threaded applications
- Multi-threaded apps with external synchronization
- When performance matters

---


> **Vector is a synchronized legacy collection, whereas ArrayList is a non-synchronized, high-performance list used in modern Java.**

