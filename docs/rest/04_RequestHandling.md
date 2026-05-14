---
title: Request Handling
parent: REST
nav_order: 4
---

# Request Handling in Spring Boot REST APIs 

# 1. What is Request Handling?

Request handling is the process of:

```text
HTTP Request
    ↓
Routing
    ↓
Parameter Extraction
    ↓
Deserialization
    ↓
Validation
    ↓
Transformation
    ↓
Business Layer Invocation
```

In Spring Boot this is primarily managed by:

* DispatcherServlet
* HandlerMapping
* HandlerAdapter
* HttpMessageConverters
* Jackson
* Bean Validation

---

# 2. Complete Spring Request Lifecycle

This is extremely important.

```text
Client Request
    ↓
Tomcat / Netty
    ↓
DispatcherServlet
    ↓
Handler Mapping
    ↓
Controller Method Resolution
    ↓
Argument Resolution
    ↓
JSON Deserialization
    ↓
Validation
    ↓
Controller Method Execution
```

---

# 3. Request Types in REST APIs

Industry APIs commonly handle:

| Request Type         | Usage                   |
|----------------------|-------------------------|
| **Path Variables**   | Resource identification |
| **Request Params**   | Filtering/pagination    |
| **Request Body**     | Complex input           |
| **Headers**          | Auth/tracing/versioning |
| **Multipart**        | File uploads            |
| **Form Data**        | Legacy integrations     |
| **Matrix Variables** | Rare                    |
| **Cookies**          | Sessions/legacy         |

---

# 4. Path Variables (Resource Identification)

## Basic Usage

```java
@GetMapping("/users/{id}")
public UserResponse getUser(
        @PathVariable Long id) {
    return service.getUser(id);
}
```

## Multiple Path Variables

```
@GetMapping("/users/{userId}/orders/{orderId}")
```

Industry usage:

* Hierarchical resource modeling

## Regex Path Variables

Rare but useful.

```
@GetMapping("/{id:[0-9]+}")
```

## UUID Path Variables

Very common in microservices.

```java
@GetMapping("/{id}")
public UserResponse get(@PathVariable UUID id){}
```

## Industry Insight

### Use path variables for:

✅ Resource identity

### Avoid:

❌ Filters/search criteria

Bad:

```http
/users/active
```

Better:

```http
/users?status=ACTIVE
```

---

# 5. Query Parameters (Filtering/Search/Pagination)

## Basic Usage

```java
@GetMapping
public List<UserResponse> getUsers(@RequestParam String status){}
```

## Optional Params

```
@RequestParam(required = false)
```

## Default Values

```
@RequestParam(defaultValue = "0")
```

## Industry Standard Usage

| Purpose    | Example           |
| ---------- | ----------------- |
| Pagination | `?page=0&size=20` |
| Filtering  | `?status=ACTIVE`  |
| Sorting    | `?sort=name,asc`  |
| Search     | `?keyword=mohan`  |

## Advanced Query Param Mapping

### Binding to Object

Very common in enterprise APIs.

```java
public class UserSearchRequest {

    private String status;
    private Integer page;
    private Integer size;
}
```

```java
@GetMapping
public List<UserResponse> search(UserSearchRequest request){}
```

Spring auto-binds query params.

## Why This Matters

Cleaner controllers:

```
search(UserSearchRequest request)
```

instead of:

```
search(String status, Integer page, ...)
```

---

# 6. Request Body Handling

# What Happens Internally?

```text
JSON Payload
    ↓
HttpMessageConverter
    ↓
Jackson ObjectMapper
    ↓
Java DTO
```

## Basic Example

```java
@PostMapping
public UserResponse create(@RequestBody CreateUserRequest request){}
```

---

# 7. DTO Design (Production Standards)

## ❌ NEVER Expose Entities

Bad:

```java
public User create(@RequestBody User user){}
```

Why dangerous?

* exposes DB structure
* password leakage
* lazy loading issues
* tight coupling
* future schema breakage

## ✅ Industry Standard

```java
public class CreateUserRequest {

    private String name;
    private String email;
}
```

---

# 8. Immutable DTOs (Modern Best Practice)

Very common now.

## Java Record DTOs

```java
public record CreateUserRequest(
        String name,
        String email
) {}
```

Benefits:
- ✅ immutable
- ✅ thread-safe
- ✅ concise
- ✅ safer

---

# 9. Validation (Critical)

Never trust incoming data.

## Bean Validation

```java
public class CreateUserRequest {

    @NotBlank
    private String name;

    @Email
    private String email;

    @Min(18)
    private Integer age;
}
```

## Validation Flow

```text
Request
    ↓
Deserialization
    ↓
Bean Validation
    ↓
Controller Method
```

## Nested Validation

Very important.

```java
public class OrderRequest {

    @Valid
    private AddressRequest address;
}
```

Without `@Valid`:

* nested validation DOES NOT execute

## Validation Groups (Advanced Enterprise Usage)

Used in:

* create vs update APIs
* partial validation

