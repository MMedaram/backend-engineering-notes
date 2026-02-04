---
title: Enumeration vs Iterator vs ListIterator
parent: Collections
nav_order: 8
---

# Enumeration vs Iterator vs ListIterator

- **Enumeration** is a **legacy cursor** used with old collections
- **Iterator** is a **modern, universal cursor** for collections
- **ListIterator** is a **powerful iterator** only for lists

---

## Core Difference

| Point | Enumeration | Iterator | ListIterator |
|-----|-------------|----------|--------------|
| Introduced in | Java 1.0 | Java 1.2 | Java 1.2 |
| Used with | Legacy classes | All collections | List only |
| Traversal direction | Forward only | Forward only | Forward & backward |
| Remove element | ❌ No | ✅ Yes | ✅ Yes |
| Add / Update element | ❌ No | ❌ No | ✅ Yes |
| Fail-fast | ❌ No | ✅ Yes | ✅ Yes |
| Replacement status | Deprecated style | Recommended | Advanced use |

---

## Enumeration

- A **legacy cursor**
- Used with old classes like:
    - `Vector`
    - `Hashtable`

### Limitations
- Read-only
- Cannot remove elements
- Not fail-fast

---

## Iterator

-Standard cursor for **all collections**
- Replaces Enumeration

### Characteristics
- Forward-only traversal
- Can remove elements safely
- Fail-fast (throws `ConcurrentModificationException`)

---

## ListIterator

- Special iterator **only for List**
- More powerful than Iterator

### Characteristics
- Bidirectional traversal
- Can add, remove, and update elements
- Provides index information

---

- **Enumeration** → Old & limited
- **Iterator** → Standard & safe
- **ListIterator** → Powerful & list-only


> **Enumeration is a legacy cursor, Iterator is a universal forward iterator, and ListIterator is a list-specific iterator that supports bidirectional traversal and modification.**
