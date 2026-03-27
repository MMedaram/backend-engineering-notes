---
title: Microservices Design Patterns
parent: Microservices
nav_order: 0
---

# Microservices Design Patterns

Microservices design patterns are used to solve distributed system challenges like communication, failures, and data consistency. For example, we use

## 1. API Gateway Pattern

- Single entry point for all client requests.
- Handles routing, authentication, rate limiting.
- Client doesn’t call services directly.

Example: Mobile app → API Gateway → Order Service / User Service

## 2. Service Discovery Pattern

- Services dynamically find each other.
- No hardcoded URLs.

Example: Using Eureka / Consul

Service registers → others discover it

## 3. Database per Service Pattern

- Each microservice owns its own database.
- No shared DB.

Prevents tight coupling and schema conflicts

## 4. Saga Pattern
- Handles distributed transactions across services.
- Instead of rollback (like DB), it uses compensating actions.

### Example:

Order created → Payment failed → Cancel order

Two types:

- Choreography (event-based)
- Orchestration (central controller)

## 5. Circuit Breaker Pattern
- Stops calling a failing service.
- Prevents cascading failures.

States:

Closed → Open → Half-open

## 6. Retry Pattern
- Retry failed requests automatically.
- Useful for temporary failures.

Must be used carefully (can overload system if misused)

## 7. Bulkhead Pattern
- Isolate resources (threads, connections).
- Failure in one part doesn’t affect others.

Like compartments in a ship

## 8. API Composition Pattern
- Combines data from multiple services.
- Done at API Gateway or aggregator service.

Example:

Get User + Orders + Payments → Single response

## 9. CQRS (Command Query Responsibility Segregation)

Separate read and write operations.

> Write → Command model
> Read → Query model

Useful for:

- High performance
- Complex systems

## 10. Event-Driven Architecture Pattern
    
- Services communicate using events (Kafka).

Example:

Order Created → Payment Service listens → processes

## 11. Strangler Fig Pattern
   
Gradually migrate monolith → microservices.

Replace parts step by step (no big bang rewrite)

## 12. External Configuration Pattern
    
- Store configs outside service.

Example:

Config Server, environment variables

## 13. Sidecar Pattern
   
Attach helper service to main service.

### Example:

**Logging, monitoring, security**

## 14. Backend for Frontend (BFF)
    
Separate backend for each frontend.

Example:

- Mobile API (lightweight)
- Web API (detailed)

## 15. Idempotency Pattern
    Same request → same result (no duplicates)

Critical for:

- Payments
- Kafka consumers
