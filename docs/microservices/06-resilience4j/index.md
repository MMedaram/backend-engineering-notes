---
title: Resilience4j
parent: Microservices
nav_order: 6
---

##  Resilience4j Patterns 

- Retry
    - Retries failed operations for temporary failures.
    - Useful for network glitches or transient errors.

- RateLimiter
  - Controls number of requests allowed per time period.
  - Protects services from traffic overload.

- TimeLimiter

    - Limits maximum execution time of a call.
    - Prevents threads from waiting too long.

- Bulkhead

    - Limits concurrent calls or threads.
    - Prevents resource exhaustion caused by slow dependencies.

- CircuitBreaker

    - Stops calling a service if failure rate becomes too high.
    - Prevents repeated calls to failing services.

