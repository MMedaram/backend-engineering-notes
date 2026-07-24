---
title: Stream toList and mapMulti
parent: Java-16
nav_order: 3
---

# `Stream.toList()` and `Stream.mapMulti()`

## Overview

Java 16 added `Stream.toList()` and `mapMulti()`. They improve common collection and one-to-many transformation pipelines, but they have deliberately different semantics from familiar alternatives.

## `Stream.toList()`

```java
List<String> ids = payments.stream()
    .map(Payment::getId)
    .map(UUID::toString)
    .toList();
```

`toList()` returns an unmodifiable list. It is not specified as `ArrayList`, so callers must not rely on its implementation class, mutability, identity, or serialization behavior.

### Compared with `collect(toList())`

| Operation                      | Mutability guarantee           | Null elements |
|--------------------------------|--------------------------------|---------------|
| `stream.toList()`              | Unmodifiable                   | Permitted     |
| `collect(Collectors.toList())` | No guarantee; commonly mutable | Permitted     |
| `List.copyOf(...)`             | Unmodifiable                   | Rejected      |

```
var views = payments.stream().map(this::toView).toList();
views.add(view); // throws UnsupportedOperationException
```

### Recommended use

Use `toList()` for service/controller read models that should not be mutated after collection. Use `Collectors.toCollection(ArrayList::new)` only when downstream code intentionally needs a mutable collection.

## `mapMulti()`

`mapMulti()` is a one-to-many intermediate operation. It can emit zero, one, or many output values without creating a short-lived stream for every input.

```java
List<String> tags = orders.stream()
    .<String>mapMulti((order, downstream) -> {
        if (order.isPriority()) downstream.accept("priority");
        order.tags().forEach(downstream);
    })
    .distinct()
    .toList();
```

## Internal behavior and performance

`flatMap()` creates a nested stream per element, while `mapMulti()` pushes values directly to the downstream consumer. This can reduce allocation and overhead for small or conditionally empty expansions. It is not automatically faster for every workload; profile representative pipelines.

## Edge cases

- Do not retain or call the downstream consumer after the mapper returns.
- Calling `accept` zero times is valid; it filters the element.
- `mapMulti` does not make side effects safe in parallel streams.
- `toList()` does not deep-copy mutable elements.
- `toList()` can contain `null`; `List.copyOf()` cannot.

## Daily backend example

```java
List<ProblemDetail> problems = violations.stream()
    .<ProblemDetail>mapMulti((violation, out) -> {
        if (violation.isFieldError()) {
            out.accept(toFieldProblem(violation));
        }
        if (violation.isGlobalError()) {
            out.accept(toGlobalProblem(violation));
        }
    })
    .toList();
```

Keep mappers pure. If error conversion needs I/O, authorization, or database access, perform that work before the stream pipeline.

## Common mistakes

1. Calling `add`, `sort`, or `remove` on a `toList()` result.
2. Assuming `toList()` rejects nulls like `List.of`.
3. Replacing clear `flatMap` code with complex imperative `mapMulti` lambdas.
4. Introducing shared mutation into a parallel stream.

