---
title: Set (I)
parent: Collection Framework
nav_order: 8
---

# Set Interface 

## Overview

- `Set` is a **collection that does NOT allow duplicate elements**.
- Models a **mathematical set**.
- Part of `java.util` package.
- Extends the `Collection` interface.

---

## Key Characteristics

| Feature | Description |
|--------|------------|
| **Duplicates** | Not allowed |
| **Order** | Depends on implementation (HashSet: no order, LinkedHashSet: insertion order, TreeSet: sorted) |
| **Null Values** | At most one null (HashSet, LinkedHashSet), not allowed in TreeSet |
| **Index-based access** | Not supported |
| **Underlying Structure** | Depends on implementation |

---

## Common Implementations

| Implementation | Description |
|---------------|------------|
| **HashSet** | Uses hash table, no ordering |
| **LinkedHashSet** | Maintains insertion order |
| **TreeSet** | Stores elements in sorted order (Red-Black Tree) |

---

## Important Rules

- Duplicate detection uses:
    - `equals()` method
    - `hashCode()` (for hash-based sets)
- If both are not properly overridden → unexpected behavior

---

## Set-Specific Methods

> Note: Set does NOT introduce many new methods beyond Collection.  
> Most behavior differences are **contract-based (no duplicates)**.

### Core Method

| Method Signature | Description | Behavior / Edge Case |
|------------------|------------|----------------------|
| `boolean add(E e)` | Adds element to set | Returns `false` if element already exists (no duplicate insertion) |

---

## Additional Behavioral Methods (Important in Practice)

| Method Signature | Description | Behavior / Edge Case |
|------------------|------------|----------------------|
| `boolean equals(Object o)` | Compares two sets | Returns true if both sets contain same elements (order doesn't matter) |
| `int hashCode()` | Returns hash code of set | Based on elements, not order |

---

## Specialized Methods in Implementations

### TreeSet (Sorted Set behavior)

| Method Signature | Description | Behavior / Edge Case |
|------------------|------------|----------------------|
| `E first()` | Returns first (lowest) element | Throws exception if empty |
| `E last()` | Returns last (highest) element | Throws exception if empty |
| `E lower(E e)` | Returns greatest element < e | Returns null if none |
| `E higher(E e)` | Returns smallest element > e | Returns null if none |
| `E floor(E e)` | Returns ≤ e | Returns null if none |
| `E ceiling(E e)` | Returns ≥ e | Returns null if none |

---

## Performance (General)

| Operation | HashSet | LinkedHashSet | TreeSet |
|----------|--------|---------------|--------|
| Add | O(1) | O(1) | O(log n) |
| Remove | O(1) | O(1) | O(log n) |
| Contains | O(1) | O(1) | O(log n) |

---

## When to Use

- When **uniqueness of elements is required**
- Fast lookup → use `HashSet`
- Maintain insertion order → use `LinkedHashSet`
- Sorted data → use `TreeSet`

---

## Developer Notes

- Always override:
    - `equals()`
    - `hashCode()`
- Avoid mutable objects as keys → may break set behavior
- No index → use iterator or enhanced for-loop
