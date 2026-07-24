---
title: Text Blocks Preview
parent: Java-13
nav_order: 1
---

# Java 13 - Text Blocks Preview

Java 13 introduced **text blocks** as a **preview feature**.

Simple meaning:

Text blocks let us write multi-line strings directly in Java code.

They are very useful for:

- SQL
- JSON
- XML
- HTML
- email templates
- test data
- generated code snippets

---

## Preview Feature Status

| Version | Status                      |
|---------|-----------------------------|
| Java 13 | Preview                     |
| Java 14 | Second preview              |
| Java 15 | Standard production feature |

Important:

In Java 13, text blocks need preview flags.

In modern Java projects, use Java 15+ text blocks as the final version.

---

## Why was it introduced?

Before text blocks, multi-line strings were painful.

Old style:

```java
String json = "{\n" +
        "  \"id\": 101,\n" +
        "  \"status\": \"PAID\"\n" +
        "}";
```

This works, but it is noisy.

We have to manage:

- `\n`
- quotes inside JSON
- string concatenation
- indentation

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

This is easier to read because the string looks close to the actual JSON.

---

## Daily backend use case: SQL query

Old style:

```java
String query = "select id, name, status\n" +
        "from users\n" +
        "where status = ?\n" +
        "order by name";
```

Text block style:

```java
String query = """
        select id, name, status
        from users
        where status = ?
        order by name
        """;
```

This is much better for long SQL queries.

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
- API mock data
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

But text blocks are very useful for small templates and tests.

---

## Syntax rules

Text blocks start with three double quotes:

```
"""
```

and end with three double quotes:

```
"""
```

The opening delimiter must be followed by a line terminator.

Valid:

```java
String text = """
        Java
        """;
```

Invalid:

```java
String text = """Java""";
```

Possible compile error:

```text
illegal text block open delimiter sequence
```

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

Example:

```java
String text = """
        line 1
        line 2
        """;
```

The actual value is:

```text
line 1
line 2
```

not:

```text
        line 1
        line 2
```

This lets us indent text blocks nicely inside Java code without adding unwanted spaces to the string.

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

But if you need three double quotes together, escape at least one quote.

```java
String sample = """
        A nested delimiter looks like \"""
        """;
```

---

## Java 13 helper String methods

Java 13 also had preview-associated String helper methods:

```
stripIndent()
translateEscapes()
formatted(...)
```

In Java 13, these methods were tied to the preview text-block feature and were marked as preview-related.

They became normal production APIs with final text blocks in Java 15.

Example:

```java
String message = """
        Hello %s,
        Your order id is %s.
        """.formatted("Mohan", "ORD-101");
```

For Java 13, remember preview flags are required.

---

## Compile and run Java 13 preview code

Compile:

```bash
javac --release 13 --enable-preview TextBlockDemo.java
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
javac --release 13 TextBlockDemo.java
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
compile-time error
```

---

### 3. No closing delimiter

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

### 4. Trailing spaces may be removed

Text blocks remove incidental trailing whitespace.

If trailing spaces are important, be careful.

Example:

Markdown sometimes uses two trailing spaces to force a line break.

In such rare cases, plain string literals or explicit escapes may be clearer.

---

### 5. It is still just a String

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
2. Do not use text blocks for very small one-line strings.
3. Be aware of the final newline.
4. Avoid depending on invisible trailing spaces.
5. Keep dynamic values out of raw text; use parameters for SQL.
6. For SQL, still use prepared statements instead of string concatenation.
7. In Java 13, compile and run with preview flags.
8. In modern projects, use Java 15+ final text blocks.

---

## Interview one-liner

Java 13 introduced text blocks as a preview feature to make multi-line strings like SQL, JSON, XML, and HTML easier to write and read. Text blocks became standard in Java 15.

---

