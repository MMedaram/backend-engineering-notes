---
title: TreeSet vs TreeMap
parent: Collections
nav_order: 16
---

# TreeSet vs TreeMap

- **TreeSet** stores **unique values** in **sorted order**
- **TreeMap** stores **key–value pairs** in **sorted order by key**

---

## Core Difference

| Point | TreeSet | TreeMap |
|-----|---------|---------|
| Stores | Values only | Key–value pairs |
| Sorting based on | Elements | Keys |
| Duplicate values | ❌ Not allowed | ❌ Keys not allowed |
| Null allowed | ❌ No | ❌ Null keys not allowed |
| Internal structure | Red-Black Tree | Red-Black Tree |
| Time complexity | O(log n) | O(log n) |
| Implements | NavigableSet | NavigableMap |
| Use case | Sorted unique values | Sorted key-based lookup |

---

## TreeSet

A **TreeSet** is a `Set` implementation that stores **unique elements in sorted order**.

### Characteristics
- Sorting is **natural ordering** or via **Comparator**
- Does not allow duplicates
- Does not allow null elements
- Slower than HashSet due to sorting

### When to use
- When you need **unique + sorted values**
- When order matters and duplicates must be avoided

---

## TreeMap

A **TreeMap** is a `Map` implementation that stores entries **sorted by key**.

### Characteristics
- Keys are sorted
- Values can be duplicated
- Does not allow null keys
- Supports range operations (headMap, tailMap)

### When to use
- When you need **sorted keys**
- When range-based queries are required

---


> **TreeSet stores unique elements in sorted order, whereas TreeMap stores key–value pairs sorted by keys.**

