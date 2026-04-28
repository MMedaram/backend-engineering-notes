---
title: Collections Framework – Hierarchy
parent: Collection Framework
nav_order: 1
---

# Collections Framework – Hierarchy

## 1. Introduction
The Java Collections Framework (JCF) was introduced in **Java 1.2** to provide a unified architecture for storing and manipulating groups of objects.

---

## 2. Root Interfaces

### 2.1 Iterable (Java 1.5)
- Root interface of the collection hierarchy
- Enables use of enhanced for-loop (`for-each`)

### 2.2 Collection (Java 1.2)
- Root interface for most collection types
- Extends `Iterable`

---

## 3. Collection Hierarchy

```
Iterable (1.5)
|
└── Collection (1.2)
├── List (1.2)
│ ├── ArrayList (1.2)
│ ├── LinkedList (1.2)
│ ├── Vector (1.0) [Legacy]
│ └── Stack (1.0) [Legacy]
│
├── Set (1.2)
│ ├── HashSet (1.2)
│ │ └── LinkedHashSet (1.4)
│ ├── SortedSet (1.2)
│ │ └── NavigableSet (1.6)
│ │ └── TreeSet (1.2)
│
└── Queue (1.5)
├── PriorityQueue (1.5)
├── Deque (1.6)
│ ├── ArrayDeque (1.6)
│ └── LinkedList (1.2)
```


---

## 4. Map Hierarchy (Separate from Collection)

```
Map (1.2)
├── HashMap (1.2)
│ └── LinkedHashMap (1.4)
├── Hashtable (1.0) [Legacy]
├── SortedMap (1.2)
│ └── NavigableMap (1.6)
│ └── TreeMap (1.2)
├── WeakHashMap (1.2)
├── IdentityHashMap (1.4)
└── ConcurrentHashMap (1.5)
```


---

## 5. Key Interfaces

| Interface        | Version | Description |
|----------------|--------|------------|
| Iterable       | 1.5    | Supports for-each loop |
| Collection     | 1.2    | Root of collection framework |
| List           | 1.2    | Ordered collection |
| Set            | 1.2    | No duplicates |
| Queue          | 1.5    | FIFO structure |
| Deque          | 1.6    | Double-ended queue |
| Map            | 1.2    | Key-value pairs |

---

## 6. Important Implementations

| Class                | Version | Notes |
|---------------------|--------|------|
| ArrayList           | 1.2    | Dynamic array |
| LinkedList          | 1.2    | Doubly linked list |
| Vector              | 1.0    | Synchronized (legacy) |
| Stack               | 1.0    | LIFO (legacy) |
| HashSet             | 1.2    | Uses HashMap internally |
| LinkedHashSet       | 1.4    | Maintains insertion order |
| TreeSet             | 1.2    | Sorted set |
| PriorityQueue       | 1.5    | Heap-based queue |
| ArrayDeque          | 1.6    | Faster than Stack/LinkedList |
| HashMap             | 1.2    | Most used map |
| LinkedHashMap       | 1.4    | Ordered map |
| TreeMap             | 1.2    | Sorted map |
| Hashtable           | 1.0    | Legacy synchronized map |
| ConcurrentHashMap   | 1.5    | Thread-safe high performance |

---

## 7. Legacy Classes (Before Collections Framework)

| Class       | Version | Remarks |
|------------|--------|--------|
| Vector     | 1.0    | Replaced by ArrayList |
| Stack      | 1.0    | Use Deque instead |
| Hashtable  | 1.0    | Replaced by HashMap |

---

## 8. Notable Enhancements by Java Version

### Java 1.2
- Introduced Collections Framework
- List, Set, Map, ArrayList, HashMap, etc.

### Java 1.4
- LinkedHashMap, LinkedHashSet
- IdentityHashMap

### Java 1.5 (Generics + Concurrency)
- Iterable
- Queue, PriorityQueue
- ConcurrentHashMap

### Java 1.6
- Deque, ArrayDeque
- NavigableSet, NavigableMap

### Java 1.8
- Streams API
- Default methods (e.g., `forEach`)
- `compute()`, `merge()` in Map

---

## 9. Summary

- **Collection** → List, Set, Queue
- **Map** → Separate hierarchy
- Legacy classes exist but should be avoided
- Modern Java favors:
    - `ArrayList`
    - `HashMap`
    - `ConcurrentHashMap`
    - `ArrayDeque`

---

## 10. Quick Points

- Map is NOT part of Collection hierarchy
- LinkedList implements both List and Deque
- HashSet internally uses HashMap
- TreeSet & TreeMap are sorted (Red-Black Tree)
- ConcurrentHashMap is preferred over Hashtable

