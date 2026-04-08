---
title: Thread Pools & Executors
parent: Concurrency
nav_order: 5
---

## Why Thread Pools?
   
###  Problem with creating threads manually
   
```
new Thread(() -> {
 // task
}).start();
```
👉 Issues:

- Thread creation is expensive
- Too many threads → memory + CPU overhead
- No control → resource exhaustion

### Solution: Thread Pool

> Reuse a fixed number of threads to execute multiple tasks

----

## Core Components

1. [Executor](https://mmedaram.github.io/backend-engineering-notes/docs/java/08-concurrent/01_executor.html)

- Basic interface

```
void execute(Runnable command);
```

2. [ExecutorService](https://mmedaram.github.io/backend-engineering-notes/docs/java/08-concurrent/02_ExecutorService.html)

- Extends `Executor`
- Adds lifecycle + task management

3. [ThreadPoolExecutor](https://mmedaram.github.io/backend-engineering-notes/docs/java/08-concurrent/03_ThreadPoolExecutor.html)

Implementation class for Executor , ExecutorServices

4. [Executors](https://mmedaram.github.io/backend-engineering-notes/docs/java/08-concurrent/04_Executors.html)

- Utility class to create thread pools

----

## Types of Thread Pools

### 1. Fixed Thread Pool

```java
ExecutorService executor = Executors.newFixedThreadPool(3);
```

-  Fixed number of threads
-  Tasks queued if threads busy

```java
package exercise.threads;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class MyThread {
    public static void main(String[] args) {

        // 1. Create the executor with a pool of 3 threads
        ExecutorService executor = Executors.newFixedThreadPool(3);

        // 2. Submit 10 tasks to the executor
        for (int i = 1; i <= 10; i++) {
            int emailId = i;
            executor.execute(() -> {
                String threadName = Thread.currentThread().getName();
                System.out.println("Thread " + threadName + " is sending email #" + emailId);

                try {
                    // Simulate time taken to send an email (2 seconds)
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                System.out.println("Email #" + emailId + " sent successfully!");
            });
        }

        // 3. Gracefully shut down
        executor.shutdown();

        try {
            // Wait for all tasks to finish before exiting the main program
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow();
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }

        System.out.println("All emails processed.");
    }
}
```

### 2. Cached Thread Pool

```java
ExecutorService executor = Executors.newCachedThreadPool();
```

- ✔ Creates threads as needed
- ✔ Reuses idle threads
- ❌ Can create too many threads (danger)


### 3. Single Thread Executor

```java
ExecutorService executor = Executors.newSingleThreadExecutor();
```

- ✔ Only one thread
- ✔ Sequential execution

### 4. Scheduled Thread Pool

```java
ScheduledExecutorService executor = Executors.newScheduledThreadPool(2);
```

✔ Run tasks after delay or periodically

----

## Core Methods

### execute()

```
executor.execute(() -> {
    System.out.println("Task");
});
```

- Fire-and-forget
- No result

### submit()

```java
Future<Integer> future = executor.submit(() -> {
    return 10;
});
```

- Returns result via Future

### shutdown()

```
executor.shutdown();
```

- Stops accepting new tasks
- Completes existing tasks


### shutdownNow()

```
executor.shutdownNow();
```

- Attempts to stop immediately
- Interrupts running threads

### awaitTermination()

```
executor.awaitTermination(5, TimeUnit.SECONDS);
```

- Waits for termination

