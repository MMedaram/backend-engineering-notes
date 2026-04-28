---
title: Fail-Fast vs Fail-Safe Iterators
parent: Collection Framework
nav_order: 19
---

# Fail-Fast vs Fail-Safe Iterators

## Overview

- These terms describe how **iterators behave when a collection is modified during iteration**
- Important for:
    - Avoiding bugs
    - Understanding concurrent behavior

---

## Fail-Fast Iterator

### Definition

- Throws **ConcurrentModificationException (CME)** if collection is modified during iteration

---

### How it Works

- Uses an internal counter:
```text
modCount
```

- Iterator stores: 

```text
expectedModCount
```

If:

> modCount != expectedModCount → Exception

```java
public static void main(String[] args) {
    List<Integer> list = new ArrayList<>();
    list.add(1);
    list.add(2);

    for (Integer i : list) {
        list.add(3); // ❌ modification
    }
}
```

#### 👉 Output:

**ConcurrentModificationException**

### Characteristics
| Feature     | Description                 |
| ----------- | --------------------------- |
| Behavior    | Throws exception            |
| Detection   | Immediate                   |
| Safety      | Prevents inconsistent state |
| Performance | Fast (no extra copy)        |


### Collections Using Fail-Fast

- **ArrayList**
- **HashMap**
- **HashSet**
- **LinkedList**

------

## Fail-Safe Iterator

### Definition
- Does NOT throw exception
- Works on a clone/snapshot of the collection

### How it Works
- Iterator uses a separate copy
- Changes to original collection do NOT affect iteration

```java
import java.util.concurrent.*;

public static void main(String[] args) {

  CopyOnWriteArrayList<Integer> list = new CopyOnWriteArrayList<>();
  list.add(1);
  list.add(2);

  for (Integer i : list) {
      
    list.add(3); // ✅ allowed
  }

  System.out.println(list); // [1, 2, 3, 3]
}
```

## Characteristics

| Feature     | Description            |
| ----------- | ---------------------- |
| Behavior    | No exception           |
| Detection   | No detection           |
| Safety      | Iterates over snapshot |
| Performance | Slower (copy overhead) |


## Collections Using Fail-Safe

- CopyOnWriteArrayList
- ConcurrentHashMap (iterators are weakly consistent)

### Key Differences

| Feature              | Fail-Fast           | Fail-Safe     |
| -------------------- | ------------------- | ------------- |
| Exception            | Yes (CME)           | No            |
| Iterator Type        | Original collection | Copy/snapshot |
| Performance          | Faster              | Slower        |
| Modification allowed | ❌                   | ✅             |
| Use case             | Single-thread       | Multi-thread  |


### One-Liner

Fail-Fast = Fail immediately on modification
Fail-Safe = Work on a safe copy, no failure