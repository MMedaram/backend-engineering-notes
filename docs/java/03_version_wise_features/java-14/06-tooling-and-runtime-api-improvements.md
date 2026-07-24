---
title: Tooling and Runtime API Improvements
parent: Java-14
nav_order: 6
---

# Java 14 - Tooling and Runtime API Improvements

Java 14 added several useful tooling and runtime improvements.

These are not all daily syntax features, but backend developers should know them for:

- packaging
- monitoring
- native/advanced memory use cases
- XML processing
- number formatting
- production diagnostics

---

## Quick Summary

| Feature                                         | Status                       | Developer Meaning                                  |
|-------------------------------------------------|------------------------------|----------------------------------------------------|
| `jpackage`                                      | Incubator                    | Build native installers/packages for Java apps     |
| JFR Event Streaming                             | Standard JDK feature         | Read JFR events continuously for monitoring        |
| Foreign-Memory Access API                       | Incubator                    | Safer API for memory outside Java heap             |
| Non-Volatile Mapped Byte Buffers                | Advanced API/JVM support     | Persistent memory use cases                        |
| Accounting Currency Format                      | Standard library improvement | Format negative currency using accounting style    |
| SAX `ContentHandler.declaration`                | XML API improvement          | Read XML declaration details                       |
| `ReadableByteChannel.read()` spec clarification | API clarification            | Read-only buffers throw `IllegalArgumentException` |

---

# 1. jpackage - Packaging Tool Incubator

Java 14 introduced `jpackage` as an **incubator tool**.

Simple meaning:

`jpackage` helps create platform-specific packages for Java applications.

Examples:

- Windows: `.msi`, `.exe`
- macOS: `.pkg`, `.dmg`
- Linux: `.deb`, `.rpm`

---

## Why was it introduced?

Shipping only a JAR is not always friendly for end users.

Some applications need:

- installer
- desktop launcher
- bundled runtime
- OS-native install/uninstall behavior

`jpackage` helps package Java applications in a way users expect on their operating system.

---

## Basic example

```bash
jpackage --name myapp --input lib --main-jar app.jar --main-class com.example.Main
```

Meaning:

- app name is `myapp`
- JAR files are in `lib`
- main JAR is `app.jar`
- main class is `com.example.Main`

---

## Important jpackage notes

In Java 14:

- `jpackage` is incubator.
- The module is `jdk.incubator.jpackage`.
- Behavior/options can change.
- It is mainly useful for packaged apps, desktop tools, and self-contained command-line tools.
- Most backend services deployed as containers do not need it daily.

`jpackage` became a standard production tool in Java 16.

---

## jpackage edge cases

### 1. No cross packaging

You cannot build a Windows installer from Linux using `jpackage`.

To build Windows packages, run on Windows.

To build macOS packages, run on macOS.

To build Linux packages, run on Linux.

---

### 2. Platform tools may be required

Some package types need platform-specific tools.

Example:

Windows MSI packaging may need additional Windows tooling.

Best practice:

Test packaging in CI on the same OS you are targeting.

---

### 3. Not usually needed for server-side Docker deployments

If your backend app is deployed as:

- Docker image
- Kubernetes workload
- Linux service with a startup script
- cloud platform artifact

then `jpackage` may not add much value.

It is more useful for installable apps and tools.

---

# 2. JFR Event Streaming

Java 14 added **JFR Event Streaming**.

JFR means:

```text
Java Flight Recorder
```

Simple meaning:

JFR can record JVM/application events, and Java 14 lets tools read those events continuously while the application is running.

---

## Why it matters

Before event streaming, JFR was often used by starting a recording and then analyzing it later.

With event streaming, monitoring tools can consume events live.

Useful for:

- production diagnostics
- latency monitoring
- allocation tracking
- JVM health dashboards
- custom observability tools

---

## Conceptual example

```java
import jdk.jfr.consumer.RecordingStream;

public class JfrStreamDemo {
    public static void main(String[] args) {
        try (RecordingStream stream = new RecordingStream()) {
            stream.enable("jdk.CPULoad");

            stream.onEvent("jdk.CPULoad", event -> {
                System.out.println("CPU event: " + event);
            });

            stream.start();
        }
    }
}
```

This is not normal CRUD code, but it is useful for tooling and monitoring.

---

## JFR edge cases

### 1. Event names matter

If the event name is wrong, your handler will not receive what you expect.

Use known JFR event names and test locally.

---

### 2. Too much event data can add overhead

Do not enable everything blindly in production.

Best practice:

Enable the events you actually need.

---

### 3. Not a replacement for application logs

JFR is great for JVM/runtime diagnostics.

Application logs are still needed for business-level flow.

---

# 3. Foreign-Memory Access API - Incubator

Java 14 introduced the **Foreign-Memory Access API** as an **incubator API**.

Simple meaning:

It provides a Java API to access memory outside the normal Java heap.

