---
title: Strong Encapsulation, Value-Based Classes, and Migration Notes
parent: Java-16
nav_order: 6
---

# Strong Encapsulation, Value-Based Classes, and Migration Notes

## Strongly encapsulate JDK internals by default

Java 16 changes the default of `--illegal-access` to `deny`. Code that used reflective access to most JDK internals may fail at runtime instead of merely emitting warnings.

```text
java.lang.IllegalAccessError:
module java.base does not export sun.security.util to unnamed module
```

## Migration approach

1. Upgrade dependencies first; modern framework versions often remove internal-JDK usage.
2. Run CI and integration tests with Java 16 and capture illegal-access failures.
3. Replace internal APIs with supported alternatives such as `java.util.Base64`, `Cleaner`, `MethodHandles.Lookup`, or standard JDK/library APIs.
4. Use `--add-opens` or `--add-exports` only as a narrow, documented temporary bridge.
5. Remove temporary flags before moving to later JDKs, where relaxed access no longer works.

### Negative cases

- `--add-opens` fixes reflective access; it does not make an unsupported internal API a safe dependency.
- `--add-exports` concerns compile/runtime access to public types in non-exported packages; it is not equivalent to opening private members for reflection.
- Avoid global flags such as opening broad modules to every unnamed module when a specific dependency and package can be upgraded instead.

## Value-based class warnings

Java 16 warns when synchronization uses value-based classes such as `Integer`, `Long`, `Double`, `Boolean`, `Optional`, or many `java.time` values. These types should be treated as values, not identity-bearing lock objects.

```
Integer accountId = 42;
synchronized (accountId) { // warning / unsafe design
    // never use wrapper identity as a lock
}
```

Use a private dedicated lock, `ReentrantLock`, database transaction/locking semantics, or a distributed lock appropriate to the consistency boundary.

```java
private final Object balanceLock = new Object();

void updateBalance() {
    synchronized (balanceLock) {
        // protect only in-memory state owned by this object
    }
}
```

## Why it matters for backend services

Synchronizing on request IDs, strings, boxed values, or JPA identifiers can cause accidental lock sharing because values may be cached, reused, or equal without being intended as a coordination mechanism. It also does not coordinate across JVMs; use database constraints/locking or a distributed coordination system for multi-instance workflows.

## Other platform notes

Java 16 also delivered Alpine Linux and Windows/AArch64 ports. Treat platform support as a deployment qualification task: validate base images, native libraries, observability agents, JIT behavior, and vendor support for the exact target architecture.

## Daily upgrade checklist

```bash
# Locate common internal-API and unsafe-reflection indicators.
rg "sun\\.|jdk\\.internal|setAccessible\(true\)|Unsafe" src test build.gradle pom.xml
```

Review every result; some may be harmless strings or supported `jdk.unsupported` use, but none should be ignored without understanding the dependency and replacement path.

## Common mistakes

1. Adding `--add-opens` permanently instead of upgrading the dependency.
2. Synchronizing on an ID, string, wrapper, `Optional`, or time object.
3. Assuming in-JVM synchronization protects data across horizontally scaled instances.
4. Testing an upgrade only on a developer machine, not on deployment architectures.
5. Treating warning suppression as a migration plan.

