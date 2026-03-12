---
title: Rate Limiter
parent: Resilience4j
nav_order: 3
---

# Rate Limiter (Spring Boot + Resilience4j)

## 1. Introduction

In microservices architectures, services are exposed through APIs and
accessed by multiple clients such as:

- Web applications
- Mobile apps 
- External services 
- Other internal microservices

Sometimes systems receive too many requests in a short period of time,
which can cause:

- Server overload 
- Increased latency 
- Resource exhaustion 
- Service crashes

To protect systems from excessive traffic, the Rate Limiter Pattern is
used.

--------------------------------

## 2. What is Rate Limiter

A Rate Limiter restricts the number of requests a client can make to a
service within a specified time period.

Example: Allow **10 requests per second**.

If a client sends **15 requests per second**:

- Allow → 10 requests 
- Reject/Delay → 5 requests

-----------------------------------

## 3. Why Rate Limiting is Needed

Without rate limiting, excessive requests can overwhelm a system.

Example: Client → API Gateway → Order Service

If 10,000 requests arrive suddenly: 
- CPU overload 
- Thread exhaustion 
- Database overload 
- System crash


Rate limiting prevents this by **controlling request flow**.

---

## 4. Where Rate Limiting is Used

Rate limiting is commonly used in:

| Scenario | Example |
|--------|--------|
| Public APIs | Limit API usage per client |
| Login endpoints | Prevent brute force attacks |
| Payment APIs | Protect transaction systems |
| Microservice communication | Prevent overload |
| External integrations | Limit third-party API calls |

---

## 5. How Rate Limiter Works

Rate limiter tracks incoming requests and allows only a fixed number during a time window.

Example configuration:

Limit = **5 requests / second**

Requests: 1 → Allowed\
2 → Allowed\
3 → Allowed\
4 → Allowed\
5 → Allowed\
6 → Rejected

The counter resets after the time window.

------------------------------------------------------------------------

## 6. Rate Limiting Algorithms

Different algorithms can implement rate limiting.

### Fixed Window

Requests counted in fixed time window.

Example: 

Window = 1 second           
Limit = 5 requests

Problem:

Requests near window boundaries can exceed limits.

------------------------------------------------------------------------

### Sliding Window

Counts requests over a moving time window for smoother traffic control.

Example:

Last 1 second window
Limit: 5 requests

More accurate and smoother traffic control.

------------------------------------------------------------------------

### Token Bucket

Tokens generated at fixed rate.

Each request consumes one token.

Example: 5 tokens generated per second.

- If token available → request allowed     
- If no token → request rejected

Allows burst traffic.

------------------------------------------------------------------------

### Leaky Bucket

Requests enter a queue and are processed at a constant rate.

Incoming → Queue → Process steadily

Prevents sudden bursts.

------------------------------------------------------------------------

## 7. Rate Limiter in Spring Boot (Resilience4j)

### Step 1 — Add Dependency


``` xml
<dependency>
 <groupId>io.github.resilience4j</groupId>
 <artifactId>resilience4j-spring-boot3</artifactId>
</dependency>
```

### Step 2. Configuration (application.yml)

``` yaml
resilience4j:
  ratelimiter:
    instances:
      paymentService:
        limitForPeriod: 5
        limitRefreshPeriod: 1s
        timeoutDuration: 0
```

| Property           | Meaning                             |
| ------------------ | ----------------------------------- |
| limitForPeriod     | Maximum number of requests allowed  |
| limitRefreshPeriod | Time period for rate limit reset    |
| timeoutDuration    | Time a request waits for permission |


Meaning:

Allow **5 requests per second**.

------------------------------------------------------------------------

## 8. Service Implementation Example

Example service with rate limiter.

``` java
@Service
public class PaymentClient {

    @RateLimiter(name = "paymentService", fallbackMethod = "fallback")
    public String callPaymentService() {

        return restTemplate.getForObject(
                "http://payment-service/pay",
                String.class
        );
    }

    public String fallback(Exception ex) {
        return "Too many requests. Please try later.";
    }
}
```

Explanation:

- @RateLimiter restricts request rate
- name matches configuration
- fallbackMethod handles rejected requests

------------------------------------------------------------------------

## 9. Execution Flow

```
Client Request
      ↓
Rate Limiter
      ↓
Limit available?
      ↓
Yes → Call service
No → Reject / fallback
```

- Request 1 → Allowed
- Request 2 → Allowed
- Request 3 → Allowed
- Request 4 → Allowed
- Request 5 → Allowed
- Request 6 → Rejected

After 1 second:

- Counter resets
- New requests allowed

-----------------------

## 10. Drawbacks

### 1. Request Rejection

Valid requests may be rejected when traffic spikes.

Example:

- User sends legitimate request
- But system limit exceeded
- Request rejected

### 2. Increased Latency

If requests wait for permission:

If timeoutDuration > 0  ;  requests may wait.

### 3. Configuration Complexity

Incorrect limits may block users or overload services.

- Block too many requests
- Allow too much traffic

### User Experience Issues

Users may see HTTP **429 Too Many Requests**.

------------------------------------------------------------------------

## 11. How to Overcome These Issues

1.  Choose proper limits based on system capacity.

Example:

```
limitForPeriod = 100
limitRefreshPeriod = 1s
```

Should match system capacity.

2.  Apply rate limiting at API Gateway.

Instead of applying rate limits in every service, apply them at:

API Gateway

Examples:

- Kong
- NGINX
- Spring Cloud Gateway


3.  Provide graceful fallback messages.

> Too many requests. Please retry after some time.

4.  Use client‑side exponential backoff.
5.  Monitor rate limiter metrics.

------------------------------------------------------------------------

## 12. Monitoring

Using Spring Boot Actuator:

Expose endpoints:

``` yaml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

Metrics:

> /actuator/metrics/resilience4j.ratelimiter.calls

------------------------------------------------------------------------

## 13. Best Practices

1.  Apply limits at API gateway first
2.  Add service-level limits for critical APIs
3.  Monitor traffic patterns
4.  Combine with Circuit Breaker and Retry

Example architecture:

Client → RateLimiter → Retry → CircuitBreaker → Service

------------------------------------------------------------------------

## 14. Summary

Rate Limiter is a resilience pattern used to control how many requests a
service can process within a given time window to protect systems from
overload and ensure fair resource usage.

Key properties:

-   limitForPeriod
-   limitRefreshPeriod
-   timeoutDuration
