---
title: Security Filter Chain
parent: Security
nav_order: 3
---

# Spring Security Filter Chain

If you deeply understand the Security Filter Chain,
you understand the heart of Spring Security.

Almost everything in Spring Security revolves around:

```text
Servlet Filters
```

This is THE core architecture.

---

# The Big Idea

Spring Security works by intercepting requests BEFORE they reach your controllers.

It does this using a chain of servlet filters.

---

# High-Level Request Flow

```text
Client Request
      ↓
Tomcat / Servlet Container
      ↓
Spring Security Filter Chain
      ↓
Authentication Filters
      ↓
Authorization Filters
      ↓
Controller
      ↓
Response
```

Your controller is NOT the first thing handling requests.

Security executes before business logic.

This is extremely important.

---

# What Is a Servlet Filter?

A servlet filter is a component that can:

* inspect requests
* modify requests
* block requests
* validate requests
* inspect responses

before the request reaches the controller.

---

# Simple Filter Analogy

Imagine airport security.

```text
Passenger
  ↓
Passport Check
  ↓
Security Scan
  ↓
Boarding Verification
  ↓
Flight Access
```

Each checkpoint performs ONE responsibility.

Spring Security works exactly like this.

Each filter performs:

* one validation
* one security responsibility

---

# Why Spring Security Uses Filters

Because filters run:

* before controllers
* for every request
* at framework level
* independently of business logic

Perfect place for:

* JWT validation
* session validation
* role checks
* CSRF protection
* exception handling
* logging
* authentication

---

# Core Internal Architecture

Spring Security internally creates: **FilterChainProxy**

This manages multiple security filters.

Internally:

```text
DelegatingFilterProxy
       ↓
FilterChainProxy
       ↓
Security Filters
```

This is one of the most asked senior interview topics.

---

# Important Internal Components

---

# 1. DelegatingFilterProxy

Bridge between:

* Servlet container
* Spring-managed beans

Tomcat knows servlet filters.
Spring knows Spring beans.

`DelegatingFilterProxy` connects both worlds.

---

# 2. FilterChainProxy

Actual Spring Security filter manager.

It:

* stores filters
* executes filters
* manages filter order

This is the real security engine.

---

# 3. SecurityFilterChain

Defines:

* which filters apply?
* to which requests

Example:

```
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http)
```

This is a modern Spring Security configuration.

---

# Real Request Flow Internally

Suppose request arrives:

```http
GET /api/orders
Authorization: Bearer xyz
```

Internally:

```text
Request
  ↓
DelegatingFilterProxy
  ↓
FilterChainProxy
  ↓
JWT Filter
  ↓
Authentication Filter
  ↓
Authorization Filter
  ↓
Controller
```

If any filter fails, the request stops immediately.

---

# Important Principle

Filters can:

```text 
ALLOW
DENY
MODIFY
REDIRECT
```

before controller execution.

---

# Common Built-In Security Filters

Spring Security contains MANY filters.

Senior developers should know major ones.

---

# 1. UsernamePasswordAuthenticationFilter

Handles form login authentication.

Example:

```http
POST /login
username=admin
password=123
```

This filter:

* extracts credentials
* validates user
* create an Authentication object

---

# 2. BasicAuthenticationFilter

Handles HTTP Basic Auth.

Example:

```http
Authorization: Basic base64(username:password)
```

Mostly used:

* internal APIs
* testing
* legacy integrations

---

# 3. BearerTokenAuthenticationFilter

Handles JWT Bearer tokens.

Example:

```http
Authorization: Bearer eyJhb...
```

Crucial in microservices.

---

# 4. SecurityContextHolderFilter

Loads/stores security context.

This makes current user available globally.

---

# 5. CsrfFilter

Protects against CSRF(Cross-Site Request Forgery) attacks.

Important for browser-based apps.

---

# 6. ExceptionTranslationFilter

Converts security exceptions into:

* 401
* 403

Without exposing internals.

---

# 7. AuthorizationFilter

Check whether the user has required permissions.

This is where access control happens.

---

# Visualizing Filter Execution

Imagine:

```http
GET /admin/users
Authorization: Bearer xyz
```

---

# Step 1 — JWT Filter

Validates token.

If invalid:

```http
401 Unauthorized
```

Controller never executes.

---

# Step 2 — Authentication Stored

Authenticated user stored in:

``` 
SecurityContextHolder
```

---

# Step 3 — Authorization Filter

Checks:

```text 
Does user have ROLE_ADMIN?
```

If not:

```http 
403 Forbidden
```

---

# Step 4 — Controller Executes

