---
title: List Interface
parent: Collection Framework
nav_order: 4
---

# List Interface

----

## 1. What is List?

- Child of Collection
- Represents an ordered collection
- Allows duplicates
- Supports index-based access

## 2. Key Points

- Maintains insertion order
- Allows duplicate elements
- Allows multiple nulls (implementation dependent but usually yes)
- Supports index-based operations
- Introduced in Java 1.2
- Common Implementations
  - ArrayList
  - LinkedList
  - Vector (legacy)
  - Stack (legacy)

## 3. Core Methods

-------------

### 3.1 Add operations

| Method Signature                                       | Description            | Behavior / Edge Case                                               |
| ------------------------------------------------------ | ---------------------- | ------------------------------------------------------------------ |
| `boolean add(E e)`                                     | Adds element at end    | Always returns true (except rare cases like capacity restrictions) |
| `void add(int index, E element)`                       | Adds at specific index | ❌ `IndexOutOfBoundsException` if index invalid                     |
| `boolean addAll(Collection<? extends E> c)`            | Adds all elements      | Returns true if at least one element added                         |
| `boolean addAll(int index, Collection<? extends E> c)` | Adds at index          | Shifts existing elements                                           |


### 3.2 Remove operations

| Method Signature                     | Description               | Behavior / Edge Case                |
| ------------------------------------ | ------------------------- | ----------------------------------- |
| `boolean remove(Object o)`           | Removes first occurrence  | Returns false if not found          |
| `E remove(int index)`                | Removes by index          | ❌ Throws exception if index invalid |
| `boolean removeAll(Collection<?> c)` | Removes matching elements | Removes only matching ones          |
| `void clear()`                       | Removes all elements      | List becomes empty                  |


### 3.3 Read / Access

| Method Signature              | Description      | Behavior / Edge Case      |
| ----------------------------- | ---------------- | ------------------------- |
| `E get(int index)`            | Fetch element    | ❌ Index check required    |
| `E set(int index, E element)` | Replace element  | Returns old value         |
| `int indexOf(Object o)`       | First occurrence | Returns `-1` if not found |
| `int lastIndexOf(Object o)`   | Last occurrence  | Useful for duplicates     |

### 3.4 Check / Search

| Method Signature                       | Description         | Behavior / Edge Case      |
| -------------------------------------- | ------------------- | ------------------------- |
| `boolean contains(Object o)`           | Checks existence    | Uses `equals()`           |
| `boolean containsAll(Collection<?> c)` | Checks all elements | Fails if even one missing |

### 3.5 Traversal

| Method Signature                          | Description       | Behavior / Edge Case         |
| ----------------------------------------- | ----------------- | ---------------------------- |
| `Iterator<E> iterator()`                  | Forward iteration | Fail-fast                    |
| `ListIterator<E> listIterator()`          | Bi-directional    | Can move forward/backward    |
| `ListIterator<E> listIterator(int index)` | Start from index  | Useful for partial traversal |


### 3.6 Sublist

| Method Signature                              | Description             | Behavior / Edge Case                                    |
| --------------------------------------------- | ----------------------- | ------------------------------------------------------- |
| `List<E> subList(int fromIndex, int toIndex)` | Returns portion of list | ⚠️ Backed by original list (changes reflect both sides) |


------------------------------




## Inherited from Collection

These are common for all collections:

| Category   | Methods                                                     |
| ---------- | ----------------------------------------------------------- |
| Add        | `add(E e)`, `addAll(Collection c)`                          |
| Remove     | `remove(Object o)`, `removeAll()`, `retainAll()`, `clear()` |
| Query      | `size()`, `isEmpty()`, `contains()`                         |
| Bulk       | `containsAll()`                                             |
| Traversal  | `iterator()`                                                |
| Conversion | `toArray()`                                                 |
| Java 8+    | `stream()`, `forEach()`, `removeIf()`                       |


## Specific to List (Index-Based)

| Category  | Methods                                     |
| --------- | ------------------------------------------- |
| Add       | `add(int index, E element)`                 |
| Add Bulk  | `addAll(int index, Collection c)`           |
| Remove    | `remove(int index)`                         |
| Access    | `get(int index)`                            |
| Update    | `set(int index, E element)`                 |
| Search    | `indexOf()`, `lastIndexOf()`                |
| Traversal | `listIterator()`, `listIterator(int index)` |
| View      | `subList(int from, int to)`                 |

