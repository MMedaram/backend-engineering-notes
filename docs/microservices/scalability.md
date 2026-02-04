---
title: Scalability – Vertical vs Horizontal
parent: Microservices Architecture
nav_order: 2
---

# Scalability in Distributed Systems

**Scalability** is the ability of an application to **handle increased load**
(more users, more requests, more data) **without sacrificing performance**.

> Example:  
> If a banking system suddenly gets **10× more customers**,  
> the system should still respond reliably and within acceptable time.

---

## Why Scalability Matters

Without proper scalability:
- Response time increases
- System becomes unstable
- Failures cascade
- Customer experience degrades

In modern systems, scalability is **not optional** — it is a design requirement.

---

## Two Main Types of Scalability

Scalability is broadly classified into:

1. **Vertical Scaling (Scale Up)**
2. **Horizontal Scaling (Scale Out)** 

---

## 1️⃣ Vertical Scaling (Scale Up)


Vertical scaling means **increasing the capacity of a single machine**.

You scale by:
- Adding more CPU cores
- Increasing RAM
- Using faster disks

> No new machines are added — the same machine becomes stronger.


### Example

Suppose your **AccountService** runs on a single Tomcat server.

When load increases, you upgrade the VM:

- CPU: `2 cores → 8 cores`
- RAM: `8 GB → 32 GB`

The application remains the same, only the hardware changes.


### Pros of Vertical Scaling

✅ Simple to implement  
✅ No code changes required  
✅ Works well for small or early-stage systems


### Cons of Vertical Scaling

❌ Hardware upgrades are expensive  
❌ Physical limits (CPU/RAM have a maximum)  
❌ Downtime during upgrade  
❌ **Single point of failure**  
❌ Not suitable for large-scale systems

> If that one server crashes, the entire service is down.

---

## 2️⃣ Horizontal Scaling (Scale Out)


Horizontal scaling means **adding more instances of the application**
and distributing traffic among them using a **Load Balancer**.

Instead of making one machine stronger, you **add more machines**.


### Example

Instead of one AccountService instance:

```
AccountService-1
AccountService-2
AccountService-3
```


A load balancer distributes requests across these instances.

All instances:
- Connect to the same database  
  **OR**
- Use distributed storage (recommended for scale)

### Pros of Horizontal Scaling

✅ Handles very high traffic  
✅ High availability  
✅ Fault tolerance (one instance failure doesn’t stop service)  
✅ Easy to scale in cloud & containers  
✅ Supports auto-scaling


### Cons of Horizontal Scaling

❌ More complex architecture  
❌ Requires load balancing  
❌ Services must be **stateless**  
❌ Session management must be external (Redis, DB)

---

## Statelessness Requirement

For horizontal scaling to work:
- Services must **not store session/state in memory**
- State must be stored in:
    - Database
    - Cache (Redis)
    - Token (JWT)

This is a **core microservices principle**.

---

## Vertical vs Horizontal Scaling

| Aspect | Vertical Scaling | Horizontal Scaling |
|----|----|----|
| Scaling method | Bigger machine | More machines |
| Complexity | Low | High |
| Cost | High (hardware) | Optimized (cloud) |
| Availability | Low | High |
| Fault tolerance | ❌ No | ✅ Yes |
| Cloud friendly | ❌ Limited | ✅ Yes |
| Microservices fit | ❌ Poor | ✅ Excellent |

---

## Which Scaling Is Used in Microservices?

> **Microservices primarily rely on horizontal scaling.**

Vertical scaling may still be used:
- At database level (temporarily)
- For legacy systems
- For quick short-term fixes

But long-term scalable systems:       
✔ Prefer **horizontal scaling**

---


## Summary

- Scalability ensures system reliability under load
- Vertical scaling is simple but limited
- Horizontal scaling is complex but powerful
- Microservices are designed for horizontal scaling
- Stateless design is mandatory for scale-out

