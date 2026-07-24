---
title: GC, Memory, and Runtime Improvements
parent: Java-15
nav_order: 3
---

# Java 15 GC, Memory, and Runtime Improvements

## Overview

Java 15 made ZGC and Shenandoah production features, re-incubated the Foreign-Memory Access API, and disabled/deprecated biased locking. These changes matter most to teams operating latency-sensitive services, memory-heavy applications, or native-memory integrations.

## ZGC became production-ready

ZGC is a scalable low-latency garbage collector intended to keep pause times short even with large heaps. Java 15 removed its experimental status; it did not make ZGC the default collector.

```bash
java -XX:+UseZGC -Xms8g -Xmx8g -jar service.jar
```

### When to consider it

- Large heaps with strict tail-latency objectives.
- Services where GC pauses materially affect request SLOs.
- Workloads validated with production-like allocation rates and heap sizes.

### Operational guidance

- Start with G1 unless measurements justify another collector.
- Fix allocation leaks and unbounded caches before changing collectors.
- Compare p50/p95/p99 latency, allocation rate, CPU, RSS, and GC logs under representative load.
- Validate the exact JDK vendor/version and deployment platform.

## Shenandoah became production-ready

Shenandoah is another low-pause collector. It performs much of its work concurrently and aims to keep pauses less dependent on heap size.

```bash
java -XX:+UseShenandoahGC -Xms8g -Xmx8g -jar service.jar
```

Like ZGC, production status means the feature no longer requires experimental flags in mainline JDK 15; it is not a guarantee that it is the best collector for every workload.

## Choosing between G1, ZGC, and Shenandoah

| Concern                                | Practical starting point                                         |
|----------------------------------------|------------------------------------------------------------------|
| General server application             | G1, with measured heap sizing and GC logs                        |
| Very strict pause goals on large heaps | Evaluate ZGC or Shenandoah under load                            |
| Throughput-first batch job             | Measure G1 and Parallel GC before adopting a low-pause collector |
| Memory leak/unbounded allocation       | Fix the application first; no collector solves this              |

## Foreign-Memory Access API — second incubator

JEP 383 continued the incubating API for safe, efficient access to memory outside the Java heap. In Java 15 it lives in `jdk.incubator.foreign`; this API is historical and changed significantly before the later standardized Foreign Function & Memory API.

```
// Java 15 incubator-era example; do not copy into modern Java unchanged.
try (MemorySegment segment = MemorySegment.allocateNative(1024)) {
    // bounded native-memory access
}
```

### Why it exists

Direct buffers have size/lifecycle limitations, `Unsafe` is unsupported and dangerous, and JNI adds cross-language complexity. The incubator API explored explicit lifecycle, spatial bounds, and thread-confinement models for foreign memory.

### Safety and migration notes

- Java 15 incubator APIs require explicit module flags and are not stable application contracts.
- Do not introduce the Java 15 API into a new service; use the API appropriate for the currently supported JDK after validating migration guidance.
- Keep off-heap ownership explicit and close resources deterministically.
- Benchmark against direct buffers/JNI only for a demonstrated need.

## Biased locking disabled and deprecated

Biased locking optimized uncontended `synchronized` blocks by biasing an object toward one thread. Modern JVM/runtime changes reduced its value and introduced maintenance cost, so Java 15 disables it by default and deprecates related options.

### Backend impact

Most applications require no code change. Treat old JVM flags such as `-XX:+UseBiasedLocking` as upgrade-review items. Investigate real lock contention with JFR, thread dumps, and metrics instead of relying on an obsolete lock optimization.

## Common mistakes

1. Selecting ZGC/Shenandoah solely because they are production features.
2. Expecting a GC switch to solve a leak or excessive allocation rate.
3. Using Java 15 incubator foreign-memory APIs as a long-lived public library contract.
4. Carrying biased-locking flags into newer deployments without checking support.
5. Comparing collectors without representative traffic, heap sizing, and GC logging.

