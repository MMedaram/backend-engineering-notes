---
title: Stream API
parent: Java-8
nav_order: 6
---

# Stream API

Java 8 introduced Streams to process collections in a clean pipeline style.

Simple meaning:

> A stream lets you say what you want to do with data, instead of writing every loop step yourself.

---

## Why Were Streams Introduced?

Before Java 8:

```
List<String> result = new ArrayList<>();

for (String name : names) {
    if (name.startsWith("A")) {
        result.add(name.toUpperCase());
    }
}
```

With streams:

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

---

## Stream Pipeline

A stream pipeline has three parts:

1. Source.
2. Intermediate operations.
3. Terminal operation.

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

Without a terminal operation, nothing runs.

---

## Intermediate Operations

| Method     | Use                                |
|------------|------------------------------------|
| `filter`   | Keep matching elements             |
| `map`      | Convert each element               |
| `flatMap`  | Flatten nested streams             |
| `sorted`   | Sort elements                      |
| `distinct` | Remove duplicates using `equals`   |
| `limit`    | Keep first N elements              |
| `skip`     | Skip first N elements              |
| `peek`     | Debug elements while pipeline runs |

---

## Terminal Operations

| Method | Use |
| --- | --- |
| `collect` | Collect result into List, Set, Map, etc. |
| `forEach` | Perform action |
| `count` | Count elements |
| `anyMatch` | Check if any element matches |
| `allMatch` | Check if all elements match |
| `noneMatch` | Check if no element matches |
| `findFirst` | Find first element |
| `findAny` | Find any element |
| `min` | Find minimum |
| `max` | Find maximum |
| `reduce` | Combine values into one result |


---

## Stream Can Be Used Only Once

```
List<String> names = Arrays.asList("A", "B", "C");

Stream<String> stream = names.stream();

stream.forEach(System.out::println);
stream.count(); // IllegalStateException
```

Exception:

```text
IllegalStateException: stream has already been operated upon or closed
```

Create a new stream each time.

---

## map vs flatMap

Use `map` when one input becomes one output.

```java
List<String> names = users.stream()
    .map(User::getName)
    .collect(Collectors.toList());
```

Use `flatMap` when one input has many values.

```java
List<String> phoneNumbers = users.stream()
    .flatMap(user -> user.getPhoneNumbers().stream())
    .collect(Collectors.toList());
```

---

## Null Edge Case

Streams can contain null values if the source has nulls.

```
List<String> names = Arrays.asList("A", null, "B");

names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

This throws:

```text
NullPointerException
```

Safer:

```java
List<String> result = names.stream()
    .filter(Objects::nonNull)
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

---

## forEach Edge Case

Avoid changing external mutable data inside stream operations.

Bad:

```
List<String> result = new ArrayList<>();

names.stream()
    .filter(name -> name.startsWith("A"))
    .forEach(result::add);
```

Better:

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))
    .collect(Collectors.toList());
```

---

## Best Practices

- Use streams for clear data processing pipelines.
- Use loops when the logic is complex or has many side effects.
- Do not reuse a stream after terminal operation.
- Prefer `collect` over mutating outside lists with `forEach`.
- Be careful with null values.
- Keep stream pipelines readable.
- Use parallel streams only when you understand the cost and data safety.

---

## Quick Summary

Streams process data through a pipeline. They are lazy, single-use, and do not store data. In Java 8, collect lists using `collect(Collectors.toList())`.
