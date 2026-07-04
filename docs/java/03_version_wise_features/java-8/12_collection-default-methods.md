---
title: Collection Default Methods
parent: Java-8
nav_order: 13
---

# Collection Default Methods

Java 8 added useful methods directly to collection interfaces.

Important methods:

- `Iterable.forEach`
- `Collection.removeIf`
- `List.replaceAll`
- `List.sort`
- `Collection.stream`
- `Collection.parallelStream`
- `Collection.spliterator`

---

## forEach

```
names.forEach(System.out::println);
```

If action is null:

```
names.forEach(null); // NullPointerException
```

---

## removeIf

Removes elements matching condition.

```
names.removeIf(name -> name.startsWith("A"));
```

Edge cases:

- If predicate is null: `NullPointerException`.
- If collection does not support removal: `UnsupportedOperationException`.

Example:

```
List<String> names = Arrays.asList("A", "B");
names.removeIf(name -> name.equals("A")); // UnsupportedOperationException
```

`Arrays.asList` returns fixed-size list.

Use:

```java
List<String> names = new ArrayList<>(Arrays.asList("A", "B"));
```

| Method                | Removes                                               |
|-----------------------|-------------------------------------------------------|
| `remove(Object)`      | Only the **first occurrence** of the specified object |
| `remove(int index)`   | The element at the specified index                    |
| `removeIf(Predicate)` | **All** elements that satisfy the predicate           |

---

## List.replaceAll

Updates each element in list.

```
names.replaceAll(String::toUpperCase);
```

Edge cases:

- If operator is null: `NullPointerException`.
- If list does not support set operation: `UnsupportedOperationException`.

---

## List.sort

```
users.sort(Comparator.comparing(User::getName));
```

Edge cases:

- Null comparator means natural ordering.
- If elements are not comparable, sorting can throw `ClassCastException`.
- If list does not support set operation, sorting can throw `UnsupportedOperationException`.

---

## stream and parallelStream

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))
    .collect(Collectors.toList());
```

Use parallel stream carefully:

```java
List<String> result = names.parallelStream()
    .filter(name -> name.startsWith("A"))
    .collect(Collectors.toList());
```

---

## spliterator

`Spliterator` helps streams split and traverse data.

Most developers do not use it directly.

```java
Spliterator<String> spliterator = names.spliterator();
```

Developer meaning:

- Useful for custom collections.
- Useful when learning stream internals.
- Not common in business code.

---

## Best Practices

- Use `forEach` for simple actions.
- Use `removeIf` instead of manual iterator removal when clear.
- Use `replaceAll` for simple list transformations.
- Use `sort` with Comparator helpers.
- Do not mutate fixed-size or unmodifiable collections.
- Avoid side effects inside stream operations.

---

## Quick Summary

Java 8 added lambda-friendly methods to collections. `forEach`, `removeIf`, `replaceAll`, `sort`, and `stream` make collection code shorter and easier to read.

