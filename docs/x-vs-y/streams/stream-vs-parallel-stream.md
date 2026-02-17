---
title: stream() vs parallelStream()
parent: Streams
nav_order: 3
---

# stream() vs parallelStream()

Java 8 introduced Streams to process collections in a functional style.
Streams can operate in:

- **Sequential mode** → `stream()`
- **Parallel mode** → `parallelStream()`

Understanding the difference is critical for writing performant and safe code.

---

## Basic Definition

| stream() | parallelStream() |
|------------|-------------------|
| Sequential processing | Parallel processing |
| Single thread | Multiple threads |
| Order preserved | Order not guaranteed |
| Predictable behavior | Non-deterministic execution |

---

## How stream() Works

```text
List<Integer> list = List.of(1, 2, 3, 4);

list.stream().forEach(System.out::println);
```

Execution:

> 1 → 2 → 3 → 4

- Uses main thread
- Processes one element at a time
- Maintains encounter order

---

## How parallelStream() Works

```text 
list.parallelStream().forEach(System.out::println);
```

**Possible** execution:

> 3 → 1 → 4 → 2

### Why?

- Uses ForkJoinPool.commonPool()
- Splits data using Spliterator
- Processes chunks in parallel threads
- Merges results

---

## Internal Working

### stream()

```
Main Thread
↓
Sequential processing
```

#### parallelStream()

```
ForkJoinPool.commonPool()
↓
Split data
↓
Process in worker threads
↓
Combine results
```

#### Default thread count:

> Number of CPU cores - 1

---

| Aspect       | stream()          | parallelStream()           |
| ------------ | ----------------- | -------------------------- |
| Thread Model | Single thread     | Multiple threads           |
| Order        | Preserved         | Not guaranteed             |
| Performance  | Stable            | Depends on workload        |
| Safety       | Safer             | Risk of concurrency issues |
| Best For     | Small/medium data | Large CPU-bound tasks      |


---

