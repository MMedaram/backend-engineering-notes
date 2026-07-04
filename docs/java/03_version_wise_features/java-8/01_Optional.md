---
title: Optional
parent: Java-8
nav_order: 1
---

# Optional

`Optional<T>` represents a value that may or may not be present.

Simple meaning:

> Instead of returning null, return a box that clearly says "value exists" or "value is missing".

---

## Why Was Optional Introduced?

Before Java 8, missing values were usually represented with `null`.

```
User user = userRepository.findById(10);

if (user != null) {
    System.out.println(user.getName());
}
```

Problem:

- Developer may forget null check.
- Code can fail with `NullPointerException`.
- Method return type does not clearly say value may be missing.

Java 8 introduced `Optional` to make absence explicit.

```java
Optional<User> user = userRepository.findById(10);
```

---

## Creating Optional

### Optional.of

Use when value must not be null.

```java
Optional<String> name = Optional.of("Mohan");
```

Edge case:

```
Optional.of(null); // NullPointerException
```

---

### Optional.ofNullable

Use when value may be null.

```java
String email = getEmail();

Optional<String> optionalEmail = Optional.ofNullable(email);
```

If `email` is null, result is `Optional.empty()`.

---

### Optional.empty

Represents no value.

```java
Optional<String> empty = Optional.empty();
```

---

## Checking Value

### isPresent

```
if (user.isPresent()) {
    System.out.println(user.get().getName());
}
```

This works, but it often becomes similar to null checks.

Prefer `map`, `ifPresent`, `orElse`, or `orElseThrow` when they make code cleaner.

---

### ifPresent

Runs code only when value exists.

```
user.ifPresent(value -> System.out.println(value.getName()));
```

If Optional is empty, nothing happens.

Note:

`ifPresentOrElse` is **not Java 8**. It was added in Java 9.

---

## Transforming Optional

### map

Use `map` when you want to convert the value inside Optional.

```java
Optional<String> userName = user.map(User::getName);
```

If user exists, it extracts name.

If user is empty, result is empty.

Edge case:

If mapper returns null, result becomes `Optional.empty()`.

```
Optional<String> result = Optional.of("A").map(value -> null);
System.out.println(result); // Optional.empty
```

---

### flatMap

Use `flatMap` when the method already returns Optional.

```java
Optional<String> phone = user.flatMap(User::getPhoneNumber);
```

Without `flatMap`, you may get:

```
Optional<Optional<String>>
```

With `flatMap`, you get:

```
Optional<String>
```

---

### filter

Keeps the value only if condition is true.

```java
Optional<User> activeUser = user.filter(User::isActive);
```

If condition is false, result becomes empty.

---

## Getting Value

### get

```java
User value = user.get();
```

If Optional is empty:

```text
NoSuchElementException
```

Avoid `get()` unless you are already sure value exists.

---

### orElse

Returns value if present, otherwise returns default.

```java
String name = optionalName.orElse("Guest");
```

Important edge case:

`orElse` always evaluates the default value.

```java
String name = optionalName.orElse(createDefaultName());
```

`createDefaultName()` runs even when `optionalName` has a value.

---

### orElseGet

Lazy version of `orElse`.

```java
String name = optionalName.orElseGet(() -> createDefaultName());
```

Here `createDefaultName()` runs only if Optional is empty.

Use `orElseGet` when default value is expensive to create.

---

### orElseThrow

Throw exception when value is missing.

```java
User user = userRepository.findById(id)
    .orElseThrow(() -> new IllegalArgumentException("User not found: " + id));
```

In Java 8, `orElseThrow` requires an exception supplier.

No-argument `orElseThrow()` was added in Java 10.

---

## Daily Coding Example

```java
public String getActiveUserEmail(long userId) {
    return userRepository.findById(userId)
        .filter(User::isActive)
        .map(User::getEmail)
        .orElseThrow(() -> new IllegalArgumentException("Active user email not found"));
}
```

Flow:

1. Find user.
2. Keep only active user.
3. Extract email.
4. Throw clear exception if missing.

---

## Where To Use Optional

Good places:

- Method return type.
- Repository/service methods.
- Stream transformation result.
- Places where "missing value" is normal.

Avoid:

- Entity fields.
- DTO fields.
- Method parameters.
- Serialization models.
- JPA fields.

---

## Common Exceptions

| Code                                    | Result                   |
|-----------------------------------------|--------------------------|
| `Optional.of(null)`                     | `NullPointerException`   |
| `Optional.empty().get()`                | `NoSuchElementException` |
| `optional.orElseThrow(...)` when empty  | Your supplied exception  |
| `optional.map(null)`                    | `NullPointerException`   |
| `optional.flatMap(mapperReturningNull)` | `NullPointerException`   |

---

## Best Practices

- Use `Optional` mainly as a return type.
- Prefer `orElseGet` when default value is costly.
- Prefer `orElseThrow` for required values.
- Avoid `get()` in normal code.
- Do not use Optional fields in JPA entities.
- Do not use Optional just to replace every null blindly.

---

## Quick Summary

Optional is a clear way to represent "value may be missing". It reduces accidental null problems, but it should mainly be used as a return type, not as fields or parameters.