This is advanced.

Most backend CRUD applications do not use it directly.

---

## Why it was introduced

Some libraries need off-heap or native memory for:

- large caches
- high-performance networking
- native interop
- persistent memory
- memory-mapped files

Before this, code often used:

- direct `ByteBuffer`
- JNI
- internal `Unsafe`

Problems:

- direct buffers have limitations
- JNI is complex
- `Unsafe` is not a normal supported application API
- memory lifecycle can be hard to manage safely

The incubator API was a step toward safer foreign memory access.

---

## Important status

In Java 14:

- this API is incubator
- package/API names changed in later releases
- do not treat Java 14 incubator code as stable

Later, this area evolved into the Foreign Function and Memory API, which became standard in Java 22.

---

## Daily backend meaning

For normal backend development:

You usually only need to know what it is.

You may care if you use libraries like:

- Netty
- high-performance cache libraries
- database drivers
- native integration libraries
- memory-mapped storage libraries

The library authors may use this kind of API internally.

---

## Foreign memory edge cases

### 1. Do not use incubator APIs casually

Incubator APIs can change.

Avoid building normal business code directly on Java 14 incubator APIs.

---

### 2. Memory lifecycle matters

When working outside the Java heap, cleanup becomes more explicit.

If memory is not closed/released properly, you can create native memory pressure.

---

### 3. Prefer stable libraries

For most backend teams, prefer stable libraries that hide low-level memory handling.

---

# 4. Non-Volatile Mapped Byte Buffers

Java 14 added support for **non-volatile mapped byte buffers**.

Simple meaning:

This is advanced support for memory-mapped files on persistent memory.

Persistent memory means data can survive power loss in certain hardware setups.

---

## Daily backend meaning

Most backend developers do not use this directly.

You should know it exists for:

- storage engines
- low-latency data platforms
- embedded databases
- specialized caching systems

For normal REST APIs and services, this is not a daily feature.

---

## Best practice

Use this only when:

- the hardware supports it
- performance need is clear
- the team understands memory-mapped IO
- failure/recovery behavior is tested

---

# 5. Accounting Currency Format

Java 14 added support for accounting-style currency formatting.

Simple meaning:

Some locales show negative currency using parentheses.

Example:

```text
($3.27)
```

instead of:

```text
-$3.27
```

---

## Example

```java
import java.text.NumberFormat;
import java.util.Locale;

public class AccountingCurrencyDemo {
    public static void main(String[] args) {
        Locale locale = Locale.forLanguageTag("en-US-u-cf-account");
        NumberFormat format = NumberFormat.getCurrencyInstance(locale);

        System.out.println(format.format(-3.27));
    }
}
```

Possible output:

```text
($3.27)
```

---

## Daily backend use case

Useful for:

- reports
- invoices
- exports
- finance dashboards
- accounting-facing APIs

Best practice:

Do not hardcode formatting manually.

Use locale-aware formatting.

---

## Edge cases

### 1. Output depends on locale data

Different locales can format differently.

Always test expected report/export output for the target locale.

---

### 2. Formatting is not calculation

`NumberFormat` is for display.

Use `BigDecimal` or proper money libraries for money calculations.

---

# 6. SAX ContentHandler Declaration Method

Java 14 added a new SAX `ContentHandler` method for XML declaration details.

Simple meaning:

XML parsing code can receive declaration values like:

- XML version
- encoding
- standalone

This matters only if your application deeply processes XML.

---

## Daily backend meaning

Most JSON-based services will not care.

You may care if you work with:

- banking XML
- SOAP
- legacy integrations
- XML signatures
- document processing

---

# 7. ReadableByteChannel read-only buffer clarification

Java 14 clarified that some channel read methods throw `IllegalArgumentException` when given read-only buffers.

Example:

```
ByteBuffer buffer = ByteBuffer.allocate(10).asReadOnlyBuffer();
channel.read(buffer);
```

Possible exception:

```text
java.lang.IllegalArgumentException
```

Developer meaning:

Pass a writable buffer when reading from a channel.

---

## Best practices

1. Use `jpackage` mainly for installable apps/tools, not normal containerized backend deployments.
2. Treat Java 14 `jpackage` as incubator; use Java 16+ for standard behavior.
3. Use JFR event streaming for JVM/runtime monitoring, not business logging.
4. Do not enable too many JFR events without measuring overhead.
5. Avoid incubator foreign-memory APIs in normal business code.
6. Use locale-aware currency formatting for reports and invoices.
7. Use writable `ByteBuffer` instances when reading from channels.
8. Test XML parsing changes if you work with old XML integrations.

---

## Summary one-liner

Java 14 added several runtime/tooling improvements, including incubating `jpackage`, JFR event streaming, incubating foreign-memory access, persistent-memory mapped buffers, accounting currency formatting, and smaller XML/NIO API updates.
