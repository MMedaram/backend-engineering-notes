---
title: JVM GC Security and Migration Notes
parent: Java-14
nav_order: 7
---

# Java 14 - JVM, GC, Security, and Migration Notes

Java 14 included several JVM, GC, security, and migration changes.

These are not daily syntax features, but they matter when upgrading real backend systems.

Focus areas:

- old GC removals
- old tool/API removals
- new GC platform support
- TLS/certificate behavior
- keytool behavior
- process execution behavior on Windows
- small core library behavior changes

---

## Quick Summary

| Area      | Change                                                 | Developer Meaning                                                   |
|-----------|--------------------------------------------------------|---------------------------------------------------------------------|
| GC        | CMS GC removed                                         | Old `-XX:+UseConcMarkSweepGC` deployments must migrate              |
| GC        | G1 NUMA-aware allocation                               | G1 can work better on large NUMA machines with `-XX:+UseNUMA`       |
| GC        | ZGC on macOS/Windows                                   | ZGC available on more platforms as experimental                     |
| GC        | ParallelScavenge + SerialOld deprecated                | Use `-XX:+UseParallelGC` instead                                    |
| Tools     | Pack200 removed                                        | `pack200`, `unpack200`, and `java.util.jar.Pack200` are gone        |
| Security  | Weak named curves disabled                             | Old TLS/cert integrations may fail                                  |
| Security  | `keytool -keyalg` default removed                      | Key generation must specify algorithm                               |
| Security  | `java.security.acl` removed                            | Very old ACL API code must migrate                                  |
| Threading | `Thread.suspend()` / `resume()` deprecated for removal | Do not use unsafe thread control APIs                               |
| Process   | Windows process argument restrictions                  | `Runtime.exec` / `ProcessBuilder` can behave stricter in some cases |
| Zip FS    | Missing ZIP/JAR file behavior clarified                | Can throw `NoSuchFileException` if not creating                     |

---

# 1. CMS Garbage Collector Removed

Java 14 removed the old **Concurrent Mark Sweep (CMS) garbage collector**.

Old JVM option:

```bash
-XX:+UseConcMarkSweepGC
```

In Java 14, this no longer works as a supported GC choice.

---

## Why was CMS removed?

CMS was an older low-pause garbage collector.

Over time, Java moved toward newer collectors like:

- G1 GC
- ZGC
- Shenandoah

CMS became harder to maintain compared with newer collectors.

---

## Migration meaning

If old deployment scripts have:

```bash
-XX:+UseConcMarkSweepGC
```

remove it and test with another collector.

Common replacement:

```bash
-XX:+UseG1GC
```

G1 is the default GC in modern Java versions for many server workloads.

---

## Edge cases

### 1. Old JVM flags can fail startup

Old CMS-specific flags may fail or be ignored.

Search deployment files for:

```text
UseConcMarkSweepGC
CMSInitiatingOccupancyFraction
UseCMSInitiatingOccupancyOnly
CMSClassUnloadingEnabled
Xconcgc
Xnoconcgc
```

---

### 2. GC behavior may change

Changing GC can change:

- pause time
- throughput
- heap usage
- CPU usage
- latency profile

Best practice:

Run load tests before production migration.

---

# 2. G1 NUMA-Aware Memory Allocation

Java 14 improved G1 GC behavior on NUMA machines.

NUMA means:

```text
Non-Uniform Memory Access
```

Simple meaning:

On large servers, memory is split across CPU/memory nodes. Accessing nearby memory can be faster than accessing far memory.

---

## Developer meaning

If your backend runs on large multi-socket servers, G1 can use NUMA-aware allocation when enabled.

Flag:

```bash
-XX:+UseNUMA
```

This is mostly a performance/runtime tuning topic.

Most small services and container workloads do not need to think about it daily.

---

## Best practice

Use it only when:

- the hardware is NUMA-based
- the JVM has enough CPU/memory to benefit
- load testing shows improvement

