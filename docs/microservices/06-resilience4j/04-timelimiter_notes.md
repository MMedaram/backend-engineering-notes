---
title: TimeLimiter
parent: Resilience4j
nav_order: 4
---

# TimeLimiter (Timeout Pattern)

## 1. Introduction

In microservices architecture, services communicate over the network
(REST APIs, HTTP calls, messaging).

Network calls can sometimes become very slow due to:

- High system load 
- Database delays 
- Network latency 
- External service slowness

If a service waits indefinitely for another service to respond, it may
cause: 
- Thread blocking 
- Resource exhaustion 
- Increased latency

To prevent this, systems use the **Timeout Pattern**, implemented using
**Resilience4j TimeLimiter**.

------------------------------------------------------------------------

## 2. What is TimeLimiter

A **TimeLimiter** sets the maximum time a service call is allowed to
take.

Example:

Allow **3 seconds** for a response.

If the response is not received within 3 seconds, the request fails with
a timeout.

------------------------------------------------------------------------

## 3. Why TimeLimiter is Needed

Without timeout control:

Service A → Service B (slow)

Service A waits indefinitely which can cause thread exhaustion.

With TimeLimiter:

Service A → TimeLimiter → Service B

If Service B does not respond in time, the request fails fast and
fallback logic runs.

------------------------------------------------------------------------

## 4. Where TimeLimiter is Used

 | Scenario                     |                  Example |
 |------------------------------| -------------------- |
 | External APIs                |       Payment gateways |
 | Third‑party integrations     | SMS / Maps APIs |
 | Microservice communication   | REST service calls |
 | Long database queries        | Reporting systems |


------------------------------------------------------------------------

## 5. How TimeLimiter Works

Execution flow:

```
Client Request
↓
TimeLimiter wrapper
↓
Service call starts
↓
Response received within allowed time?

Yes → Return response
No → Timeout triggered
```

------------------------------------------------------------------------

## 6. Dependency

``` xml
<dependency>
 <groupId>io.github.resilience4j</groupId>
 <artifactId>resilience4j-spring-boot3</artifactId>
</dependency>
```

------------------------------------------------------------------------

## 7. Configuration (application.yml)

```yaml
resilience4j:
  timelimiter:
    instances:
      paymentService:
        timeoutDuration: 3s
        cancelRunningFuture: true
```

 | Property        |      Meaning     |
 | --------------  | ----------------- |
 | timeoutDuration |      Maximum execution time |
 | cancelRunningFuture |  Cancel running task when timeout occurs  |


Meaning:

Allow service call to run **3 seconds only**.

------------------------------------------------------------------------

## 8. Example Implementation

``` java
@TimeLimiter(name = "paymentService", fallbackMethod = "fallback")
public CompletableFuture<String> callPaymentService() {

    return CompletableFuture.supplyAsync(() ->
        restTemplate.getForObject(
            "http://payment-service/pay",
            String.class
        )
    );
}
```

Fallback:

``` java
public CompletableFuture<String> fallback(Exception ex) {
    return CompletableFuture.completedFuture(
        "Payment service timed out. Please try later."
    );
}
```

------------------------------------------------------------------------

## 9. Example Scenario

timeoutDuration = 3 seconds

Service response time = 5 seconds

Result:

Timeout occurs after 3 seconds and fallback response is returned.

------------------------------------------------------------------------

## 10. Drawbacks

### Premature failures

Too small timeout may reject valid requests.

### Interrupted operations

If cancelRunningFuture is enabled, tasks may be interrupted.

### User experience impact

Users may see timeout errors.

------------------------------------------------------------------------

## 11. How to Overcome These Issues

1.  Choose timeout based on average service response time
2.  Combine with Retry for transient issues
3.  Combine with Circuit Breaker for repeated failures
4.  Monitor latency metrics

------------------------------------------------------------------------

## 12. Monitoring

Using Spring Boot Actuator.

``` yaml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

Metrics:

> /actuator/metrics/resilience4j.timelimiter.calls

------------------------------------------------------------------------

## 13. Best Practices

1.  Always configure timeout for external service calls
2.  Combine TimeLimiter with Circuit Breaker
3.  Monitor latency before tuning timeout
4.  Provide fallback responses

------------------------------------------------------------------------

## 14. Summary

**TimeLimiter (Timeout Pattern)**

A resilience pattern that limits how long a service call can run. If
execution exceeds the configured duration, the call fails and fallback
logic executes.

Key properties:

-   timeoutDuration
-   cancelRunningFuture

