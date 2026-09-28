---
title: Java-21
parent: Java Versions
nav_order: 21
---

# Java 21 - Features and Enhancements

Java 21 was released in September 2023. It is an LTS release and an important production baseline for backend applications.

Java 21 finalized several language and concurrency features that had been tested in earlier releases. It also added useful collection, security, runtime, and everyday APIs.

## Production Feature Map

| Area | Feature | Why a developer should know it |
|---|---|---|
| Concurrency | Virtual Threads | Run large numbers of blocking tasks with a simple thread-per-task style |
| Language | Pattern Matching for `switch` | Replace complex type checks and casts with safer branches |
| Language | Record Patterns | Read record components directly while checking a value |
| Collections | Sequenced Collections | Use common first, last, and reversed operations across ordered collections |
| Runtime | Generational ZGC | Reduce low-latency GC overhead for suitable workloads |
| Security | Key Encapsulation Mechanism API | Use a standard API for establishing shared secrets |
| Tooling | Dynamic Agent Loading Warnings | Prepare profilers, mocking tools, and observability agents for stronger JVM integrity |
| Core APIs | Daily API Improvements | Use `Math.clamp`, range searches, delimiter-preserving splits, repeat methods, and closable HTTP clients |
| Migration | Platform and Compatibility Changes | Identify deployment and build behaviour that can change during a Java 21 upgrade |

## Preview and Incubator Tracking

These features were not production-standard features in Java 21. They are recorded here only to show their lifecycle.

| Feature in Java 21 | Java 21 status | Later result |
|---|---|---|
| String Templates | Preview | Previewed again in Java 22, then withdrawn; never became standard |
| Unnamed Patterns and Variables | Preview | Became standard in Java 22 |
| Unnamed Classes and Instance `main` Methods | Preview | Evolved and became Compact Source Files and Instance Main Methods in Java 25 |
| Scoped Values | Preview | Became standard in Java 25 |
| Structured Concurrency | Preview | Continued as a preview in later releases |
| Foreign Function and Memory API | Third Preview | Became standard in Java 22 |
| Vector API | Sixth Incubator | Continued incubating in later releases |

## Important Correction

The Key Encapsulation Mechanism API is a standard API in Java 21. It is not a preview API and does not require `--enable-preview`.

## Recommended Learning Order

1. Virtual Threads
2. Pattern Matching for `switch`
3. Record Patterns
4. Sequenced Collections
5. Daily API Improvements
6. Dynamic Agent Loading changes
7. Generational ZGC
8. Platform migration notes
9. Key Encapsulation Mechanism API when working with security protocols

## Senior Developer View

Do not adopt a Java feature only because it is newer. For each feature, check:

- Does it make the code easier to maintain?
- Does the team and framework version support it?
- What happens for `null`, empty input, invalid input, and partial failure?
- Does it change performance, memory usage, security, or deployment behaviour?
- Can the feature be tested and observed in production?
- Is it standard, preview, incubator, deprecated, or marked for removal?