Do not add tuning flags blindly.

---

# 3. ZGC on macOS and Windows

Java 14 made ZGC available on:

- macOS
- Windows

In Java 14, this was still **experimental**.

Enable:

```bash
java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC -jar app.jar
```

ZGC became a production feature in Java 15.

---

## Developer meaning

ZGC is designed for low pause times.

It can be useful for:

- latency-sensitive services
- large heaps
- workloads where pause time matters more than max throughput

But in Java 14, it was experimental.

Use careful testing before production.

---

# 4. ParallelScavenge + SerialOld Combination Deprecated

Java 14 deprecated the old combination:

```text
ParallelScavenge + SerialOld
```

The related option:

```bash
-XX:-UseParallelOldGC
```

can trigger a deprecation warning.

Recommended replacement:

```bash
-XX:+UseParallelGC
```

This uses Parallel young and Parallel old collection together.

---

## Developer meaning

Check old JVM startup scripts.

If you see old GC combinations, simplify them and test with modern options.

---

# 5. Pack200 Removed

Java 14 removed Pack200 tools and API.

Removed tools:

```text
pack200
unpack200
```

Removed API:

```
java.util.jar.Pack200
```

---

## Why it matters

Pack200 was used long ago to compress JAR files.

Most modern backend applications do not use it.

But old build scripts or deployment pipelines may still have it.

Search for:

```text
pack200
unpack200
java.util.jar.Pack200
```

---

## Migration advice

Use modern compression/package approaches:

- normal JAR/WAR packaging
- container images
- ZIP/TAR artifacts
- build tool packaging
- `jlink` for custom runtimes
- `jpackage` for installable apps

---
    
# 6. Weak Named Curves Disabled by Default

Java 14 disabled many weak elliptic curves by default for:

- TLS
- certificate path validation
- signed JARs

---

## Developer meaning

Old systems using weak TLS/certificate algorithms may fail after upgrade.

Possible exception:

```text
javax.net.ssl.SSLHandshakeException
```

or certificate validation errors.

---

## What to do

Best fix:

- upgrade server certificates
- use modern TLS configuration
- avoid weak curves
- coordinate with external vendors if their endpoint is old

Avoid simply re-enabling weak algorithms unless there is a temporary emergency and a security review.

---

# 7. keytool Requires -keyalg

Java 14 removed the default key algorithm for some `keytool` commands.

Old style:

```bash
keytool -genkeypair -alias app -keystore app.p12
```

In Java 14, this can fail because `-keyalg` is required.

Possible message:

```text
The -keyalg option must be specified
```

Better:

```bash
keytool -genkeypair -alias app -keyalg RSA -keysize 2048 -keystore app.p12
```

Or use EC where appropriate:

```bash
keytool -genkeypair -alias app -keyalg EC -groupname secp256r1 -keystore app.p12
```

---

## Daily backend meaning

Check scripts that generate:

- keystores
- test certificates
- local TLS certificates
- integration test keys
- CI certificates

Add explicit `-keyalg`.

---

# 8. java.security.acl Removed

Java 14 removed the old deprecated package:

```
java.security.acl
```

This includes old ACL-related types like:

- `Acl`
- `AclEntry`
- `Group`
- `Owner`
- `Permission`

Most modern backend applications do not use these.

If old code imports `java.security.acl.*`, it must be migrated.

---

# 9. Thread suspend/resume Deprecated for Removal

Java 14 terminally deprecated old thread control methods like:

```
Thread.suspend()
Thread.resume()
ThreadGroup.suspend()
ThreadGroup.resume()
```

Simple meaning:

Do not use these methods.

They are unsafe because suspending a thread can leave locks held and cause deadlocks.

---

## Better options

Use safer concurrency tools:

- `interrupt()`
- `ExecutorService`
- `Future.cancel(true)`
- `CountDownLatch`
- `Semaphore`
- `Lock`
- `volatile` flags
- structured concurrency in later Java versions

Example:

