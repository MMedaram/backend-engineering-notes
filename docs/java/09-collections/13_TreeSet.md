---
title: TreeSet (C)
parent: Collection Framework
nav_order: 13
---

# TreeSet

## Overview

- `TreeSet` is a **NavigableSet implementation**.
- Stores elements in **sorted order**.
- Internally uses a **Red-Black Tree (self-balancing BST)**.
- Does **NOT allow duplicates**.

---

## Key Characteristics

| Feature | Description |
|--------|------------|
| **Order** | Sorted (ascending by default) |
| **Duplicates** | Not allowed |
| **Null Values** | Not allowed (throws NullPointerException) |
| **Thread Safety** | Not synchronized |
| **Performance** | O(log n) for most operations |

---

## Internal Working

- Backed by **TreeMap**
- TreeSet → TreeMap<E, Object>

Stores elements as:

> map.put(element, PRESENT);

Uses:

- compareTo() (natural ordering)
- OR Comparator

----

### Time Complexity

| Operation  | Time Complexity |
| ---------- | --------------- |
| Add        | O(log n)        |
| Remove     | O(log n)        |
| Contains   | O(log n)        |
| Navigation | O(log n)        |


### Constructors

| Constructor                           | Description             |
| ------------------------------------- | ----------------------- |
| `TreeSet()`                           | Natural ordering        |
| `TreeSet(Comparator<? super E> comp)` | Custom sorting          |
| `TreeSet(Collection<? extends E> c)`  | From collection         |
| `TreeSet(SortedSet<E> s)`             | From another sorted set |


## TreeSet-Specific Methods

(From NavigableSet + SortedSet)

| Method Signature   | Description      | Behavior / Edge Case       |
| ------------------ | ---------------- | -------------------------- |
| `boolean add(E e)` | Adds element     | Returns false if duplicate |
| `E first()`        | Smallest element | Exception if empty         |
| `E last()`         | Largest element  | Exception if empty         |
| `E lower(E e)`     | Element < e      | null if none               |
| `E floor(E e)`     | Element ≤ e      | null if none               |
| `E ceiling(E e)`   | Element ≥ e      | null if none               |
| `E higher(E e)`    | Element > e      | null if none               |
| `E pollFirst()`    | Remove first     | null if empty              |
| `E pollLast()`     | Remove last      | null if empty              |


----

## Code Examples

### 1. Natural Sorting

```java
public static void main(String[] args) {
    TreeSet<Integer> set = new TreeSet<>();

    set.add(30);
    set.add(10);
    set.add(20);

    System.out.println(set); // [10, 20, 30]
}
```

### 2. Custom Comparator

```java
public static void main(String[] args) {
    TreeSet<Integer> set = new TreeSet<>((a, b) -> b - a);

    set.add(10);
    set.add(20);
    set.add(30);

    System.out.println(set); // [30, 20, 10]
}
```


----

## Important Rules

1. Elements must be comparable if not **ClassCastException**
2. Custom Objects MUST define ordering

```java
class Student implements Comparable<Student> {
    int id;

    Student(int id) {
        this.id = id;
    }

    public int compareTo(Student s) {
        return this.id - s.id;
    }
}
```


## HashSet vs LinkedHashSet vs TreeSet

| Feature     | HashSet    | LinkedHashSet      | TreeSet        |
| ----------- | ---------- | ------------------ | -------------- |
| Order       | No         | Insertion          | Sorted         |
| Structure   | Hash table | Hash + Linked List | Red-Black Tree |
| Performance | O(1)       | O(1)               | O(log n)       |
| Null        | 1 allowed  | 1 allowed          | Not allowed    |


### When to Use

- When you need:
  - Sorted data
  - Range queries
  - Navigation (floor, ceiling)

### When NOT to Use
  
- When fast performance required → use HashSet
- When insertion order needed → use LinkedHashSet
- When null handling required → avoid TreeSet
  
## Developer Notes

- Internally uses TreeMap
- All operations maintain sorted order
- Range methods return views
- Very common in interviews + real systems (ranking, scheduling)