Example:

```java
interface Create {}
interface Update {}
```

```
@NotNull(groups = Update.class)
```

---

# 10. PATCH Request Handling

One of the hardest real-world topics.

# Problem

PATCH means:

* Partial update

Need to distinguish:

* field absent
* field explicitly null

## Common Industry Approaches

### Approach 1 — Nullable DTO Fields

```java
public class UpdateUserRequest {

    private String name;
}
```

Problem:

* Cannot distinguish absent vs null

### Approach 2 — JsonNullable (Better)

Very common in enterprise systems.

```java
JsonNullable<String> name;
```

### Approach 3 — JSON Patch RFC6902

Advanced but powerful.

```json
[
  { "op": "replace", "path": "/name", "value": "Mohan" }
]
```

Used in:

* Kubernetes
* enterprise APIs

---

# 11. Multipart Requests

Common for:

* profile images
* document uploads

## File Upload

```java
@PostMapping("/upload")
public ResponseEntity<?> upload(@RequestParam MultipartFile file){}
```

## Multipart + JSON Together

Real-world usage.

```
@PostMapping(consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
```

---

# 12. Header Handling

Very important in microservices.

## Reading Headers

```
@RequestHeader("Authorization")
```

## Common Industry Headers

| Header           | Purpose       |
| ---------------- | ------------- |
| Authorization    | JWT/Auth      |
| X-Correlation-ID | tracing       |
| X-Tenant-ID      | multi-tenancy |
| Accept-Language  | localization  |
| Idempotency-Key  | retry safety  |

---

# 13. Idempotency-Key Handling

Critical in payment/order systems.

# Problem

Client retries:

* POST /payments

Can create duplicates.

# Solution

```http
Idempotency-Key: abc-123
```

Server stores processed requests.

Used by:

* Stripe
* PayPal
* Banking APIs

---

# 14. Custom Argument Resolvers (Advanced)

Huge enterprise feature.

# Example

Instead of:

```
@RequestHeader("X-User-Id")
```

You inject:

```
CurrentUser currentUser
```

via:

* HandlerMethodArgumentResolver

Used heavily in:

* internal platforms
* security frameworks

---

# 15. Request Logging

Very important for production debugging.

# Best Practices

Log:
- ✅ request ID
- ✅ endpoint
- ✅ latency
- ✅ correlation ID

Avoid logging:
- ❌ passwords
- ❌ tokens
- ❌ PII

---

# 16. Request Size & Payload Concerns

Large payloads can:

* increase GC pressure
* slow deserialization
* exhaust memory

# Best Practices

- ✅ pagination
- ✅ streaming
- ✅ compression
- ✅ upload limits

---

# 17. Content-Type Handling

Very important.

# JSON APIs

```http
Content-Type: application/json
```

# Multipart

```http
multipart/form-data
```

# Common Error

* 415 Unsupported Media Type

---

# 18. Deserialization Edge Cases

# Unknown Fields

Default:

* ignored

Can enforce strict mode.

# Enum Failures

```
"status": "INVALID"
```

can break deserialization.

# Null Handling

Need careful handling:

* primitives vs wrappers
* default values

---

# 19. Security Considerations

# Mass Assignment Vulnerability

Dangerous:

```
User entity exposed directly
```

Attacker sends:

```json
{
  "role": "ADMIN"
}
```

# Always Use DTOs

This is one major reason.

---

# 20. API Versioning & Request Compatibility

Never break old clients.

# Safe Changes

✅ adding optional fields

# Dangerous Changes

- ❌ renaming fields
- ❌ removing fields
- ❌ changing enum values

---

# 21. Industry-Grade Controller Pattern

# Recommended Flow

```text
Controller
    ↓
Request DTO
    ↓
Validation
    ↓
Mapper
    ↓
Service
```

---

# 22. Common Anti-Patterns

## ❌ Fat Controllers

Business logic inside controller.

## ❌ Entity Exposure

Huge long-term problem.

## ❌ No Validation

Security & data integrity issues.

## ❌ Overloaded Request DTOs

One DTO for all operations.

## ❌ Massive Nested Payloads

Hard to maintain.

---

# 23. Senior Engineering Insights

# Request Handling is API Design

Not just:

```
@RequestBody
```

It affects:

* security
* scalability
* backward compatibility
* client integrations
* maintainability

# Good APIs Optimize For

- ✅ clarity
- ✅ stability
- ✅ predictability
- ✅ evolvability

---

# 24. Real Enterprise Recommendations

| Concern       | Recommended        |
| ------------- | ------------------ |
| DTOs          | records/classes    |
| Validation    | Bean Validation    |
| Mapping       | MapStruct          |
| Serialization | Jackson            |
| API Docs      | OpenAPI            |
| Logging       | structured logging |
| Tracing       | correlation IDs    |

---

# 25. Summary

Request handling includes:

* parameter extraction
* deserialization
* validation
* security
* transformation
* compatibility

Senior backend engineering starts when you design these flows intentionally.
