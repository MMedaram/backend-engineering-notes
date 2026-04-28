---
title: Collection (I)
parent: Collection Framework
nav_order: 2
---

# Collection Interface

## 1. What is Collection?

- Root interface of Java Collections Framework (JCF) (except Map)
- Represents a group of objects (elements)
- Found in java.util package
- Extends Iterable

## 2. Key Points
- Parent of: List, Set, Queue   
- Does not support key-value → only values
- Can contain duplicates (depends on implementation)
- Some implementations allow null, some don’t
- Introduced in Java 1.2
- Works with Generics (Java 1.5+)

## 3. Methods

###  Add Operations

| Method Signature                            | Description       | Behavior / Edge Case                                                                    |
| ------------------------------------------- | ----------------- | --------------------------------------------------------------------------------------- |
| `boolean add(E e)`                          | Adds element      | Returns `false` only in special cases (e.g., `Set` rejects duplicate)                   |
| `boolean addAll(Collection<? extends E> c)` | Adds all elements | Returns `true` if **at least one element added**; ignores elements that cannot be added |


### Remove Operations

| Method Signature                     | Description               | Behavior / Edge Case                                          |
| ------------------------------------ | ------------------------- | ------------------------------------------------------------- |
| `boolean remove(Object o)`           | Removes element           | Returns `false` if element not found                          |
| `boolean removeAll(Collection<?> c)` | Removes matching elements | Removes **only matching elements**; ignores non-matching ones |
| `boolean retainAll(Collection<?> c)` | Keeps common elements     | Removes elements **not present in given collection**          |
| `void clear()`                       | Removes all elements      | Always empties collection                                     |


### Read / Query Operations

| Method Signature                       | Description         | Behavior / Edge Case                               |
| -------------------------------------- | ------------------- | -------------------------------------------------- |
| `boolean contains(Object o)`           | Checks presence     | Uses `equals()` internally                         |
| `boolean containsAll(Collection<?> c)` | Checks all elements | Returns `false` if **even one element is missing** |
| `int size()`                           | Number of elements  | Always exact count                                 |
| `boolean isEmpty()`                    | Checks empty        | Equivalent to `size() == 0`                        |


### Traversal

| Method Signature         | Description | Behavior / Edge Case                                                              |
| ------------------------ | ----------- | --------------------------------------------------------------------------------- |
| `Iterator<E> iterator()` | Iteration   | Throws `ConcurrentModificationException` if modified during iteration (fail-fast) |


### Conversion

| Method Signature         | Description              |
| ------------------------ | ------------------------ |
| `Object[] toArray()`     | Converts to Object array |
| `<T> T[] toArray(T[] a)` | Converts to typed array  |


### Equality & Hashing

| Method Signature         | Description      | Behavior / Edge Case                |
| ------------------------ | ---------------- | ----------------------------------- |
| `Object[] toArray()`     | Convert to array | Returns new array                   |
| `<T> T[] toArray(T[] a)` | Typed array      | If size smaller → new array created |



### Java 8+ Default Methods

| Method Signature                                | Description        | Behavior / Edge Case                    |
| ----------------------------------------------- | ------------------ | --------------------------------------- |
| `boolean removeIf(Predicate<? super E> filter)` | Conditional remove | Removes elements where condition = true |
| `Stream<E> stream()`                            | Sequential stream  | Does not modify collection              |
| `void forEach(Consumer<? super E> action)`      | Loop               | Internal iteration                      |



