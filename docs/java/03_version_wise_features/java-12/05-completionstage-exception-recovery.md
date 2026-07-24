---
title: CompletionStage exception recovery
parent: Java-12
nav_order: 5
---

# Java 12 - CompletionStage Exception Recovery Methods

Java 12 added more exception recovery methods to `CompletionStage`.

The important methods are:

```
exceptionallyAsync(...)
exceptionallyCompose(...)
exceptionallyComposeAsync(...)
```

Simple meaning:

They help recover from failed async operations in a cleaner way.

---

## Why was it introduced?

Before Java 12, we had:

```
exceptionally(...)
handle(...)
whenComplete(...)
```

These are useful, but one common case was awkward:

> If async call A fails, call async fallback B.

Before Java 12, developers often wrote nested futures or used `handle(...).thenCompose(...)`.

Java 12 made this easier with `exceptionallyCompose()`.

---

## Quick Method Map

| Method                          | Meaning                                                   |
|---------------------------------|-----------------------------------------------------------|
| `exceptionally(fn)`             | Recover with a normal value                               |
| `exceptionallyAsync(fn)`        | Recover with a normal value asynchronously                |
| `exceptionallyCompose(fn)`      | Recover by calling another async operation                |
| `exceptionallyComposeAsync(fn)` | Recover by calling another async operation asynchronously |

---

# 1. exceptionally()

This existed before Java 12, but it is important for comparison.

```
CompletableFuture<String> future = CompletableFuture
        .supplyAsync(() -> {
            throw new RuntimeException("Service failed");
        })
        .exceptionally(error -> "fallback value");

System.out.println(future.join());
```

Output:

```text
fallback value
```

Use this when fallback is already available.

---

# 2. exceptionallyAsync()

`exceptionallyAsync()` runs the recovery function asynchronously.

```
CompletableFuture<String> future = CompletableFuture
        .supplyAsync(() -> {
            throw new RuntimeException("Primary service failed");
        })
        .exceptionallyAsync(error -> {
            log.warn("Recovering from failure", error);
            return "fallback value";
        });

System.out.println(future.join());
```

Use this when recovery work should not run in the same completion thread.

---

## exceptionallyAsync() with custom executor

For backend applications, prefer a custom executor for blocking fallback work.

```java
ExecutorService fallbackExecutor = Executors.newFixedThreadPool(4);

CompletableFuture<String> future = CompletableFuture
        .supplyAsync(() -> callPrimaryService())
        .exceptionallyAsync(error -> loadFallbackFromDatabase(), fallbackExecutor);
```

Why?

Because the default async executor may use the common pool.

Blocking calls in the common pool can hurt other async tasks.

---

# 3. exceptionallyCompose()

`exceptionallyCompose()` is the most important Java 12 addition here.

It lets us recover by returning another `CompletionStage`.

Example:

```java
CompletableFuture<String> result = CompletableFuture
        .supplyAsync(() -> callPrimaryService())
        .exceptionallyCompose(error -> callBackupService());
```

Here:

- primary service runs first
- if it succeeds, backup service is not called
- if it fails, backup service is called
- final result is a clean `CompletableFuture<String>`

No nested future problem.

---

## Daily backend use case: fallback API call

```java
public CompletableFuture<UserProfile> getUserProfile(String userId) {
    return profileClient.fetchFromPrimary(userId)
            .exceptionallyCompose(error -> {
                log.warn("Primary profile service failed. Trying backup.", error);
                return profileClient.fetchFromBackup(userId);
            });
}
```

Good for:

- primary/backup service calls
- fallback cache lookup
- retry through another async method
- degraded response flow

---

## Daily backend use case: fallback cache

```java
public CompletableFuture<Product> getProduct(String productId) {
    return productApi.fetchProduct(productId)
            .exceptionallyCompose(error -> cacheApi.fetchProduct(productId));
}
```

If the main API fails, it tries cache asynchronously.

---

## Why not use exceptionally() here?

Bad pattern:

```java
CompletableFuture<CompletableFuture<UserProfile>> nested = primaryFuture
        .exceptionally(error -> profileClient.fetchFromBackup(userId));
```

This creates:

```text
CompletableFuture<CompletableFuture<UserProfile>>
```

That is harder to use.

Better:

```java
CompletableFuture<UserProfile> result = primaryFuture
        .exceptionallyCompose(error -> profileClient.fetchFromBackup(userId));
```

---

# 4. exceptionallyComposeAsync()

This method is like `exceptionallyCompose()`, but the recovery function is triggered asynchronously.

```java
CompletableFuture<UserProfile> result = profileClient.fetchFromPrimary(userId)
        .exceptionallyComposeAsync(error -> profileClient.fetchFromBackup(userId));
```

With custom executor:

```java
CompletableFuture<UserProfile> result = profileClient.fetchFromPrimary(userId)
        .exceptionallyComposeAsync(
                error -> profileClient.fetchFromBackup(userId),
                fallbackExecutor
        );
```

Use this when deciding or starting fallback work should run on a specific executor.

---

## Difference between handle(), exceptionally(), and exceptionallyCompose()

| Method                 | Runs on success? | Runs on failure? | Can change result?         | Good For                              |
|------------------------|------------------|------------------|----------------------------|---------------------------------------|
| `whenComplete`         | Yes              | Yes              | No, mainly side effects    | Logging/metrics                       |
| `handle`               | Yes              | Yes              | Yes                        | Convert success/failure into a result |
| `exceptionally`        | No               | Yes              | Yes, normal value fallback |
| `exceptionallyCompose` | No               | Yes              | Yes, async fallback        |

---

## Edge cases and negative cases

### 1. If original stage succeeds, recovery is not called

```
CompletableFuture<String> future = CompletableFuture
        .completedFuture("OK")
        .exceptionallyCompose(error -> {
            throw new RuntimeException("Should not run");
        });

System.out.println(future.join());
```

Output:

```text
OK
```

---

### 2. Recovery function is null

```
future.exceptionallyCompose(null);
```

Output:

```text
java.lang.NullPointerException
```

---

### 3. Recovery function throws exception

```
CompletableFuture<String> result = failedFuture()
        .exceptionallyCompose(error -> {
            throw new IllegalStateException("Fallback failed");
        });

result.join();
```

Possible output:

```text
java.util.concurrent.CompletionException: java.lang.IllegalStateException: Fallback failed
```

---

### 4. Fallback future also fails

```
CompletableFuture<String> result = failedFuture()
        .exceptionallyCompose(error -> failedBackupFuture());

result.join();
```

Possible output:

```text
java.util.concurrent.CompletionException
```

The final future fails because both primary and fallback failed.

---

### 5. join() wraps exceptions

```
future.join();
```

If the future failed, `join()` usually throws:

```text
CompletionException
```

The real cause is inside:

```
catch (CompletionException e) {
    Throwable realCause = e.getCause();
}
```

---

### 6. Do not block inside async recovery

Bad:

```
primaryFuture.exceptionally(error -> backupFuture.join());
```

This blocks a thread.

Better:

```
primaryFuture.exceptionallyCompose(error -> backupFuture);
```

---

## Small helper examples

```java
private CompletableFuture<String> failedFuture() {
    CompletableFuture<String> future = new CompletableFuture<>();
    future.completeExceptionally(new RuntimeException("Primary failed"));
    return future;
}

private CompletableFuture<String> failedBackupFuture() {
    CompletableFuture<String> future = new CompletableFuture<>();
    future.completeExceptionally(new RuntimeException("Backup failed"));
    return future;
}
```

---

## Best practices

1. Use `exceptionally()` for simple fallback values.
2. Use `exceptionallyCompose()` for async fallback calls.
3. Use `exceptionallyAsync()` when recovery itself should run asynchronously.
4. Use custom executors for blocking fallback work.
5. Do not call `join()` inside recovery just to flatten futures.
6. Log enough context before fallback, but avoid noisy duplicate logs.
7. Test both success and failure paths.
8. Always think about what happens if fallback also fails.

---

## Summary

Java 12 improved `CompletionStage` exception handling by adding async and compose-style recovery methods, especially `exceptionallyCompose()`, which lets a failed async flow recover by calling another async flow without creating nested futures.

---