Only now:

```
@GetMapping("/admin/users")
```

gets executed.

---

# MOST IMPORTANT SECURITY PRINCIPLE

```text
Controller should execute ONLY after security succeeds.
```

That is the entire purpose of the filter chain.

---

# SecurityContextHolder (Critical Concept)

Once authentication succeeds:

```
SecurityContextHolder
    .getContext()
    .setAuthentication(authentication);
```

Current user becomes globally available.

Anywhere in application:

```java
Authentication auth = SecurityContextHolder.getContext().getAuthentication();
```

This is how Spring knows:

* current user
* current roles
* current permissions

---

# Important Filter Ordering

Filter order is CRITICAL.

Wrong order breaks security.

Example:

```text
JWT validation
MUST happen BEFORE authorization
```

Otherwise:

* authorization has no authenticated user

---

# Example Order

Simplified:

```text
SecurityContextHolderFilter
        ↓
JWT Filter
        ↓
Authentication Filter
        ↓
Authorization Filter
        ↓
Controller
```

---

# Custom JWT Filter Example

In real systems, we often create custom filters.

Example:

```java
public class JwtAuthenticationFilter
    extends OncePerRequestFilter {}
```

Why?

Because:

* validate JWT
* extract claims
* load user
* create an Authentication object

before controller executes.

---

# Why OncePerRequestFilter?

Ensures filter executes:

* exactly once per request

Very commonly used in JWT systems.

---

# Example JWT Filter Logic

Typical flow:

```text
Extract JWT
    ↓
Validate Signature
    ↓
Check Expiration
    ↓
Extract Username
    ↓
Load User
    ↓
Create Authentication Object
    ↓
Store in SecurityContextHolder
```

This is a real enterprise flow.

---

# Important Industry Practice

Authentication should happen:

* in filters
* before business logic
* centrally

NOT inside controllers.

BAD:

```
if(tokenValid){
   ...
}
```

inside every endpoint.

Spring Security prevents this duplication.

---

# Multiple Security Filter Chains

Advanced but VERY important.

Spring Security can have:

* different security for different endpoints

Example:

```text 
/api/** → JWT auth
/admin/** → OAuth2 login
/public/** → no auth
```

Each can use different filters.

Very common in enterprise systems.

---

# Stateless vs Stateful Filters

---

# Stateful Security

Uses:

* sessions
* cookies

Filter loads user from session.

Example:

```text 
JSESSIONID
```

---

# Stateless Security

Uses:

* JWT
* no session

Every request is independently authenticated.

Most modern APIs use this.

---

# Exception Handling in Filter Chain

Suppose JWT expired.

JWT filter throws exception.

`ExceptionTranslationFilter` converts it into:

```http 
401 Unauthorized
```

without crashing application.

This is why centralized security is powerful.

---

# Common Beginner Mistakes

---

# Mistake 1

Trying to secure endpoints manually.

BAD:

```
if(user == null)
```

inside every controller.

---

# Mistake 2

Not understanding filter order.

Wrong ordering causes:

* authentication failures
* authorization failures
* SecurityContext issues

---

# Mistake 3

Doing DB lookups repeatedly in filters.

It can hurt performance heavily.

---

# Mistake 4

Storing request-specific data in singleton beans.

Security data belongs in:

* SecurityContext
* request scope
* thread-local storage

---

# Mistake 5

Not clearing SecurityContext properly.

It can cause thread leakage issues.

---

# Real Enterprise Security Flow

Modern systems usually work like this:

```text
Client
   ↓
API Gateway
   ↓
JWT Validation Filter
   ↓
SecurityContext Creation
   ↓
Authorization Filter
   ↓
Business APIs
```

Sometimes:

* gateway validates token
* microservice validates again

Defense-in-depth.

---

# Senior-Level Understanding

Senior engineers understand:

* filter ordering
* authentication flow
* SecurityContext lifecycle
* stateless security
* exception handling
* request interception
* performance implications

Not just annotations.

---

# Most Important Interview Question

## "How does Spring Security work internally?"

Strong answer:

```text
Spring Security works using a chain of servlet filters.
Incoming requests pass through authentication and authorization filters before reaching controllers.
Filters validate tokens/sessions, create Authentication objects, store them in SecurityContextHolder, and enforce access rules.
```

This is the CORE architecture.

---

# Final Mental Model

```text
Incoming Request
        ↓
Security Filters
        ↓
Authentication
        ↓
SecurityContext
        ↓
Authorization
        ↓
Controller
        ↓
Response
```

If you deeply understand this flow,
everything else in Spring Security becomes easier.
