---
title: Utility API Improvements
parent: Java-8
nav_order: 20
---

# Utility API Improvements

Java 8 added several small but useful utility improvements.

Important APIs:

- `String.join`
- `StringJoiner`
- `Arrays.parallelSort`
- Math exact methods
- Unsigned integer helpers
- `Objects` helper methods

---

## String.join

```java
String csv = String.join(",", "A", "B", "C");
```

Output:

```text
A,B,C
```

With list:

```java
String csv = String.join(",", names);
```

Edge cases:

- If delimiter is null: `NullPointerException`.
- If an element is null, Java uses string `"null"`.

---

## StringJoiner

```
StringJoiner joiner = new StringJoiner(", ", "[", "]");

joiner.add("A");
joiner.add("B");

System.out.println(joiner.toString()); // [A, B]
```

Useful when building joined strings step by step.

---

## Arrays.parallelSort

```
int[] numbers = {5, 1, 4, 2};

Arrays.parallelSort(numbers);
```

Uses parallel sorting for large arrays.

For small arrays, normal `Arrays.sort` may be enough.

---

## Math Exact Methods

Java 8 added methods that throw exception on overflow.

```java
int result = Math.addExact(2_000_000_000, 1_000_000_000);
```

Throws:

```text
ArithmeticException: integer overflow
```

Useful methods:

- `addExact`
- `subtractExact`
- `multiplyExact`
- `incrementExact`
- `decrementExact`
- `negateExact`
- `toIntExact`

Use when silent overflow would be dangerous.

---

## Unsigned Helpers

```java
int result = Integer.compareUnsigned(a, b);
long value = Integer.toUnsignedLong(a);
```

Useful for:

- Low-level protocols.
- Binary formats.
- Hashing.
- Network values.

Most business code does not need unsigned helpers often.

---

## Objects Helpers

```
Objects.isNull(value);
Objects.nonNull(value);
Objects.requireNonNull(value, "value is required");
```

Useful in streams:

```java
List<String> clean = values.stream()
    .filter(Objects::nonNull)
    .collect(Collectors.toList());
```

`requireNonNull` throws:

```text
NullPointerException
```

with your message if value is null.

---

## Best Practices

- Use `String.join` for simple joining.
- Use `Collectors.joining` inside stream pipelines.
- Use Math exact methods when overflow must be detected.
- Use `Objects.requireNonNull` for required constructor arguments.
- Use `Objects::nonNull` to filter null values in streams.
- Use `Arrays.parallelSort` only when array size is large enough to benefit.

---

## Quick Summary

Java 8 added useful small APIs for joining strings, sorting arrays, detecting arithmetic overflow, unsigned operations, and null checks.

