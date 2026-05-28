---
title: CompletableFuture (java8)
parent: Concurrency
nav_order: 8
---

# CompletableFuture in Java 8

## 🔹 What is CompletableFuture?

`CompletableFuture` is a class introduced in Java 8 under:

```
java.util.concurrent.CompletableFuture
```

It is used for:

* Asynchronous programming
* Non-blocking execution
* Chaining multiple tasks
* Combining parallel computations
* Handling async exceptions cleanly

---

# 🔹 Why CompletableFuture Was Introduced

Before Java 8, Java had `Future`.

Example:

```java
Future<String> future = executor.submit(() -> "Hello");
```

Problems with `Future`:

* Cannot chain tasks
* No callback mechanism
* Blocking calls using `get()`
* Challenging exception handling

Java 8 introduced `CompletableFuture` to solve these limitations.

---

# 🔹 Basic CompletableFuture Example

```java
import java.util.concurrent.CompletableFuture;

public class Main {

    public static void main(String[] args) {

        CompletableFuture<String> future =
            CompletableFuture.supplyAsync(() -> "Hello from async task");

        System.out.println(future.join());
    }
}
```

---

# 🔹 Creating CompletableFuture

## 1. runAsync()

Used when a task does NOT return value.

```java
public static void main(String[] args) {
    CompletableFuture.runAsync(() -> System.out.println("Running async task"));
}
```

---

## 2. supplyAsync()

Used when task RETURNS value.

```java
CompletableFuture<String> future =
    CompletableFuture.supplyAsync(() -> "Hello");
```

---

# 🔹 Chaining Operations

One of the most powerful features.

---

## thenApply()

Transforms result.

```java
public static void main(String[] args) {
    CompletableFuture.supplyAsync(() -> 10)
            .thenApply(x -> x * 2)
            .thenApply(x -> x + 5)
            .thenAccept(System.out::println);
    
}
```

Output:

```text
25
```

---

## thenAccept()

Consumes result.

```
.thenAccept(result -> System.out.println(result));
```

---

## thenRun()

Run a task without input/output.

```
.thenRun(() -> System.out.println("Done"));
```

---

# 🔹 Combining Multiple Futures

## thenCombine()

Combines results from two futures.

```java
public static void main(String[] args) {
    CompletableFuture<Integer> f1 =
            CompletableFuture.supplyAsync(() -> 10);

    CompletableFuture<Integer> f2 =
            CompletableFuture.supplyAsync(() -> 20);

    f1.thenCombine(f2, (a, b) -> a + b)
            .thenAccept(System.out::println);
    
}
```

Output:

```text
30
```

---

# 🔹 Waiting for Multiple Futures

## allOf()

Wait for all futures to complete.

```
CompletableFuture<Void> all =
    CompletableFuture.allOf(f1, f2);

all.join();
```

---

# 🔹 Exception Handling

## exceptionally()

```java
public static void main(String[] args) {
    CompletableFuture.supplyAsync(() -> {
                if (true)
                    throw new RuntimeException("Error");

                return 10;
            })
            .exceptionally(ex -> {
                System.out.println("Handled: " + ex);
                return 0;
            });
    
}
```

---

## handle()

Handles both success and failure.

```
.handle((result, ex) -> {

    if (ex != null) {
        return 0;
    }

    return result;
});
```

---

# 🔹 Async vs. Non-Async Methods

| Method           | Execution Thread            |
|------------------|-----------------------------|
| thenApply()      | Same thread                 |
| thenApplyAsync() | Separate thread/thread pool |

---

# 🔹 Important Methods Cheat Sheet

| Method          | Purpose                   |
|-----------------|---------------------------|
| runAsync()      | Async task without result |
| supplyAsync()   | Async task with result    |
| thenApply()     | Transform result          |
| thenAccept()    | Consume result            |
| thenRun()       | Execute another task      |
| thenCombine()   | Combine two futures       |
| allOf()         | Wait for all futures      |
| exceptionally() | Handle exceptions         |
| handle()        | Handle success/failure    |

