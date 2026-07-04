---
title: Parallel Streams
parent: Java-8
nav_order: 8
---

# Parallel Streams

Java 8 allows streams to run in parallel.

```
list.parallelStream()
    .map(this::process)
    .collect(Collectors.toList());
```

Simple meaning:

> Java splits the work across multiple threads and combines the result.

---

## What Thread Pool Does It Use?

Parallel streams use:

```
ForkJoinPool.commonPool()
```

This is a shared JVM-wide pool.

If many parts of the application use the common pool, they can affect each other.

---

## Good Use Case

Parallel streams can help when:

- Data set is large.
- Work is CPU-heavy.
- Each item can be processed independently.
- No shared mutable state.

```java
List<Integer> result = numbers.parallelStream()
    .map(this::expensiveCalculation)
    .collect(Collectors.toList());
```

---

## Bad Use Case: Small Data

```java
List<String> result = Arrays.asList("A", "B", "C")
    .parallelStream()
    .map(String::toLowerCase)
    .collect(Collectors.toList());
```

Parallel overhead may be more expensive than the actual work.

---

## Bad Use Case: Blocking IO

```
urls.parallelStream()
    .map(this::callExternalApi)
    .collect(Collectors.toList());
```

Problem:

- HTTP calls block threads.
- Common pool can get stuck.
- Other parallel tasks may suffer.

Use a dedicated executor or async client for IO-heavy work.

---

## Bad Use Case: Shared Mutable State

```
List<String> result = new ArrayList<>();

names.parallelStream()
    .forEach(name -> result.add(name)); // unsafe
```

This can cause wrong results or random exceptions.

Better:

```java
List<String> result = names.parallelStream()
    .collect(Collectors.toList());
```

---

## Ordering Edge Case

```
names.parallelStream()
    .forEach(System.out::println);
```

Output order is not guaranteed.

If order matters:

```
names.parallelStream()
    .forEachOrdered(System.out::println); 
```

#### forEachOrdered :: 
 Performs an action for each element of this stream,in the encounter order of the stream if the stream has a defined encounter order.

But `forEachOrdered` can reduce parallel performance.

---

## Exception Behavior

```
numbers.parallelStream()
    .map(n -> 10 / n)
    .collect(Collectors.toList());
```

If `n` is zero:

```text
ArithmeticException
```

Some other tasks may already have started before the exception is seen.

---

## Best Practices

- Use parallel streams only after measuring.
- Avoid for small collections.
- Avoid for blocking IO.
- Avoid shared mutable state.
- Prefer pure functions inside stream operations.
- Be careful with ordering.
- For backend services, dedicated executors are often safer for async work.

---

## Quick Summary

Parallel streams are useful for large CPU-heavy independent work. They are risky for IO, small data, shared mutable state, and order-sensitive logic.

