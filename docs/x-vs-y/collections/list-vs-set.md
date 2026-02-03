---
title: List vs Set
parent: Collections
nav_order: 1
---

# List vs Set

- **List** allows **duplicate elements** and maintains **insertion order**
- **Set** does **not allow duplicates** and does **not guarantee order**

---

## Core Difference

| Point | List | Set |
|-----|------|-----|
| Duplicates | Allowed | Not allowed |
| Order | Maintains insertion order | No guaranteed order |
| Index-based access | ✅ Yes | ❌ No |
| Allows null | ✅ Yes | At most one null |
| Performance | Faster access by index | Faster uniqueness check |
| Common implementations | ArrayList, LinkedList | HashSet, TreeSet |

---

## List

A **List** is an ordered collection that:
- Allows duplicate values
- Preserves insertion order
- Supports index-based access

### When to use
- When order matters
- When duplicates are allowed
- When positional access is needed

---

## Set

A **Set** is a collection that:
- Stores **unique elements**
- Does not allow duplicates
- Does not support index-based access

### When to use
- When uniqueness matters
- When duplicates must be avoided
- When fast lookup is required

---

> **List** → Order + Duplicates     
> **Set** → Unique elements

