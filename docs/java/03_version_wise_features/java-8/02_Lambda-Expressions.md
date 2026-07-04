---
title: Lambda Expressions
parent: Java-8
nav_order: 2
---

# Lambda Expressions

A lambda expression is a short way to pass behavior as data.

Simple meaning:

> A lambda is an unnamed method that can be passed to another method.

---

## Why Were Lambdas Introduced?

Before Java 8, we used anonymous classes for small pieces of behavior.

```java
Runnable task = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello");
    }
};
```

Java 8 lambda:

```java
Runnable task = () -> System.out.println("Hello");
```

Benefits:

- Less boilerplate.
- Easier collection processing.
- Works with Streams API.
- Makes behavior easier to pass around.

---

## Basic Syntax

No input:

```
() -> System.out.println("Hello")
```

One input:

```
name -> name.toUpperCase()
```

Multiple inputs:

```
(a, b) -> a + b
```

Block body:

```
(a, b) -> {
    int sum = a + b;
    return sum;
}
```

---

## Lambda Needs Functional Interface

A lambda works only where Java expects a functional interface.

Functional interface means:

> Interface with exactly one abstract method.

```java
@FunctionalInterface
interface Calculator {
    int add(int a, int b);
}
```

Usage:

```java
Calculator calculator = (a, b) -> a + b;

int result = calculator.add(10, 20);
```

---

## Common Functional Interfaces

| Interface        | Method   | Meaning                                              |
|------------------|----------|------------------------------------------------------|
| `Predicate<T>`   | `test`   | Takes value, returns boolean                         |
| `Function<T, R>` | `apply`  | Converts one value to another                        |
| `Consumer<T>`    | `accept` | Takes value, returns nothing                         |
| `Supplier<T>`    | `get`    | Takes nothing, returns value                         |
| `Runnable`       | `run`    | Takes nothing, returns nothing                       |
| `Callable<T>`    | `call`   | Takes nothing, returns value and can throw exception |

---

## Variable Capture

Lambdas can read local variables from outside.

```java
int limit = 10;

Predicate<Integer> isBig = value -> value > limit;
```

But captured local variables must be final or effectively final.

This does not work:

```
int limit = 10;

Predicate<Integer> isBig = value -> value > limit;

limit = 20; // compile-time error
```

---

## this Keyword Difference

In lambda, `this` refers to the outer class.

In anonymous class, `this` refers to the anonymous class object.

This matters mostly in advanced or framework code.

---

## Lambda With Streams

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))
    .map(name -> name.toUpperCase())
    .collect(Collectors.toList());
```

Cleaner with method reference:

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

---

## Edge Cases

### Null Functional Interface

```
Predicate<String> predicate = null;
predicate.test("A"); // NullPointerException
```

### Exception In Lambda

```
names.forEach(name -> {
    if (name == null) {
        throw new IllegalArgumentException("name is null");
    }
});
```

The exception is thrown when the lambda runs, not when it is created.

### Checked Exceptions

Many functional interfaces do not allow checked exceptions.

This is awkward:

```
names.forEach(name -> Files.readAllLines(Paths.get(name))); // compile-time error
```

Handle exception inside lambda or move logic to a method that handles it.

---

## Best Practices

- Keep lambdas small.
- Use clear variable names.
- Use method references when they improve readability.
- Avoid complex business logic inside lambdas.
- Avoid side effects in stream lambdas.
- Remember captured local variables must be effectively final.

---

## Quick Summary

Lambda expressions reduce boilerplate and make Java more functional. They work with functional interfaces and are heavily used with Streams, Collections, and CompletableFuture.
