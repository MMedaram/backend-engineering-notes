---
title: Java-14
parent: Java Versions
nav_order: 14
---

# Java 14 - Features / Enhancements

Java 14 was released on **17 March 2020**.

Java 14 was a **short-term release**, not an LTS release.

For backend developers, Java 14 is important because:

- `switch` expressions became a standard production feature.
- `record` classes arrived as a preview feature.
- pattern matching for `instanceof` arrived as a preview feature.
- helpful `NullPointerException` messages made debugging easier.
- text blocks came back as a second preview with small improvements.
- old JVM/tools like CMS GC and Pack200 were removed.

---

## Java 14 Feature Map

| Area       | Feature                                     | Developer Meaning                                                |
|------------|---------------------------------------------|------------------------------------------------------------------|
| Language   | Records - Preview                           | Create simple data carrier classes with less boilerplate         |
| Language   | Pattern Matching for `instanceof` - Preview | Check type and create a typed variable in one step               |
| Language   | Switch Expressions - Standard               | Use `switch` safely as a value-producing expression              |
| Debugging  | Helpful `NullPointerException` messages     | JVM tells which part of a chain was `null`                       |
| Language   | Text Blocks - Second Preview                | Write multi-line SQL, JSON, XML, HTML, etc. more cleanly         |
| Tooling    | Packaging Tool - Incubator                  | Create native app packages using `jpackage`                      |
| Monitoring | JFR Event Streaming                         | Read JFR events continuously for monitoring                      |
| Memory     | Foreign-Memory Access API - Incubator       | Access memory outside the Java heap through an experimental API  |
| Memory     | Non-Volatile Mapped Byte Buffers            | Advanced support for persistent memory use cases                 |
| GC         | G1 NUMA-aware allocation                    | Better G1 behavior on large NUMA machines                        |
| GC         | ZGC on macOS and Windows                    | ZGC became available on more platforms as experimental           |
| Migration  | CMS GC removed                              | Old `-XX:+UseConcMarkSweepGC` deployments must move away         |
| Migration  | Pack200 removed                             | Old `pack200`, `unpack200`, and `java.util.jar.Pack200` are gone |
| Security   | Weak named curves disabled                  | Old TLS/certificate setups may fail after upgrade                |
| Security   | `keytool -keyalg` default removed           | Key generation must specify the algorithm                        |

---

## Preview / Incubator / Experimental / Special Status

| Feature                           | Status In Java 14 | What Happened Later                                                                   |
|-----------------------------------|-------------------|---------------------------------------------------------------------------------------|
| Records                           | Preview           | Second preview in Java 15, standard in Java 16                                        |
| Pattern Matching for `instanceof` | Preview           | Second preview in Java 15, standard in Java 16                                        |
| Text Blocks                       | Second preview    | Standard in Java 15                                                                   |
| Switch Expressions                | Standard          | Preview was in Java 12 and Java 13                                                    |
| `jpackage` Packaging Tool         | Incubator         | Incubator again in Java 15, standard in Java 16                                       |
| Foreign-Memory Access API         | Incubator         | Evolved over later releases into Foreign Function and Memory API, standard in Java 22 |
| ZGC on macOS/Windows              | Experimental      | ZGC became a production feature in Java 15                                            |

Important:

Preview features need explicit compile/run flags.

### Records — Preview

Java 14 introduced records as compact data carriers behind preview flags. Java 16 standardized them.

[Read the canonical Java 16 Records note →](../java-16/01-records-standard.md)

### Pattern Matching for `instanceof` — Preview

Java 14 introduced a combined type test and flow-scoped binding as a preview feature. Java 16 standardized it.

[Read the canonical Java 16 Pattern Matching note →](../java-16/02-pattern-matching-instanceof-standard.md)

### Text Blocks — Second Preview

Java 14 refined text blocks, including the `\s` and line-continuation escapes. Java 15 standardized the feature.

[Read the canonical Java 15 Text Blocks note →](../java-15/01-text-blocks-standard.md)

Incubator APIs are not final and can change in later Java versions.

Experimental JVM features may need special JVM flags and should be tested carefully before production use.

---

## Daily Coding Topics To Focus First

These are most useful for normal backend development:

1. Records - Preview
2. Pattern Matching for `instanceof` - Preview
3. Switch Expressions - Standard
4. Helpful `NullPointerException` messages
5. Text Blocks - Second Preview

---

## JVM / Platform Topics To Know

These are useful for performance, migration, production debugging, and interviews:

1. `jpackage` packaging tool - incubator
2. JFR event streaming
3. Foreign-memory access API - incubator
4. Non-volatile mapped byte buffers
5. G1 NUMA-aware allocation
6. ZGC on macOS and Windows
7. CMS GC removal
8. Pack200 removal
9. Security and migration cleanup

---

## What Java 14 Means For Backend Developers

Focus most on:

- Using final `switch` expressions cleanly in mapping logic.
- Understanding records as a preview feature and knowing they became standard in Java 16.
- Understanding pattern matching for `instanceof` as a preview feature and knowing it became standard in Java 16.
- Reading helpful NPE messages during debugging.
- Knowing Java 14 text blocks are still preview and become standard in Java 15.
- Checking old JVM flags during migration, especially CMS GC and Pack200.
- Checking TLS/certificate behavior if old curves or old key generation scripts are used.

For interviews, Java 14 is commonly discussed as:

- The release where switch expressions became standard.
- The first preview of records.
- The first preview of pattern matching for `instanceof`.
- The release that introduced helpful NPE messages.
- The release that removed CMS GC and Pack200.

---

## Quick Summary

Java 14 made switch expressions production-ready, introduced records and pattern matching for `instanceof` as preview features, improved NPE messages, continued text blocks as a second preview, added useful runtime/tooling improvements like JFR event streaming and `jpackage`, and removed old pieces like CMS GC and Pack200.
