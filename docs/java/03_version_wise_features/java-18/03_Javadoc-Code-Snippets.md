---
title: Javadoc Code Snippets
parent: Java-18
nav_order: 3
---

# Java 18 - Javadoc Code Snippets (`@snippet`)

## What is Javadoc?

Javadoc is Java's built-in documentation tool.

It is used to generate HTML documentation from comments written inside Java source code.

Example:

```java
/**
 * Adds two numbers.
 */
public int add(int a, int b) {
    return a + b;
}
```

Developers use Javadoc to explain:

* classes
* interfaces
* methods
* constructors
* fields

---

# What was the problem before Java 18?

Before Java 18, adding code examples inside Javadoc was not easy.

Developers usually wrote code like this:

```java
/**
 * Example:
 *
 * <pre>{@code
 * List<String> names = List.of("John", "David");
 * }</pre>
 */
```

Problems:

* looks messy
* difficult to read
* difficult to maintain
* large examples become confusing
* external examples are hard to reuse

---

# What is `@snippet`?

Java 18 introduced the **`@snippet`** tag.

It allows developers to write clean and readable code examples directly inside Javadoc.

Instead of using `<pre>` and `{@code}`, simply use:

```
{@snippet :
    // Java code here
}
```

Much cleaner and easier to understand.

---

# Simple Example

```java
/**
 * Returns the square of a number.
 *
 * Example:
 *
 * {@snippet :
 * int result = square(5);
 * System.out.println(result);
 * }
 */
public int square(int number) {
    return number * number;
}
```

The generated documentation displays the example as formatted Java code.

---

# Real-world Example

Suppose you create a utility method.

```java
public class StringUtils {

    public static boolean isEmpty(String value) {
        return value == null || value.isBlank();
    }
}
```

Documentation

```java
/**
 * Checks whether a string is empty.
 *
 * Example:
 *
 * {@snippet :
 * boolean result = StringUtils.isEmpty("");
 * System.out.println(result);
 * }
 */
```

Anyone reading the documentation immediately understands how to use the method.

---

# Banking Example

Suppose your banking application has

```java
public class AccountService {

    public Account getAccount(Long accountId) {

    }
}
```

Documentation

```java
/**
 * Returns account details.
 *
 * Example:
 *
 * {@snippet :
 * Account account =
 *         accountService.getAccount(101L);
 *
 * System.out.println(account);
 * }
 */
```

A new developer can understand the API without reading the implementation.

---

# Before Java 18

```java
/**
 * <pre>{@code
 * System.out.println("Hello");
 * }</pre>
 */
```

---

# Java 18

```java
/**
 * {@snippet :
 * System.out.println("Hello");
 * }
 */
```

Cleaner.

More readable.

Less typing.

---

# External Snippets

Instead of writing large examples inside comments, Java also allows using code stored in separate files.

Suppose

```text
examples/

EmployeeExample.java
```

Javadoc can refer to that file.

Advantages

* no duplicate code
* easy to maintain
* same example can be reused
* easier to test

This is very useful for large libraries.

---

# Why was it introduced?

The main goals were:

* cleaner documentation
* readable code examples
* reusable examples
* easier maintenance
* better developer experience

---

# Where is it used?

Mostly in

* Java libraries
* Frameworks
* Spring libraries
* SDKs
* Open-source projects
* Internal company libraries

---

# Will normal business applications use it?

Usually not.

If your project has

* 10 REST APIs
* service classes
* repository classes

you may never write a single `@snippet`.

However,

If you build

* reusable utilities
* company framework
* SDK
* public API
* shared library

then `@snippet` becomes very useful.

---

# Advantages

* cleaner than `<pre>{@code}`
* easier to read
* easier to maintain
* supports external code examples
* improves generated documentation
* better developer experience

---

# Limitations

* useful only in Javadoc
* does not affect application execution
* mainly benefits library developers
* not required in every project

---

# Negative Cases / Common Mistakes

## 1. Thinking `@snippet` executes code

Wrong.

It only displays code in documentation.

---

## 2. Writing outdated examples

Poor documentation is worse than no documentation.

Always keep examples updated.

---

## 3. Writing very large snippets

Large examples reduce readability.

Keep snippets short.

If the example is long, move it to an external file.

---

## 4. Forgetting imports or required context

Readers should be able to understand the example easily.

Avoid incomplete examples.

---

# Best Practices

✅ Keep snippets short.

✅ Show the most common use case.

✅ Use meaningful variable names.

✅ Keep examples up to date.

✅ Use external snippet files for large examples.

✅ Make examples copy-paste friendly.

✅ Focus on showing **how to use** the API rather than how it works internally.

---

# Daily Developer Usage

As an application developer,

you will mostly **read** Javadoc snippets rather than write them.

You may write them when:

* creating reusable utility classes
* building internal company libraries
* documenting public APIs
* developing SDKs

---

# Quick Revision

* Introduced in Java 18
* New Javadoc tag: `@snippet`
* Used to write clean code examples
* Replaces the older `<pre>{@code}` style
* Supports both inline and external code examples
* Improves API documentation readability
* Does not execute code
* Mainly useful for libraries, SDKs, and reusable APIs

---

# One-line Summary

`@snippet` is a Java 18 Javadoc feature that makes it easy to write clean, readable, and maintainable code examples directly inside API documentation.
