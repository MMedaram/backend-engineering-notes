---
title: Functional Interfaces
parent: Java-8
nav_order: 3
---

# Functional Interfaces

A functional interface is an interface with exactly one abstract method.

Simple meaning:

> It is the target type for a lambda expression.

---

## Why Were Functional Interfaces Introduced?

Java 8 added lambdas.

But Java is strongly typed, so every lambda needs a type.

That type is usually a functional interface.

```java
Predicate<String> isEmpty = value -> value.isEmpty();
```

Here the lambda becomes an implementation of `Predicate<String>`.

---

## Custom Functional Interface

```java
@FunctionalInterface
interface GreetingService {
    void greet(String name);
}
```

Usage:

```
GreetingService service = name -> System.out.println("Hello " + name);
service.greet("Mohan");
```

---

## @FunctionalInterface

The annotation is optional but recommended.

It tells the compiler:

> This interface must remain functional.

This fails:

```java
@FunctionalInterface
interface Calculator {
    int add(int a, int b);
    int subtract(int a, int b); // compile-time error
}
```

---

## Can It Have Default And Static Methods?

Yes.

```java
@FunctionalInterface
interface Calculator {
    int add(int a, int b);

    default int addTen(int value) {
        return add(value, 10);
    }

    static Calculator simple() {
        return (a, b) -> a + b;
    }
}
```

Default and static methods do not count as abstract methods.

---

## Common Built-In Functional Interfaces

| Interface           | Method                  | Use                             |
|---------------------|-------------------------|---------------------------------|
| `Predicate<T>`      | `boolean test(T value)` | Check condition                 |
| `Function<T, R>`    | `R apply(T value)`      | Convert value                   |
| `Consumer<T>`       | `void accept(T value)`  | Use value, return nothing       |
| `Supplier<T>`       | `T get()`               | Provide value                   |
| `UnaryOperator<T>`  | `T apply(T value)`      | Input and output same type      |
| `BinaryOperator<T>` | `T apply(T a, T b)`     | Combine two values of same type |

---

## Predicate

```
Predicate<Integer> isEven = number -> number % 2 == 0;

System.out.println(isEven.test(10)); // true
```

Useful for filters:

```
numbers.stream()
    .filter(isEven)
    .collect(Collectors.toList());
```

---

## Function

```java
Function<String, Integer> length = value -> value.length();
```

Useful for mapping:

```java
List<Integer> lengths = names.stream()
    .map(length)
    .collect(Collectors.toList());
```

---

## Consumer

```java
Consumer<String> printer = value -> System.out.println(value);
```

Useful for actions:

```
names.forEach(printer);
```

---

## Supplier

```java
Supplier<LocalDateTime> now = () -> LocalDateTime.now();
```

Useful for lazy value creation.

---

## Primitive Functional Interfaces

Primitive interfaces avoid boxing/unboxing.

Examples:

- `IntPredicate`
- `IntFunction<R>`
- `IntConsumer`
- `IntSupplier`
- `ToIntFunction<T>`
- `LongPredicate`
- `DoubleConsumer`

Example:

```java
ToIntFunction<User> ageExtractor = User::getAge;
```

---

## Edge Cases

### Too Many Abstract Methods

If an interface has more than one abstract method, lambda cannot target it.

### Checked Exceptions

Most Java built-in functional interfaces do not declare checked exceptions.

If your lambda throws checked exception, handle it inside the lambda or create your own functional interface.

### Null Lambda Reference

```
Predicate<String> predicate = null;
predicate.test("A"); // NullPointerException
```

---

## Best Practices

- Use built-in functional interfaces when possible.
- Create custom functional interfaces only when they make meaning clearer.
- Use `@FunctionalInterface`.
- Use primitive functional interfaces for heavy numeric processing.
- Keep lambda logic small.

---

## Quick Summary

Functional interfaces are the foundation of lambdas in Java 8. They have one abstract method and can also contain default and static methods.
