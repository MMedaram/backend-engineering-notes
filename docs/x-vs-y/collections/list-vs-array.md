---
title: List vs Array
parent: Collections
nav_order: 7
---

# List vs Array

- **Array** has a **fixed size** and stores elements of the **same type**
- **List** has a **dynamic size** and provides **rich APIs**

---

## Core Difference

| Point | Array | List |
|-----|------|------|
| Size | Fixed | Dynamic (resizable) |
| Data type | Primitives & Objects | Objects only |
| Part of Collections Framework | ❌ No | ✅ Yes |
| Insertion / deletion | Manual & costly | Easy using methods |
| Built-in methods | Very limited | Rich API |
| Type safety | Weak (covariant) | Strong (generics) |
| Performance | Faster | Slightly slower |

---

## Array

An **Array** is a basic data structure that stores elements in **contiguous memory** with a **fixed length**.

### Characteristics
- Size must be known in advance
- Can store **primitive types** (int, char, etc.)
- Faster due to less overhead

### When to use
- Fixed-size data
- Performance-critical code
- Storing primitives

---

## List

A **List** is part of the **Collections Framework** that stores elements dynamically.

### Characteristics
- Size can grow or shrink
- Stores objects (uses wrapper classes for primitives)
- Provides many utility methods

### When to use
- Dynamic data
- Frequent insertions/deletions
- Need collection utilities

---

> **Array is a fixed-size data structure that can store primitives and objects, whereas List is a dynamic collection that stores objects and provides rich APIs.**
