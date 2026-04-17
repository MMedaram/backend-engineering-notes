---
title: Collections Utility Class
parent: Collection Framework
nav_order: 3
---

# Collections Utility Class

-----

## 1. What is Collections?
   
- Utility class in java.util package
- Contains static methods for working with collections
- Works on List, Set, etc.
- Introduced in Java 1.2

## 2. Key Points

- ❗ Not same as Collection (interface vs utility class)
- ❗ All methods are static
- ❗ Provides:
   - Algorithms (sort, search)
   - Wrappers (synchronized, unmodifiable)
   - Utility operations (reverse, shuffle, etc.)
  
## 3. Core Methods

---

### 3.1 Sorting

| Method Signature                                   | Description                | Behavior / Edge Case                                            |
| -------------------------------------------------- | -------------------------- | --------------------------------------------------------------- |
| `void sort(List<T> list)`                          | Sorts list (natural order) | Elements must implement `Comparable`, else `ClassCastException` |
| `void sort(List<T> list, Comparator<? super T> c)` | Custom sorting             | Comparator decides order                                        |

### 3.2 Searching

| Method Signature                                                           | Description      | Behavior / Edge Case                               |
| -------------------------------------------------------------------------- | ---------------- | -------------------------------------------------- |
| `int binarySearch(List<? extends Comparable> list, T key)`                 | Searches element | List **must be sorted**, else unpredictable result |
| `int binarySearch(List<? extends T> list, T key, Comparator<? super T> c)` | Custom search    | Uses comparator                                    |


#### binarySearch() without sorting

```
List<Integer> list = List.of(3,1,2);
Collections.binarySearch(list, 2);
```

👉 Result = unpredictable (WRONG usage)


### 3.3 Reordering 

| Method Signature                          | Description      | Behavior / Edge Case                    |
| ----------------------------------------- | ---------------- | --------------------------------------- |
| `void reverse(List<?> list)`              | Reverses list    | In-place operation                      |
| `void shuffle(List<?> list)`              | Random order     | Different order each time               |
| `void rotate(List<?> list, int distance)` | Rotates elements | Positive → right shift, negative → left |

### 3.4 Min / Max 

| Method Signature                                               | Description   | Behavior / Edge Case |
| -------------------------------------------------------------- | ------------- | -------------------- |
| `T min(Collection<? extends T> c)`                             | Finds minimum | Needs `Comparable`   |
| `T max(Collection<? extends T> c)`                             | Finds maximum | Needs `Comparable`   |
| `T min(Collection<? extends T> c, Comparator<? super T> comp)` | Custom min    | Uses comparator      |
| `T max(Collection<? extends T> c, Comparator<? super T> comp)` | Custom max    | Uses comparator      |


### 3.5 Modification Utilities

| Method Signature                                         | Description           | Behavior / Edge Case                                                  |
| -------------------------------------------------------- | --------------------- | --------------------------------------------------------------------- |
| `void fill(List<? super T> list, T obj)`                 | Replaces all elements | Overwrites entire list                                                |
| `void copy(List<? super T> dest, List<? extends T> src)` | Copies elements       | `dest.size()` must be ≥ `src.size()` else `IndexOutOfBoundsException` |
| `boolean replaceAll(List<T> list, T oldVal, T newVal)`   | Replaces occurrences  | Returns true if any replaced                                          |



#### copy() size issue

```
List<Integer> src = List.of(1,2,3);
List<Integer> dest = new ArrayList<>();

Collections.copy(dest, src);
```

- 👉 ❌ Throws IndexOutOfBoundsException
- 👉 Because dest size = 0

✔ Correct:

```
List<Integer> dest = new ArrayList<>(Arrays.asList(0,0,0));
Collections.copy(dest, src);
```


### 3.6 Frequency and Disjoint

| Method Signature                                       | Description        | Behavior / Edge Case       |
| ------------------------------------------------------ | ------------------ | -------------------------- |
| `int frequency(Collection<?> c, Object o)`             | Count occurrences  | Uses `equals()`            |
| `boolean disjoint(Collection<?> c1, Collection<?> c2)` | No common elements | Returns true if no overlap |


### 3.7 Immutable Collections

| Method Signature                                   | Description         | Behavior / Edge Case                               |
| -------------------------------------------------- | ------------------- | -------------------------------------------------- |
| `List<T> emptyList()`                              | Returns empty list  | Immutable                                          |
| `List<T> singletonList(T o)`                       | Single element list | Immutable                                          |
| `List<T> unmodifiableList(List<? extends T> list)` | Read-only list      | Any modification → `UnsupportedOperationException` |


#### Unmodifiable List 

```
List<Integer> list = Collections.unmodifiableList(List.of(1,2,3));
list.add(4);
```

👉 ❌ Throws UnsupportedOperationException


### 3.8 Thread-Safe Wrappers

| Method Signature                         | Description         | Behavior / Edge Case               |
| ---------------------------------------- | ------------------- | ---------------------------------- |
| `List<T> synchronizedList(List<T> list)` | Thread-safe wrapper | Needs manual sync during iteration |
| `Set<T> synchronizedSet(Set<T> s)`       | Thread-safe set     | Same limitation                    |
| `Map<K,V> synchronizedMap(Map<K,V> m)`   | Thread-safe map     | Legacy-style sync                  |


-----------------------

| Term          | Meaning            |
| ------------- | ------------------ |
| `Collection`  | Interface          |
| `Collections` | Utility class      |
| `Arrays`      | Utility for arrays |


-------------------------------

## Key

- Collections.sort() internally uses TimSort (Java 8+)
- binarySearch() → O(log n) but only if sorted
- unmodifiableList() ≠ immutable (original list can still change)
- Prefer `List.of()` (Java 9+) over `Collections.unmodifiableList()`
- `synchronizedList()` is legacy approach → prefer `CopyOnWriteArrayList`


