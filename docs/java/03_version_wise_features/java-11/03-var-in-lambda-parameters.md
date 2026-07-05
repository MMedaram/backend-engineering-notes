---
title: var In Lambda Parameters
parent: Java-11
nav_order: 3
---

# var In Lambda Parameters

Java 10 introduced `var` for local variables.

Java 11 allowed `var` in lambda parameters.

Example:

```
(var name) -> name.toUpperCase()
```

This is mostly useful when you want to add annotations to lambda parameters.

---

## Why Was It Introduced?

Before Java 11, lambdas could be written with inferred types:

```
names.stream()
    .map(name -> name.toUpperCase())
    .collect(Collectors.toList());
```

Or explicit types:

```
names.stream()
    .map((String name) -> name.toUpperCase())
    .collect(Collectors.toList());
```

But if you wanted annotations while keeping type inference, there was no clean syntax.

Java 11 added:

```
names.stream()
    .map((@Nonnull var name) -> name.toUpperCase())
    .collect(Collectors.toList());
```

---

## Basic Example

```java
List<String> upperNames = names.stream()
    .map((var name) -> name.toUpperCase())
    .collect(Collectors.toList());
```

This is equivalent to:

```java
List<String> upperNames = names.stream()
    .map(name -> name.toUpperCase())
    .collect(Collectors.toList());
```

So why use `var`?

Usually, for annotations.

---

## Annotation Example

```java
BiFunction<String, String, String> join =
    (@Nonnull var first, @Nonnull var second) -> first + second;
```

This allows tools and static analyzers to read parameter annotations.

Note:

`@Nonnull` is not part of the JDK. It usually comes from a library such as Jakarta annotations, JetBrains annotations, or another static-analysis library.

---

## Important Rules

### Rule 1: You Cannot Mix var And No var

This does not work:

```
(var first, second) -> first + second; // compile-time error
```

Use `var` for all parameters:

```
(var first, var second) -> first + second;
```

Or use no `var`:

```
(first, second) -> first + second;
```

---

### Rule 2: You Cannot Mix var And Explicit Types

This does not work:

```
(var first, String second) -> first + second; // compile-time error
```

Use all explicit types:

```
(String first, String second) -> first + second;
```

Or all `var`:

```
(var first, var second) -> first + second;
```

---

### Rule 3: Parentheses Are Required With var

This does not work:

```
var name -> name.toUpperCase(); // compile-time error
```

Use:

```
(var name) -> name.toUpperCase();
```

---

### Rule 4: var Does Not Change The Type

The type is still inferred from the functional interface.

```
Function<String, Integer> length = (var value) -> value.length();
```

Here `value` is inferred as `String`.

It is not dynamic.

---

## Daily Coding Use Cases

### 1. Normal Stream Mapping

Usually, do this:

```
names.stream()
    .map(name -> name.toUpperCase())
    .collect(Collectors.toList());
```

No need for `var`.

### 2. Annotated Lambda Parameter

Use `var` when annotation is useful:

```
users.stream()
    .filter((@Nonnull var user) -> user.isActive())
    .collect(Collectors.toList());
```

### 3. Consistent Style In Multi-Parameter Lambda

```java
Comparator<User> byName =
    (var left, var right) -> left.getName().compareTo(right.getName());
```

This works, but many teams still prefer:

```java
Comparator<User> byName =
    Comparator.comparing(User::getName);
```

---

## Best Practices

- Do not add `var` to every lambda just because it is available.
- Use normal inferred lambda syntax for simple lambdas.
- Use `var` when you need annotations on lambda parameters.
- Use `var` for all lambda parameters or none.
- Do not mix `var` with explicit parameter types.
- Remember that `var` in lambda parameters is not a preview feature. It is standard in Java 11.

---

## Quick Summary

Java 11 lets you write `var` in lambda parameters. It is mainly useful for adding annotations while keeping inferred parameter types.

