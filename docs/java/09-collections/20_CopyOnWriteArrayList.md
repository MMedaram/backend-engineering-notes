---
title: CopyOnWriteArrayList
parent: Collection Framework
nav_order: 20
---

# CopyOnWriteArrayList 

## Overview

- `CopyOnWriteArrayList` is a **thread-safe variant of ArrayList**
- Part of `java.util.concurrent`
- Uses **copy-on-write strategy**

---

## Core Idea

👉 On every **write operation**:
- A **new copy of the array** is created
- Modification happens on the new copy

👉 Reads:
- Use **old snapshot**
- No locking needed

---

## Key Characteristics

| Feature | Description |
|--------|------------|
| **Thread Safety** | Yes |
| **Read Performance** | Very fast (no lock) |
| **Write Performance** | Slow (copy happens) |
| **Iterator Type** | Fail-safe (snapshot-based) |
| **Duplicates** | Allowed |
| **Null Values** | Allowed |

---

## Internal Working

```
Original Array → Copy → Modify → Replace reference
```

Example:

> [1,2,3] → copy → [1,2,3] → add(4) → [1,2,3,4]


### Important Methods

| Method Signature              | Description       | Behavior / Edge Case |
| ----------------------------- | ----------------- | -------------------- |
| `boolean add(E e)`            | Adds element      | Creates new copy     |
| `E get(int index)`            | Retrieves element | No locking           |
| `E set(int index, E element)` | Updates value     | Copy happens         |
| `boolean remove(Object o)`    | Removes element   | Copy happens         |
| `Iterator<E> iterator()`      | Returns iterator  | Snapshot-based       |


## Key Rule

> Iterator works on a snapshot of the array at the time of creation

### Example 1: Modification During Iteration

```java
public static void main(String[] args) {
    CopyOnWriteArrayList<Integer> list = new CopyOnWriteArrayList<>();

    list.add(1);
    list.add(2);
    list.add(3);

    for (Integer i : list) {
        if (i == 2) {
            list.add(4); // ✅ allowed
        }
        System.out.println(i);
    }
}
```

Output:
```
1
2
3
```

👉 Notice:

4 is NOT printed            
Because iterator is using old snapshot

### Example 2: After Iteration

> System.out.println(list);

Output:

> [1, 2, 3, 4]

👉 Modification happened, but not visible during iteration

### Example 3: No ConcurrentModificationException

```
for (Integer i : list) {
list.remove(i); // ✅ allowed
}
```

👉 No exception thrown

### Example 4: Iterator Remove (IMPORTANT ⚠️)

```
Iterator<Integer> it = list.iterator();

while (it.hasNext()) {
it.next();
it.remove(); // ❌ UnsupportedOperationException
}
```

👉 Why?

Iterator is read-only snapshot

-----


| Operation         | Allowed | Visible in Current Iteration |
| ----------------- | ------- | ---------------------------- |
| Add               | ✅       | ❌                            |
| Remove            | ✅       | ❌                            |
| Update            | ✅       | ❌                            |
| Iterator remove() | ❌       | N/A                          |


-----

### Performance

| Operation | Complexity        |
| --------- | ----------------- |
| Read      | O(1)              |
| Write     | O(n) (copy array) |


## When to Use

- Read-heavy applications
- Rare writes
- Multi-threaded environments

Examples:

- Caching
- Event listeners
- Configuration data

## When NOT to Use
- Frequent writes ❌
- Large data sets ❌
- Memory-sensitive systems ❌

----------

### CopyOnWriteArrayList vs ArrayList

| Feature     | ArrayList | CopyOnWriteArrayList |
| ----------- | --------- | -------------------- |
| Thread Safe | ❌         | ✅                    |
| Iterator    | Fail-fast | Fail-safe            |
| Writes      | Fast      | Slow                 |
| Reads       | Fast      | Very fast            |

git