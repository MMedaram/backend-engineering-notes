---
title: Fail-fast vs Fail-safe Iterator
parent: Collections
nav_order: 13
---

# Fail-fast vs Fail-safe Iterator


- **Fail-fast iterator** throws an exception when the collection is modified
- **Fail-safe iterator** works on a copy and does not throw an exception

---

## Core Difference

| Point | Fail-fast | Fail-safe |
|-----|----------|-----------|
| Behavior on modification | Throws exception | Continues safely |
| Exception thrown | ConcurrentModificationException | No exception |
| Works on | Original collection | Copy of collection |
| Thread safety | ❌ Not thread-safe | ✅ Thread-safe |
| Performance | Faster | Slightly slower |
| Memory usage | Low | Higher |
| Examples | ArrayList, HashMap | ConcurrentHashMap, CopyOnWriteArrayList |

---

## Fail-fast Iterator

A **fail-fast iterator** immediately fails if the collection is structurally modified during iteration.

### Characteristics
- Detects concurrent modification
- Throws `ConcurrentModificationException`
- Helps identify bugs early

> Fail-fast iterators use **modCount** to detect structural modification.
 
### What is a structural modification?

- add()
- remove()
- clear()
- put() (for maps)

### How fail-fast works

1. Iterator stores current modCount as expectedModCount
2. On every next() / hasNext()
3. JVM checks:

    ```
    if (modCount != expectedModCount)
    throw ConcurrentModificationException
    ```

---

## Fail-safe Iterator

A **fail-safe iterator** iterates over a **snapshot copy** of the collection.

### Characteristics
- No exception on modification
- Works safely in multi-threaded environments
- Changes may not be reflected during iteration


## How ConcurrentHashMap Avoids the Exception

###  No `modCount` checks

- It does not track a global modification count
- So no comparison → no exception

---

> **Fail-fast iterators throw an exception on concurrent modification, whereas fail-safe iterators work on a copy and continue safely.**

