---
title: Method References
parent: Java-8
nav_order: 5
---

# Method References

Method reference is a shorter way to write a lambda when the lambda only calls an existing method.

Simple meaning:

> If a method already exists, point to it instead of writing a lambda that just calls it.

---

## Why Were Method References Introduced?

With lambdas, we can write:

```
names.forEach(name -> System.out.println(name));
```

But the lambda only calls `System.out.println`.

Java 8 allows:

```
names.forEach(System.out::println);
```

---

## Types Of Method References

| Type                      | Example               |
|---------------------------|-----------------------|
| Static method             | `Integer::parseInt`   |
| Instance method of object | `System.out::println` |
| Instance method of class  | `String::toUpperCase` |
| Constructor               | `ArrayList::new`      |

---

## Static Method Reference

Lambda:

```java
Function<String, Integer> parser = value -> Integer.parseInt(value);
```

Method reference:

```java
Function<String, Integer> parser = Integer::parseInt;
```

Edge case:

```
parser.apply("abc"); // NumberFormatException
```

---

## Instance Method Of Existing Object

```
names.forEach(System.out::println);
```

This calls `println` on the existing `System.out` object.

---

## Instance Method Of Class

```java
List<String> upperNames = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

This is same as:

```java
List<String> upperNames = names.stream()
    .map(name -> name.toUpperCase())
    .collect(Collectors.toList());
```

Edge case:

If `names` contains null, `String::toUpperCase` throws:

```text
NullPointerException
```

---

## Constructor Reference

```java
Supplier<List<String>> listSupplier = ArrayList::new;

List<String> list = listSupplier.get();
```

With streams:

```java
List<UserDto> dtos = users.stream()
    .map(UserDto::new)
    .collect(Collectors.toList());
```

This works when `UserDto` has a constructor that accepts the stream element type.

---

## Best Practices

- Use method references when they make code easier to read.
- Prefer lambdas when extra logic is needed.
- Avoid method references that hide important behavior.
- Watch out for null values in streams.
- Remember that a method reference needs a functional interface target.

---

## Quick Summary

Method references make lambdas shorter when the lambda simply calls an existing method. They improve readability when used carefully.

