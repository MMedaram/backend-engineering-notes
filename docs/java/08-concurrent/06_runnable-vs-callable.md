---
title: Runnable vs Callable
parent: Concurrency
nav_order: 6
---


# Runnable vs Callable

----

## 1. What is Runnable?

**Runnable** is a **functional interface** used to represent a task that:

- does NOT return a result
- does NOT throw checked exceptions

```java
@FunctionalInterface
public interface Runnable {
    void run();
}
```

- Only one method → run()

#### Example :

```java
Runnable task = () -> {
    System.out.println("Task running");
};
```

### Execution Flow:

```
Thread t = new Thread(task);
t.start();
```

```
start()
   ↓
JVM creates new thread
   ↓
calls run() method
```

| Feature      | Behavior               |
| ------------ | ---------------------- |
| Return value | ❌ None                 |
| Exception    | ❌ Cannot throw checked |
| Use case     | Fire-and-forget tasks  |


## Real Use Case
- Logging
- Sending notifications
- Background cleanup

-----

# What is Callable?

Callable is like Runnable, but:

- Returns result
- Can throw exception

```java
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```

- Generic type <V> = return type

## Execution

Cannot run directly with Thread

❌ This will NOT work:

> new Thread(task); // ERROR

## Correct Way (ExecutorService)

```java
ExecutorService executor = Executors.newFixedThreadPool(2);

Future<Integer> future = executor.submit(task);
```


## Why Callable Needs Future

### Because:

- Task runs in another thread
- Result is not immediately available

So we need a placeholder for result

👉 That is Future


-----


# Runnable Vs Callable

| Feature         | Runnable          | Callable      |
| --------------- | ----------------- | ------------- |
| Method          | `run()`           | `call()`      |
| Return          | ❌ No              | ✅ Yes         |
| Exception       | ❌ No checked      | ✅ Yes         |
| Execution       | Thread / Executor | Executor only |
| Result handling | ❌                 | ✅ via Future  |

------

## Internal Execution Flow 

### Runnable Flow

> Runnable → Thread → start() → run() executes


### Callable Flow

```
Callable → ExecutorService.submit()
         ↓
Task wrapped into FutureTask
         ↓
Executed by thread pool
         ↓
Result stored in Future
```

Important internal class:

- FutureTask (implements Runnable + Future)


**When you do:**

> executor.submit(callable);

```
Callable → wrapped into FutureTask
FutureTask → implements Runnable
Executor → executes it like Runnable
Result stored internally
```

### Runnable vs Callable Together

```
ExecutorService executor = Executors.newFixedThreadPool(2);

// Runnable
executor.execute(() -> {
    System.out.println("Runnable task");
});

// Callable
Future<Integer> future = executor.submit(() -> {
    return 42;
});

System.out.println(future.get());
executor.shutdown();
```

------------------------------


## Use Runnable when:

- No result needed
- Fire-and-forget tasks

## Use Callable when:

- Need result
- Need exception handling


