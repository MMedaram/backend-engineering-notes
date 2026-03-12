---
title: Retry
parent: Resilience4j
nav_order: 2
---

# Retry Pattern (Spring Boot + Resilience4j)

---

## 1. Introduction

In **microservices architecture**, services communicate with other services over the **network (HTTP, REST, messaging)**.

Unlike local method calls, network calls can fail frequently due to temporary issues such as:

- Network glitches
- Temporary service downtime
- Database connection issues
- Load balancer switching nodes
- Short service restarts
- Timeout due to high load

Many of these failures are **transient (temporary)** and can succeed if the request is attempted again.

To handle such failures, distributed systems use the **Retry Pattern**.

---

## 2. What is Retry Pattern

The **Retry Pattern** automatically retries a failed operation **a limited number of times** before returning a failure.

Instead of failing immediately after the first error, the system:

1. Detects the failure
2. Waits for a configured delay
3. Retries the operation
4. Stops retrying after reaching the maximum attempts

Example flow:

> Service A → Service B

**Execution:**

- Attempt 1 → Fail 
- Attempt 2 → Retry
- Attempt 3 → Retry
- Attempt 4 → Success

The client still experiences **one logical request**, but internally the system made **multiple attempts**.

---

## 3. Why Retry is Needed

Temporary failures are common in distributed systems.

Examples:

| Situation | Retry Helps |
|----------|-------------|
| Temporary network glitch | Yes |
| Short database outage | Yes |
| Service restarting | Yes |
| Load balancer switching nodes | Yes |

Example:

**Without retry:**

- Order Service → Payment Service (temporary failure)
- Result → Error returned

**With retry:**        

- Order Service → Retry → Payment Service
- Second attempt succeeds

The user never sees the failure.

---

## 4. Where Retry is Used

Retry is typically used in **external communication or unstable network environments**.

Common use cases:

- External API calls
- Payment gateway requests
- Internal microservice communication
- Database connection retry
- Message processing retry
- Network-dependent operations

Example:

Order Service → Payment Service

If the payment service temporarily fails, retry can automatically recover the request.

---

## 5. When NOT to Use Retry

Retry should **not be used blindly**.

Avoid retry in the following scenarios:

| Scenario | Reason |
|--------|--------|
| Validation errors | Bad input will always fail |
| Permanent failures | Retry will not fix the problem |
| Non-idempotent operations | Can cause duplicate actions |

Example of a dangerous retry scenario:

```
Create Payment            
Retry request         
```

If the first request succeeded but the response was lost:

- Attempt 1 → Payment created
- Attempt 2 → Payment created again

This results in **duplicate payments**.

---

## 6. Retry Terminology

Important retry configuration concepts:

| Term | Meaning |
|------|--------|
| maxAttempts | Maximum number of attempts including the first call |
| waitDuration | Delay between retry attempts |
| retryExceptions | Exceptions that trigger retry |
| ignoreExceptions | Exceptions that should not retry |

---

## 7. Retry Workflow

```text
Client Request
↓
Method Execution
↓
Failure Occurs
↓
Retry Policy Check
↓
Retry Allowed?
↓
Yes → Wait → Retry
No → Return Failure
```


---

## 8. Retry Implementation in Spring Boot

In Spring Boot microservices, retry is commonly implemented using the **Resilience4j Retry module**.

---

## Step 1 — Add Dependency

Add Resilience4j starter dependency.

```xml
<dependency>
 <groupId>io.github.resilience4j</groupId>
 <artifactId>resilience4j-spring-boot3</artifactId>
</dependency>
```

This dependency provides multiple resilience features:

- Retry
- Circuit Breaker
- Bulkhead
- Rate Limiter
- TimeLimiter

## Step 2 - Configuration in application.yml

Retry configuration is defined under:

