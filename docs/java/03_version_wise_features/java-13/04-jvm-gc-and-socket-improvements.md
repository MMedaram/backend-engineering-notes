---
title: JVM GC and Socket Improvements
parent: Java-13
nav_order: 4
---

# Java 13 - JVM, GC, and Socket Improvements

Java 13 added important runtime improvements.

These are not features you use like a normal Java API, but backend developers should know them for:

- startup performance
- memory behavior
- production tuning
- migration testing
- interview discussions

Main topics:

1. Dynamic CDS Archives
2. ZGC uncommit unused memory
3. Reimplemented legacy Socket API

---

# 1. Dynamic CDS Archives

## What is CDS?

CDS means:

```text
Class Data Sharing
```

Simple meaning:

The JVM can store class metadata in an archive and reuse it later.

This can help:

- reduce startup time
- reduce memory usage
- make repeated starts faster

---

## What changed in Java 13?

Java 13 added **Dynamic CDS Archives**.

Before this, AppCDS setup could require a class list and extra trial runs.

Java 13 made it easier by allowing the JVM to create an archive when the application exits.

---

## Create dynamic archive

```bash
java -XX:ArchiveClassesAtExit=app.jsa -cp app.jar com.example.Main
```

This runs the app and writes an archive file:

```text
app.jsa
```

---

## Use dynamic archive

```bash
java -XX:SharedArchiveFile=app.jsa -cp app.jar com.example.Main
```

Now the JVM can use the archived class data.

---

## Daily backend meaning

This can help applications that start often:

- CLI tools
- serverless functions
- short-lived batch jobs
- test tools
- small services with frequent restarts

For long-running services, startup improvement may be less important, but still useful in deployment and scaling scenarios.

---

## Edge cases

| Case | Developer Meaning |
|------|-------------------|
| Archive path not writable | JVM cannot create archive |
| App classes change | Recreate the archive |
| Different JDK/runtime image | Archive may not be usable |
| Long-running app | Benefit mostly affects startup |
| Wrong classpath/module path | Archive may not match expected classes |

Best practice:

Measure startup before and after using CDS. Do not assume the gain is always large.

---

# 2. ZGC Uncommit Unused Memory

## What is ZGC?

ZGC is a low-latency garbage collector.

In Java 13, ZGC was still **experimental**.

It became production-ready later in Java 15.

---

## What changed in Java 13?

Java 13 improved ZGC so it can return unused heap memory to the operating system.

Simple meaning:

If the app used a lot of memory earlier, but later does not need it, ZGC can release unused memory back to the OS.

This helps in memory-sensitive environments.

---

## Enable ZGC in Java 13

Because ZGC was experimental in Java 13:

```bash
java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC -jar app.jar
```

---

## Important flags

Disable uncommit:

```bash
java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC -XX:-ZUncommit -jar app.jar
```

Set uncommit delay:

```bash
java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC -XX:ZUncommitDelay=120 -jar app.jar
```

Default delay:

```text
300 seconds
```

---

## Important Xms/Xmx behavior

ZGC will not shrink below the minimum heap size:

```text
-Xms
```

If you set:

```bash
-Xms2g -Xmx2g
```

then heap cannot shrink below 2 GB.

So uncommit is effectively not useful in that setup.

Better for memory-return behavior:

```bash
-Xms256m -Xmx2g
```

This gives ZGC room to shrink.

---

## SoftMaxHeapSize

Java 13 also added:

```bash
-XX:SoftMaxHeapSize=<bytes>
```

For ZGC, this gives the JVM a soft target for heap size.

Example:

```bash
java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC -Xmx2g -XX:SoftMaxHeapSize=1g -jar app.jar
```

Simple meaning:

The JVM tries to stay around 1 GB, but can grow up to 2 GB if needed to avoid failure.

---

## Daily backend meaning

This matters for:

- containers
- cloud workloads
- memory-based billing
- applications with traffic spikes
- low-latency services

Do not switch GC only because a version note says so.

Test with your real workload.

---

## Edge cases

| Case                            | Result                               |
|---------------------------------|--------------------------------------|
| ZGC not enabled                 | ZGC-specific flags may not matter    |
| `-Xms == -Xmx`                  | Heap cannot shrink below fixed size  |
| Very low heap settings          | More GC pressure                     |
| Experimental feature in Java 13 | Requires extra care and testing      |
| Production on old Java 13       | Not ideal because Java 13 is not LTS |

---

# 3. Reimplemented Legacy Socket API

## What changed?

Java 13 reimplemented the old `java.net.Socket` and `java.net.ServerSocket` internals.

Developer meaning:

Most application code does not change.

Your existing code still looks like this:

```
try (Socket socket = new Socket("example.com", 80)) {
    // use socket
}
```

The change is mostly inside the JDK.

---

## Why was it changed?

The old socket implementation was very old and hard to maintain.

Java 13 replaced it with a cleaner implementation based on newer JDK internals.

Benefits:

- easier to maintain
- easier to debug
- better integration with JDK tooling
- prepares the platform for future concurrency improvements

---

## Compatibility escape option

If an old application has socket behavior problems after migration, Java 13 allowed using the old implementation:

```bash
java -Djdk.net.usePlainSocketImpl=true -jar app.jar
```

This should be a temporary migration workaround, not a long-term solution.

---

## Edge cases

Most apps are not affected, but fragile code may notice differences in:

- exact exception type/message
- socket close behavior
- custom `SocketImpl` behavior
- instrumentation agents that depend on internal socket classes
- low-level performance characteristics

Best practice:

If your application does heavy socket usage, run integration and load tests after Java migration.

---

## Best practices

1. Treat CDS as a startup optimization and measure it.
2. Recreate CDS archives when app/JDK/classpath changes.
3. Use ZGC Java 13 only with testing because it was experimental.
4. Do not set `-Xms` equal to `-Xmx` if you expect ZGC to return memory.
5. For socket-heavy apps, test timeouts, connection reset behavior, and load after migration.
6. Use old socket implementation flag only as a temporary workaround.

---

## Interview one-liner

Java 13 improved the runtime with Dynamic CDS Archives for easier startup optimization, ZGC memory uncommit for returning unused heap to the OS, and a cleaner reimplementation of the legacy Socket API with mostly no application code changes.

---

## References

- [JEP 350: Dynamic CDS Archives](https://openjdk.org/jeps/350)
- [JEP 351: ZGC - Uncommit Unused Memory](https://openjdk.org/jeps/351)
- [JEP 353: Reimplement the Legacy Socket API](https://openjdk.org/jeps/353)
- [Oracle JDK 13 Release Notes](https://www.oracle.com/java/technologies/javase/13-relnote-issues.html)
