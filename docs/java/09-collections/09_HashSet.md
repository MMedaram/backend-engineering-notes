---
title: HashSet (C)
parent: Collection Framework
nav_order: 9
---

# HashSet 

## Overview

- `HashSet` is a **Set implementation** that uses a **hash table (HashMap internally)**.
- Stores **unique elements** (no duplicates).
- Does **NOT maintain insertion order**.
- Allows **one null value**.

---

## Internal Working

- Backed by a **HashMap**
    - Elements are stored as **keys**
    - Value is a constant dummy object (`PRESENT`)
- Uses:
    - `hashCode()` → to find bucket
    - `equals()` → to check duplicates

---

## Key Characteristics

| Feature | Description |
|--------|------------|
| **Duplicates** | Not allowed |
| **Order** | Not guaranteed |
| **Null** | Only one null allowed |
| **Thread Safety** | Not synchronized |
| **Performance** | Very fast (O(1) average) |

---

## Time Complexity

| Operation | Time Complexity |
|----------|----------------|
| Add | O(1) |
| Remove | O(1) |
| Contains | O(1) |
| Worst Case | O(n) (hash collision) |

---

## Constructors

| Constructor | Description |
|------------|------------|
| `HashSet()` | Default capacity (16) and load factor (0.75) |
| `HashSet(int initialCapacity)` | Custom initial capacity |
| `HashSet(int initialCapacity, float loadFactor)` | Custom capacity + load factor |
| `HashSet(Collection<? extends E> c)` | Creates set from collection |

---

## HashSet-Specific Behavior Methods

| Method Signature | Description | Behavior / Edge Case |
|------------------|------------|----------------------|
| `boolean add(E e)` | Adds element | Returns `false` if duplicate |
| `boolean contains(Object o)` | Checks presence | Uses `hashCode()` + `equals()` |
| `boolean remove(Object o)` | Removes element | Returns false if not present |
| `Iterator<E> iterator()` | Returns iterator | Order is unpredictable |

---

## Important Concepts

### 1. Duplicate Handling

```
HashSet<String> set = new HashSet<>();
set.add("A");
set.add("A");

System.out.println(set); // Output: [A]
```

- Second insert is ignored
- add() returns false


### 2. Role of hashCode() and equals()

```java
class Student {
    int id;

    Student(int id) {
        this.id = id;
    }
}

public static void main(String[] args) {

    HashSet<Student> set = new HashSet<>();
    set.add(new Student(1));
    set.add(new Student(1));

    System.out.println(set.size()); // Output: 2 ❌
}

```

#### Why?
- equals() and hashCode() not overridden

```java
class Student {
    int id;

    Student(int id) {
        this.id = id;
    }

    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Student)) return false;
        Student s = (Student) o;
        return id == s.id;
    }

    public int hashCode() {
        return id;
    }
}

public static void main(String[] args) {
    HashSet<Student> set = new HashSet<>();
    set.add(new Student(1));
    set.add(new Student(1));

    System.out.println(set.size()); // Output: 1 ✅
}

```

-----

## Load Factor & Capacity 

- **Default capacity** = 16
- **Load factor** = 0.75

#### Resize happens when:

> size > capacity * loadFactor

#### Example:

> 16 * 0.75 = 12 → after 12 elements → resize

-----

## When to Use

- Fast lookups (search, insert, delete)
- When order does not matter
- When you need unique elements only

## When NOT to Use

- When order matters → use LinkedHashSet
- When sorting required → use TreeSet
- When thread safety needed → use:
  - Collections.synchronizedSet()
  - ConcurrentHashMap based set

## Developer Notes

- Always override:
  - equals()
  - hashCode()
  
- Performance depends on good hash function
- Collisions reduce performance
- Not suitable for index-based access
