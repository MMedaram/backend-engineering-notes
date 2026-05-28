---
title: OAuth2
parent: Security
nav_order: 5
---

# OAuth2

OAuth2 is one of the MOST important authentication and authorization protocols in modern systems.

Used everywhere:

* Google Login
* GitHub Login
* Facebook Login
* Enterprise SSO
* Microservices
* API authorization
* Cloud platforms

Modern authentication heavily relies on OAuth2.

---

# MOST IMPORTANT CONCEPT

OAuth2 is primarily:

```text
Authorization Framework
```

NOT authentication protocol.

This is a VERY important distinction.

---

# Common Beginner Confusion

Many developers think:

```text
OAuth2 = Authentication
```

Not exactly.

OAuth2 mainly solves:

```text
How can one application access resources
on behalf of a user safely?
```

---

# Real-World Example

Suppose:

```text
Spotify wants access to your Google profile.
```

You should NOT give Spotify:

```text
Your Google password
```

Instead:

```text
Google gives Spotify limited access token.
```

THIS is OAuth2.

---

# Core OAuth2 Idea

```text
User grants limited permission
to an application
without sharing credentials.
```

That is the heart of OAuth2.

---

# Simple OAuth2 Flow

```text
User
   ↓
Client Application
   ↓
Authorization Server
   ↓
Access Token
   ↓
Resource Server
```

---

# Example — Google Login

```text
User clicks "Login with Google"
        ↓
Redirect to Google
        ↓
User logs into Google
        ↓
User approves permissions
        ↓
Google returns authorization code
        ↓
Application exchanges code for access token
        ↓
Application accesses user profile
```

This is standard OAuth2 flow.

---

# Important OAuth2 Roles

OAuth2 defines several core actors.

Very important interview topic.

---

# 1. Resource Owner

Usually:

```text
The User
```

Owner of data/resources.

Example:

* Google account owner
* GitHub account owner

---

# 2. Client Application

Application requesting access.

Example:

* Spotify
* Slack
* Your Spring Boot app

---

# 3. Authorization Server

Responsible for:

* authenticating users
* issuing tokens
* managing permissions

Examples:

* Google OAuth Server
* Okta
* Keycloak
* Auth0

---

# 4. Resource Server

Server hosting protected resources.

Examples:

* Google APIs
* User profile API
* Email APIs

---

# OAuth2 Architecture

```text
+-------------------+
|   Resource Owner  |
|      (User)       |
+-------------------+
          ↓
+-------------------+
| Client Application|
+-------------------+
          ↓
+-------------------+
| Authorization     |
| Server            |
+-------------------+
          ↓
+-------------------+
| Resource Server   |
+-------------------+
```

---

# MOST IMPORTANT OAuth2 PRINCIPLE

```text
Client application never sees user's password.
```

This is a huge security advantage.

---

# OAuth2 Tokens

OAuth2 commonly uses:

```text
Access Tokens
```

Often JWT-based today.

---

# Access Token

Represents granted permission.

Example:

```text
Can read profile
Can access email
```

Usually short-lived.

---

# Refresh Token

Used to obtain new access tokens.

Longer-lived.

Common in enterprise systems.

---

# OAuth2 Scopes

Scopes define permissions.

Example:

```text
email
profile
read:user
write:repo
```

Very important concept.

---

# Example Scope Request

```text
Access:
- email
- profile
```

Application can ONLY access approved scopes.

---

# OAuth2 Authorization Flow

Most important flow:

```text
Authorization Code Flow
```

Used by:

* web applications
* enterprise systems
* modern secure apps

---

# Authorization Code Flow

```text
User
   ↓
Client Redirects User
   ↓
Authorization Server Login
   ↓
User Grants Permission
   ↓
Authorization Code Returned
   ↓
Client Exchanges Code
   ↓
Access Token Returned
```

This is THE most important OAuth2 flow.

---

# Step-by-Step OAuth2 Flow

---

# Step 1 — User Clicks Login

Example:

```text
Login with Google
```

---

# Step 2 — Redirect to Authorization Server

Client redirects browser:

```http
GET https://accounts.google.com/o/oauth2/auth
```

with:

* client_id
* redirect_uri
* scopes
* response_type

---

# Step 3 — User Authenticates

Google authenticates user.

This part is:

```text
Authentication
```

NOT OAuth authorization yet.

---

# Step 4 — User Grants Consent

Google shows:

```text
This app wants access to:
- email
- profile
```

User approves/rejects.

---

# Step 5 — Authorization Code Returned

Google redirects:

```http
GET /callback?code=abc123
```

This code is temporary.

---

# Why Authorization Code?

Because:

```text
Access token should not travel directly through browser.
```

Security improvement.

---

# Step 6 — Exchange Code for Token

Backend sends:

```http
POST /token
```

to authorization server.

Includes:

* authorization code
* client_secret
* client_id

---

# Step 7 — Access Token Returned

Authorization server responds:

```json
{
  "access_token": "xyz",
  "expires_in": 3600
}
```

---

# Step 8 — Access Protected Resources

Application sends:

```http
Authorization: Bearer xyz
```

to resource server.

---

# OAuth2 + JWT

Modern OAuth2 systems commonly use:

```text
JWT access tokens
```

This combines:

* OAuth2 authorization
* JWT stateless tokens

Very common enterprise architecture.

---

# OAuth2 vs JWT

Critical interview topic.

---

# JWT

JWT is:

```text
Token Format
```

Used to represent claims securely.

---

# OAuth2

OAuth2 is:

```text
Authorization Framework
```

Defines:

* flows
* permissions
* token issuance

---

# Important Distinction

