---
title: String indent and transform
parent: Java-12
nav_order: 2
---

# Java 12 - String indent() and transform()

Java 12 added two useful methods to `String`:

```
indent(int n)
transform(Function<String, R> function)
```

These are small methods, but they are helpful in daily code when working with multi-line text and string pipelines.

---

## Quick Summary

| Method                | Use                                                         |
|-----------------------|-------------------------------------------------------------|
| `indent(int n)`       | Add or remove indentation from each line                    |
| `transform(Function)` | Apply a function directly to a string and return any result |

---

# 1. String.indent(int n)

## What does it do?

`indent(n)` adjusts indentation for every line in a string.

Rules:

| n value  | Behavior                                                       |
|----------|----------------------------------------------------------------|
| `n > 0`  | Adds `n` spaces at the beginning of each line                  |
| `n < 0`  | Removes up to `n` leading whitespace characters from each line |
| `n == 0` | Keeps indentation but normalizes line endings                  |

It also normalizes line endings to `\n`.

---

## Add indentation

```
String text = "Java\nSpring";

String result = text.indent(4);

System.out.println(result);
```

Output:

```text
    Java
    Spring
```

Each line got 4 leading spaces.

---

## Remove indentation

```
String text = "    Java\n    Spring";

String result = text.indent(-4);

System.out.println(result);
```

Output:

```text
Java
Spring
```

It removed up to 4 leading whitespace characters from each line.

---

## If line has fewer spaces

```
String text = "  Java\n    Spring";

String result = text.indent(-4);

System.out.println(result);
```

Output:

```text
Java
Spring
```

For the first line, only 2 spaces existed, so only 2 were removed.

---

## n = 0 still normalizes line endings

```
String text = "Java\r\nSpring";

String result = text.indent(0);

System.out.println(result);
```

Output uses `\n` line endings.

This is useful when text can come from Windows, Linux, logs, or generated files.

---

## Important: indent adds line feed

`indent()` processes lines and adds `\n` after each resulting line.

Example:

```
String result = "Java".indent(2);

System.out.println(result.endsWith("\n"));
```

Output:

```text
true
```

This is important in tests and string comparison.

---

## Daily backend use case: format logs

```
String payload = "{\n  \"id\": 101,\n  \"status\": \"PAID\"\n}";

String logText = "Request payload:\n" + payload.indent(2);

System.out.println(logText);
```

Useful when logging multi-line:

- JSON
- SQL
- generated reports
- validation messages

---

## Daily backend use case: generate readable text

In Java 12, text blocks were not a standard feature yet.

So multi-line strings were still commonly built with `\n`:

```java
String address = "Mohan\nHyderabad\nIndia";

String emailBody = "Address:\n" + address.indent(4);
```

---

## Edge cases for indent()

| Case                     | Result                                    |
|--------------------------|-------------------------------------------|
| Empty string             | Returns empty string                      |
| `n > 0`                  | Adds spaces                               |
| `n < 0`                  | Removes leading whitespace                |
| `n == 0`                 | Normalizes line endings                   |
| Tabs at line start       | Tab counts as one whitespace character    |
| String reference is null | `NullPointerException` before method call |

Example:

```
String text = null;
text.indent(2);
```

Output:

```text
java.lang.NullPointerException
```

---

# 2. String.transform(Function)

## What does it do?

`transform()` lets us apply a function directly to a string.

The function can return:

- another string
- an integer
- a boolean
- a custom object
- any other type

Example:

```
String name = "  mohan  ";

String result = name.transform(value -> value.trim().toUpperCase());

System.out.println(result);
```

Output:

```text
MOHAN
```

---

## Why not just call methods directly?

For simple code, direct method calls are fine:

```java
String result = name.trim().toUpperCase();
```

`transform()` is useful when you want to pass the whole string into a reusable function.

---

## Daily backend use case: normalize input

```java
public String normalizeEmail(String email) {
    return email.transform(value -> value.trim().toLowerCase());
}
```

---

## Daily backend use case: convert string to object

```java
public UserId parseUserId(String rawUserId) {
    return rawUserId
            .trim()
            .transform(UserId::new);
}
```

Example helper class:

```java
public class UserId {
    private final String value;

    public UserId(String value) {
        if (value.isBlank()) {
            throw new IllegalArgumentException("User id must not be blank");
        }
        this.value = value;
    }
}
```

---

## Daily backend use case: reusable pipeline

```java
public String cleanName(String name) {
    return name.transform(this::removeExtraSpaces)
            .transform(this::capitalize);
}

private String removeExtraSpaces(String value) {
    return value.trim().replaceAll("\\s+", " ");
}

private String capitalize(String value) {
    if (value.isEmpty()) {
        return value;
    }
    return value.substring(0, 1).toUpperCase() + value.substring(1).toLowerCase();
}
```

This reads like:

```text
Take this string -> clean spaces -> capitalize
```

---

## transform() can return non-string values

```
int length = "Java".transform(String::length);

System.out.println(length);
```

Output:

```text
4
```

Another example:

```java
boolean valid = "mohan@example.com"
        .transform(email -> email.contains("@"));
```

---

## Edge cases for transform()

### 1. Function is null

```
"Java".transform(null);
```

Output:

```text
java.lang.NullPointerException
```

### 2. Function throws exception

```java
String result = "abc".transform(value -> {
    throw new IllegalArgumentException("Invalid input");
});
```

Output:

```text
java.lang.IllegalArgumentException: Invalid input
```

The exception is passed to the caller.

### 3. Function can return null

```
String result = "Java".transform(value -> null);

System.out.println(result);
```

Output:

```text
null
```

This is allowed, but use it carefully.

---

## Best practices

1. Use `indent()` for readable multi-line text.
2. Remember `indent()` normalizes line endings and usually adds `\n`.
3. Use `transform()` when it improves pipeline readability.
4. Do not use `transform()` just to look fancy.
5. Validate null before calling string methods if input can be null.
6. For locale-sensitive case conversion, prefer `Locale.ROOT` when normalizing technical values.

Example:

```java
String normalized = email.trim().toLowerCase(Locale.ROOT);
```

---

## Summary

Java 12 added `String.indent()` for adjusting multi-line indentation and `String.transform()` for applying a function directly to a string, making text formatting and string pipelines cleaner.

---

