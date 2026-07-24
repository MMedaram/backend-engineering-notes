---
title: Java-15
parent: Java Versions
nav_order: 15
---

# Java 15 - Features / Enhancements

Java 15 was released on **15 September 2020**.

Java 15 was a **short-term release**, not an LTS release.

For backend developers, Java 15 is important because:

- text blocks became a standard production feature.
- records came back as a second preview.
- pattern matching for `instanceof` came back as a second preview.
- sealed classes were introduced as a preview feature.
- ZGC and Shenandoah became production garbage collectors.
- Nashorn JavaScript engine was removed.
- hidden classes were added for frameworks and runtime code generation.
- useful small API/tooling changes were added, like `CharSequence.isEmpty()` and compressed heap dumps from `jcmd`.

---

## Java 15 Feature Map

| Area       | Feature                                            | Developer Meaning                                                  |
|------------|----------------------------------------------------|--------------------------------------------------------------------|
| Language   | Text Blocks - Standard                             | Write multi-line SQL, JSON, XML, HTML, etc. cleanly                |
| Language   | Records - Second Preview                           | Create simple immutable data carrier classes with less boilerplate |
| Language   | Pattern Matching for `instanceof` - Second Preview | Check type and create a typed variable in one step                 |
| Language   | Sealed Classes - Preview                           | Restrict which classes can extend/implement a type                 |
| Core API   | `CharSequence.isEmpty()`                           | Check empty strings/builders/etc. through the common interface     |
| Runtime    | Hidden Classes                                     | Better support for frameworks that generate classes at runtime     |
| GC         | ZGC Production                                     | Low-latency GC became production-ready                             |
| GC         | Shenandoah Production                              | Another low-pause GC became production-ready                       |
| Memory     | Foreign-Memory Access API - Second Incubator       | Continued experimental API for off-heap/native memory              |
| Security   | EdDSA                                              | Modern Ed25519/Ed448 digital signatures                            |
| Networking | Reimplemented DatagramSocket API                   | Cleaner UDP/multicast implementation with little app-code change   |
| JVM        | Biased Locking disabled/deprecated                 | Old lock optimization disabled and marked for removal              |
| Migration  | Nashorn removed                                    | `jjs` and `jdk.nashorn.*` are gone                                 |
| Migration  | Solaris/SPARC ports removed                        | Old Solaris/SPARC builds are no longer part of JDK                 |
| Migration  | RMI Activation deprecated for removal              | Old RMI Activation usage must migrate                              |
| Tooling    | `jcmd GC.heap_dump gz`                             | Write compressed heap dumps                                        |

---

## Preview / Incubator / Experimental / Special Status

| Feature                           | Status In Java 15       | What Happened Later                                                     |
|-----------------------------------|-------------------------|-------------------------------------------------------------------------|
| Text Blocks                       | Standard                | Preview was in Java 13 and Java 14                                      |
| Records                           | Second preview          | Became standard in Java 16                                              |
| Pattern Matching for `instanceof` | Second preview          | Became standard in Java 16                                              |
| Sealed Classes                    | Preview                 | Second preview in Java 16, standard in Java 17                          |
| Foreign-Memory Access API         | Second incubator        | Evolved later into Foreign Function and Memory API, standard in Java 22 |
| ZGC                               | Production              | Experimental in earlier releases                                        |
| Shenandoah                        | Production              | Experimental in earlier releases                                        |
| Biased Locking                    | Disabled and deprecated | Removed in a later JDK release                                          |

Important:

Preview features need explicit compile/run flags.

Incubator APIs are not final and can change in later Java versions.

Standard features do not need preview flags.

---

## Daily Coding Topics To Focus First

These are most useful for normal backend development:

1. Text Blocks - Standard
2. Records - Second Preview
3. Pattern Matching for `instanceof` - Second Preview
4. Sealed Classes - Preview
5. `CharSequence.isEmpty()`

---

## JVM / Platform Topics To Know

These are useful for performance, production support, migration, and interviews:

1. ZGC production
2. Shenandoah production
3. Hidden classes
4. EdDSA security support
5. Foreign-memory access API - second incubator
6. Nashorn removal
7. Biased locking disabled/deprecated
8. DatagramSocket API reimplementation
9. RMI Activation deprecation
10. compressed heap dumps with `jcmd`

---

## What Java 15 Means For Backend Developers

Focus most on:

- Using text blocks for SQL, JSON, XML, HTML, test payloads, and logs/templates.
- Knowing records are still preview in Java 15 and became final in Java 16.
- Knowing pattern matching for `instanceof` is still preview in Java 15 and became final in Java 16.
- Understanding sealed classes as the beginning of controlled inheritance in Java, finalized in Java 17.
- Checking old systems for Nashorn usage before upgrading.
- Knowing ZGC and Shenandoah became production options.
- Checking old JVM flags around biased locking.

For interviews, Java 15 is commonly discussed as:

- The release where text blocks became final.
- The first preview of sealed classes.
- The second preview of records.
- The second preview of pattern matching for `instanceof`.
- The release where ZGC and Shenandoah became production garbage collectors.
- The release that removed Nashorn.

---

## Quick Summary

Java 15 finalized text blocks, continued records and pattern matching for `instanceof` as second previews, introduced sealed classes as a preview feature, made ZGC and Shenandoah production-ready, added hidden classes and EdDSA support, and removed or deprecated older runtime pieces such as Nashorn, Solaris/SPARC ports, biased locking, and RMI Activation.
