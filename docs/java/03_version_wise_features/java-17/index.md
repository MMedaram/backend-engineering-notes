---
title: Java-17
parent: Java Versions
nav_order: 17
---

# Java 17 - Features / Enhancements

Java 17 was released on **14 September 2021**.

Java 17 is an **LTS release**.

For backend developers, Java 17 is important because:

- sealed classes became a standard production feature.
- pattern matching for `switch` started as a preview feature.
- the random number API became cleaner and more flexible.
- `HexFormat` made hex encoding and decoding easier.
- `InstantSource` made time-based code easier to test.
- Java deserialization filters became more flexible.
- JDK internal APIs became strongly encapsulated.
- the old broad `--illegal-access` migration option stopped helping.
- Security Manager was deprecated for removal.
- RMI Activation and the experimental AOT/JIT compiler were removed.
- Foreign Function and Memory API and Vector API continued as incubator APIs.
- Java 17 became a strong baseline for modern backend applications.

---

## Java 17 Feature Map

| Area | Feature | Developer Meaning |
|------|---------|-------------------|
| Language | Sealed Classes - Standard | Restrict which classes can extend or implement a type |
| Language | Pattern Matching for `switch` - Preview | Match by type directly inside `switch` |
| Random API | Enhanced Pseudo-Random Number Generators | Use a common random generator API and newer algorithms |
| Utility API | `HexFormat` | Convert bytes to hex and hex back to bytes without custom code |
| Time API | `InstantSource` | Inject current time into code so tests are easier |
| Serialization | Context-Specific Deserialization Filters | Apply safer filters for Java object deserialization |
| Migration | Strong Encapsulation of JDK Internals | Old code using internal JDK classes can fail more clearly |
| Migration | `--illegal-access` Obsolete | Broadly opening old JDK internals no longer works |
| Migration | Removed RMI Activation | `rmid` and RMI Activation APIs are removed |
| Migration | Removed Experimental AOT/JIT Compiler | `jaotc` and related experimental modules are removed |
| Migration | Deprecated Security Manager for Removal | Security Manager still exists, but should not be used for new design |
| Migration | Deprecated Applet API for Removal | Applet APIs are legacy and should not be used |
| Native API | Foreign Function and Memory API - Incubator | Experimental API for native functions and native memory |
| Performance API | Vector API - Second Incubator | Experimental API for CPU vector/SIMD operations |
| JVM | Always-Strict Floating-Point Semantics | Floating-point behavior is consistent by default |
| Platform | macOS/AArch64 Port | Better support for Apple Silicon machines |
| Desktop | New macOS Rendering Pipeline | New Metal rendering pipeline for Java desktop apps |
| Logging | Asynchronous Unified Logging | JVM logging can be made asynchronous |

---

## Preview / Incubator / Special Status

| Feature | Status In Java 17 | What Happened Later |
|---------|-------------------|---------------------|
| Sealed Classes | Standard | Preview was in Java 15 and Java 16 |
| Pattern Matching for `switch` | Preview | Became standard in Java 21 |
| Foreign Function and Memory API | Incubator | Became standard in Java 22 as `java.lang.foreign` |
| Vector API | Second incubator | Continued to evolve in later Java versions |
| Security Manager | Deprecated for removal | Permanently disabled in Java 24 |
| Applet API | Deprecated for removal | Legacy browser applet model; do not use |
| `--illegal-access` | Obsolete | Use targeted `--add-opens` only as a short-term migration fix |

Important:

Preview features need explicit compile and run flags.

Incubator APIs are not final and can change in later Java versions.

Standard features do not need preview flags.

---

## Daily Coding Topics To Focus First

These are most useful for normal backend development:

1. Sealed Classes - Standard
2. Pattern Matching for `switch` - Preview
3. Enhanced Random Generator API
4. `HexFormat`
5. `InstantSource`
6. Context-specific deserialization filters
7. Strong encapsulation migration checks

---

## JVM / Platform Topics To Know

These are useful for migration, performance, production support, and interviews:

1. Strong encapsulation of JDK internals
2. Removed RMI Activation
3. Removed experimental AOT/JIT compiler
4. Security Manager deprecated for removal
5. Applet API deprecated for removal
6. Always-strict floating-point semantics
7. Foreign Function and Memory API incubator
8. Vector API second incubator
9. macOS/AArch64 port
10. macOS Metal rendering pipeline
11. asynchronous JVM logging
12. security hardening around SHA-1, XML signatures, TLS, JNDI, and Kerberos

---

## Java 17 LTS Baseline

Java 17 also includes many important features that were introduced in earlier versions.

These are not new Java 17 features, but they are part of the Java 17 LTS developer baseline:

- records from Java 16
- pattern matching for `instanceof` from Java 16
- `Stream.toList()` from Java 16
- text blocks from Java 15
- helpful `NullPointerException` messages from Java 14
- switch expressions from Java 14
- `Files.mismatch()` from Java 12
- `Collectors.teeing()` from Java 12
- String methods like `indent()` and `transform()` from Java 12

Meaning:

If a project upgrades from Java 8 or Java 11 to Java 17, developers should learn both:

- Java 17 release features
- useful features added between Java 12 and Java 16

These notes focus on Java 17 release changes. Earlier features are covered in their own version folders.

---

## What Java 17 Means For Backend Developers

Focus most on:

- using sealed classes for controlled domain models.
- understanding pattern matching for `switch`, but remembering it is preview in Java 17.
- using `HexFormat` instead of writing custom hex conversion code.
- injecting `InstantSource` for time-based business logic and tests.
- using the new random generator API for simulations, test data, and non-security random values.
- avoiding JDK internal APIs and updating old libraries.
- not relying on Security Manager, RMI Activation, old AOT flags, or applet APIs.
- being careful with Java object deserialization and using strict filters if it cannot be avoided.

---

## Quick Migration Checklist

When moving to Java 17:

1. Run tests on Java 17.
2. Run `jdeps --jdk-internals` on application jars.
3. Update old frameworks and libraries before adding JVM workarounds.
4. Search startup scripts for `--illegal-access`.
5. Search startup scripts for broad `--add-opens`.
6. Check for Security Manager usage.
7. Check for RMI Activation or `rmid`.
8. Check for old AOT or Graal compiler flags from the JDK.
9. Check code that deserializes Java objects.
10. Test TLS, certificates, XML signatures, and Kerberos integrations if your app uses them.

---

## Simple Rule

For daily backend development:

Learn **sealed classes**, **`HexFormat`**, **`InstantSource`**, and **random generator API** first.

Use **pattern matching for `switch`** only if your project allows Java 17 preview features.

Treat **strong encapsulation** as a migration topic that can break old libraries.
