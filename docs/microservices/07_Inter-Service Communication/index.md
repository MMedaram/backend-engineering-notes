---
title: Inter-Service Communication
parent: Microservices
nav_order: 7
---

# Synchronous Inter-Service Communication in Microservices

## What is Synchronous Communication?

Caller waits for response.

```text
Service A
    ↓
Request
    ↓
Service B
    ↓
Response
    ↓
Service A continues
```

## Characteristics:

- ✅ immediate response
- ✅ request-response model
- ✅ easier to understand
- ❌ tight coupling
- ❌ latency propagation
- ❌ cascading failures

-----

## Spring Boot HTTP Communication Options

### Option 1 — RestTemplate

```
restTemplate.getForObject(...)
```

Status:

- Legacy
- Maintenance Mode

Still found in many enterprise systems.

### Option 2 — WebClient

```
webClient.get()
```
Status:
- Current Spring Recommendation

### Advantages:

- non-blocking
- reactive
- modern

### Option 3 — OpenFeign

```
@FeignClient("customer-service")
```

Status:

- Most Popular in Spring Cloud Microservices

#### Advantages:

- declarative
- clean code
- integrates with service discovery

-------

## Typical Enterprise Evolution

Many organizations moved like this:

```
RestTemplate
↓
Feign
↓
Feign + WebClient
```

| Technology       | Modern Usage | Banking Usage             |
|------------------|--------------|---------------------------|
| RestTemplate     | Legacy       | Existing systems          |
| WebClient        | High         | New services              |
| OpenFeign        | Very High    | Most common               |
| gRPC             | Growing      | High-performance services |
| SOAP             | Legacy       | Core banking integration  |
| GraphQL          | Limited      | API aggregation           |
| Direct DB Access | Avoid        | Anti-pattern              |
| Service Mesh     | Advanced     | Large platforms           |

