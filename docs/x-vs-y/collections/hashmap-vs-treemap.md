---
title: HashMap vs TreeMap
parent: Collections
nav_order: 6
---

# HashMap vs TreeMap

- **HashMap** stores entries in **no particular order**
- **TreeMap** stores entries in **sorted order**

---

## Core Difference

| Point | HashMap | TreeMap |
|-----|--------|---------|
| Internal structure | Hash table | Red-Black Tree |
| Ordering | No order guaranteed | Sorted order |
| Sorting | ❌ No | ✅ Yes |
| Time complexity | O(1) average | O(log n) |
| Allows null key | ✅ One null key | ❌ Not allowed |
| Allows null values | ✅ Yes | ✅ Yes |
| Thread-safe | ❌ No | ❌ No |
| Implements | Map | NavigableMap |

---

## HashMap

A **HashMap** stores key–value pairs using hashing.

### Characteristics
- Very fast for basic operations
- Does not maintain any order
- Allows one null key and multiple null values

### When to use
- When **performance** is more important than ordering
- When you don’t care about sorted keys

---

## TreeMap

A **TreeMap** stores key–value pairs in a **sorted structure**.

### Characteristics
- Keys are sorted using **natural order** or **Comparator**
- Slower than HashMap
- Does not allow null keys

### When to use
- When **sorted keys** are required
- When range queries are needed (headMap, tailMap)

---

> - **HashMap** → Fast, unordered    
> - **TreeMap** → Sorted, slower

---
