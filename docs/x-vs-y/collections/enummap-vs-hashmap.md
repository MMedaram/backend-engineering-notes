---
title: EnumMap vs HashMap
parent: Collections
nav_order: 18
---

# EnumMap vs HashMap

- **EnumMap** is a **specialized Map for enum keys** and is very fast
- **HashMap** is a **general-purpose Map** for any object keys

---

## Core Difference

| Point | EnumMap | HashMap |
|-----|--------|---------|
| Key type | Enum only | Any object |
| Internal structure | Array-based | Hash table |
| Performance | Faster | Slower |
| Memory usage | Less | More |
| Allows null key | ❌ No | ✅ One |
| Allows null values | ✅ Yes | ✅ Yes |
| Ordering | Natural enum order | No order guaranteed |
| Thread-safe | ❌ No | ❌ No |

---

## EnumMap

An **EnumMap** is a Map implementation designed **only for enum keys**.

### Characteristics
- Uses array internally → very fast
- Maintains **natural order of enum constants**
- More memory efficient
- Does not allow null keys

### When to use
- Keys are enums
- High performance is required
- Predictable ordering is useful

> “EnumMap is optimized for enum keys and is faster than HashMap.”

---

## HashMap

A **HashMap** is a general-purpose Map implementation.

### Characteristics
- Works with any object as key
- Uses hashing
- Slightly slower than EnumMap
- Allows one null key

### When to use
- Keys are not enums
- General-purpose use cases
- Flexibility is needed

> “HashMap is used when keys are not restricted to enums.”

---


> **EnumMap is a specialized, high-performance map for enum keys, whereas HashMap is a general-purpose map for any object keys.**
