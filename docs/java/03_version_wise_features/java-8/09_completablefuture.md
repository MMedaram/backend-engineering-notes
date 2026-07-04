---
title: CompletableFuture
parent: Java-8
nav_order: 10
---

# CompletableFuture

Java 8 introduced `CompletableFuture` for asynchronous programming.

Simple meaning:

> Start work now, get the result later, and define what should happen next.

---

## Why Was CompletableFuture Introduced?

Before Java 8, `Future` allowed async work but was limited.

```java
Future<String> future = executor.submit(() -> callApi());
String result = future.get(); // blocks
```

Problems:

- Hard to chain steps.
- Hard to combine multiple async results.
- Exception handling was awkward.
- `get()` blocks the current thread.

`CompletableFuture` solves this with async pipelines.

---

## Basic Example

```
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "Hello";
});

future.thenAccept(System.out::println);
```

`supplyAsync` is used when task returns a value.

`runAsync` is used when task returns nothing.

```java
CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
    System.out.println("Running async");
});
```

---

## thenApply

Transforms result.

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> "mohan")
    .thenApply(String::toUpperCase);
```

Result:

```text
MOHAN
```

---

## thenCompose

Use when next step also returns `CompletableFuture`.

```java
CompletableFuture<List<Order>> ordersFuture = findUser(userId)
    .thenCompose(user -> findOrders(user));
```

Without `thenCompose`, you may get nested futures:

```
CompletableFuture<CompletableFuture<List<Order>>>
```

---

## thenCombine

Combines two independent futures.

```java
CompletableFuture<User> userFuture = findUser(userId);
CompletableFuture<Account> accountFuture = findAccount(userId);

CompletableFuture<UserProfile> profileFuture =
    userFuture.thenCombine(accountFuture, UserProfile::new);
```

Use when both tasks can run independently.

---

## allOf

Waits for all futures to finish.

```java
CompletableFuture<Void> all = CompletableFuture.allOf(future1, future2, future3);
```

Important:

`allOf` returns `CompletableFuture<Void>`, not a list of results.

You still need to read each future result.

```java
CompletableFuture<Void> all = CompletableFuture.allOf(f1, f2);

CompletableFuture<List<String>> result = all.thenApply(v ->
    Arrays.asList(f1.join(), f2.join())
);
```

---

## anyOf

Completes when any one future completes.

```java
CompletableFuture<Object> fastest = CompletableFuture.anyOf(cacheFuture, dbFuture);
```

Useful for fastest-response-wins workflows.

---

## Exception Handling

### exceptionally

Returns fallback value.

```java
CompletableFuture<String> future = callApi()
    .exceptionally(error -> "fallback");
```

### handle

Handles success and failure.

```java
CompletableFuture<String> future = callApi()
    .handle((result, error) -> {
        if (error != null) {
            return "fallback";
        }
        return result;
    });
```

---

## join vs get

```
future.get();
```

throws checked exceptions:

- `InterruptedException`
- `ExecutionException`

```
future.join();
```

throws unchecked:

- `CompletionException`

Avoid blocking if possible. Use chaining.

---

## Executor Edge Case

By default, async methods use:

```
ForkJoinPool.commonPool()
```

For backend applications, use a dedicated executor for blocking work:

```java
ExecutorService executor = Executors.newFixedThreadPool(10);

CompletableFuture<String> future =
    CompletableFuture.supplyAsync(this::callExternalApi, executor);
```

Shutdown executor when application owns it:

```
executor.shutdown();
```

---

## Common Mistakes

- Blocking with `get()` too early.
- Using common pool for blocking IO.
- Forgetting exception handling.
- Creating too many async tasks.
- Thinking `allOf` returns results.
- Ignoring thread pool sizing.

---

## Best Practices

- Use `thenApply` for simple transformation.
- Use `thenCompose` for dependent async calls.
- Use `thenCombine` for independent async calls.
- Use custom executor for blocking tasks.
- Handle exceptions clearly.
- Avoid blocking unless you are at the boundary of your application.

---

## Quick Summary

CompletableFuture helps build async pipelines. It is powerful for combining and chaining async work, but thread pool choice and exception handling are very important.