```yaml
resilience4j:
  retry:
    instances:
      paymentService:
        maxAttempts: 3
        waitDuration: 1s
        retryExceptions:
          - java.io.IOException
        ignoreExceptions:
          - java.lang.IllegalArgumentException
```

This means:

```
Attempt 1 → Initial call
Attempt 2 → Retry
Attempt 3 → Retry
```

| Property         | Description                              |
| ---------------- | ---------------------------------------- |
| maxAttempts      | Total attempts including first call      |
| waitDuration     | Delay between retries                    |
| retryExceptions  | Exceptions that trigger retry            |
| ignoreExceptions | Exceptions that should not trigger retry |

---

## 9. Service Implementation Example

Example service calling another microservice.

```java
@Service
public class PaymentClient {

    @Autowired
    private RestTemplate restTemplate;

    @Retry(name = "paymentService")
    public String callPaymentService() {

        return restTemplate.getForObject(
                "http://payment-service/pay",
                String.class
        );
    }
}
```

**Explanation:**

- @Retry enables retry for this method
- name refers to the retry configuration

---

## 10. Retry with Fallback

Fallback executes if all retries fail.

```java
@Retry(name = "paymentService", fallbackMethod = "fallback")
public String callPaymentService() {

    return restTemplate.getForObject(
            "http://payment-service/pay",
            String.class
    );
}

public String fallback(Exception ex) {
    return "Payment service temporarily unavailable";
}
```

---

## 11. Retry Delay Strategies

Retry delay can follow different patterns.

### 1. Fixed Delay
    
Each retry waits the same duration.

**Example**            

```
Attempt 1
Wait 1s
Attempt 2
Wait 1s
Attempt 3
```

### 2. Exponential Backoff

Delay increases gradually.

Example:

```
Attempt 1
Wait 1s
Attempt 2
Wait 2s
Attempt 3
Wait 4s
```

This prevents system overload.

#### Example configuration:

```yaml
resilience4j:
  retry:
    instances:
      paymentService:
        maxAttempts: 4
        waitDuration: 1s
        enableExponentialBackoff: true
        exponentialBackoffMultiplier: 2
```

---

## 12. Drawbacks / Disadvantages of Retry

Retry improves reliability but also introduces risks.

### 1. Duplicate Operations

Retrying non-idempotent operations can create duplicate transactions.

Example:

```
Create Payment
Retry request
```

Result:

> Payment created twice

### 2. Retry Storm

If many services retry simultaneously, it can cause a traffic explosion.

Example:

```
1000 requests
Each retries 3 times
Total load = 3000 requests
```

This can overload the system.

### 3. Increased Latency

Retries increase response time.

Example:

```
Attempt 1 → Fail
Wait 1s
Attempt 2 → Success
```

Total response time becomes longer.

### 4. Resource Consumption

Multiple retries consume:

- CPU
- Threads
- Network connections

---

## 13. How to Overcome Retry Problems

### 1. Use Idempotent APIs

Operations should produce the same result if executed multiple times.

Example:

> PUT /payment/{id}

Instead of:

> POST /payment


### 2. Use Idempotency Keys

Client sends a unique request ID.

Example:

> Idempotency-Key: 123456

Server ensures the request is processed only once.

### 3. Limit Retry Attempts

Example:

> maxAttempts = 3

Avoid very high retry counts.

### 4. Use Exponential Backoff

Increase delay between retries to avoid traffic spikes.

### 5. Combine Retry with Circuit Breaker

Typical production flow:

```
Retry
↓
Circuit Breaker
↓
Service Call
```

If service keeps failing:

- Circuit Breaker opens
- Retries stop

---

## 14. Monitoring Retry

Retry metrics can be monitored using Spring Boot Actuator.

> /actuator/metrics/resilience4j.retry.calls

---

## Best Practices

- Retry only transient failures
- Use exponential backoff
- Limit retry attempts
- Avoid retry for non-idempotent operations
- Combine retry with circuit breaker
- Monitor retry metrics

