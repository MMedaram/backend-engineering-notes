---
title: ArrayList (C)
parent: Collection Framework
nav_order: 5
---

# ArrayList 

---

## 1. Overview

- Resizable array implementation of `List`
- Introduced in **Java 1.2**
- Part of `java.util`

---

## 2. Key Points

- Maintains **insertion order**
- Allows **duplicates and nulls**
- Fast **random access (O(1))**
- Internally uses **dynamic array**
- Not thread-safe

---

## 3. Type Hierarchy

```
public class ArrayList<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, Serializable{
    
}
```

### ✔ Extends

- AbstractList

### ✔ Implements
- List
- RandomAccess
- Cloneable
- Serializable

### ✔ Method Source
- Implements List → which extends Collection
- So it gets all List + Collection methods


----

## 4. Constructors

| Constructor                            | Description             | Behavior / When to Use                    |
| -------------------------------------- | ----------------------- | ----------------------------------------- |
| `ArrayList()`                          | Default constructor     | Lazy init → first add creates capacity 10 |
| `ArrayList(int initialCapacity)`       | Predefined capacity     | Use when size is known (avoids resizing)  |
| `ArrayList(Collection<? extends E> c)` | From another collection | Creates new copy                          |


---

## 5. Internal Structure

> transient Object[] elementData;

- Backed by array
- Provides fast index-based access

---

## 6. Capacity Behavior

#### Default Capacity
- 10 (after first insertion)

#### Resize Formula

> newCapacity = oldCapacity + (oldCapacity >> 1); // 1.5x growth

##### Example Growth

> 10 → 15 → 22 → 33 → ...


---

## 7. Class-Specific Methods

| Method                                 | Description        | When to Use               |
| -------------------------------------- | ------------------ | ------------------------- |
| `void ensureCapacity(int minCapacity)` | Increases capacity | Bulk inserts / known size |
| `void trimToSize()`                    | Shrinks array      | Free unused memory        |

## 8. Behavior Characteristics

| Operation       | Behavior              |
| --------------- | --------------------- |
| `get(index)`    | Direct access (fast)  |
| `add(index, e)` | Shifts elements right |
| `remove(index)` | Shifts elements left  |

## 9. Performance Summary

| Operation           | Complexity | Reason              |
| ------------------- | ---------- | ------------------- |
| get                 | O(1)       | Direct array access |
| add (end)           | O(1)*      | Amortized           |
| add/remove (middle) | O(n)       | Shifting required   |
| search              | O(n)       | Linear scan         |

--------------------------

## 10. Important Scenarios

### 1. Resize Cost
- Happens when capacity is full
- Creates new array + copies elements → costly

### 2. Shifting Cost
- list.add(0, value);
- All elements shift → expensive

### 3. Pre-sizing (Best Practice)
- new ArrayList<>(1000);
- Avoids multiple resizes

### 4. Copy Constructor
- new ArrayList<>(existingList);
- Creates independent copy

--------------

## 11. Fail-Fast Behavior

- Uses internal modCount

```
for (Integer i : list) {
list.add(10); // ❌ ConcurrentModificationException
}

```

-------------------------

## 12.Real Usage Guidance

#### ✔ Use ArrayList when:

- Frequent reads
- Mostly append operations
- Data size predictable

#### ❌ Avoid when:

- Frequent middle insert/delete
- Heavy concurrent modifications

---------------------


> **ArrayList = Resizable array with fast reads and costly shifts**