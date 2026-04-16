---
title: application.yaml kafka
parent: Kafka
nav_order: 16
---


```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092

    # -----------------------------
    # PRODUCER CONFIG
    # -----------------------------
    producer:
      acks: all
      retries: 3
      batch-size: 16384
      linger-ms: 10
      buffer-memory: 33554432

      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer

      properties:
        enable.idempotence: true
        max.in.flight.requests.per.connection: 5
        retry.backoff.ms: 1000

    # -----------------------------
    # CONSUMER CONFIG
    # -----------------------------
    consumer:
      group-id: transaction-group
      auto-offset-reset: earliest
      enable-auto-commit: false

      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.JsonDeserializer

      properties:
        spring.json.trusted.packages: "*"

    # -----------------------------
    # LISTENER CONFIG
    # -----------------------------
    listener:
      ack-mode: manual
      concurrency: 3

    # -----------------------------
    # ADMIN (OPTIONAL)
    # -----------------------------
    admin:
      auto-create: false
```



## Why Consumer Retry Is NOT in application.yaml

What consumer does

`Kafka → Consumer → Your business logic`

Failures are:

- DB down
- validation error
- API failure

**👉 These are business/application-level failures**

### Key Difference

Kafka does NOT know:

> Is this error retryable or not?

Example

```
Case 1: DB down → retry needed ✅  
Case 2: Invalid data → retry useless ❌
```

> 👉 Only your code knows this

##### That’s Why We Use @RetryableTopic

**Example**

```
@RetryableTopic(
attempts = "3",
backoff = @Backoff(delay = 5000, multiplier = 2)
)
@KafkaListener(...)

```

| Aspect            | Producer           | Consumer          |
| ----------------- | ------------------ | ----------------- |
| Failure Type      | network/system     | business + system |
| Who decides retry | Kafka client       | your application  |
| Config location   | `application.yaml` | annotation / code |
| Complexity        | simple             | complex           |
