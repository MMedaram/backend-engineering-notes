---
title: start() vs run()
parent: Threads
nav_order: 3
---


# start() vs run()

```
Thread t = new Thread(() -> {
    System.out.println("Running in thread");
});

t.run();   // ❌ normal method call (NO new thread)
t.start(); // ✅ creates new thread
```

| Aspect              | `start()`                                        | `run()`                               |
| ------------------- |--------------------------------------------------| ------------------------------------- |
| Purpose             | Starts a new thread                              | Contains the task logic               |
| Thread creation     | ✅ Creates a new thread                           | ❌ No new thread                       |
| Execution           | Asynchronous (parallel/concurrent)               | Synchronous (normal method call)      |
| Call stack          | New thread stack created                         | Uses current thread stack             |
| JVM involvement     | Calls native method → OS thread                  | Just a normal method                  |
| Thread state change | NEW → RUNNABLE                                   | No state change                       |
| Who executes        | New worker thread                                | Calling thread (e.g., main)           |
| Multiple calls      |❌throws **IllegalThreadStateException** exception | ✅ Can call multiple times             |
| Performance         | True concurrency                                 | No concurrency                        |
| Real-world usage    | Always used to start threads                     | Never called directly (almost always) |



#### When you call start():

```
start()
   ↓
JVM calls native method
   ↓
Creates new OS thread
   ↓
Calls run() internally in new thread
```

**So**

> You NEVER call run() directly — JVM does it for you.

