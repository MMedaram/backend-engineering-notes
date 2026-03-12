---
title: Bulkhead Pattern
parent: Resilience4j
nav_order: 5
---

# Bulkhead Pattern

## 1. Introduction

In microservices architecture, a service usually communicates with
multiple downstream services. If one dependency becomes slow or
unavailable, it can consume all available threads and block the system.

The **Bulkhead Pattern** prevents this by isolating resources for
different dependencies so that failure in one part does not affect the
entire system.

------------------------------------------------------------------------

## 2. What Problem Bulkhead Solves

Example architecture:

```
Order Service
   ├── Payment Service
   ├── Inventory Service
   └── Notification Service
```

If the **Payment Service becomes slow**, without Bulkhead:

-   Threads get blocked waiting for Payment Service
-   Inventory service calls fail
-   Notification service calls fail
-   Entire system becomes slow or unavailable

This is called **resource exhaustion**.

------------------------------------------------------------------------

## 3. What Bulkhead Does

Bulkhead isolates resources for each dependency.

Example configuration:

```
Payment Service → 20 threads
Inventory Service → 10 threads
Notification Service → 10 threads
```

If Payment service becomes slow:

-   Only its 20 threads are affected
-   Other services continue to function normally

------------------------------------------------------------------------

## 4. Why It Is Called Bulkhead

The concept comes from ship design.

Ships are divided into compartments called **bulkheads**.

If one compartment floods:

-   Other compartments remain dry
-   The ship continues to float

Similarly in microservices:

Each dependency is isolated with its own resource allocation.

------------------------------------------------------------------------

## 5. Types of Bulkhead in Resilience4j

Resilience4j supports two types of Bulkhead.

| Type                | Description                |
| ------------------- | -------------------------- |
| Semaphore Bulkhead  | Limits concurrent calls    |
| ThreadPool Bulkhead | Uses separate thread pools |


------------------------------------------------------------------------

## 6. Semaphore Bulkhead

Semaphore Bulkhead limits the number of concurrent requests.

Example:

maxConcurrentCalls = 20

If 50 requests arrive:

-   20 requests allowed
-   30 requests rejected

This prevents system overload.

------------------------------------------------------------------------

# 7. ThreadPool Bulkhead

ThreadPool Bulkhead creates separate thread pools for each dependency.

Example:

```
Payment Service → ThreadPool (20 threads)
Inventory Service → ThreadPool (10 threads)
```

If Payment service becomes slow:

-   Only Payment thread pool is blocked
-   Inventory and other services continue normally

------------------------------------------------------------------------

## 8. Real Production Example

Example:

`Transaction Service → Payment Gateway`

If Payment gateway becomes slow:

Without Bulkhead:

-   All transaction threads blocked
-   System becomes unavailable

With Bulkhead:

-   Only payment calls are blocked
-   Other operations continue working

------------------------------------------------------------------------

## 9. Example Configuration (application.yml)

``` yaml
resilience4j:
  bulkhead:
    instances:
      paymentService:
        maxConcurrentCalls: 20
        maxWaitDuration: 0
```

Explanation:

  | Property     |        Meaning   |
  | --------------- | --------------- |
  | maxConcurrentCalls |  Maximum concurrent calls allowed |
  | maxWaitDuration    |  How long a request waits if limit reached |

------------------------------------------------------------------------

## 10. Using Bulkhead with Other Resilience Patterns

Bulkhead is often combined with other patterns.

Typical flow:

`Client Request -> Bulkhead -> Retry -> TimeLimiter -> Circuit Breaker ->
Service`

Each layer protects the system from a different type of failure.

------------------------------------------------------------------------

## 11. When Bulkhead Is Important

Bulkhead is especially important when:

-   A service calls multiple downstream services
-   External services may become slow
-   Thread pools are limited
-   High traffic systems (banking, fintech, e-commerce)

------------------------------------------------------------------------

## 12. Summary

Bulkhead is a resilience pattern that isolates system resources such as
threads or concurrent calls so that failure or slowness in one
dependency does not affect the entire system.

Key benefit:

Prevent **resource exhaustion** and **cascading failures**.