---

# 🔹 Internal Working

By default, CompletableFuture uses:

```
ForkJoinPool.commonPool()
```

Unless custom Executor is provided.

Example:

```
ExecutorService executor =
    Executors.newFixedThreadPool(5);

CompletableFuture.supplyAsync(() -> {
    return "Hello";
}, executor);
```

---

# 🔹 Why CompletableFuture Is Powerful

Advantages:

* Non-blocking programming
* Better CPU utilization
* Parallel execution
* Cleaner async code
* Easy error handling
* Functional style programming
* Better scalability

---

# 🔹 Difference Between Future and CompletableFuture

| Feature                | Future    | CompletableFuture |
|------------------------|-----------|-------------------|
| Blocking support       | Yes       | Yes               |
| Non-blocking callbacks | No        | Yes               |
| Task chaining          | No        | Yes               |
| Combine futures        | No        | Yes               |
| Exception handling     | Difficult | Easy              |
| Functional programming | No        | Yes               |

---

# 🔹 Real Microservice Use Case of CompletableFuture

Suppose we have an E-Commerce microservice.

When a user opens a product page, application needs:

1. Product Details Service
2. Inventory Service
3. Pricing Service
4. Review Service

Instead of calling sequentially:

```text
Call Product Service
Wait

Call Inventory Service
Wait

Call Pricing Service
Wait

Call Review Service
Wait
```

We can call ALL services in parallel using CompletableFuture.

---

# 🔹 Real Example

```java
import java.util.concurrent.CompletableFuture;

public class ProductAggregator {

    public static void main(String[] args) {

        CompletableFuture<String> productFuture =
            CompletableFuture.supplyAsync(ProductAggregator::getProduct);

        CompletableFuture<String> inventoryFuture =
            CompletableFuture.supplyAsync(ProductAggregator::getInventory);

        CompletableFuture<String> pricingFuture =
            CompletableFuture.supplyAsync(ProductAggregator::getPrice);

        CompletableFuture<String> reviewFuture =
            CompletableFuture.supplyAsync(ProductAggregator::getReviews);

        CompletableFuture.allOf(
                productFuture,
                inventoryFuture,
                pricingFuture,
                reviewFuture
        ).join();

        System.out.println(productFuture.join());
        System.out.println(inventoryFuture.join());
        System.out.println(pricingFuture.join());
        System.out.println(reviewFuture.join());
    }

    static String getProduct() {
        sleep();
        return "Product Details";
    }

    static String getInventory() {
        sleep();
        return "Inventory Available";
    }

    static String getPrice() {
        sleep();
        return "Price: 500";
    }

    static String getReviews() {
        sleep();
        return "Reviews: 4.5";
    }

    static void sleep() {
        try {
            Thread.sleep(2000);
        } catch (Exception e) {
        }
    }
}
```

---

# 🔹 Performance Benefit

Sequential calls:

```text
2 + 2 + 2 + 2 = 8 seconds
```

Parallel CompletableFuture calls:

```text
~2 seconds total
```

Huge performance improvement.

---

# 🔹 Common Interview Questions

## Q1. Why CompletableFuture over Future?

Because it supports:

* Chaining
* Non-blocking callbacks
* Combining tasks
* Better exception handling

---

## Q2. Difference between thenApply and thenCompose?

### thenApply()

Transforms result.

### thenCompose()

Flattens nested futures.

Example:

```
.thenCompose(data -> getAnotherFuture(data))
```

---

## Q3. Difference between join() and get()?

| join()              | get()              |
|---------------------|--------------------|
| Unchecked exception | Checked exception  |
| Cleaner syntax      | Requires try-catch |

---

# 🔹 Simple One-Line Definition

> CompletableFuture is a Java 8 feature used for asynchronous, non-blocking programming that allows chaining, combining, and handling multiple tasks efficiently.
