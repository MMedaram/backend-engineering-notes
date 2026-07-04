---
title: Collectors Deep Dive
parent: Java-8
nav_order: 7
---

# Collectors Deep Dive

Collectors are used at the end of stream pipelines to collect data into a useful result.

Common results:

- List
- Set
- Map
- Grouped data
- Joined string
- Summary statistics

---

## Why Were Collectors Introduced?

Streams process data, but after processing we usually need a final result.

```java
List<String> activeUserNames = users.stream()
    .filter(User::isActive)
    .map(User::getName)
    .collect(Collectors.toList());
```

`collect` gathers the stream output into a list.

---

## toList

```java
List<String> names = users.stream()
    .map(User::getName)
    .collect(Collectors.toList());
```

Important:

In Java 8, `Collectors.toList()` does not promise exact list type, mutability, or thread-safety.

If you need a specific list:

```java
List<String> names = users.stream()
    .map(User::getName)
    .collect(Collectors.toCollection(ArrayList::new));
```

---

## toSet

```java
Set<String> departments = users.stream()
    .map(User::getDepartment)
    .collect(Collectors.toSet());
```

Edge cases:

- Duplicates are removed.
- Order is not guaranteed.

If order matters:

```java
Set<String> departments = users.stream()
    .map(User::getDepartment)
    .collect(Collectors.toCollection(LinkedHashSet::new));
```

---

## joining

```java
String csv = names.stream()
    .collect(Collectors.joining(","));
```

With prefix and suffix:

```java
String result = names.stream()
    .collect(Collectors.joining(", ", "[", "]"));
```

If stream contains null, joining can throw:

```text
NullPointerException
```

---

## toMap

```java
Map<Long, String> namesById = users.stream()
    .collect(Collectors.toMap(
        User::getId,
        User::getName
    ));
```

Duplicate key edge case:

```text
IllegalStateException
```

Fix with merge function:

```java
Map<Long, String> namesById = users.stream()
    .collect(Collectors.toMap(
        User::getId,
        User::getName,
        (oldValue, newValue) -> oldValue
    ));
```

Null key/value can cause:

```text
NullPointerException
```

---

## groupingBy

Groups data by a key.

```java
Map<String, List<User>> usersByDepartment = users.stream()
    .collect(Collectors.groupingBy(User::getDepartment));
```

With counting:

```java
Map<String, Long> countByDepartment = users.stream()
    .collect(Collectors.groupingBy(
        User::getDepartment,
        Collectors.counting()
    ));
```

With mapping:

```java
Map<String, List<String>> namesByDepartment = users.stream()
    .collect(Collectors.groupingBy(
        User::getDepartment,
        Collectors.mapping(User::getName, Collectors.toList())
    ));
```

---

## partitioningBy

Splits data into true and false groups.

```java
Map<Boolean, List<User>> activeAndInactive = users.stream()
    .collect(Collectors.partitioningBy(User::isActive));
```

---

## collectingAndThen

Runs one final transformation after collecting.

```java
List<String> names = users.stream()
    .map(User::getName)
    .collect(Collectors.collectingAndThen(
        Collectors.toList(),
        Collections::unmodifiableList
    ));
```

Java 10 added `Collectors.toUnmodifiableList()`, but Java 8 does not have it.

---

## summarizingInt

```java
IntSummaryStatistics stats = users.stream()
    .collect(Collectors.summarizingInt(User::getAge));
```

Gives count, sum, min, max, and average.

---

## Best Practices

- Use `toCollection` when you need a specific collection type.
- Handle duplicate keys in `toMap`.
- Avoid null keys and values when collecting to maps.
- Use `groupingBy` for many groups.
- Use `partitioningBy` for true/false groups.
- Use summary collectors for reporting numbers.
- Do not assume `toList()` returns a mutable `ArrayList`.

---

## Quick Summary

Collectors convert stream data into final results. The most useful Java 8 collectors are `toList`, `toSet`, `toMap`, `groupingBy`, `partitioningBy`, `mapping`, `joining`, and summary collectors.

