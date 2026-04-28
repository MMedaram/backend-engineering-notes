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

| Feature | ArrayList | LinkedList |
|--------|----------|------------|
| **Structure** | Index-based data structure where each element has an index | Node-based structure; each node contains previous reference, value, and next reference |
| **Insertion & Removal** | Slow in the middle due to shifting elements | Fast from any position; only references are updated |
| **Time Complexity (Insert/Delete)** | O(n) | O(1) |
| **Element Retrieval** | Fast due to direct index access | Slow; requires traversal |
| **Time Complexity (Access)** | O(1) | O(n) |
| **Access Type** | Supports random access | Does not support random access |
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

