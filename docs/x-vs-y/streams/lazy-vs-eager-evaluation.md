---
title: Lazy vs Eager Evaluation
parent: Streams
nav_order: 10
---

# Lazy vs Eager Evaluation

Evaluation strategy determines **when** expressions are executed.

In Java (especially Streams), understanding lazy vs eager evaluation
is essential for writing efficient and predictable code.

---

## Basic Difference

| Lazy Evaluation | Eager Evaluation |
|----------------|------------------|
| Executes only when needed | Executes immediately |
| Delays computation | Computes right away |
| Improves performance | May waste computation |
| Used in Streams (intermediate ops) | Used in Collections & normal method calls |

---

## What is Eager Evaluation?

Eager evaluation means:

> Expression is evaluated immediately when it is encountered.


### Example (Normal Java Method Call)

```java
public static String getValue() {
    System.out.println("Executed");
    return "Hello";
}

String value = getValue();
```

---

## What is Lazy Evaluation?

Lazy evaluation means:

> Computation is delayed until the result is actually needed.

In Streams:

- Intermediate operations are lazy
- Execution starts only at terminal operation

---

> Lazy evaluation delays execution until a terminal operation is invoked.   
> In Java Streams, intermediate operations like map() and filter() are lazy, while terminal operations like collect() and forEach() trigger execution.    
> Eager evaluation, on the other hand, executes immediately.

