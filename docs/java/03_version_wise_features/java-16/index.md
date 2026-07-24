---
title: Java-16
parent: Java Versions
nav_order: 16
---

# Java 16 - Features / Enhancements

Java 16 standardized records and pattern matching for `instanceof`. It also added `Stream.toList()`, `mapMulti`, `jpackage`, Unix-domain socket channels, and stronger encapsulation of JDK internals.

## Java 16 Feature Map

| Area       | Feature                                      | Developer Meaning                                                       |
|------------|----------------------------------------------|-------------------------------------------------------------------------|
| Language   | Records — Standard                           | Compact data-carrier classes without preview flags                      |
| Language   | Pattern Matching for `instanceof` — Standard | Type test and typed binding in one flow-safe expression                 |
| Streams    | `toList()` and `mapMulti()`                  | Modern immutable collection and efficient stream transformation options |
| Tooling    | `jpackage`                                   | Native application package generation                                   |
| Networking | Unix-domain socket channels                  | Local IPC without TCP networking                                        |

## Preview history

### Records — Standard

Java 16 standardized records after preview releases in Java 14 and Java 15.

[Read the canonical Records note →](01-records-standard.md)

### Pattern Matching for `instanceof` — Standard

Java 16 standardized the feature after preview releases in Java 14 and Java 15.

[Read the canonical Pattern Matching for `instanceof` note →](02-pattern-matching-instanceof-standard.md)

### Sealed Classes — Second Preview

Java 16 continued sealed classes as a refined preview. Java 17 standardized them.

[Read the canonical Java 17 Sealed Classes note →](../java-17/01-sealed-classes-standard.md)

