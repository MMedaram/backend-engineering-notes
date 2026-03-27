---
title: Topic
parent: Kafka
nav_order: 6
---

# Topic

A topic is a logical stream of events.

It is similar to: 
- a table (append-only) 
- a category of events

### Example

customer.created.v1\
account.created.v1\
account.transaction.v1

------------------------------------------------------------------------

## Real Microservice Example

Order Service → order.created.v1 topic

Consumers: 
- Payment Service
- Inventory Service
- Notification Service

------------------------------------------------------------------------

## Why Topics Exist

-   organize data
-   separate event types
-   allow multiple consumers

------------------------------------------------------------------------

## How Topic Works Internally

A topic is NOT a single file.

Topic → Partitions

A topic is split into multiple partitions.

------------------------------------------------------------------------

## Kafka CLI --- Create Topic

``` 
kafka-topics.sh
  --create
  --topic order.created.v1
  --partitions 3
  --replication-factor 1
  --bootstrap-server localhost:9092
```

## using Spring boot

```java
package com.bank.customer.config;

import org.apache.kafka.clients.admin.NewTopic;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.config.TopicBuilder;

@Configuration
public class KafkaTopicConfig {

    @Bean
    public NewTopic customerCreatedTopic() {
        return TopicBuilder.name("customer.created.v1")
                .partitions(3)
                .replicas(1)
                .build();
    }

}

```
------------------------------------------------------------------------

## Verify Topic

```
kafka-topics.sh --list --bootstrap-server localhost:9092
```

------------------------------------------------------------------------

## Best Practices

Good: 
- order.created.v1
- payment.completed.v1

Avoid:

- topic1
- test

------------------------------------------------------------------------
