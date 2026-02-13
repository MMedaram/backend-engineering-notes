---
title: API Gateway in Microservices
parent: Microservices Architecture
nav_order: 4
---

# API Gateway in Microservices

In a **microservices architecture**, applications are split into many small,
independent services. Each service has its own API, security, scaling rules,
and deployment lifecycle.

An **API Gateway** solves the problems that arise when clients interact with
multiple microservices directly.

> **Core idea:**  
> API Gateway acts as a **single entry point** for all external clients.

---

## Why Do We Need an API Gateway?

Without an API Gateway:
- Clients must call many services directly
- Clients must know internal service URLs
- Security logic gets duplicated
- Changes in backend impact clients

With an API Gateway:
- Clients talk to **one endpoint**
- Backend services remain hidden
- Cross-cutting concerns are centralized

---

## What Is an API Gateway?

> An API Gateway is a server that sits between clients and microservices,
routing requests, enforcing security, and handling cross-cutting concerns.

---

### High-Level Architecture View

```text
Client
|
v
API Gateway
|
+--> Auth / Security
+--> Rate Limiting
+--> Routing
+--> Transformation
|
v
Microservices
```


---

## Key Responsibilities of API Gateway


## 1️⃣ Single Entry Point

### Problem Without Gateway
- Client must call 10–20 different service URLs
- Client tightly coupled to backend structure

### With Gateway

Client  → Gateway → Services


✔ Simplifies client logic  
✔ Reduces coupling  
✔ Backend services can change freely

## 2️⃣ Centralized Authentication & Authorization

### Without Gateway
- Every microservice:
    - Validates tokens
    - Integrates with OAuth / Keycloak
- Leads to duplicated code

### With Gateway
- Authentication done **once**
- Services focus only on business logic

> Gateway acts as the **security guard** at the entrance.

---

## 3️⃣ Rate Limiting & Security Protection

API Gateway protects services from:
- Excessive requests
- DDoS-like traffic
- Malicious clients
- Unauthorized access

Examples:
- Limit requests per IP
- Block suspicious traffic
- Enforce quotas

> Gateway acts as a **shield** in front of microservices.

## 4️⃣ Request & Response Transformation

Different clients may require:
- Different payload sizes
- Different response formats
- Extra headers (trace IDs)

Gateway can:
- Rewrite URLs
- Add/remove headers
- Modify request/response payloads

Without gateway:
- Each service must handle transformations ❌

## 5️⃣ Request Aggregation (Reduce N+1 Calls)

### Example: User Profile Screen

Data needed from:
- User Service
- Order Service
- Notification Service

### Without Gateway

Client → 3 separate calls

### With Gateway

Client → Gateway → Services → Aggregated Response

✔ Fewer network calls   
✔ Faster mobile performance  
✔ Better user experience   

---

## 6️⃣ Hide Internal Network Topology

Internal service details like:
- Ports (8081, 9000)
- Internal DNS
- Deployment structure

❌ Should never be exposed

Gateway exposes:    https://api.mybank.com    


Services remain:
- Private
- Secure
- Isolated

---

## 7️⃣ Observability & Monitoring

API Gateway can add:
- Correlation ID
- Trace ID
- Centralized logs
- Metrics (Prometheus)

This gives:
- End-to-end request visibility
- Easier debugging
- Better monitoring

---

## 8️⃣ API Versioning & Routing Control

Gateway enables:    
- /v1/orders → old service    
- /v2/orders → new service    

Benefits:
- No client changes
- Safe backward compatibility
- Controlled rollouts

Routing is often:
- Service-discovery based
- Config-driven

---

## API Gateway vs Direct Service Access

| Aspect | Direct Access | API Gateway |
|----|----|----|
| Entry point | Many | One |
| Security | Duplicated | Centralized |
| Client complexity | High | Low |
| Observability | Limited | Centralized |
| Versioning | Hard | Easy |

---

## Common API Gateway Implementations

- Spring Cloud Gateway
- Kong
- NGINX
- AWS API Gateway
- Azure API Management

---

## When API Gateway Is a Must

✔ External clients (mobile/web)  
✔ Many backend services  
✔ Need centralized security  
✔ Need observability & control

---

> An API Gateway acts as a single entry point in microservices, handling routing, security, rate limiting, aggregation, and observability while hiding internal service details from clients.

> **API Gateway controls access to microservices, so services can focus purely on business logic.**

