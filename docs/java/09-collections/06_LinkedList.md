---
title: LinkedList(C)
parent: Collection Framework
nav_order: 6
---

# LinkedList

---

## 1. Overview

- Doubly linked list implementation of `List`
- Also implements `Deque`
- Introduced in Java 1.2

---

## 2. Key Points

- Maintains insertion order
- Allows duplicates and nulls
- No fast random access (O(n))
- Efficient insert/delete at ends
- Not thread-safe

### Implements
- List
- Deque
- Cloneable
- Serializable

### Extends
- AbstractSequentialList

---

## 3. Constructors

| Constructor | Description | Behavior |
|------------|------------|----------|
| LinkedList() | Default | Empty list |
| LinkedList(Collection<? extends E> c) | From collection | Copies elements |

---

## 4. Internal Structure

Node structure:
- data
- next
- prev

Doubly linked list → each element connected to both sides

---

## 5. Class-Specific Methods (Deque)

| Method | Description | Behavior |
|--------|------------|----------|
| addFirst(E e) | Add at beginning | O(1) |
| addLast(E e) | Add at end | O(1) |
| removeFirst() | Remove first | O(1) |
| removeLast() | Remove last | O(1) |
| getFirst() | Get first | Exception if empty |
| getLast() | Get last | Exception if empty |
| offerFirst(E e) | Safe add | Returns false if fails |
| pollFirst() | Safe remove | Returns null if empty |

---

## 6. Behavior Characteristics

| Operation | Behavior |
|----------|---------|
| get(index) | Traverses nodes (slow) |
| add(index) | Traversal + insert |
| remove(index) | Traversal + unlink |
| add/remove ends | Fast |

---

## 7. Performance

| Operation | Complexity |
|----------|-----------|
| get | O(n) |
| add/remove ends | O(1) |
| add/remove middle | O(n) |
| search | O(n) |

---

## 8. Important Scenarios

### Fast Head/Tail Operations
- addFirst()
- removeLast()

- get(index) → costly

### Memory Overhead
- Each node has extra references

---

## 9. Fail-Fast Behavior

- Uses modCount
- Modification during iteration → ConcurrentModificationException

---

## 10. Usage Guidance

### Use when:
- Frequent insert/delete at ends
- Queue / Deque usage

### Avoid when:
- Frequent random access
- Large datasets (memory heavy)

---

## 11. Important Insights

- No RandomAccess interface
- Traversal optimization:
  - Starts from head or tail based on index
- Rarely used compared to ArrayList

---

## 12. Mental Model

LinkedList = Fast insert/delete, slow access, more memory
