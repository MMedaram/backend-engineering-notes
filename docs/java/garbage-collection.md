---
title: Garbage Collection (GC)
parent: Java
nav_order: 2
---

# Garbage Collection (GC) in Java

**Garbage Collection** is the process of automatically reclaiming heap memory
by removing objects that are no longer reachable.

In Java, garbage collection is **fully automatic** and handled by the **JVM**.
Java developers do not explicitly free memory like in C/C++.

> **Definition:**  
> Removing unused objects from heap memory so that new objects can occupy that memory is called Garbage Collection.

---

## Why Garbage Collection Is Needed

Without garbage collection:
- Memory leaks would be common
- Applications would crash frequently
- Developers would manually manage memory

GC provides:
- Memory safety
- Fewer crashes
- Simpler application code

---

## Important GC Concepts

### Reachability
An object becomes **eligible for GC** when:
- No active references point to it
- It is not reachable from GC roots

GC Roots include:
- Local variables
- Static variables
- Active threads
- JNI references

> GC does **not** delete objects immediately — it only deletes **unreachable** ones.

---

## Types of Garbage Collectors in Java

Java provides **multiple garbage collectors**, each optimized for different needs.
There are **7 major GC types**.
---

## 1️⃣ Serial Garbage Collector

- Oldest and simplest GC
- Uses **single thread** for GC
- Uses **Stop-The-World** approach
- Application pauses completely during GC

### Best Suited For
- Single-threaded applications
- Small heap sizes (≤ 100MB)
- Simple tools or batch jobs

### JVM Option

```
-XX:+UseSerialGC
```

#### Key Point

✔ No thread communication overhead   
❌ Very long pause times

---

## 2️⃣ Parallel Garbage Collector

- Uses multiple threads for GC
- Heap divided into regions
- GC work done in parallel
- Still Stop-The-World

### Goal

- Maximize throughput
- Reduce GC time using parallelism

### Best Suited For

- Medium to large heap
- CPU-rich systems
- Applications where pause time is acceptable

### JVM Option

```
-XX:+UseParallelGC
```

---

## 3️⃣ Concurrent Mark-Sweep (CMS) – Deprecated ❌

- Most GC work done concurrently
- Reduces pause time
- Higher CPU usage
- Poor performance with large heaps

**Status**

- Deprecated in Java 9
- Removed in Java 14

### JVM Option (Legacy)

```
-XX:+UseConcMarkSweepGC
```

---

## 4️⃣ G1 (Garbage First) Garbage Collector

- Default GC since Java 9
- Heap divided into equal-sized regions
- Collects regions with most garbage first
- Mix of concurrent + stop-the-world phases
- Predictable pause times

#### Best Suited For

- Large heap sizes (≥ 4GB)
- Balanced throughput + low latency
- Most enterprise applications

#### JVM Option

```
-XX:+UseG1GC
```

#### Key Strength

✔ Predictable pauses  
✔ Good default choice  

---

## 5️⃣ Z Garbage Collector (ZGC)

- Introduced in Java 11 (preview)
- Production-ready from Java 15
- Almost fully concurrent
- Pause times < 10ms
- Supports very large heaps (TBs)

#### Best Suited For

- Real-time systems
- Trading platforms
- Low-latency services

#### JVM Option

```
-XX:+UseZGC
```

---

## 6️⃣ Shenandoah Garbage Collector

- Similar to ZGC
- Ultra-low pause times
- Concurrent compaction
- Pause time independent of heap size

#### Best Suited For

- Latency-critical applications
- Alternative to ZGC

#### JVM Option

```
-XX:+UseShenandoahGC
```

---

## 7️⃣ Epsilon Garbage Collector (Special Purpose)

- Does NOT collect garbage
- Allocates memory only 

### Used for:

- Performance testing
- GC overhead measurement
- Memory allocation analysis

### Result

- Eventually throws OutOfMemoryError

#### JVM Option

```
-XX:+UseEpsilonGC
```

> Important: Epsilon GC is never used in production.

---

## Throughput vs Latency

### Throughput

- % of time application is running
- Higher throughput = better CPU utilization

#### Example:

- App runs: 90s
- GC runs: 10s
- Throughput = 90%

### Latency

- Time application is paused during GC
- Lower latency = better responsiveness

#### Example:

- GC pause = 10ms
- Latency = 10ms per GC

---

| GC Type    | Focus             | Use Case       |
| ---------- | ----------------- | -------------- |
| Serial     | Simplicity        | Small apps     |
| Parallel   | Throughput        | Batch jobs     |
| CMS        | Low latency       | Deprecated     |
| G1         | Balanced          | Default choice |
| ZGC        | Ultra-low latency | Real-time apps |
| Shenandoah | Ultra-low latency | Large heaps    |
| Epsilon    | No GC             | Testing only   |

---

## GC Is Not Immediate

- GC runs when JVM decides
- System.gc() is only a request, not a command

## GC Is Heap-Specific

- GC manages heap memory
- Stack memory is cleaned automatically

## GC Tuning Is Advanced

- Most apps should use default G1
- Over-tuning GC often causes problems

---

## Interview Key Takeaways

- GC is automatic in Java
- Objects are removed based on reachability
- Stop-the-world pauses impact latency
- G1 is default since Java 9
- ZGC & Shenandoah focus on ultra-low latency
- Epsilon does not collect garbage

> Choosing the right garbage collector is about balancing
throughput, latency, and heap size — not using the latest GC blindly.

