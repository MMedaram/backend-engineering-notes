---
title: Circuit Breaker
parent: Resilience4j
nav_order: 6
---

# Circuit Breaker (Resilience4j)

## 1. Introduction

In a **microservices architecture**, services communicate with each other through **network calls** (HTTP, REST, messaging).  
Network calls are unreliable compared to local method calls because they can fail due to:

- Network latency
- Service downtime
- Database issues
- High traffic
- Timeout failures

If one service fails and other services keep calling it repeatedly, it can cause **cascading failures** that may bring down the entire system.

To solve this problem, **Resilience Patterns** are used.

One of the most important resilience patterns is the **Circuit Breaker Pattern**.

---

## 2. What is a Circuit Breaker

A **Circuit Breaker** is a design pattern used in distributed systems to **prevent repeated calls to a failing service**.

Instead of continuously calling a failing service, the circuit breaker:

1. Detects failures
2. Stops sending requests temporarily
3. Returns a fallback response
4. Periodically checks if the service has recovered

The pattern is inspired by **electrical circuit breakers**.

If there is an overload, the circuit **opens** to prevent damage.

---

## 3. Why Circuit Breaker is Needed

### Problem Without Circuit Breaker

Example microservices architecture:

> Client → Order Service → Payment Service → Notification Service

If **Payment Service is down**:

- Order Service keeps calling Payment Service
- Requests wait for timeout
- Threads get blocked
- Connection pool gets exhausted
- Entire system slows down or crashes


This situation is called **Cascading Failure**.

---

### Solution With Circuit Breaker

> Client → Order Service → Circuit Breaker → Payment Service

If Payment Service fails repeatedly:

- Circuit Breaker opens
- Requests stop going to Payment Service
- Fallback response returned
- System continues functioning

---

## 4. Circuit Breaker States

Circuit Breaker works with **three states**.

---

### 1. CLOSED State (Normal Operation)

In this state:

- All requests are allowed
- Failures are monitored

`Client → Service A → Service B`

If failure rate exceeds configured threshold:

> CLOSED → OPEN


---

### 2. OPEN State (Failure State)

In this state:

- Requests are blocked immediately
- No calls go to the failing service
- Fallback response is returned

`Client → Service A → Circuit Breaker → Fallback`

After a configured time:

> OPEN → HALF OPEN


---

### 3. HALF OPEN State (Testing State)

In this state:

- Limited number of requests are allowed
- Used to test whether the service has recovered

If requests succeed:

> HALF OPEN → CLOSED

If failures continue:

> HALF OPEN → OPEN

---

## 5. Circuit Breaker Workflow

```
Request Received
↓
Check Circuit Breaker State

CLOSED → Call service
OPEN → Return fallback
HALF OPEN → Allow limited test calls
```

---

## 6. Resilience4j Library

In **Spring Boot applications**, the recommended library for Circuit Breaker is: 

**Resilience4j**


It provides multiple resilience patterns:

| Pattern | Purpose |
|------|------|
| Circuit Breaker | Prevent repeated calls to failing service |
| Retry | Retry failed requests |
| Rate Limiter | Control request rate |
| Bulkhead | Limit concurrent calls |
| Time Limiter | Timeout control |

Older Netflix library:

Hystrix (Deprecated)


---

# 7. Step-by-Step Implementation in Spring Boot

## Step 1 — Add Dependency

Add the Resilience4j Spring Boot starter.

```xml
<dependency>
 <groupId>io.github.resilience4j</groupId>
 <artifactId>resilience4j-spring-boot3</artifactId>
</dependency>
```

This dependency provides:

- Circuit breaker annotations
- Auto configuration
- Integration with Spring Boot

---

## Step 2 - Configuration in application.yml

Circuit breaker configuration is defined under:

resilience4j

**Example:**        

```yaml
resilience4j:
  circuitbreaker:
    instances:
      paymentService:
        failureRateThreshold: 50
        minimumNumberOfCalls: 10
        waitDurationInOpenState: 10s
        permittedNumberOfCallsInHalfOpenState: 3
        slidingWindowSize: 10
        slidingWindowType: COUNT_BASED
```

| Property                              | Description                                            |
| ------------------------------------- | ------------------------------------------------------ |
| failureRateThreshold                  | Percentage of failures to open circuit                 |
| minimumNumberOfCalls                  | Minimum calls required before calculating failure rate |
| waitDurationInOpenState               | Time circuit stays open before moving to HALF OPEN     |
| permittedNumberOfCallsInHalfOpenState | Number of test calls allowed in HALF OPEN              |
| slidingWindowSize                     | Number of calls used to calculate failure rate         |
| slidingWindowType                     | COUNT_BASED or TIME_BASED                              |


---

## Step 3 -  Service Implementation Example

Example service calling another microservice.

```java
@Service
public class PaymentClient {

    @Autowired
    private RestTemplate restTemplate;

    @CircuitBreaker(name = "paymentService", fallbackMethod = "paymentFallback")
    public String callPaymentService() {

        return restTemplate.getForObject(
                "http://payment-service/pay",
                String.class
        );
    }

    public String paymentFallback(Exception ex) {
        return "Payment service currently unavailable";
    }
}
```

Explanation:

- @CircuitBreaker connects the method with configuration
- name should match the configuration instance
- fallbackMethod handles failures


---

## 8. Failure Rate Calculation Example

Configuration:

```
failureRateThreshold = 50%
minimumNumberOfCalls = 10
```

Example call results:

```
Total calls = 10
Failures = 6
```

Failure rate calculation:

> (6 / 10) × 100 = 60%

Since:

> 60% > 50%

Circuit breaker will move to OPEN state.

---

## 9. Monitoring Circuit Breaker

Spring Boot Actuator can expose circuit breaker metrics.

Add dependency:

```xml
 <dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Expose endpoints:

> management.endpoints.web.exposure.include: "*"

Useful endpoints:

- /actuator/circuitbreakers
- /actuator/health
- /actuator/metrics

---

## Best Practices

- Configure timeouts properly
- Always provide fallback methods
- Use separate circuit breakers per downstream service
- Monitor circuit breaker metrics
- Avoid overly aggressive thresholds

Example:

- paymentServiceCircuitBreaker
- notificationServiceCircuitBreaker
- customerServiceCircuitBreaker

