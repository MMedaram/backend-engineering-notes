---
title: REST Basics
parent: REST
nav_order: 1
---

# REST Basics

## 1. What is REST?

REST (Representational State Transfer) is an **architectural style** for designing distributed systems, especially web APIs.

It is **not a protocol** like HTTP, but it is commonly implemented using HTTP.

---

## 2. Core Idea

Everything is treated as a **resource**, and resources are accessed using **URIs (URLs)**.

Example:

* `/users`
* `/users/123`
* `/users/123/orders`

---

## 3. REST Constraints

A system is considered RESTful only if it follows these constraints:

### 3.1 Client-Server

* Client and server are independent
* UI and backend evolve separately

### 3.2 Stateless

* Each request contains all required information
* Server does NOT store client session

👉 Implication:

* Use tokens (JWT) instead of server sessions

---

### 3.3 Uniform Interface

This is the **core of REST**:

#### a. Resource Identification

* Each resource has a unique URI

#### b. Resource Representation

* JSON / XML representation of resource

#### c. Self-descriptive Messages

* Headers describe how to process request/response

#### d. HATEOAS (optional in practice)

* Response contains links to related actions

---

### 3.4 Cacheable

* Responses should define cacheability
* Improves performance

---

### 3.5 Layered System

* Client doesn’t know if it’s connected directly to server or via gateway/proxy

---

## 4. Resource Modeling 

### ❌ Bad Design

```
/getUser
/createOrder
/deleteProduct
```

### ✅ Good REST Design

```
GET    /users/{id}
POST   /orders
DELETE /products/{id}
```

👉 Use **nouns**, not verbs.

---

## 5. HTTP Methods & Semantics

| Method | Purpose        | Idempotent | Safe  |
| ------ | -------------- | ---------- | ----- |
| GET    | Read data      | ✅ Yes      | ✅ Yes |
| POST   | Create         | ❌ No       | ❌ No  |
| PUT    | Replace        | ✅ Yes      | ❌ No  |
| PATCH  | Partial update | ❌ No       | ❌ No  |
| DELETE | Remove         | ✅ Yes      | ❌ No  |

---

## 6. Idempotency

An operation is **idempotent** if multiple identical requests have the same effect.

### Example:

```
DELETE /users/123
```

* First call → deletes user
* Second call → still deleted (same result)

---

## 7. Safe Operations

Safe operations do NOT modify server state.

* GET is safe
* POST/PUT/DELETE are NOT safe

---

## 8. Status Codes (Conceptual)

Use HTTP status codes correctly:

* 2xx → Success
* 4xx → Client error
* 5xx → Server error

Example:

* `200 OK`
* `201 Created`
* `404 Not Found`
* `500 Internal Server Error`

---

## 9. Content Negotiation

Client specifies expected format using headers:

```
Accept: application/json
```

Server responds accordingly.

---

## 10. Why REST Matters 

* Standardized communication
* Scalability (stateless)
* Works well with microservices
* Easy integration across systems

---

## 11. Common Mistakes

❌ Using verbs in URLs     
❌ Ignoring HTTP semantics         
❌ Returning 200 for everything           
❌ Not handling idempotency         
❌ Mixing business logic in controllers      

---

## 12. Design Insights 

* REST is about **modeling domain as resources**, not endpoints
* Think in terms of **state transitions**
* API design matters more than implementation

---

## 13. Summary

* REST = architectural style
* Resource-based design
* Stateless communication
* Strong reliance on HTTP semantics
* Proper API design is critical for scalability

---

