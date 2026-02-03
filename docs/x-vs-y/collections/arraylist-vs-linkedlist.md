---
title: ArrayList vs LinkedList
parent: Collections
nav_order: 2
---

# ArrayList vs LinkedList

- **ArrayList** is backed by a **dynamic array**
- **LinkedList** is backed by a **doubly linked list**

---

## Core Difference

| Point | ArrayList | LinkedList |
|-----|-----------|------------|
| Internal structure | Dynamic array | Doubly linked list |
| Access (get/set) | Fast – O(1) | Slow – O(n) |
| Insertion / deletion (middle) | Slow – O(n) | Fast – O(1)\* |
| Memory usage | Less | More (extra pointers) |
| Cache friendliness | High | Low |
| Implements | List | List, Deque |


---

## ArrayList

An **ArrayList** stores elements in a **contiguous array**.

### Characteristics
- Very fast for **read/access**
- Slower for **insert/delete in middle**
- Automatically resizes when full

### When to use
- Frequent read operations
- Index-based access needed
- Less insert/delete in middle

> “ArrayList is preferred when read operations are more frequent.”

---

## LinkedList

A **LinkedList** stores elements as **nodes**, each pointing to previous and next.

### Characteristics
- Slower access
- Faster insertion/deletion once position is known
- Uses more memory due to pointers

### When to use
- Frequent insert/delete operations
- Queue / Deque operations
- Less random access

---

> - **ArrayList** → Fast access
> - **LinkedList** → Fast insert/delete

---

