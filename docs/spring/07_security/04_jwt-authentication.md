---
title: JWT Authentication
parent: Security
nav_order: 4
---

# JWT Authentication

JWT Authentication is one of the most important concepts in modern backend systems.

Especially in:

* Microservices
* REST APIs
* Cloud-native systems
* Mobile applications
* Distributed architectures

Most modern APIs today use JWT-based authentication.

---

# Core Idea

Instead of storing user sessions on the server, the server issues a signed token to the client.

The client sends this token on every request.

Server verifies the token before allowing access.

---

# High-Level JWT Flow

```text
User Login
    ↓
Server Validates Credentials
    ↓
Server Generates JWT
    ↓
Client Stores JWT
    ↓
Client Sends JWT on Every Request
    ↓
Server Validates JWT
    ↓
Access Granted
```

---

# Why JWT Became Popular

Traditional session-based systems require:

```text
Server-side session storage
```

JWT enables:

```text
Stateless Authentication
```

Very useful for:

* scalable systems
* distributed systems
* microservices
* cloud deployments

---

# Traditional Session Authentication

Old-style systems work like this:

```text
Login
   ↓
Server Creates Session
   ↓
Session ID Stored in Cookie
   ↓
Server Looks Up Session on Every Request
```

Requires:

* server memory
* session replication
* sticky sessions

Harder to scale.

---

# JWT Authentication

JWT systems work differently.

```text
Login
   ↓
Server Generates JWT
   ↓
Client Stores JWT
   ↓
Client Sends JWT
   ↓
Server Verifies Signature
```

No session lookup required.

---

# JWT Structure

A JWT contains 3 parts:

```text
HEADER.PAYLOAD.SIGNATURE
```

Example:

```text
eyJhbGciOiJIUzI1Ni...
```

---

# Visual Structure

```text
HEADER
   ↓
PAYLOAD
   ↓
SIGNATURE
```

Separated using dots:

```text
aaa.bbb.ccc
```

---

# 1. Header

Contains metadata about token.

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Meaning:

* algorithm used
* token type

---

# 2. Payload

Contains claims/data.

Example:

```json
{
  "sub": "mohan",
  "role": "ADMIN"
}
```

This contains identity information.

---

# Important JWT Principle

Payload is:

```text
ENCODED
NOT ENCRYPTED
```

Anyone can decode JWT payload.

NEVER store:

* passwords
* secrets
* sensitive personal data

inside JWT.

---

# 3. Signature

Most important security part.

Server creates signature using:

```text
Header + Payload + Secret Key
```

Example:

```text
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

Signature prevents token tampering.

---

# MOST IMPORTANT JWT SECURITY PRINCIPLE

If payload changes:

```text
Signature becomes invalid
```

This is how tampering is detected.

---

# Complete JWT Authentication Flow

Now we connect JWT with a Spring Security filter chain.

```text
POST /login
      ↓
UsernamePasswordAuthenticationFilter
      ↓
AuthenticationManager
      ↓
UserDetailsService
      ↓
Database Validation
      ↓
JWT Generated
      ↓
Client Stores Token
      ↓
Authorization: Bearer xxx
      ↓
JwtAuthenticationFilter
      ↓
SecurityContextHolder
      ↓
AuthorizationFilter
      ↓
Controller
```

This is the real Spring Security JWT architecture.

---

# Step 1 — User Login Request

Suppose a client sends:

```http
POST /login
Content-Type: application/json
```

Body:

```json
{
  "username": "mohan",
  "password": "123"
}
```

---

# What Happens Internally?

```text
Client Request
      ↓
Spring Security Filter Chain
      ↓
UsernamePasswordAuthenticationFilter
      ↓
AuthenticationManager
      ↓
UserDetailsService
      ↓
Database Validation
```

---

# Step 2 — AuthenticationManager

Spring Security delegates authentication to:

```
AuthenticationManager
```

This is the core authentication engine.

It verifies credentials.

---

# Internal Authentication Flow

```text
UsernamePasswordAuthenticationToken
                ↓
AuthenticationManager
                ↓
AuthenticationProvider
                ↓
UserDetailsService
                ↓
Load User From DB
                ↓
Password Validation
```

---

# Step 3 — UserDetailsService

Usually:

```java
@Service
public class CustomUserDetailsService
    implements UserDetailsService{}
```

Purpose:

```text
Load user from database
```

Example:

```java
@Override
public UserDetails loadUserByUsername(String username) {
    return userRepository.findByUsername(username)
        .orElseThrow(() -> new UsernameNotFoundException());
}
```

---

# Step 4 — Password Validation

Spring Security compares:

```text
Raw Password
        vs
