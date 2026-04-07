---
title: ThreadPoolExecutor
parent: Concurrency
nav_order: 3
---

# ThreadPoolExecutor

---

**ThreadPoolExecutor** is a core class in the Java concurrency framework (`java.util.concurrent`) that provides a flexible thread pool implementation. It manages a pool of worker threads to efficiently execute asynchronous tasks, improving performance and resource control in multithreaded applications.

## Key facts

- **Package**: java.util.concurrent
- **First introduced**: Java 5 (JDK 1.5)
- **Superclass**: AbstractExecutorService
- **Implements**: Executor, ExecutorService
- **Primary usage**: Managing concurrent task execution through thread pooling

## Function and design

`ThreadPoolExecutor` enables efficient task execution by reusing a fixed number of threads rather than creating a new thread for every task. 

Developers configure it using parameters such as `corePoolSize`, `maximumPoolSize`, `keepAliveTime`, and a `BlockingQueue` for queued tasks. 

These settings define how threads are `created`, `reused`, and `terminated`. The executor adjusts the pool size dynamically based on workload and allows core threads to time out when idle.

## Queuing and rejection policies

The executor uses a `BlockingQueue` to hold tasks awaiting execution. Depending on the queue type—such as `LinkedBlockingQueue`, `SynchronousQueue`, or `ArrayBlockingQueue` —it balances throughput and resource use. When the pool and queue are full, it employs a RejectedExecutionHandler policy. Built-in options include:

- **AbortPolicy**: throws an exception.
- **CallerRunsPolicy**: executes the task in the calling thread.
- **DiscardPolicy**: silently discards the task.
- **DiscardOldestPolicy**: drops the oldest unhandled task and retries.

## Extensibility and monitoring

Developers can subclass ThreadPoolExecutor to customize behavior through hook methods:

- `beforeExecute(Thread, Runnable)`
- `afterExecute(Runnable, Throwable)`
- `terminated()`

It also exposes metrics like active thread count, pool size, and completed task count for runtime monitoring.

The design supports dynamic tuning, making it foundational for scalable, responsive Java systems, including frameworks such as Spring’s ThreadPoolTaskExecutor which builds upon it for managed environments. 




