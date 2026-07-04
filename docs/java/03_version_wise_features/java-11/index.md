---
title: Java-11
parent: Java Versions
nav_order: 11
---

# Java 11 - Features / Enhancements

Java 11 was released on **25 September 2018**.

Java 11 is an **LTS release**. Many companies moved from Java 8 to Java 11 because it gave them a stable long-term baseline after Java 9 and Java 10.

For backend developers, Java 11 is important because it added useful everyday APIs, made the HTTP Client standard, improved JVM tooling.

---

1. [Everyday API Improvements](01-everyday-api-improvements.md)
2. [HTTP Client Standard API](02-http-client-standard-api.md)
3. [var In Lambda Parameters](03-var-in-lambda-parameters.md)
4. [Single-File Source-Code Programs](04-single-file-source-code-programs.md)
5. [Removed Java EE and CORBA Modules](05-removed-java-ee-and-corba-modules.md)
6. [JVM Tooling and GC Improvements](06-jvm-tooling-and-gc-improvements.md)
7. [Security and Crypto Improvements](07-security-and-crypto-improvements.md)
8. [Deprecations and Migration Notes](08-deprecations-and-migration-notes.md)

---

## Java 11 Feature Map

| Area        | Feature                                 | Developer Meaning                                      |
|-------------|-----------------------------------------|--------------------------------------------------------|
| Strings     | `isBlank`, `strip`, `lines`, `repeat`   | Cleaner text handling                                  |
| Files       | `readString`, `writeString`             | Easier small file read/write                           |
| Optional    | `isEmpty`                               | Clearer absent-value checks                            |
| Functional  | `Predicate.not`                         | Cleaner negative filters                               |
| Collections | `toArray(IntFunction)`                  | Easier typed array conversion                          |
| HTTP        | Standard `java.net.http.HttpClient`     | Built-in HTTP/2 and async HTTP client                  |
| Language    | `var` in lambda parameters              | Allows annotations while keeping inferred lambda types |
| Tools       | Single-file source launcher             | Run small Java files without manual `javac`            |
| Migration   | Java EE and CORBA modules removed       | Add external dependencies for JAXB, JAX-WS, etc.       |
| JVM         | Flight Recorder                         | Production-friendly runtime profiling                  |
| JVM         | Low-overhead heap profiling             | Better allocation profiling for tools                  |
| GC          | Epsilon GC                              | Experimental no-op GC for testing                      |
| GC          | ZGC                                     | Experimental low-latency GC in Java 11                 |
| Security    | TLS 1.3                                 | Modern TLS support                                     |
| Security    | ChaCha20-Poly1305, Curve25519, Curve448 | Modern crypto algorithms                               |
| Cleanup     | Nashorn deprecated for removal          | JavaScript engine removed later in Java 15             |
| Cleanup     | Pack200 deprecated for removal          | Pack200 removed later in Java 14                       |

---

## Preview / Incubator / Experimental Status

Java 11 did **not** introduce preview language features.

But it did include features with special status:

| Feature     | Status In Java 11                 | What Happened Later                                                    |
|-------------|-----------------------------------|------------------------------------------------------------------------|
| HTTP Client | Standard in Java 11               | Incubated in Java 9 and Java 10, became production standard in Java 11 |
| ZGC         | Experimental in Java 11           | Became production in Java 15                                           |
| Epsilon GC  | Experimental in Java 11           | Still mainly a testing/profiling tool, not a normal production GC      |
| Nashorn     | Deprecated for removal in Java 11 | Removed in Java 15                                                     |
| Pack200     | Deprecated for removal in Java 11 | Removed in Java 14                                                     |

---

## What Java 11 Means For Backend Developers

Focus most on:

- String and file utility methods.
- Standard HTTP Client.
- `Optional.isEmpty`.
- `Predicate.not`.
- Java 8 to Java 11 migration issues.
- Java EE module removal.
- TLS 1.3 compatibility.
- JVM tools like Flight Recorder.

For interviews, Java 11 is commonly discussed as:

- The next major enterprise baseline after Java 8.
- The release where the new HTTP Client became standard.
- The release that removed Java EE and CORBA modules from the JDK.

---

## Quick Summary

Java 11 is an LTS release. It added useful APIs for String, Files, Optional, Predicate, and collections. It standardized the HTTP Client API, allowed `var` in lambda parameters, added single-file source execution, removed Java EE and CORBA modules, introduced Flight Recorder, added TLS 1.3, and included experimental GCs like Epsilon and ZGC.

