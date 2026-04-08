---
title: Future
parent: Concurrency
nav_order: 7
---

# Future (I)

## 1. What is Future?

**Future** is a placeholder for a result that will be available in the future (asynchronous computation).

You submit a task → it runs in another thread → Future lets you track & retrieve result

---

## 2. Creating Future

You never create it manually.

```java
ExecutorService executor = Executors.newFixedThreadPool(2);
Future<Integer> future = executor.submit(() -> 10);
```

- `submit()` returns a Future

---

## 3. Internal Working

When you call:

> executor.submit(callable);

👉 Internally:

```
Callable
   ↓
Wrapped into FutureTask
   ↓
FutureTask implements Runnable + Future
   ↓
Thread pool executes it
   ↓
Result stored inside FutureTask
```

---

## 4. Future Interface Methods

```java
public interface Future<V> {
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit);

    boolean cancel(boolean mayInterruptIfRunning);
    boolean isDone();
    boolean isCancelled();
}
```

---

### 4.1 get()  (Blocking)

```java
Integer result = future.get();
```

- Blocks current thread until result is ready

```
Future<Integer> future = executor.submit(() -> {
    Thread.sleep(3000);
    return 100;
});

System.out.println("Before get()");
Integer result = future.get(); // waits 3 sec
System.out.println("Result: " + result);

```
### output

```
Before get()
(wait 3 sec)
Result: 100
```

> get() = blocking call

---

## 4.2 get(timeout)

```
future.get(2, TimeUnit.SECONDS);
```

- waits only for given time
- Throws TimeoutException if not completed.

```
try {
    future.get(1, TimeUnit.SECONDS);
} catch (TimeoutException e) {
    System.out.println("Timeout!");
}
```

---

## 4.3 isDone()

```
future.isDone();
```

Returns:

- true → completed
- false → still running

---

## 4.4. cancel()

```
future.cancel(true);
```

Attempts to cancel execution.

| Value   | Meaning          |
| ------- | ---------------- |
| `true`  | Interrupt thread |
| `false` | Don't interrupt  |


---

## 4.5 isCancelled()

> future.isCancelled()

---

```
ExecutorService executor = Executors.newFixedThreadPool(1);

Future<Integer> future = executor.submit(() -> {
    Thread.sleep(2000);
    return 50;
});

System.out.println("isDone: " + future.isDone());

Integer result = future.get();

System.out.println("Result: " + result);
System.out.println("isDone: " + future.isDone());

executor.shutdown();
```

## 5. Exception Handling

```java
Future<Integer> future = executor.submit(() -> {
    throw new RuntimeException("Error");
});
```

```
try {
    future.get();
} catch (ExecutionException e) {
    System.out.println(e.getCause());
}
```

---


---

## 6. Limitations of Future

- Blocking calls (get)
- No chaining
  - You cannot do:  `Task1 → Task2 → Task3`
- No combination of tasks
- Poor exception handling
- No callbacks

---

## 7. Why CompletableFuture?

To overcome Future limitations:
- Non-blocking
- Chaining support
- Better exception handling

---

## Final Insight

Future is useful but limited. Prefer CompletableFuture for modern async programming.
