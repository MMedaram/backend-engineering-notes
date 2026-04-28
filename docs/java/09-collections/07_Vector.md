---
title: Vector (C)
parent: Collection Framework
nav_order: 7
---


# Vector

## Overview

- Vector is a **legacy class** in Java that is similar to ArrayList.
- It uses a **growable array** as its underlying data structure.
- Unlike ArrayList, **Vector is synchronized (thread-safe)**.

---

## Key Features

| Feature | Description |
|--------|------------|
| **Data Structure** | Growable (dynamic) array |
| **Thread Safety** | Synchronized (thread-safe by default) |
| **Order** | Insertion order is preserved |
| **Duplicates** | Allowed |
| **Null Values** | Allowed |
| **Heterogeneous Data** | Allowed (if using raw type) |
| **Access** | Supports random access (implements RandomAccess) |

---

## Interfaces Implemented

- `List`
- `RandomAccess`
- `Serializable`
- `Cloneable`

---

## Thread Safety

- All methods in Vector are **synchronized**.
- This makes it **safe for multi-threaded environments**.
- However, it introduces **performance overhead** compared to ArrayList.

---

## Capacity & Growth

- Default initial capacity = **10**
- When capacity is exceeded:
    - New capacity = **(currentCapacity × 2)**

---

## Constructors

| Constructor | Description |
|------------|------------|
| `Vector()` | Creates a vector with default capacity (10) |
| `Vector(int initialCapacity)` | Creates vector with specified initial capacity |
| `Vector(int initialCapacity, int capacityIncrement)` | Custom growth size instead of doubling |
| `Vector(Collection<? extends E> c)` | Creates vector from another collection |

---

## Time Complexity

| Operation | Time Complexity |
|----------|----------------|
| Access (get/set) | O(1) |
| Add (end) | O(1) amortized |
| Insert/Delete (middle) | O(n) |
| Search | O(n) |

---

## When to Use Vector

- When you **need thread-safe operations** without external synchronization
- Legacy systems where Vector is already used

---

## When NOT to Use

- Avoid in modern applications due to:
    - Performance overhead (synchronization)
    - Better alternatives like:
        - `ArrayList` (non-thread-safe, faster)
        - `Collections.synchronizedList()`
        - `CopyOnWriteArrayList`

---

## Quick Comparison

| Feature | Vector | ArrayList |
|--------|--------|----------|
| Thread Safe | Yes | No |
| Performance | Slower | Faster |
| Synchronization | Built-in | External required |
| Usage | Legacy | Modern preferred |

---

## Developer Notes

- Vector is part of **legacy collection classes**
- Rarely used in new codebases
- Prefer **ArrayList + synchronization utilities** instead