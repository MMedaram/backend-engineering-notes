---
title: Microservices Architecture
parent: Backend Engineering Notes
nav_order: 2
---

# Microservices Architecture 

---

## Evolution of Software Architecture

Software architecture has evolved over time to address challenges related to
**scalability, maintainability, deployment, and team autonomy**.

Understanding this evolution helps explain **why microservices exist** and
**when they should be used**.

---

## 1️⃣ Monolithic Architecture

All application components are built, deployed, and run as **a single unit**.

```text
UI + Business Logic + Data Access
↓
Single Application
↓
One Database
```


### Characteristics
- Single codebase
- Single deployment
- Shared database
- Simple to start

### Pros
✔ Easy to develop initially  
✔ Easy to test and deploy  
✔ Simple debugging

### Cons
❌ Hard to scale selectively  
❌ Tight coupling  
❌ Slower deployments as app grows   
❌ One failure can affect entire app    

### Typical Usage
- Small applications   
- Early-stage products    
- Proof of concepts

---

## 2️⃣ Modular Monolithic Architecture

A **monolith with strong internal boundaries**, where code is split into
well-defined modules.

```text
Monolith
├── User Module
├── Order Module
├── Payment Module
```

Still deployed as **one application**, but **designed cleanly**.

### Characteristics
- Single deployment
- Logical separation of modules
- Clear package boundaries
- Shared database

### Pros
✔ Better maintainability   
✔ Easier refactoring    
✔ Good stepping stone to microservices    

### Cons
❌ Still scales as a whole  
❌ One deployment for all modules

### Typical Usage
- Medium-sized systems
- Teams preparing for microservices
- Systems needing strong domain separation     

---

## 3️⃣ Service-Oriented Architecture (SOA)

An architecture where **business capabilities are exposed as services**,
usually communicating via **ESB (Enterprise Service Bus)**.

```text

Service A ─┐
Service B ─┼─ ESB ─→ Consumers
Service C ─┘

```

### Characteristics
- Centralized ESB
- SOAP/XML commonly used
- Heavy governance
- Shared schemas

### Pros
✔ Reuse of services  
✔ Enterprise integration focus

### Cons
❌ ESB becomes bottleneck  
❌ Tight coupling via shared contracts  
❌ Slower evolution  
❌ Complex infrastructure

### Typical Usage
- Legacy enterprise systems
- Large organizations (older designs)

---

## 4️⃣ Microservices Architecture

An architecture where applications are composed of **small, independent services**
that own their data and can be deployed independently.

```text
Service A → DB A
Service B → DB B
Service C → DB C

```

### Characteristics
- Independent deployments
- Decentralized data ownership
- Lightweight communication (REST, events)
- Autonomous teams

### Pros
✔ High scalability   
✔ Faster deployments     
✔ Better fault isolation     
✔ Team autonomy   

### Cons
❌ Higher operational complexity    
❌ Distributed system challenges   
❌ Requires strong DevOps maturity    

### Typical Usage
- Large-scale systems
- High-traffic platforms
- Teams needing independent scaling

---

## 5️⃣ Event-Driven & Cloud-Native Architectures (Modern Trend)

An extension of microservices where:
- Services communicate via events
- Infrastructure is cloud-managed
- Systems are resilient by design

Examples:
- Event-driven microservices
- Serverless
- Cloud-native architectures

### Characteristics
- Asynchronous communication
- Eventual consistency
- High resilience

---

## Architecture Evolution Summary

| Architecture | Deployment | Coupling | Scalability | Complexity |
|------------|-----------|----------|-------------|-----------|
| Monolithic | Single | High | Low | Low |
| Modular Monolith | Single | Medium | Medium | Medium |
| SOA | Multiple | High (ESB) | Medium | High |
| Microservices | Independent | Low | High | High |

---

## Key Insight (Very Important)

> **Microservices are not a replacement for monoliths —  
they are an evolution chosen to solve specific scaling and organizational problems.**

---

## Final Takeaway

> **Start simple.  
Modularize early.  
Adopt microservices only when the system and teams are ready.**