Encoded Password
```

using:

```
PasswordEncoder
```

Example:

```
BCryptPasswordEncoder
```

NEVER compare plain-text passwords manually.

---

# Step 5 — Authentication Success

If credentials valid:

Spring creates:

```
Authentication authentication
```

Example:

```
UsernamePasswordAuthenticationToken
```

containing:

* user
* roles
* authorities

---

# Step 6 — JWT Generation

NOW server generates JWT.

This is VERY important:

```text
JWT generation happens AFTER successful authentication.
```

JWT is NOT authentication itself.

---

# JWT Generation Flow

```text
Authenticated User
        ↓
Extract Username
        ↓
Extract Roles
        ↓
Create Claims
        ↓
Generate JWT Signature
        ↓
Return Token
```

---

# Example JWT Payload

```json
{
  "sub": "mohan",
  "role": "ADMIN",
  "iat": 1710000000,
  "exp": 1710000900
}
```

---

# Example JWT Service

Typical class:

```java
@Service
public class JwtService{}
```

Responsibilities:

* generate token
* validate token
* extract claims

---

# Simple JWT Generation Example

```java
public String generateToken(UserDetails user) {

    return Jwts.builder()
        .setSubject(user.getUsername())
        .claim("role", "ADMIN")
        .setIssuedAt(new Date())
        .setExpiration(new Date(
            System.currentTimeMillis() + 1000 * 60 * 15
        ))
        .signWith(secretKey)
        .compact();
}
```

---

# Important JWT Claims

```text
sub → username/user id
iat → issued at
exp → expiration
```

These are standard claims.

---

# Step 7 — Server Returns JWT

Response:

```json
{
  "accessToken": "eyJhbGc..."
}
```

---

# Step 8 — Client Stores JWT

Usually:

* memory
* secure cookie
* HttpOnly cookie

NOT recommended:

```text
localStorage for sensitive apps
```

due to XSS risks.

---

# Step 9 — Client Sends JWT

Every request includes:

```http
Authorization: Bearer eyJhbGc...
```

This is called:

```text
Bearer Authentication
```

---

# Real Request Example

```http
GET /api/orders
Authorization: Bearer eyJhbGc...
```

NOW the important part begins.

---

# JWT Validation Flow

Incoming request:

```text
Request
   ↓
Security Filter Chain
   ↓
JwtAuthenticationFilter
   ↓
Controller
```

JWT validation happens INSIDE filters.

---

# MOST IMPORTANT JWT PRINCIPLE

```text
JWT validation happens BEFORE controller execution.
```

Controllers should NEVER manually validate JWTs.

BAD:

```
if(tokenValid) {
   ...
}
```

inside controllers.

---

# JWT Filter

In real systems:

```java
public class JwtAuthenticationFilter
    extends OncePerRequestFilter{}
```

Very common interview topic.

---

# Why OncePerRequestFilter?

Ensures filter executes:

```text
Exactly once per request
```

Perfect for JWT validation.

---

# JWT Filter Responsibilities

The filter should:

```text
Extract Token
      ↓
Validate Signature
      ↓
Check Expiration
      ↓
Extract Claims
      ↓
Load User
      ↓
Create Authentication
      ↓
Store in SecurityContextHolder
```

This is REAL enterprise flow.

---

# Step 10 — Extract Authorization Header

Inside filter:

```java
String authHeader =
    request.getHeader("Authorization");
```

---

# Step 11 — Check Bearer Token

```
if(authHeader == null ||
   !authHeader.startsWith("Bearer ")) {
    filterChain.doFilter(request, response);
    return;
}
```

If no token exists:

continue request.

---

# Step 12 — Extract JWT

```java
String jwt = authHeader.substring(7);
```

Removes:

```text
Bearer
```

prefix.

---

# Step 13 — Extract Username

```java
String username =
    jwtService.extractUsername(jwt);
```

---

# Step 14 — Validate JWT

Validation includes:

* signature
* expiration
* issuer
* audience

Example:

```
if(jwtService.isTokenValid(jwt, userDetails))
```

---

# Important Security Principle

Never trust JWT payload directly.

ALWAYS validate:

```text
Signature first
```

---

# Step 15 — Load User

```java
UserDetails userDetails =
    userDetailsService.loadUserByUsername(username);
```

---

# Why Load User Again?

Because:

```text
JWT only proves identity.
```

But the latest user data may have changed:

* roles
* permissions
* account status

Very important enterprise concept.

---

# Step 16 — Create Authentication Object

```java
UsernamePasswordAuthenticationToken auth =
    new UsernamePasswordAuthenticationToken(
        userDetails,
        null,
        userDetails.getAuthorities()
    );
```

---

# Step 17 — Store Authentication

Critical step:

```
SecurityContextHolder
    .getContext()
    .setAuthentication(auth);
