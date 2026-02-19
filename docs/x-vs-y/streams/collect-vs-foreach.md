---
title: collect() vs forEach()
parent: Streams
nav_order: 6
---

# collect() vs forEach() in Java Streams

Both `collect()` and `forEach()` are terminal operations in Java Streams.
However, they serve very different purposes.

Understanding the difference is essential for writing clean,
functional-style code.

---

## Basic Difference

| collect() | forEach() |
|------------|-----------|
| Used to gather results | Used to perform actions |
| Returns a value | Returns void |
| Functional & safe | Often side-effect based |
| Preferred for data transformation | Preferred for logging / printing |

---

## What is forEach()?

`forEach()` is used to perform an action on each element.

It does not produce a result.
It is mainly used for:

- Printing
- Logging
- Triggering side effects

---

### Example – Printing Elements

```
List<String> names = List.of("A", "B", "C");

names.stream()
     .forEach(System.out::println);
```

---

## What is collect()?

`collect()` is used to gather stream elements into a collection or
another container.

It returns a result.

### Example – Collecting to List

```text
List<String> upperNames =
    names.stream().map(String::toUpperCase).collect(Collectors.toList());
```

✔ Returns a new List      
✔ No side effects     

---


## forEach()

- Performs an action
- Encourages mutation
- Can cause thread-safety issues in parallel streams

## collect()

- Follows functional programming style
- Avoids shared mutable state
- Safer in parallel streams
- Designed for accumulation


---

> `forEach()` is used to perform actions on each stream element and returns void, often causing side effects. 
> `collect()` is used to accumulate stream elements into a collection and is preferred in functional-style programming, especially for parallel streams.
