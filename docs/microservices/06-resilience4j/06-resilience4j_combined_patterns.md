---
title: Combining Resilience4j Patterns
parent: Resilience4j
nav_order: 6
---

# Combining Resilience4j Patterns in a Single Service Call

## Overview

In real microservices systems, resilience patterns are rarely used
individually. Typically a **single service call is protected by multiple
patterns together** to handle different failure scenarios.

Commonly combined patterns: 

- Retry 
- TimeLimiter (Timeout) 
- Bulkhead 
- Circuit Breaker

Each pattern protects the system from a **different type of failure**.

------------------------------------------------------------------------

## 1. The Problem in Microservices

Example architecture:

Order Service → Payment Service

Possible issues:

| Problem                      | Example                          |
| ---------------------------- | -------------------------------- |
| Temporary failure            | Network glitch                   |
| Slow response                | Payment service takes 20 seconds |
| Too many concurrent requests | Thread pool exhausted            |
| Service completely down      | Payment service crashed          |

Without protection:
- Threads block 
- Latency increases 
- Cascading failures occur

------------------------------------------------------------------------

# 2. Combined Resilience Flow

Typical production flow:

```
Client Request
      ↓
Bulkhead
      ↓
Retry
      ↓
TimeLimiter
      ↓
Circuit Breaker
      ↓
External Service
      ↓
Fallback
```

Each layer protects the system from a different problem.

------------------------------------------------------------------------

# 3. Role of Each Pattern

| Pattern         | Problem it Solves                       |
| --------------- | --------------------------------------- |
| Bulkhead        | Prevents resource exhaustion            |
| Retry           | Handles temporary failures              |
| TimeLimiter     | Stops long waiting calls                |
| Circuit Breaker | Stops repeated calls to failing service |


------------------------------------------------------------------------

# 4. Step-by-Step Execution

Example service call:

Order Service → Payment Service

### Step 1 --- Bulkhead (Isolation)

Limits number of concurrent requests.

Example:

maxConcurrentCalls = 20

If 100 requests arrive:

-   20 allowed
-   80 rejected immediately

Prevents thread pool exhaustion.

------------------------------------------------------------------------

### Step 2 --- Retry (Temporary Failure Recovery)

Configuration:

maxAttempts = 3

Execution:

```
Attempt 1 → Fail
Attempt 2 → Retry
Attempt 3 → Success
```
Handles transient failures.

------------------------------------------------------------------------

### Step 3 --- TimeLimiter (Timeout Control)

Configuration:

timeout = 3 seconds

Execution:

- Call Payment Service
- Wait 3 seconds

If response not received:

Timeout exception triggered.

Prevents long blocking calls.

------------------------------------------------------------------------

### Step 4 --- Circuit Breaker (Stop Repeated Failures)

Configuration example:

```
failureRateThreshold = 50%
minimumCalls = 10
```

If 6 out of 10 requests fail:

Circuit state → OPEN

New requests:

Return fallback immediately without calling service.

------------------------------------------------------------------------

## 5. Example End-to-End Execution

Configuration:

```
Bulkhead = 20 concurrent calls
Retry = 3 attempts
Timeout = 3 seconds
CircuitBreaker threshold = 50%
```

Execution:

```
Client request arrives
      ↓
Bulkhead allows request
      ↓
Retry attempt 1
      ↓
Payment service slow
      ↓
TimeLimiter timeout after 3s
      ↓
Retry attempt 2
      ↓
Payment service still failing
      ↓
Retry attempt 3
      ↓
Failure recorded
      ↓
CircuitBreaker tracks failures
      ↓
Failure threshold reached
      ↓
CircuitBreaker opens
      ↓
Next requests → fallback immediately
```

------------------------------------------------------------------------

## 6. Combined Spring Boot Annotation Example

``` java
@Bulkhead(name = "paymentService")
@Retry(name = "paymentService")
@TimeLimiter(name = "paymentService")
@CircuitBreaker(name = "paymentService", fallbackMethod = "fallback")
public CompletableFuture<String> callPaymentService() {

    return CompletableFuture.supplyAsync(() ->
        restTemplate.getForObject(
            "http://payment-service/pay",
            String.class
        )
    );
}
```

Fallback method:

``` java
public CompletableFuture<String> fallback(Exception ex) {
    return CompletableFuture.completedFuture(
        "Payment service unavailable"
    );
}
```

------------------------------------------------------------------------

## 7. Combined Configuration Example

``` yaml
resilience4j:

  retry:
    instances:
      paymentService:
        maxAttempts: 3
        waitDuration: 500ms

  timelimiter:
    instances:
      paymentService:
        timeoutDuration: 3s

  bulkhead:
    instances:
      paymentService:
        maxConcurrentCalls: 20

  circuitbreaker:
    instances:
      paymentService:
        failureRateThreshold: 50
        minimumNumberOfCalls: 10
        waitDurationInOpenState: 10s
```

------------------------------------------------------------------------

## 8. Architecture View

```
Client
   ↓
Order Service
   ↓
Bulkhead
   ↓
Retry
   ↓
TimeLimiter
   ↓
Circuit Breaker
   ↓
Payment Service
```

If failure occurs:

Fallback response returned.

------------------------------------------------------------------------

## 9. Why This Combination Is Important

Without resilience:

Slow service → thread blocking → cascading failures → system crash

With resilience:

Slow service → timeout → retry → circuit breaker → fallback

System remains stable.

------------------------------------------------------------------------

## 10. Summary

These patterns together create a **fault-tolerant microservice system**.

-   Retry handles transient failures
-   TimeLimiter stops long waits
-   Bulkhead protects system resources
-   Circuit Breaker prevents repeated failures

Combined usage ensures system stability and reliability.


---

# Note

```
@RateLimiter
@Bulkhead
@Retry
@TimeLimiter
@CircuitBreaker 

service
```

**Retry** repeats the entire protected call, not just the service call.

So the chain from **TimeLimiter → CircuitBreaker → Service** is executed again.

---
