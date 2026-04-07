---
title: Executor
parent: Concurrency
nav_order: 1
---

# Executor (java.util.concurrent)

The **Executor** in the Java `java.util.concurrent` package is a core interface that decouples task submission from the mechanics of how tasks are run. It defines a simple abstraction for executing Runnable commands, forming the foundation of Java’s modern concurrency framework.

## Key facts

- **Introduced:** Java 5 (2004)
- **Package:** `java.util.concurrent`
- **Core method:** void execute(Runnable command)
- **Common implementations:** `ThreadPoolExecutor`, `ScheduledThreadPoolExecutor`
- **Purpose:** Simplify and manage concurrent task execution

## Role in Concurrency Framework

`Executor` provides a high-level API for launching asynchronous tasks without managing threads directly. It allows applications to focus on defining tasks, while underlying executors handle scheduling, thread reuse, and lifecycle control. This abstraction replaced manual thread creation, reducing boilerplate and improving performance predictability.

### Implementation Hierarchy
Several key interfaces and classes extend or implement Executor:

- **ExecutorService**: Adds task submission with `submit()` and lifecycle control (`shutdown()`).

- **ScheduledExecutorService**: Adds delayed and periodic execution capabilities.

- **ThreadPoolExecutor**: A flexible pool-based executor implementation.

- **ForkJoinPool**: Optimized for parallel, divide-and-conquer tasks.

These implementations let developers choose between fixed-size, cached, or work-stealing thread pools, matching the concurrency model to application needs.

## Common Usage

A typical usage pattern involves obtaining an executor from factory methods in `Executors`, submitting `Runnable` or `Callable` tasks, and later shutting down the executor. This model supports efficient thread reuse and system resource management, making it standard practice in concurrent Java applications.

## Significance
The `Executor` abstraction underpins much of Java’s modern concurrency design. It promotes scalable, maintainable, and robust multithreading by separating task logic from execution policy—an essential evolution from the earlier manual thread management model.


