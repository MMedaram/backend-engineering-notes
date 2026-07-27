---
title: UTF-8 Default Encoding
parent: Java-18
nav_order: 1
---

# Java 18 - Why UTF-8 Became the Default Encoding

## What is character encoding?

Computers store everything as bytes.

A character encoding is a rule that tells Java how to convert text into bytes and bytes back into text.

Common encodings are:

* ASCII
* UTF-8
* ISO-8859-1
* Windows-1252

---

## Why was this needed?

Text can look different on different systems if the encoding is not the same.

Example:

* developer machine uses one encoding
* production server uses another encoding

Then the same file or string may be read differently.

This can create issues like:

* wrong names in files
* broken special characters
* unreadable text
* inconsistent behavior between laptop and server

---

## What was the problem before Java 18?

Before Java 18, Java used the **operating system default encoding** when a charset was not specified.

That meant:

* Windows could use one default
* Linux could use another default
* macOS could use another default

So the same Java code could behave differently on different machines.

---

## Simple example

Suppose a file contains:

* `José`
* `नमस्ते`
* `こんにちは`

If Java reads the file using the wrong encoding, the text may become corrupted.

Example of bad output:

* `JosÃ©`
* unreadable symbols
* wrong characters

---

## Why UTF-8?

Java 18 made UTF-8 the default because UTF-8 is:

* widely used
* supports all languages
* compatible with ASCII
* standard for modern web applications
* suitable for REST APIs, JSON, XML, YAML, and text files

It gives the same result across all platforms.

---

## What changed in Java 18?

Starting with Java 18:

* Java uses **UTF-8 by default**
* this happens on all platforms
* the behavior is now more predictable

This reduces encoding-related bugs.

---

## Banking example

In a banking system, you may store customer details such as:

* names
* address text
* remarks
* transaction notes
* file data

If the wrong encoding is used, customer names like `José`, `Müller`, or `राम` may be displayed incorrectly.

With UTF-8 as the default, the text is handled more consistently.

---

## Example in code

```java
String text = Files.readString(Path.of("customer.txt"));
```

In Java 18 and later, this uses UTF-8 by default if no charset is specified.

---

## Can we still use another encoding?

Yes.

You can still specify a charset manually when needed.

```java
String text = Files.readString(Path.of("customer.txt"), StandardCharsets.ISO_8859_1);
```

Use another encoding only when you are working with legacy files or systems that do not use UTF-8.

---

## Developer best practice

Use UTF-8 for:

* source code files
* JSON files
* XML files
* YAML files
* properties files
* SQL scripts
* logs
* text files

When reading old files, always check the real encoding and specify it clearly.

---

## Negative cases / common mistakes

### 1. Assuming all files are UTF-8

Not always true.

Older systems may use a different encoding.

---

### 2. Not specifying charset for legacy files

This can cause wrong text to appear.

---

### 3. Mixing encodings in one application

This can cause unpredictable results.

---

### 4. Thinking UTF-8 fixes all text issues

UTF-8 only solves encoding consistency.

It does not fix bad data or wrong file content.

---

## Why this matters for developers

This change helps developers because:

* code behaves the same on every machine
* less production confusion
* fewer text-related bugs
* better support for global languages
* better fit with modern cloud and web systems

---

## Important point

Java 18 changed the **default** encoding.

It did **not** remove support for other encodings.

You can still choose a different charset when required.

---

## Quick revision

* Encoding is the rule used to convert text into bytes
* Before Java 18, Java used the operating system default encoding
* That caused different behavior on different machines
* Java 18 changed the default to UTF-8
* UTF-8 is now the standard default on all platforms
* You can still use another charset if needed

---

## One-line summary

Java 18 made UTF-8 the default encoding so Java applications behave consistently across all systems and avoid text corruption caused by different platform defaults.
