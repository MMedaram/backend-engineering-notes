---
title: Text Blocks Standard
parent: Java-15
nav_order: 1
---

# Java 15 - Text Blocks Standard

Java 15 made **text blocks** a **standard production feature**.

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

## Feature Status

| Version | Status                      |
|---------|-----------------------------|
| Java 13 | First preview               |
| Java 14 | Second preview              |
| Java 15 | Standard production feature |

Important:

In Java 15, text blocks do **not** need preview flags.

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

Problems:

- too many `\n`
- escaped quotes inside JSON
- string concatenation
- harder code review
- easy formatting mistakes

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

This is easier because the Java code looks close to the actual JSON.

---

## Daily backend use case: SQL query

```java
String query = """
        select id, name, status
        from users
        where status = ?
        order by created_at desc
        """;
```

Good for:

- JDBC
- Spring JDBC
- native JPA queries
- reports
- integration tests

Important:

Text blocks make SQL readable, but they do not make SQL safe automatically.

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

Use parameters/bind variables.

---

## Daily backend use case: JSON test payload

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
- controller tests
- API mocks
- WireMock stubs
- Kafka event samples

---

## Daily backend use case: formatted message

```java
String message = """
        Hello %s,
        Your order %s is confirmed.
        """.formatted(customerName, orderId);
```

This is cleaner than building a long string manually.

For large production templates, use a template engine.

For small test data and small messages, text blocks are excellent.

---

## Syntax rules

Text blocks start and end with three double quotes:

```
"""
```

The opening delimiter must be followed by a line break.

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

Possible result:

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

This lets us indent text blocks nicely inside Java code without adding unwanted spaces.

---

## Line endings are normalized

Text block line endings are normalized to:

```text
\n
```

This helps when code moves between Windows, Linux, and macOS.

---

## Java 15 text block escapes

Use `\` at the end of a line when you want the source code to wrap but the actual string should not contain a newline.

```java
String message = """
        This is one \
        single line.
        """;
```

Actual value:

```text
This is one single line.
```

Use `\s` when a visible trailing space matters.

```java
String label = """
        Name:\s
        """;
```

This makes the trailing space intentional and visible.

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
        A delimiter looks like \"""
        """;
```

---

## Text blocks are not interpolation

Bad assumption:

```java
String name = "Mohan";

String message = """
        Hello ${name}
        """;
```

Output contains:

```text
${name}
```

It does not automatically replace the variable.

Use `formatted()`:

```java
String message = """
        Hello %s
        """.formatted(name);
```

---

## Edge cases and negative cases

### 1. No line break after opening delimiter

Bad:

```java
String text = """Java""";
```

Possible result:

```text
compile-time error
```

---

### 2. Unexpected final newline

```java
String text = """
        OK
        """;
```

Actual value:

```text
OK\n
```

If exact string matching matters, check the final newline.

---

### 3. Invisible trailing spaces

Do not rely on invisible spaces at the end of a line.

Use `\s` when a trailing space matters.

---

### 4. It is still only a String

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

### 5. Do not concatenate user input into SQL

Text blocks improve readability only.

They do not protect against SQL injection.

Always use parameters for external input.

---

## Best practices

1. Use text blocks for SQL, JSON, XML, HTML, and test payloads.
2. Do not use text blocks for simple one-line strings.
3. Be careful with the final newline.
4. Use `\s` when trailing space must be visible.
5. Use line continuation `\` when source lines should wrap but output should not.
6. Use `formatted()` for simple formatting.
7. Do not concatenate user input into SQL text blocks.
8. For real production templates, prefer a template engine.

---

## Compiler behavior

The compiler processes incidental indentation, line terminators, and text-block escapes before creating an ordinary `String` value. The closing delimiter position participates in indentation calculation. Use `\s` only when a trailing space is significant and a trailing `\` only when suppressing a physical newline is intentional.

---

## JVM behavior

A text block has no special runtime type: after compilation it is an immutable `String`. It does not defer interpolation, parameterize values, or provide a template engine. Repeated formatting still creates ordinary strings and allocations.

---

## Spring Boot usage

Text blocks are excellent for integration-test JSON payloads, SQL test fixtures, and readable static templates. For production request and response bodies, prefer serializing DTOs with Jackson rather than manually constructing JSON.

For SQL, use parameter binding through JDBC, JPA, or Spring Data. A text block makes static SQL readable; it does not make dynamic SQL safe.

---

## Hibernate/JPA notes

Use text blocks to improve readability of JPQL or native queries, including annotation-based queries. Bind every runtime value through named or positional parameters. Native-query portability, result mapping, and database-specific SQL remain separate concerns to test.

---

## Jackson behavior

Jackson receives a normal string. Text blocks are a strong fit for explicit test fixtures, but object serialization is safer for production JSON because it tracks DTO field changes and escaping rules automatically.

---

## Performance

Static text blocks have ordinary string-literal cost. Avoid repeated `.formatted()` calls in tight loops and do not build large dynamic payloads as strings when a serializer or builder is more appropriate. Profile allocation-heavy paths.

---

## Common mistakes

1. Assuming source indentation is always preserved exactly.
2. Using `.formatted()` with untrusted values to construct SQL.
3. Forgetting that the final newline can be part of the value.
4. Treating a text block as JSON validation or escaping.
5. Hiding large independently owned templates in annotations.

---

## Daily coding sample

```
String requestBody = """
    {
      "customerId": "%s",
      "amount": %s
    }
    """.formatted(customerId, amount);

mockMvc.perform(post("/payments")
        .contentType(APPLICATION_JSON)
        .content(requestBody))
    .andExpect(status().isCreated());
```

This is appropriate for an explicit integration-test fixture. In application code, pass a request DTO to Jackson instead.

---

## Interview questions

1. How is incidental indentation determined?
2. Is a text block a JVM type distinct from `String`?
3. Why does a text block not prevent SQL injection?
4. What do `\s` and trailing `\` do?
5. When would a resource file be preferable?

---

## Summary one-liner

Java 15 made text blocks a standard feature, allowing readable multi-line strings for SQL, JSON, XML, HTML, and test data without noisy escapes and concatenation.
