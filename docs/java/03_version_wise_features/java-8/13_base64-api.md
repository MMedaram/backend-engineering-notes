---
title: Base64 API
parent: Java-8
nav_order: 14
---

# Base64 API

Java 8 added built-in Base64 encoding and decoding in:

```
java.util.Base64
```

---

## Why Was It Introduced?

Before Java 8, developers often used:

- `sun.misc.BASE64Encoder`
- Apache Commons Codec
- Other external libraries

Problem:

- `sun.misc` APIs are internal JDK APIs.
- External library is extra dependency for a common task.

Java 8 provided an official API.

---

## Basic Encoding

```
String text = "hello";

String encoded = Base64.getEncoder()
    .encodeToString(text.getBytes(StandardCharsets.UTF_8));

System.out.println(encoded); // aGVsbG8=
```

Decode:

```java
byte[] decodedBytes = Base64.getDecoder().decode(encoded);

String decoded = new String(decodedBytes, StandardCharsets.UTF_8);
```

---

## Types Of Base64 Encoders

| Type  | Method                             | Use                               |
|-------|------------------------------------|-----------------------------------|
| Basic | `getEncoder`, `getDecoder`         | Normal Base64                     |
| URL   | `getUrlEncoder`, `getUrlDecoder`   | URL and filename safe             |
| MIME  | `getMimeEncoder`, `getMimeDecoder` | Email/MIME style with line breaks |

---

## URL Safe Encoding

```java
String token = Base64.getUrlEncoder()
    .encodeToString(data);
```

URL-safe Base64 avoids characters that are awkward in URLs.

---

## Edge Cases

Invalid Base64:

```
Base64.getDecoder().decode("not valid base64");
```

Throws:

```text
IllegalArgumentException
```

Null input:

```
Base64.getEncoder().encode(null);
```

Throws:

```text
NullPointerException
```

---

## Base64 Is Not Encryption

Base64 only converts bytes into text.

It does not protect data.

Anyone can decode Base64.

Use hashing/encryption for security.

---

## Best Practices

- Use `java.util.Base64`, not `sun.misc` classes.
- Always specify charset when converting String to bytes.
- Use URL encoder for tokens in URLs.
- Do not treat Base64 as encryption.
- Handle invalid input during decoding.

---

## Quick Summary

Java 8 added an official Base64 API with basic, URL-safe, and MIME encoders/decoders. It is encoding, not security.

