---
title: Java-12
parent: Java Versions
nav_order: 12
---

# Java 12 - Features / Enhancements

Java 12 was released on **19 March 2019**.

Java 12 was a **short-term release**, not an LTS release. It came after Java 11 and continued Java's six-month release cycle.

For backend developers, Java 12 is useful to know because it added a few daily coding APIs, introduced `switch` expressions as a preview feature, and improved JVM/GC behavior.

---

## Java 12 Feature Map

| Area                | Feature                                              | Developer Meaning                                          |
|---------------------|------------------------------------------------------|------------------------------------------------------------|
| Language            | Switch Expressions - Preview                         | Cleaner `switch` logic that can return a value             |
| Strings             | `String.indent(int)`                                 | Add/remove indentation from multi-line text                |
| Strings             | `String.transform(Function)`                         | Apply a function directly to a string                      |
| Files               | `Files.mismatch(Path, Path)`                         | Find the first byte difference between two files           |
| Streams             | `Collectors.teeing(...)`                             | Run two collectors and merge both results                  |
| Async               | `CompletionStage.exceptionallyAsync(...)`            | Recover from async failure asynchronously                  |
| Async               | `CompletionStage.exceptionallyCompose(...)`          | Recover from async failure with another async flow         |
| Numbers/i18n        | Compact Number Formatting                            | Format numbers like `1K`, `1M`, etc.                       |
| Constants           | JVM Constants API                                    | Model class-file and runtime constants in code             |
| Testing/Performance | Microbenchmark Suite                                 | JDK-level support for small performance benchmarks         |
| GC                  | Shenandoah GC - Experimental                         | Low-pause garbage collector, experimental in Java 12       |
| GC                  | G1 Abortable Mixed Collections                       | G1 can stop long mixed GC work more safely                 |
| GC                  | G1 Returns Unused Memory Promptly                    | G1 can return unused heap memory to OS sooner              |
| JVM Startup         | Default CDS Archives                                 | Better startup support without manual CDS archive creation |
| Platform            | One AArch64 Port                                     | JDK cleanup for ARM 64-bit platform support                |
| Unicode             | Unicode 11 Support                                   | More Unicode characters/scripts supported                  |
| Tools               | `jdeps` improvements                                 | Better module dependency reporting                         |
| Security/Monitoring | New JFR security events                              | Better runtime visibility for security-related activity    |
| Migration           | Removed old `finalize` methods from some JDK classes | Move toward safer cleanup patterns like try-with-resources |

---

## Preview / Experimental / Special Status

| Feature             | Status In Java 12          | What Happened Later                                  |
|---------------------|----------------------------|------------------------------------------------------|
| Switch Expressions  | Preview                    | Preview again in Java 13, became standard in Java 14 |
| Shenandoah GC       | Experimental               | Became production in Java 15                         |
| ZGC class unloading | ZGC was still experimental | ZGC became production in Java 15                     |

Important:

Java 12 `switch` expressions were preview. Preview features are not final APIs. Syntax or behavior can still change before becoming standard.

In Java 12, switch expression examples used `break value` for returning from block cases. Later Java versions changed this direction and the final Java 14 version uses `yield`.

---

## Daily Coding Topics To Generate First

These are most useful for normal backend development:

1. Switch Expressions - Preview
2. String `indent()` and `transform()`
3. `Files.mismatch()`
4. `Collectors.teeing()`
5. CompletionStage exception recovery methods
6. Compact Number Formatting

---

## Platform / JVM Topics To Generate Later

These are useful for JVM understanding, performance, and migration:

1. Shenandoah GC - Experimental
2. G1 GC improvements
3. Default CDS Archives
4. JVM Constants API
5. Microbenchmark Suite
6. Unicode 11 support
7. JFR security events
8. `jdeps` improvements
9. One AArch64 Port

---

## Migration Notes To Know

Java 12 removed or changed some older JDK internals and cleanup mechanisms.

Developer points to remember:

- Do not depend on old JDK-internal classes.
- Prefer `try-with-resources` for closing files/resources.
- Do not rely on `finalize()` for cleanup.
- If using tools around modules, check `jdeps` behavior.
- If testing preview switch expressions, compile/run with preview flags.

---

## What Java 12 Means For Backend Developers

Focus most on:

- `switch` expressions conceptually, because they became standard later.
- `Files.mismatch()` for file comparison use cases.
- `Collectors.teeing()` for collecting two results in one stream pass.
- `CompletionStage` failure recovery improvements.
- Compact number formatting for user-facing counts.
- G1 and Shenandoah basics for JVM/GC interviews.

For interviews, Java 12 is commonly discussed as:

- A short-term release after Java 11.
- The first preview of switch expressions.
- A release with useful small APIs rather than one huge backend feature.
- A release with multiple JVM and GC improvements.

---

## Quick Summary

Java 12 introduced switch expressions as a preview feature, added useful APIs like `String.indent`, `String.transform`, `Files.mismatch`, `Collectors.teeing`, and better `CompletionStage` exception recovery. It also added compact number formatting, JVM Constants API, microbenchmark support, default CDS archives, G1 improvements, experimental Shenandoah GC, Unicode 11 support, JFR security events, and tooling/migration improvements.
