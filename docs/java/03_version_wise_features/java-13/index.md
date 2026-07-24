---
title: Java-13
parent: Java Versions
nav_order: 13
---

# Java 13 - Features / Enhancements

Java 13 was released on **17 September 2019**.

Java 13 was a **short-term release**, not an LTS release. It came after Java 12 and continued Java's six-month release cycle.

For backend developers, Java 13 is mainly important because it introduced **text blocks as a preview feature** and improved **switch expressions** with the `yield` keyword.

---

## Java 13 Feature Map

| Area        | Feature                                        | Developer Meaning                                                  |
|-------------|------------------------------------------------|--------------------------------------------------------------------|
| Language    | Text Blocks - Preview                          | Write multi-line SQL, JSON, HTML, XML, etc. more cleanly           |
| Language    | Switch Expressions - Second Preview            | Use `switch` as an expression, now with `yield`                    |
| NIO         | `FileSystems.newFileSystem(Path, ...)` methods | Easier to open ZIP/JAR files as file systems                       |
| NIO         | Absolute bulk `ByteBuffer` get/put methods     | Copy buffer data without changing buffer position                  |
| XML         | DOM/SAX namespace-aware factory methods        | Create namespace-aware XML parsers more directly                   |
| JVM Startup | Dynamic CDS Archives                           | Improve startup by archiving loaded application classes            |
| GC          | ZGC uncommits unused memory                    | ZGC can return unused heap memory to the OS                        |
| Networking  | Reimplemented legacy Socket API                | Cleaner socket implementation with little/no app code change       |
| Unicode     | Unicode 12.1 support                           | More Unicode characters and scripts supported                      |
| Security    | TLS/keytool/Kerberos improvements              | Better TLS visibility and Kerberos referral handling               |
| Migration   | Removed/deprecated old APIs/options            | Cleanup around old javadoc APIs, `rmic`, `noverify`, old JSSE APIs |

---

## Preview / Experimental / Special Status

| Feature                          | Status In Java 13                 | What Happened Later                                  |
|----------------------------------|-----------------------------------|------------------------------------------------------|
| Text Blocks                      | Preview                           | Preview again in Java 14, became standard in Java 15 |
| Switch Expressions               | Second preview                    | Became standard in Java 14                           |
| ZGC                              | Experimental                      | Became production in Java 15                         |
| String text-block helper methods | Preview-associated API in Java 13 | Became standard in Java 15 with final text blocks    |

Important:

Preview features are not final. They need explicit compile/run flags and can change before becoming standard.

---

## Daily Coding Topics To Focus First

These are most useful for normal backend development:

1. Text Blocks - Preview
2. Switch Expressions - Second Preview
3. NIO and XML API improvements

---

## JVM / Platform Topics To Know

These are useful for performance, migration, and interviews:

1. Dynamic CDS Archives
2. ZGC uncommit unused memory
3. Reimplemented legacy Socket API
4. Unicode 12.1 support
5. Security and migration cleanup notes

---

## What Java 13 Means For Backend Developers

Focus most on:

- Text blocks for readable SQL, JSON, HTML, XML, and test data.
- Java 13 switch expression syntax using `yield`.
- Preview feature rules and compile/run flags.
- NIO improvements if you work with ZIP/JAR files or buffers.
- XML namespace-aware factory methods if you parse XML.
- JVM startup and ZGC memory behavior at a high level.

For interviews, Java 13 is commonly discussed as:

- The first preview of text blocks.
- The second preview of switch expressions.
- The release where `yield` replaced Java 12's `break value` style.
- A small release with useful language previews and JVM cleanup.

---

## Quick Summary

Java 13 introduced text blocks as a preview feature, improved switch expressions with `yield`, added NIO and XML API conveniences, introduced Dynamic CDS Archives, improved ZGC by returning unused memory to the OS, reimplemented the legacy Socket API, upgraded Unicode support, and included security/migration cleanup.
