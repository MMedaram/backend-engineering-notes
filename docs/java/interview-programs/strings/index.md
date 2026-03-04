---
title: Strings
parent: Programs – Java
nav_order: 2
---

# Strings 

A `String` in Java is:

- An **immutable** sequence of characters
- Stored in **String Pool** for memory optimization
- Represented internally as a `char[]` (Java 8) or `byte[]` with coder (Java 9+)

Once created, a `String` **cannot be modified**.

---

## Why String is Immutable?
- Security (used in URLs, DB connections, class loading)
- Thread safety
- Caching & String Pool optimization
- HashCode caching (important for HashMap)

---

## String Pool 
- String literals go to **String Pool**
- `new String("abc")` creates object in **heap**
- `intern()` moves string to pool

```java
String a = "java";
String b = "java";          // same reference
String c = new String("java"); // different reference
```

---

## Common String Operations 

### 🔹 Length & Access
- length()
- charAt(int index)
- substring(int begin)
- substring(int begin, int end)

### 🔹 Comparison

- equals()
- equalsIgnoreCase()
- compareTo()
- compareToIgnoreCase()

> ⚠️ Never use == for content comparison.

### 🔹 Searching

- contains()
- indexOf()
- lastIndexOf()
- startsWith()
- endsWith()

### 🔹 Modification (Creates New String)
- toUpperCase()
- toLowerCase()
- trim()
- strip()        // Java 11+
- stripLeading()
- stripTrailing()
- replace()
- replaceAll()
- replaceFirst()

### 🔹 Splitting & Joining
- split()
- String.join()

### Java 11+ String Enhancements (Very Important)

- isBlank()       // checks whitespace only
- lines()         // stream of lines
- repeat(int n)
- strip()         // Unicode-aware trim


### Example:

> "  ".isBlank(); // true
> 
> "ab\ncd".lines();

### Java 12+ / 15+ Updates (FYI)

- Compact Strings (internal optimization)

- Text Blocks (""" """) – not a String method, but related

```java 
String json = """
{
"name": "Java"
}
""";
```

## Mutable Alternatives

### 🔹 StringBuilder

- Mutable
- Faster
- Not thread-safe

### 🔹 StringBuffer

- Mutable
- Thread-safe
- Slower

> Use StringBuilder in almost all cases.