```text
OAuth2 may use JWT tokens,
but JWT itself is NOT OAuth2.
```

Very important concept.

---

# OAuth2 in Spring Security

Spring Security provides support for:

```text
OAuth2 Login
OAuth2 Client
OAuth2 Resource Server
```

---

# Common Spring Security OAuth2 Usage

---

# 1. OAuth2 Login

Example:

```text
Login with Google
```

Spring handles:

* redirects
* token exchange
* authentication

---

# 2. Resource Server

Spring validates:

```text
Bearer access tokens
```

Usually JWTs.

Very common in APIs.

---

# 3. OAuth2 Client

Spring application itself calls other APIs.

Example:

```text
Call Google APIs
Call GitHub APIs
```

---

# Spring Security OAuth2 Login Flow

```text
Browser
   ↓
Spring Security OAuth2 Filter
   ↓
Redirect to Google
   ↓
Google Login
   ↓
Authorization Code
   ↓
Token Exchange
   ↓
Authentication Created
   ↓
SecurityContextHolder
```

OAuth2 also integrates into filter chain.

---

# OAuth2 Filters

Spring Security internally uses OAuth2 filters.

Example:

```text
OAuth2AuthorizationRequestRedirectFilter
OAuth2LoginAuthenticationFilter
BearerTokenAuthenticationFilter
```

Very advanced interview topic.

---

# OAuth2 Resource Server

Suppose API receives:

```http
Authorization: Bearer xyz
```

Spring Security validates:

* token signature
* issuer
* audience
* scopes

before controller executes.

Exactly like JWT authentication flow.

---

# OAuth2 Scopes vs Roles

Important distinction.

---

# Roles

Usually application permissions.

Example:

```text
ROLE_ADMIN
ROLE_USER
```

---

# Scopes

OAuth permissions.

Example:

```text
read:profile
write:orders
```

---

# OAuth2 Security Advantages

---

# 1. Password Isolation

Applications never see user passwords.

Huge security improvement.

---

# 2. Fine-Grained Permissions

Apps only get approved access.

Example:

```text
Read-only email access
```

---

# 3. Centralized Authentication

Enterprise SSO becomes possible.

Examples:

* Okta
* Azure AD
* Keycloak

---

# 4. Better Enterprise Integration

OAuth2 works very well with:

* cloud systems
* APIs
* microservices

---

# OAuth2 + Microservices

Very common architecture:

```text
Client
   ↓
API Gateway
   ↓
Authorization Server
   ↓
JWT Access Token
   ↓
Microservices
```

---

# Popular OAuth2 Providers

Common providers:

* Google
* GitHub
* Facebook
* Okta
* Auth0
* Keycloak
* Azure AD

---

# OpenID Connect (OIDC)

Very important modern topic.

OAuth2 itself does NOT define identity standard properly.

So:

```text
OpenID Connect (OIDC)
```

was built on top of OAuth2.

---

# OAuth2 vs OpenID Connect

---

# OAuth2

Focuses on:

```text
Authorization
```

---

# OIDC

Focuses on:

```text
Authentication + Identity
```

Very important distinction.

---

# ID Token

OIDC introduces:

```text
ID Token
```

Usually JWT-based.

Contains:

* user identity
* profile info
* authentication details

---

# MOST IMPORTANT MODERN REALITY

When developers say:

```text
Login with Google
```

they usually mean:

```text
OAuth2 + OpenID Connect
```

NOT pure OAuth2 alone.

---

# Important Industry Practices

---

# 1. Always Use Authorization Code Flow

Most secure for server-side applications.

---

# 2. Never Expose Client Secret

Client secrets belong ONLY on backend.

---

# 3. Always Validate Tokens

Validate:

* issuer
* audience
* expiration
* signature

---

# 4. Use HTTPS Everywhere

OAuth tokens must NEVER travel over HTTP.

---

# 5. Keep Access Tokens Short-Lived

Enterprise standard:

```text
5–15 minutes
```

---

# 6. Use Refresh Tokens Carefully

Store securely.

Rotate when possible.

---

# Common Beginner Mistakes

---

# Mistake 1

Thinking OAuth2 is authentication only.

Actually:

```text
OAuth2 is authorization framework.
```

---

# Mistake 2

Confusing JWT with OAuth2.

JWT != OAuth2.

---

# Mistake 3

Not validating token issuer/audience.

Critical security issue.

---

# Mistake 4

Sending tokens over HTTP.

Huge vulnerability.

---

# Mistake 5

Exposing client secrets in frontend apps.

Very dangerous.

---

# Real Enterprise OAuth2 Architecture

Modern enterprise systems usually look like:

```text
Frontend
    ↓
OAuth2 Provider
    ↓
Access Token
    ↓
API Gateway
    ↓
Microservices
```

Usually integrated with:

* SSO
* JWT
* OIDC
* API gateways

---

# Senior-Level Understanding

Senior engineers understand:

* OAuth2 flows
* Authorization Code Flow
* scopes
* JWT integration
* OIDC
* token validation
* enterprise SSO
* API security
* authorization delegation

Not just login buttons.

---

# Most Important Interview Question

## "What is OAuth2?"

Strong answer:

```text
OAuth2 is an authorization framework that allows applications to access protected resources on behalf of users without exposing user credentials.
It works using access tokens issued by an authorization server.
Modern systems commonly combine OAuth2 with JWT and OpenID Connect for authentication and identity management.
```

This is the core understanding.

---

# Final Mental Model

```text
User
   ↓
Authorization Server
   ↓
Access Token Issued
   ↓
Client Uses Token
   ↓
Resource Server Validates Token
   ↓
Access Granted
```

If you deeply understand OAuth2,
modern authentication systems become much easier.