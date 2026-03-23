---
title: Preventing Memory Leaks
parent: Memory-Management
nav_order: 4
---

# Preventing Memory Leaks in Java Applications

Java uses automatic garbage collection, so traditional manual memory leaks (like in C/C++) do not occur.

However, **logical memory leaks** can still happen when objects that are no longer needed remain strongly reachable, preventing the Garbage Collector (GC) from reclaiming memory.

Over time, this leads to:

- Increasing heap usage
- Frequent GC cycles
- Performance degradation
- `OutOfMemoryError`

---

## What Is a Memory Leak in Java?

A memory leak occurs when:

> Objects that are no longer needed remain strongly reachable and therefore cannot be garbage collected.

Important distinction:

- **High memory usage** ≠ Memory leak
- **Unbounded object retention** = Memory leak

---

## Common Causes of Memory Leaks in Backend Applications

### 1. Unbounded Static Collections

#### Problem

```java
public class CacheHolder {
    private static final List<String> cache = new ArrayList<>();

    public static void add(String value) {
        cache.add(value);
    }
}
```

If the collection grows indefinitely, memory usage increases continuously.

#### Prevention

- Avoid unbounded static collections
- Use bounded caches
- Prefer libraries like Caffeine or Redis


### 2. Improper Caching

#### Problem

```java
Map<String, User> users = new HashMap<>();
```

Without eviction policies, the cache grows forever.

#### Prevention

- Use size-based eviction
- Use TTL (time-to-live)
- Use mature caching libraries
- Avoid building custom infinite caches

### 3. ThreadLocal Misuse

Common in web applications using thread pools.

#### Problem

```
private static final ThreadLocal<User> currentUser = new ThreadLocal<>();
currentUser.set(user);
```

If not cleared, thread pools (e.g., Tomcat) reuse threads and retain references.

#### Prevention

Always clear:

```text
try {
currentUser.set(user);
} finally {
currentUser.remove();
}
```

### 4. Listeners / Callbacks Not Removed

Example:

```
someService.registerListener(myListener);
```

If listener is never unregistered:

- Service keeps reference
- Object never GC’d

#### Prevention:

- Deregister listeners properly
- Use weak references if appropriate

### 5. Large Objects Held in Memory

Example:

- Storing entire file in memory
- Large JSON payload retained in static field
- Loading full DB table into List

#### Problem

```java
List<User> users = userRepository.findAll();
```

If table has 5 million rows → huge memory pressure.

#### Prevention

- Pagination
- Streaming APIs
- Cursor-based processing
- Batch processing

### 6. Hibernate / JPA Session Leaks

If you:

- Keep long-running transactions
- Never clear persistence context
- Load large datasets

Hibernate keeps objects in first-level cache.

#### Prevention

- Use pagination
- Call entityManager.clear() in batch jobs
- Use stateless sessions for bulk operations

### 7. Unclosed Resources

Streams, DB connections, InputStreams.

#### Problem

```java
FileInputStream fis = new FileInputStream(file);
```
If not closed → resource leak (may cause indirect memory pressure).

#### Prevention

- Use try-with-resources:

```text
try (FileInputStream fis = new FileInputStream(file)) {
// use
}

```

### 8.  Executors Not Shut Down

#### Problem

```java
ExecutorService executor = Executors.newFixedThreadPool(10);
```

Never calling:
- executor.shutdown();
- Threads remain alive → references retained.

### 9. Inner Classes Holding Outer References

Non-static inner classes hold reference to outer class.

If long-lived inner class exists:
Outer object cannot be GC’d.

#### Prevention

Use:

- static class InnerClass

when possible.

### 10. Logging Large Objects

Be careful with:

```
log.info("Response: {}", largeObject);
```

If logger framework buffers async messages, large objects may stay in memory longer than expected.

---

## Static Fields and Memory Retention

Static fields are referenced by the Class object, which is typically reachable through the ClassLoader (a GC root).

As long as the class is loaded:

- Static fields remain reachable
- Objects referenced by static fields remain in memory

They become eligible for GC only if:

- The static reference is cleared, or
- The class is unloaded

**Practical rule:**     

> Treat static fields as application-lifetime objects.

---

## General Principles for Preventing Memory Leaks

- Avoid unbounded growth in collections.
- Clear ThreadLocal and listeners, resources  properly.
- Use well-tested libraries instead of custom memory structures.
- Process large data in batches.
- Monitor memory continuously in production.
  - Use:
    - JFR
    - Prometheus + Grafana
    - APM tools 
    - Enable heap dump on OOM for post-failure analysis.