```

NOW Spring Security considers user authenticated.

---

# MOST IMPORTANT CONCEPT

```text
SecurityContextHolder stores current authenticated user.
```

This is how Spring Security knows:

* who user is?
* roles
* permissions

---

# Step 18 — Continue Filter Chain

```
filterChain.doFilter(request, response);
```

Request continues.

---

# Next Step — Authorization

Now authorization filters check:

```text
Does user have permission?
```

Example:

```
.hasRole("ADMIN")
```

---

# Example Request Flow

Suppose:

```http
GET /admin/users
Authorization: Bearer xyz
```

---

# Internally

```text
JWT Filter
    ↓
Token Validated
    ↓
Authentication Created
    ↓
SecurityContextHolder Updated
    ↓
Authorization Filter
    ↓
ROLE_ADMIN Check
    ↓
Controller Executes
```

---

# What If JWT Invalid?

JWT filter rejects request.

Example:

```http
401 Unauthorized
```

Controller NEVER executes.

---

# What If Role Missing?

Authentication succeeds.

But authorization fails.

Example:

```http
403 Forbidden
```

Very important distinction.

---

# 401 vs 403

| Status | Meaning |
|---|---|
| 401 | Not authenticated |
| 403 | Authenticated but not authorized |

Critical interview question.

---

# JWT Filter Placement

Filter ordering matters.

JWT filter MUST execute before:

```text
AuthorizationFilter
```

Otherwise, authorization has no authenticated user.

---

# Typical Spring Security Configuration

```
http
    .csrf(csrf -> csrf.disable())
    .sessionManagement(session ->
        session.sessionCreationPolicy(
            SessionCreationPolicy.STATELESS
        )
    )
```

---

# Why STATELESS?

Because JWT systems do NOT use sessions.

Every request is independently authenticated.

---

# Adding JWT Filter

Typical configuration:

```
http.addFilterBefore(
    jwtAuthenticationFilter,
    UsernamePasswordAuthenticationFilter.class
);
```

This is VERY important.

---

# Why addFilterBefore?

Ensures JWT validation happens BEFORE:

```text
UsernamePasswordAuthenticationFilter
```

and authorization processing.

---

# Access Token vs Refresh Token

Modern production systems use BOTH.

---

# Access Token

Short-lived.

Example:

```text
15 minutes
```

Used for API access.

---

# Refresh Token

Longer-lived.

Example:

```text
7 days
```

Used to obtain new access tokens.

---

# Refresh Flow

```text
Access Token Expired
        ↓
Client Sends Refresh Token
        ↓
Server Validates Refresh Token
        ↓
New Access Token Generated
```

Very common enterprise architecture.

---

# Important Industry Practices

---

# 1. Always Use HTTPS

JWT must NEVER travel over plain HTTP.

Otherwise, token theft becomes easy.

---

# 2. Keep Access Tokens Short-Lived

Good practice:

```text
5–15 minutes
```

NOT:

```text
24 hours
```

---

# 3. Do NOT Store Sensitive Data in JWT

JWT payload is readable.

Never store:

* passwords
* secrets
* personal data

---

# 4. Use Strong Algorithms

Enterprise standards:

```text
RS256
ES256
```

Better than shared-secret systems.

---

# 5. Rotate Keys

Production systems rotate signing keys.

Usually using:

```text
kid (Key ID)
```

---

# 6. Avoid localStorage for Sensitive Apps

Safer:

* HttpOnly cookies
* secure cookies

Very important security topic.

---

# Common Beginner Mistakes

---

# Mistake 1

Trying to authenticate inside controllers.

BAD:

```
if(tokenValid)
```

inside every endpoint.

---

# Mistake 2

Not understanding filter order.

Causes:

* authentication failures
* SecurityContext issues

---

# Mistake 3

Very long token expiration.

Huge security risk.

---

# Mistake 4

Storing passwords in JWT payload.

Extremely dangerous.

---

# Mistake 5

Not validating token signature.

Critical vulnerability.

---

# Senior-Level Understanding

Senior engineers understand:

* JWT lifecycle
* filter-chain integration
* SecurityContext lifecycle
* stateless architecture
* access vs refresh tokens
* signing algorithms
* key rotation
* revocation strategies
* authorization flow

Not just token generation code.

---

# Most Important Interview Question

## "How does JWT authentication work internally in Spring Security?"

Strong answer:

```text
User credentials are authenticated using AuthenticationManager.
After successful authentication, the server generates a signed JWT token.
For future requests, a JWT filter extracts and validates the token before controller execution.
The filter creates an Authentication object and stores it inside SecurityContextHolder.
Authorization filters then verify permissions before allowing access to controllers.
```

This is the REAL architecture.

---

# Final Mental Model

```text
Login Request
      ↓
AuthenticationManager
      ↓
JWT Generated
      ↓
Client Stores Token
      ↓
Authorization Header
      ↓
JwtAuthenticationFilter
      ↓
Token Validation
      ↓
SecurityContextHolder
      ↓
AuthorizationFilter
      ↓
Controller
```

If you deeply understand THIS flow,
modern Spring Security becomes much easier.