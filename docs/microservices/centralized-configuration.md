---
title: Centralized Configuration
parent: Microservices Architecture
nav_order: 5
---

# Centralized Configuration in Microservices

In a microservices architecture, managing configuration separately
for each service becomes difficult as the system grows.

Centralized configuration solves this problem by storing
all configuration values in a **central location** instead of
scattering them across multiple services.

---

## Why Centralized Configuration Is Needed

Without centralized configuration:

- Each service has its own `application.yml`
- Configuration duplication across services
- Updating a property requires redeployment
- Hard to manage environment-specific values
- Risk of inconsistency

In enterprise systems, this becomes unmanageable.

---

## What Is Centralized Configuration?

> Centralized configuration means storing configuration
in one shared location so all services can fetch and use it dynamically.

Common storage options:
- Git repository
- Database
- Config server
- Cloud config service

In this document, we focus on:

> **Using a Database as configuration storage**

---

# Why Use a Database for Configuration?

Using a database provides:

✔ Dynamic updates  
✔ Runtime changes (no redeployment required)  
✔ Central control  
✔ Audit capability  
✔ Easy environment separation

Especially useful when:
- Operations team needs to change values quickly
- Feature flags must be toggled dynamically
- Thresholds / limits change frequently

---

## High-Level Architecture

```text
             ┌────────────────────────┐
             │    PROPERTIES TABLE    │
             │  (Central DB Storage)  │
             └───────────┬────────────┘
                         │
                         ▼
             ┌────────────────────────┐
             │  Spring Cloud Config   │
             │      Server (JDBC)     │
             │   @EnableConfigServer  │
             └───────────┬────────────┘
                         │
    ┌──────────────┬─────┴─────┬──────────────┐
    ▼              ▼           ▼              ▼
  Account      Customer      Order         Notification 

```

---

## Data Base table Example:

| APPLICATION       | PROFILE | LABEL | KEY                     | VALUE             |
|------------------|---------|-------|--------------------------|-------------------|
| ACCOUNT-SERVICE | PROD    | jdbc  | max.transfer.limit       | 500000            |
| ACCOUNT-SERVICE | PROD    | jdbc  | kafka.bootstrap.servers  | kafka01:9092      |

---

## Config Server Configuration (JDBC Backend)

```yaml
spring:
    cloud:
      config:
        server:
          default-label: jdbc
          bootstrap: true
          jdbc:
            sql: SELECT KEY, VALUE FROM PROPERTIES WHERE APPLICATION=? AND PROFILE=? AND LABEL=?
            order: 1
```
This means:

- Config Server reads configuration from DB
- Uses custom SQL query
- Filters by:
  - APPLICATION
  - PROFILE 
  - LABEL

- Returns configuration dynamically

---

## Do We Need to Expose APIs in Config Server?

No.

When using:

```
@EnableConfigServer
```

> Spring Cloud Config automatically exposes internal REST endpoints.

Example endpoint:

> GET /{application}/{profile}


### Example:

> GET http://config-server:8888/ACCOUNT-SERVICE/PROD/jdbc

Spring:

- Executes configured SQL
- Builds property source
- Returns structured JSON response
- Client service loads properties into Spring Environment
- No custom controllers are required.


### How Client Microservices Fetch Configuration

Client service configuration:

```text
spring:
  config:
    import: configserver:
```

(or older style using **spring.cloud.config.uri**)

#### Startup flow:

```
Service starts
↓
Calls Config Server
↓
Receives JSON configuration
↓
Loads into Spring Environment
↓
@Bean creation begins
```
After this:

- @Value
- @ConfigurationProperties

work normally.

---

## Security Considerations

✔ Protect Config Server with authentication   
✔ Restrict access (internal network only)    
✔ Encrypt sensitive properties      
✔ Mask secrets in logs      
✔ Audit configuration changes       
✔ Secure DB credentials       

**Never expose Config Server publicly.**

---

## Best Practices

✔ Use `@ConfigurationProperties` instead of scattered `@Value`     
✔ Keep configuration separate from business logic     
✔ Avoid runtime DB calls per request     
✔ Cache configuration in client services       
✔ Version configuration changes      
✔ Document critical properties      


> In enterprise microservices, centralized configuration is handled using Spring Cloud Config Server backed by a database. Services fetch configuration at startup using application name and profile. Config Server executes a JDBC query to retrieve relevant properties and exposes them via built-in REST endpoints. Dynamic refresh can be achieved using Actuator or Spring Cloud Bus. This approach ensures centralized, secure, and scalable configuration management.
