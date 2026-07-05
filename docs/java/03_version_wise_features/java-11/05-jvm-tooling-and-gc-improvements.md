---
title: JVM Tooling and GC Improvements
parent: Java-11
nav_order: 5
---

# JVM Tooling and GC Improvements

Java 11 added or improved several JVM-level features.

Important topics:

- Flight Recorder
- Low-overhead heap profiling
- Epsilon GC
- ZGC
- Nest-based access control
- Dynamic class-file constants

Most of these do not change daily Java syntax, but they matter for performance, observability, and JVM internals.

---

## 1. Flight Recorder

Java Flight Recorder, usually called **JFR**, records runtime events from a Java application.

It can capture:

- CPU usage
- Allocation events
- GC activity
- Thread blocking
- Locks
- Exceptions
- File and socket I/O

---

## Why Was Flight Recorder Important In Java 11?

Before Java 11, Flight Recorder was mainly associated with Oracle JDK commercial tooling.

Java 11 made Flight Recorder available as part of OpenJDK.

Developer meaning:

- More teams could use JFR for production-like profiling.
- It became easier to investigate real performance issues.

---

## Start JFR When App Starts

```bash
java -XX:StartFlightRecording=filename=app.jfr,duration=60s -jar app.jar
```

This records for 60 seconds and writes to `app.jfr`.

---

## Start JFR For Running Process

Find process id:

```bash
jcmd
```

Start recording:

```bash
jcmd <pid> JFR.start name=profile filename=app.jfr duration=60s
```

Dump recording:

```bash
jcmd <pid> JFR.dump name=profile filename=app.jfr
```

Stop recording:

```bash
jcmd <pid> JFR.stop name=profile
```

---

## JFR Edge Cases

- Recording files can grow large.
- Recordings can contain sensitive data like class names, file paths, host names, and exception messages.
- Very detailed settings can add more overhead.
- Always test recording settings before using them in production.

---

## 2. Low-Overhead Heap Profiling

Java 11 added a low-overhead way for tools to sample heap allocations.

Developer meaning:

- Profilers can show where objects are allocated.
- Helps find allocation-heavy code.
- Useful for memory tuning.

You normally do not call this directly in application code.

It is used through profilers and JVM tooling.

---

## 3. Epsilon GC

Epsilon is a no-op garbage collector.

It allocates memory but does not reclaim memory.

When heap is full, the JVM fails.

Status:

- Experimental in Java 11.
- Not a normal production GC.

Enable:

```bash
java -XX:+UnlockExperimentalVMOptions -XX:+UseEpsilonGC -Xmx512m -jar app.jar
```

---

## Why Would Anyone Use Epsilon?

Useful for:

- GC overhead testing.
- Short-lived jobs.
- Memory pressure testing.
- JVM research.

Not useful for normal backend services because memory is never reclaimed.

### Edge Case

When heap is exhausted:

```text
OutOfMemoryError
```

or the JVM may terminate depending on flags.

---

## 4. ZGC

ZGC is a low-latency garbage collector.

Goal:

- Very short GC pauses.
- Work with small to very large heaps.

Status:

- Experimental in Java 11.
- Became production in Java 15.

Enable in Java 11:

```bash
java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC -jar app.jar
```

In Java 15+, ZGC became production and no longer needed experimental unlock.

---

## ZGC Developer Meaning

ZGC is useful when:

- Low latency matters.
- Long GC pauses hurt user experience.
- Heap is large.

But in Java 11:

- It was experimental.
- Platform support was limited.
- It should be tested carefully before production use.

---

## 5. Nest-Based Access Control

This is mostly JVM/internal.

It improves how nested classes access each other's private members.

Before Java 11, the compiler often generated synthetic helper methods.

Java 11 added a JVM-level concept called a **nest**.

Simple meaning:

Classes that belong together, like an outer class and inner class, can access private members more naturally at JVM level.

Developer meaning:

- No normal code change.
- Cleaner bytecode.
- Better support for tools and language features.

---

## 6. Dynamic Class-File Constants

This is also mostly JVM/internal.

It allows constants in class files to be computed more flexibly at runtime.

Developer meaning:

- Normal backend code rarely uses this directly.
- It helps language implementers, JVM tools, and advanced runtime features.

---

## Best Practices

- Use JFR for real performance investigations.
- Treat JFR files as potentially sensitive.
- Do not enable Epsilon GC for normal backend services.
- Treat ZGC in Java 11 as experimental.
- Use production ZGC only from Java 15+ after testing.
- Do not worry about nest-based access or dynamic constants in daily code, but know they improved JVM internals.

---

## Quick Summary

Java 11 made Flight Recorder available in OpenJDK, added low-overhead heap profiling support, introduced experimental Epsilon GC and experimental ZGC, and improved JVM internals with nest-based access control and dynamic class-file constants.

