---
title: Set vs Map
parent: Collections
nav_order: 9
---

# Set vs Map

- **Set** stores **only values** and does **not allow duplicates**
- **Map** stores **key–value pairs**, where keys are **unique**

---

## Core Difference

| Point | Set | Map |
|-----|-----|-----|
| Stores | Values only | Key–value pairs |
| Duplicates | ❌ Not allowed | ❌ Keys not allowed |
| Key–value concept | ❌ No | ✅ Yes |
| Allows null | One null (depends on impl) | One null key (HashMap), many null values |
| Common implementations | HashSet, TreeSet | HashMap, TreeMap |
| Use case | Uniqueness | Fast lookup by key |

---

## Set

A **Set** is a collection that stores **unique elements only**.

### Characteristics
- No duplicate elements
- No concept of key
- Order depends on implementation

### When to use
- When **uniqueness matters**
- When duplicates must be avoided
- When order is not important (or sorted, if TreeSet)

---

## Map

A **Map** stores data in **key–value pairs**.

### Characteristics
- Keys must be unique
- Values can be duplicated
- Fast lookup using keys

### When to use
- When you need to **associate one value with another**
- When fast searching by key is required

---

> **Set stores unique values without keys, whereas Map stores key–value pairs with unique keys.**

