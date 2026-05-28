---
title: Authentication vs Authorization
parent: Security
nav_order: 2
---

# Authentication vs. Authorization

---

## Authentication

Authentication means:

> Proving WHO you are.

Examples:

* username/password
* OTP
* JWT token
* Google login
* fingerprint
* API key

Authentication answers:

```text
"Who are you?"
```

---

## Authorization

Authorization means:

> Determining WHAT you are allowed to do.

Examples:

* admin can delete users
* customers can only view their own orders
* employees can access HR APIs
* service A can call service B

Authorization answers:

```text
"What are you allowed to access or perform?"
```

---

# The Most Important Mental Model

## Authentication comes FIRST

```text
Request
   ↓
Authentication
   ↓
Authorization
   ↓
Business Logic
```

If identity is not known,
authorization cannot even start.

You cannot decide permissions for an unknown user.

---

# Real-Life Analogy

Imagine entering a corporate office.

---

## Authentication

Security guard checks:

* employee ID
* face
* badge

Question:

```text
Who are you?
```

---

## Authorization

Now access rules are checked:

* Can you enter an engineering floor?
* Can you access server room?
* Can you access finance systems?

Question:

```text
What are you allowed to access?
```

---

# Banking Example

Endpoint:

```http
GET /accounts/1001
```

---

## Authentication Check

System validates:

* JWT token
* session
* username/password

Result:

```text
User = john@example.com
```

Identity is now known.

---

## Authorization Check

Now system checks:

```text
Does John own account 1001?
```

OR

```text
Does John have ADMIN role?
```

Only then access is allowed.

---

## Common Beginner Confusion

People often think:

```text
"User logged in successfully"
= fully secure system
```

Wrong.

Authentication alone is NOT enough.

A user may be authenticated but still unauthorized.

---

# Example of Dangerous Mistake

Suppose this API exists:

```java
@GetMapping("/admin/users")
public List<User> getUsers() {
    return userService.findAll();
}
```

If authentication exists but authorization does not:

* any logged-in user can access admin APIs
* severe security vulnerability

This is called:

```text
Broken Access Control
```

One of the most common security vulnerabilities in the world.

---

# Authentication Types in Real Systems

---

# 1. Username + Password

Traditional login.

```text
username + password
   ↓
verify in DB
   ↓
authenticated
```

Still the most common.

---

# 2. Session Authentication

After login:

```text
Server creates session
↓
Browser stores session cookie
↓
Cookie sent every request
```

Used in:

* Spring MVC apps
* traditional web apps

---

# 3. JWT Authentication

After login:

```text
Server generates JWT
↓
Client stores token
↓
Client sends JWT every request
```

Used in:

* microservices
* REST APIs
* mobile apps
* SPAs

---

# 4. OAuth2 / SSO

Login using:

* Google
* GitHub
* Okta
* Azure AD
* Keycloak

User identity comes from an external provider.

---

# 5. API Key Authentication

Used mostly:

* service integrations
* third-party APIs

Example:

```http
X-API-KEY: abc123
```

---

# Authorization Types

---

# 1. Role-Based Access Control (RBAC)

Most common.

Example:

```text
ROLE_ADMIN
ROLE_USER
ROLE_MANAGER
```

Rules:

| Role    | Access        |
| ------- | ------------- |
| ADMIN   | all APIs      |
| USER    | basic APIs    |
| SUPPORT | support tools |

Spring Security heavily uses RBAC.

---

# 2. Permission-Based Authorization

More granular.

Examples:

```text
READ_USER
DELETE_USER
TRANSFER_FUNDS
```

Better for large enterprise systems.

---

# 3. Resource Ownership Authorization

Crucial in real systems.

Example:

```text
User can access ONLY their own orders
```

Not role-based.

Ownership-based.

Example:

```
if(order.getUserId().equals(currentUserId))
```

Common in:

* banking
* ecommerce
* healthcare

---

