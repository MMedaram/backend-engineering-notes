---
title: HashMap vs LinkedHashMap
parent: Collections
nav_order: 15
---

# HashMap vs LinkedHashMap

- **HashMap** does **not maintain any order**
- **LinkedHashMap** maintains **insertion order** (or access order)

---

## Core Difference

| Point | HashMap | LinkedHashMap |
|-----|--------|---------------|
| Ordering | No order guaranteed | Maintains insertion order |
| Internal structure | Hash table | Hash table + doubly linked list |
| Performance | Slightly faster | Slightly slower |
| Memory usage | Less | More (extra links) |
| Allows null key | ✅ One | ✅ One |
| Allows null values | ✅ Yes | ✅ Yes |
| Use case | Fast lookup | Ordered iteration |
| Introduced in | Java 1.2 | Java 1.4 |

---

## HashMap

A **HashMap** stores key–value pairs using hashing, without maintaining any order.

### Characteristics
- Fast access
- No predictable iteration order
- Most commonly used Map implementation

### When to use
- Order does not matter
- Performance is more important than iteration order

---

## LinkedHashMap

A **LinkedHashMap** is a HashMap that also maintains a **linked list of entries**.

### Characteristics
- Preserves **insertion order** by default
- Can also maintain **access order** (LRU behavior)
- Slightly slower due to extra bookkeeping

### When to use
- When iteration order matters
- When implementing **LRU cache**

---

> **HashMap provides fast key–value access without any order   
> LinkedHashMap maintains insertion or access order using a linked list.**

---

