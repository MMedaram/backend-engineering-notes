---
title: JVM, GC, Memory, and Incubator APIs
parent: Java-16
nav_order: 5
---

# Java 16 JVM, GC, Memory, and Incubator APIs

## Overview

Java 16 added Elastic Metaspace and ZGC concurrent thread-stack processing, while continuing several incubator APIs: Vector API, Foreign Linker API, and Foreign-Memory Access API. Treat incubators as experiments, not long-lived product contracts.

## Elastic Metaspace

Metaspace holds class metadata outside the Java heap. Elastic Metaspace improves the JVM's ability to return unused metadata memory to the operating system.

This does not eliminate class-loader leaks. If a redeploying application retains a class loader, its classes and metadata remain reachable regardless of allocator improvements.

### Production checks

- Monitor class count, loaded/unloaded classes, metaspace usage, and native RSS.
- Diagnose repeated deployment/plugin leaks with JFR, heap dumps, and class-loader analysis.
- Do not set arbitrary metaspace limits before understanding the workload.

## ZGC concurrent thread-stack processing

Java 16 improves ZGC by processing thread stacks concurrently. This is an implementation improvement for ZGC users, not a reason to select ZGC without measured pause/throughput requirements.

## Vector API — incubator

The Vector API enables expressible SIMD computations for workloads such as analytics, image processing, cryptography, and encoding. It is rarely a first-line concern for CRUD services.

```java
// Conceptual shape; API is incubating and evolves by JDK release.
var species = IntVector.SPECIES_PREFERRED;
```

Use a maintained library or benchmark heavily before introducing vector code. Hardware capability, JIT behavior, fallback paths, and API churn all matter.

## Foreign Linker and Foreign-Memory Access APIs — incubators

These APIs explored safer Java access to native functions and off-heap memory. Java 16's Foreign-Memory API is the third incubator, and the Foreign Linker API is an incubator; their names and lifecycle models changed before the later standardized FFM API.

### Safety rules

- Never treat Java 16 incubator code as stable API surface.
- Keep ownership and closure of off-heap memory explicit.
- Isolate native integration behind an adapter and test failure behavior, ABI compatibility, and platform differences.
- Use current-JDK FFM guidance for new work rather than copying Java 16 examples.

## Common mistakes

1. Blaming metaspace for a class-loader leak instead of finding the retention path.
2. Selecting ZGC based only on a feature list.
3. Shipping incubator APIs as a public library contract.
4. Assuming SIMD or off-heap memory is automatically faster.
5. Ignoring native resource lifecycle and platform tests.