# 4. Attribute-Based Access Control (ABAC)

Advanced enterprise security.

Decision based on:

* department
* location
* device
* time
* risk score

Example:

```text
HR employees can access payroll
ONLY during office hours
ONLY from corporate network
```

Common in:

* banks
* government
* healthcare

---

# Authentication in Spring Security

Spring Security represents authenticated users using:

```
Authentication
```

Example:

```java
Authentication auth =
 SecurityContextHolder
   .getContext()
   .getAuthentication();
```

Contains:

* principal
* authorities
* credentials
* authenticated status

---

# Authorization in Spring Security

Spring Security checks permissions using:

```
.hasRole("ADMIN")
```

OR

```
.hasAuthority("READ_USER")
```

OR

```
@PreAuthorize("hasRole('ADMIN')")
```

---

# Important HTTP Status Codes

---

# 401 Unauthorized

Actually means:

```text
Not authenticated
```

Examples:

* invalid token
* missing token
* expired session

---

# 403 Forbidden

Means:

```text
Authenticated BUT not allowed
```

Example:

* USER trying admin API

This distinction is VERY important in interviews.

---

# Real Request Flow

Example:

```http
GET /admin/users
Authorization: Bearer xyz
```

---

## Step 1 — Authentication

JWT validated.

User identified as:

```text
john@example.com
ROLE_USER
```

---

## Step 2 — Authorization

Endpoint requires:

```text
ROLE_ADMIN
```

User has only:

```text
ROLE_USER
```

Result:

```http
403 Forbidden
```

Authentication succeeded.
Authorization failed.

---

# Crucial Industry Concept

Authentication and authorization SHOULD be separated.

Why?

Because:

* authentication systems evolve independently
* companies may use external identity providers
* authorization rules become complex
* microservices need centralized identity

Large systems often use:

```text
Authentication → centralized
Authorization → application specific
```

Example:

```text
Okta authenticates user
↓
Application decides permissions
```

---

# Authentication vs. Authorization in Microservices

---

# Authentication

Usually done by:

* API Gateway
* Identity Provider
* OAuth2 server

---

# Authorization

Usually done:

* inside each service
* based on roles/permissions

Why?

Because each service owns business rules.

Example:

```text
Order Service decides:
who can cancel orders
```

---

# Common Security Mistakes

---

# Mistake 1

Only authentication, no authorization.

Very dangerous.

---

# Mistake 2

Checking roles only at UI layer.

Backend must ALWAYS enforce security.

Never trust frontend.

---

# Mistake 3

Using only roles for everything.

Large systems need:

* permissions
* ownership checks
* ABAC

---

# Mistake 4

Hardcoding admin checks everywhere.

Bad:

```
if(user.equals("admin"))
```

Use centralized authorization.

---

# Mistake 5

Returning wrong status codes.

* 401 → authentication problem
* 403 → authorization problem

---

# Real Enterprise Flow

Modern enterprise systems usually work like this:

```text
User
 ↓
Identity Provider (Okta/Auth0/Keycloak)
 ↓
JWT Access Token
 ↓
API Gateway
 ↓
Microservices
 ↓
Authorization Rules
```

Authentication and authorization are distributed responsibilities.

---

# Senior Engineer Thinking

A senior engineer thinks:

```text
How do we identify users?
How do we manage permissions?
How do services trust each other?
How do we revoke access?
How do we prevent privilege escalation?
How do we audit sensitive operations?
```

Not just:

```text
How do I add login?
```

---

# The Most Important Takeaway

Authentication and authorization are DIFFERENT.

---

## Authentication

```text
WHO are you?
```

Verifies identity.

---

## Authorization

```text
WHAT are you allowed to do?
```

Controls permissions.

---

# Final Mental Model

```text
Authentication
   ↓
Identity established
   ↓
Authorization
   ↓
Permission decision
   ↓
Access granted or denied
```

Everything in Spring Security builds on top of this distinction.
