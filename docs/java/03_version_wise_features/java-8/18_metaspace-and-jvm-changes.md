---
title: Metaspace and JVM Changes
parent: Java-8
nav_order: 19
---

# Metaspace and JVM Changes

Java 8 replaced PermGen with Metaspace.

This is one of the most important JVM changes in Java 8.

---

## What Was PermGen?

PermGen means Permanent Generation.

Before Java 8, the JVM stored class metadata in PermGen.

Class metadata means information about loaded classes, methods, fields, annotations, and related runtime data.

Common old error:

```text
java.lang.OutOfMemoryError: PermGen space
```

---

## What Is Metaspace?

Java 8 removed PermGen and introduced Metaspace.

Metaspace stores class metadata in native memory, not in the normal Java heap.

Developer meaning:

- `-XX:PermSize` and `-XX:MaxPermSize` are no longer useful.
- Metaspace can grow by default.
- You can still limit it.

---

## Important JVM Options

Set maximum Metaspace:

```bash
-XX:MaxMetaspaceSize=256m
```

Initial Metaspace size:

```bash
-XX:MetaspaceSize=128m
```

---

## New Error

In Java 8, you may see:

```text
java.lang.OutOfMemoryError: Metaspace
```

Common causes:

- Too many classes loaded.
- Classloader leak.
- Repeated redeployments in application server.
- Runtime code generation.
- Too many proxies.

Frameworks that may generate classes:

- Spring
- Hibernate
- ByteBuddy
- CGLIB
- Mockito

---

## Daily Debugging Tips

Check loaded classes:

```bash
jcmd <pid> VM.classloader_stats
```

Heap/class histogram:

```bash
jcmd <pid> GC.class_histogram
```

Enable class loading logs in Java 8:

```bash
-XX:+TraceClassLoading
```

---

## Best Practices

- Remove old `MaxPermSize` options when moving to Java 8.
- Set `MaxMetaspaceSize` in controlled production environments if needed.
- Watch for classloader leaks in app servers.
- Be careful with tools that generate many classes.
- Use monitoring to track Metaspace usage.

---

## Quick Summary

Java 8 replaced PermGen with Metaspace. This removed many old PermGen issues, but class metadata can still run out of memory if class loading grows unexpectedly.

