---
title: Concurrency Enhancements
parent: Java-8
nav_order: 16
---

# Concurrency Enhancements

Java 8 improved concurrency utilities.

Important additions:

- `LongAdder`
- `LongAccumulator`
- `DoubleAdder`
- `DoubleAccumulator`
- `StampedLock`
- `ConcurrentHashMap` bulk operations
- `ConcurrentHashMap.newKeySet`
- `ForkJoinPool.commonPool`

---

## LongAdder

`LongAdder` is useful for counters updated by many threads.

```
LongAdder counter = new LongAdder();

counter.increment();
counter.add(5);

long value = counter.sum();
```

Why not always use `AtomicLong`?

`AtomicLong` is good, but under high contention many threads fight for the same value.

`LongAdder` spreads updates internally and combines them when `sum()` is called.

---

## LongAdder Edge Case

`sum()` is not a perfect atomic snapshot while updates are happening.

Use `LongAdder` for statistics/counters.

Use `AtomicLong` when you need strict compare-and-set logic.

Good use:

- Request count.
- Metrics.
- Hit count.

Avoid for:

- Bank balance.
- Sequence generator requiring exact atomic value.

---

## LongAccumulator

Use custom accumulation logic.

```
LongAccumulator max = new LongAccumulator(Long::max, Long.MIN_VALUE);

max.accumulate(10);
max.accumulate(50);

System.out.println(max.get()); // 50
```

---

## StampedLock

`StampedLock` supports three modes:

- Write lock.
- Read lock.
- Optimistic read.

```
StampedLock lock = new StampedLock();

long stamp = lock.writeLock();
try {
    value++;
} finally {
    lock.unlockWrite(stamp);
}
```

---

## Optimistic Read

```
long stamp = lock.tryOptimisticRead();
int current = value;

if (!lock.validate(stamp)) {
    stamp = lock.readLock();
    try {
        current = value;
    } finally {
        lock.unlockRead(stamp);
    }
}
```

Simple meaning:

> Read without locking first. If someone changed data, retry with real read lock.

---

## StampedLock Edge Cases

- It is not reentrant.
- Always unlock using the correct stamp.
- Wrong stamp can cause `IllegalMonitorStateException`.
- More complex than `ReentrantReadWriteLock`.
- Do not use unless you need its performance behavior.

---

## ConcurrentHashMap Enhancements

Java 8 added bulk operations:

- `forEach`
- `search`
- `reduce`

```
map.forEach(1, (key, value) -> {
    System.out.println(key + " = " + value);
});
```

`1` is the parallelism threshold.

Smaller threshold means more likely to run in parallel.

---

## newKeySet

Create a concurrent set backed by `ConcurrentHashMap`.

```
Set<String> activeUsers = ConcurrentHashMap.newKeySet();

activeUsers.add("Mohan");
```

Useful when many threads update a set.

---

## Best Practices

- Use `LongAdder` for high-concurrency metrics.
- Use `AtomicLong` for exact atomic updates and CAS logic.
- Use `StampedLock` only when you understand its locking model.
- Use `ConcurrentHashMap.newKeySet()` for concurrent sets.
- Avoid blocking operations inside `ConcurrentHashMap` bulk operations.
- Measure performance before replacing simple locks.

---

## Quick Summary

Java 8 added better concurrency tools. `LongAdder` is excellent for counters, `StampedLock` can help read-heavy code, and `ConcurrentHashMap` gained useful bulk operations.

