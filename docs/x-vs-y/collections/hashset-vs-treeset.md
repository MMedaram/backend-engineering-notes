---
title: HashSet vs TreeSet
parent: Collections
nav_order: 3
---

# HashSet vs TreeSet

- **HashSet** stores elements using a **hash table**
- **TreeSet** stores elements in a **sorted (red-black tree) structure**

---

## Core Difference 

| Point | HashSet | TreeSet |
|-----|---------|---------|
| Internal structure | Hash table | Red-Black Tree |
| Ordering | No order guaranteed | Sorted order |
| Sorting | ❌ No | ✅ Yes |
| Duplicate elements | ❌ Not allowed | ❌ Not allowed |
| Null values | One null allowed | ❌ Not allowed |
| Performance | Faster | Slower |
| Time complexity | O(1) average | O(log n) |
| Implements | Set | NavigableSet |

---

## HashSet

A **HashSet** stores elements based on their **hash code**.

### Characteristics
- No guaranteed order
- Very fast operations
- Allows one `null` value

### When to use
- When **order does not matter**
- When performance is critical
- When you only care about **uniqueness**

---

## TreeSet

A **TreeSet** stores elements in a **sorted order**.

### Characteristics
- Maintains **natural ordering** or **custom comparator**
- Slower than HashSet
- Does not allow `null`

### When to use
- When **sorted output** is required
- When range-based operations are needed
- When navigation methods are useful

---

- **HashSet** → Fast, unordered
- **TreeSet** → Sorted, slower