```java
class Worker implements Runnable {
    private volatile boolean running = true;

    public void stop() {
        running = false;
    }

    @Override
    public void run() {
        while (running) {
            // do work
        }
    }
}
```

---

# 10. ProcessBuilder and Runtime.exec Changes on Windows

Java 14 tightened some command argument handling for:

```
Runtime.exec(...)
ProcessBuilder
```

This mainly affects Windows applications in security-manager scenarios, especially around `.bat`, `.cmd`, and special characters.

Special characters can include:

```text
& | < > ^ "
```

---

## Daily backend meaning

Most backend services should avoid shell-style string commands.

Prefer argument lists:

```java
ProcessBuilder builder = new ProcessBuilder(
        "my-tool.exe",
        "--input",
        inputFile.toString()
);
```

Avoid:

```
Runtime.getRuntime().exec("my-tool.exe --input " + inputFile);
```

Why:

String command building can create quoting bugs and command injection risk.

---

## Edge case

If old Windows automation starts failing after upgrade, check:

- `.bat` or `.cmd` execution
- quoted arguments
- special characters
- security manager settings
- `jdk.lang.Process.allowAmbiguousCommands`

---

# 11. Zip File System Missing File Behavior

Java 14 changed ZIP file system behavior when the target ZIP/JAR file does not exist and you are not creating it.

Possible exception:

```text
java.nio.file.NoSuchFileException
```

Example:

```java
Path zip = Path.of("missing.zip");
FileSystem fs = FileSystems.newFileSystem(zip);
```

If the file does not exist and create is not enabled, expect `NoSuchFileException`.

---

## Create new ZIP file system

```
Map<String, String> env = Map.of("create", "true");

try (FileSystem fs = FileSystems.newFileSystem(zip, env)) {
    // write files into ZIP
}
```

Best practice:

Be explicit when you intend to create a new ZIP/JAR file.

---

# 12. Network and NIO Behavior Notes

Java 14 included smaller behavior/spec updates that may matter in lower-level code.

Examples:

- `ReadableByteChannel.read()` with read-only buffers throws `IllegalArgumentException`.
- `DatagramSocket.send()` and `MulticastSocket.send()` can throw `IllegalArgumentException` if no destination address is available.
- `MulticastSocket.getOption(IP_MULTICAST_IF)` can return `null` when outgoing interface is not set.
- `InetSocketAddress.toString()` format changed for IPv6 literals and unresolved addresses.

Developer meaning:

Do not parse `toString()` output for logic.

Use proper APIs like:

```
address.getHostString();
address.getPort();
```

---

## Migration checklist

Search old code, scripts, and deployment config for:

```text
UseConcMarkSweepGC
CMS
pack200
unpack200
java.util.jar.Pack200
java.security.acl
Thread.suspend
Thread.resume
UseParallelOldGC
keytool -genkeypair
keytool -genseckey
Runtime.exec
ProcessBuilder
sun.nio.cs.map
```

---

## Best practices

1. Remove CMS GC flags before upgrading to Java 14.
2. Use G1 as the first common migration target unless your workload proves another GC is better.
3. Treat Java 14 ZGC as experimental and load test before use.
4. Remove Pack200 from build/deployment scripts.
5. Add explicit `-keyalg` to key generation scripts.
6. Test TLS integrations with old vendors and internal systems.
7. Avoid old weak TLS/certificate algorithms.
8. Replace `Thread.suspend()` and `Thread.resume()` with safe concurrency patterns.
9. Use `ProcessBuilder` argument lists instead of string-built commands.
10. Be explicit when creating ZIP/JAR file systems.
11. Do not parse network object `toString()` output for business logic.
12. Run integration and load tests after JVM flag changes.

---

## Summary one-liner

Java 14 removed CMS GC and Pack200, deprecated old GC/thread options, added G1 NUMA support and experimental ZGC support on macOS/Windows, tightened some security defaults, and changed a few lower-level library behaviors that matter during migration.

