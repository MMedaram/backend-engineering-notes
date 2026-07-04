---
title: Primitive Streams and Optionals
parent: Java-8
nav_order: 17
---

# Primitive Streams and Optionals

Java 8 added primitive stream types:

- `IntStream`
- `LongStream`
- `DoubleStream`

It also added primitive Optional types:

- `OptionalInt`
- `OptionalLong`
- `OptionalDouble`

---

## Why Were They Introduced?

Normal streams work with objects.

```
Stream<Integer>
```

But `Integer` is an object wrapper around `int`.

When many numbers are processed, boxing/unboxing can add overhead.

Primitive streams avoid this overhead.

---

## IntStream Example

```java
int sum = IntStream.of(1, 2, 3, 4)
    .sum();
```

Range:

```
IntStream.range(1, 5)
    .forEach(System.out::println);
```

Output:

```text
1
2
3
4
```

`range` excludes end.

`rangeClosed` includes end.

```
IntStream.rangeClosed(1, 5); // 1, 2, 3, 4, 5
```

---

## mapToInt

```java
int totalAge = users.stream()
    .mapToInt(User::getAge)
    .sum();
```

Use primitive streams for numeric operations:

- `sum`
- `average`
- `min`
- `max`
- `summaryStatistics`

---

## OptionalInt

```
OptionalInt max = IntStream.of(10, 20, 30).max();

if (max.isPresent()) {
    System.out.println(max.getAsInt());
}
```

Empty stream has no max.

```java
OptionalInt max = IntStream.empty().max();
```

Calling this on empty OptionalInt:

```
max.getAsInt();
```

throws:

```text
NoSuchElementException
```

---

## average

```java
OptionalDouble average = users.stream()
    .mapToInt(User::getAge)
    .average();
```

Empty stream returns `OptionalDouble.empty()`.

---

## summaryStatistics

```java
IntSummaryStatistics stats = users.stream()
    .mapToInt(User::getAge)
    .summaryStatistics();
```

Gives:

- Count.
- Sum.
- Min.
- Max.
- Average.

Edge case:

For empty stream:

```java
IntSummaryStatistics stats = IntStream.empty().summaryStatistics();
```

Count is 0.

Min and max use default extreme values:

- Min = `Integer.MAX_VALUE`.
- Max = `Integer.MIN_VALUE`.

Check count before trusting min/max.

---

## Best Practices

- Use `mapToInt`, `mapToLong`, `mapToDouble` for numeric calculations.
- Use `OptionalInt` carefully; check presence before `getAsInt`.
- Use `orElse` for default numeric values.
- Remember `range` excludes end and `rangeClosed` includes end.
- Use `summaryStatistics` when you need multiple metrics.

---

## Quick Summary

Primitive streams avoid boxing and provide useful numeric methods. Primitive Optionals represent missing numeric results like max/average on an empty stream.

