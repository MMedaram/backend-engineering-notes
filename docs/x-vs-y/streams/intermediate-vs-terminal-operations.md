---
title: Intermediate vs Terminal Operations
parent: Streams
nav_order: 11
---

# Intermediate vs Terminal Operations

Java Streams process data through a pipeline of operations.
These operations are divided into:

- **Intermediate Operations**
- **Terminal Operations**

Understanding the difference is fundamental to using Streams correctly.

---

## Basic Difference

| Intermediate Operations | Terminal Operations |
|-------------------------|--------------------|
| Lazy | Trigger execution |
| Return a Stream | Return a result or void |
| Can be chained | Ends the stream pipeline |
| Do not execute immediately | Execute the entire pipeline |

---

## What Are Intermediate Operations?

Intermediate operations:

- Transform or filter the stream
- Return another Stream
- Are lazy (no execution happens immediately)

They build the pipeline.

### Common Intermediate Operations

- filter()
- map()
- flatMap()
- sorted()
- distinct()
- limit()
- skip()
- peek()

---

## What Are Terminal Operations?

Terminal operations:

- Trigger execution of the stream pipeline
- Produce a result or side effect
- Close the stream
- After a terminal operation, the stream cannot be reused.

### Common Terminal Operations

- forEach()
- collect()
- reduce()
- findFirst()
- findAny()
- count()
- anyMatch()
- allMatch()
- noneMatch()


---

> Intermediate operations like filter() and map() are lazy and return a new stream, building a pipeline. Terminal operations like collect() and forEach() trigger execution of the pipeline and produce a result or side effect.