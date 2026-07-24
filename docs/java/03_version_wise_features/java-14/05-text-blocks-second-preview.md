---
title: Text Blocks Second Preview
parent: Java-14
nav_order: 5
---

# Java 14 - Text Blocks Second Preview

Java 14 continued **text blocks** as a **second preview feature**.

Simple meaning:

Text blocks let us write multi-line strings directly in Java code.

They are useful for:

- SQL
- JSON
- XML
- HTML
- email templates
- test payloads
- generated code snippets

---

## Preview Feature Status

| Version | Status                      |
|---------|-----------------------------|
| Java 13 | First preview               |
| Java 14 | Second preview              |
| Java 15 | Standard production feature |

Important:

In Java 14, text blocks still need preview flags.

In modern Java projects, use Java 15+ text blocks as the final version.

---

## What changed in Java 14?

Java 14 added two useful escape sequences for text blocks:

| Escape             | Meaning              |
|--------------------|----------------------|
| `\` at end of line | Suppress the newline |
| `\s`               | Add a single space   |

These give more control over formatting.

---

## Why text blocks were introduced

Before text blocks, multi-line strings were noisy.

Old style:

```java
String json = "{\n" +
        "  \"id\": 101,\n" +
        "  \"status\": \"PAID\"\n" +
        "}";
```

Problems:

- too many `\n`
- escaped quotes inside JSON
- string concatenation
- hard-to-read indentation

---

## Text block style

```java
String json = """
        {
          "id": 101,
          "status": "PAID"
        }
        """;
```

The string looks close to the actual JSON.

---

## Daily backend use case: SQL query

```java
String query = """
        select id, name, status
        from users
        where status = ?
        order by name
        """;
```

This is easier to read than concatenated SQL.

Important:

Still use parameters for values.

Bad:

```java
String query = """
        select id, name
        from users
        where name = '""" + name + "'";
```

Better:

```java
String query = """
        select id, name
        from users
        where name = ?
        """;
```

Use `PreparedStatement`, JDBC template parameters, JPA parameters, or your framework's safe binding mechanism.

---

## Daily backend use case: JSON test data

```java
String requestBody = """
        {
          "customerId": 1001,
          "amount": 2500,
          "currency": "INR"
        }
        """;
```

Useful in:

- unit tests
- integration tests
- API mocks
- documentation examples

---

## Daily backend use case: HTML/email template

```java
String email = """
        <html>
          <body>
            <p>Hello Mohan,</p>
            <p>Your order is confirmed.</p>
          </body>
        </html>
        """;
```

For real production email templates, a template engine is usually better.

Text blocks are still very useful for small templates and tests.

---

## New in Java 14: suppress newline

Use `\` at the end of a line when you do not want the newline in the actual string.

```java
String message = """
        This is one \
        single line.
        """;
```

The value is:

```text
This is one single line.
```

This is useful when source code readability needs line breaks but the actual string should not.

---

## New in Java 14: explicit single space

Use `\s` when a trailing space matters.

```java
String text = """
        Name:\s
        """;
```

The value contains a space after `Name:`.

This is useful because text blocks can remove incidental whitespace, and trailing spaces are hard to see in code review.

---

## Final newline behavior

If the closing delimiter is on its own line, the string includes a final newline.

```
String text = """
        Java
        """;

System.out.println(text.equals("Java\n"));
```

Output:

```text
true
```

If you do not want the final newline, put the closing delimiter on the last content line.

```
String text = """
        Java""";

System.out.println(text.equals("Java"));
```

Output:

```text
true
```

---

## Incidental indentation

Java removes common incidental indentation.

```java
String text = """
        line 1
        line 2
        """;
```

The value is:

```text
line 1
line 2
```

not:

```text
        line 1
        line 2
```

This lets you indent text blocks nicely inside Java code.

---

## Line endings are normalized

Text block line endings are normalized to:

```text
\n
```

This helps when code moves between Windows, Linux, and macOS.

---

## Quotes inside text blocks

Normal double quotes do not need escaping.

```java
String json = """
        {
          "name": "Mohan"
        }
        """;
```

If you need three double quotes together, escape at least one quote.

```java
String sample = """
        A nested delimiter looks like \"""
        """;
```

---

## Compile and run Java 14 preview code

Compile:

```bash
javac --release 14 --enable-preview TextBlockDemo.java
```

Run:

```bash
java --enable-preview TextBlockDemo
```

Run JAR:

```bash
java --enable-preview -jar app.jar
```

---

## Edge cases and negative cases

### 1. Missing preview flag

```bash
javac --release 14 TextBlockDemo.java
```

Possible result:

```text
error: text blocks are a preview feature and are disabled by default
```

---

### 2. No line terminator after opening delimiter

Bad:

```java
String text = """Java""";
```

Possible result:

```text
illegal text block open delimiter sequence
```

Valid:

```java
String text = """
        Java
        """;
```

---

### 3. Missing closing delimiter

Bad:

```
String text = """
        Java
```

Possible result:

```text
compile-time error
```

---

### 4. Unexpected final newline

```java
String text = """
        OK
        """;
```

The value is:

```text
OK

```

Meaning:

It contains `OK\n`.

If exact matching matters in tests, check final newline carefully.

---

### 5. Trailing spaces are hard to see

If trailing spaces matter, do not rely on invisible spaces.

Use `\s`:

```java
String label = """
        Name:\s
        """;
```

---

### 6. Text blocks are not string interpolation

Bad assumption:

```java
String name = "Mohan";

String message = """
        Hello ${name}
        """;
```

This does not replace `${name}` automatically.

Use `formatted()`:

```java
String message = """
        Hello %s
        """.formatted(name);
```

---

### 7. It is still just a String

```
String a = """
        Java
        """;

String b = "Java\n";

System.out.println(a.equals(b));
```

Output:

```text
true
```

Text blocks do not create a new string type.

---

## Best practices

1. Use text blocks for SQL, JSON, XML, HTML, and test payloads.
2. Do not use text blocks for simple one-line strings.
3. Be careful with the final newline.
4. Use `\s` when a trailing space must be visible.
5. Use line continuation `\` when source code should wrap but the string should not.
6. Do not concatenate user input into SQL text blocks.
7. Use `formatted()` for simple message formatting.
8. In Java 14, compile and run with preview flags.
9. In modern projects, use Java 15+ final text blocks.

---

## Summary one-liner

Java 14 provided the second preview of text blocks and added escapes for newline control and explicit spaces. Text blocks became standard in Java 15.

