---
title: Service Registry & Service Discovery (Eureka)
parent: Microservices
nav_order: 3
---

# Service Registry & Service Discovery (Eureka)

In a **microservices architecture**, services must communicate with each other.
However, services are **dynamic by nature**, which makes direct communication difficult.

**Service Registry** and **Service Discovery** solve this problem.

---

## What Problem Does Service Discovery Solve?

In a microservices environment:

- Services scale up and down dynamically
- Containers restart frequently
- IP addresses and ports change
- Hard-coding service URLs is impossible

Without service discovery:
- Services become tightly coupled
- Deployments break communication
- Scaling becomes risky


## Core Idea

> **Service Registry** keeps track of all running services and their locations.  
> **Service Discovery** allows services to find each other dynamically at runtime.

---

## What Is Eureka?

**Eureka** is Netflix’s open-source **service registry and discovery solution**, widely
used with **Spring Cloud**.

Eureka follows a **client-side service discovery** model.


## Components of Eureka

Eureka consists of **two main components**:


### 1️⃣ Eureka Server (Service Registry)

- Central registry for all services
- Stores service metadata:
    - Service name
    - IP address
    - Port
    - Status (UP / DOWN)
- Exposes a discovery endpoint
- Provides a dashboard


### 2️⃣ Eureka Client (Microservices)

- Any microservice that:
    - Registers itself with Eureka
    - Discovers other services from Eureka
- Sends periodic heartbeats to Eureka

---

## How Eureka Works (Step-by-Step Flow)

1. Service starts
2. Service registers with Eureka Server
3. Eureka marks service as UP
4. Service sends heartbeat every 30 seconds
5. Client requests service details from Eureka
6. Load balancer selects an instance(if we have many)
7. Request is sent to the chosen instance


---

## Setting Up Eureka Server (Spring Boot)

### Step 1️⃣ Add Dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

### Step 2️⃣ Enable Eureka Server

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}

```

### Step 3️⃣ application.yml

```yaml
server:
  port: 8761

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false

```

>  Access dashboard at: http://localhost:8761

---

## Registering a Service with Eureka (Client)

### Step 1️⃣ Add Dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

```

### Step 2️⃣ Enable Client

```
@EnableEurekaClient
```

> In newer Spring Cloud versions, this is auto-enabled.

### Step 3️⃣ application.yml

```yaml

spring:
  application:
    name: ACCOUNT-SERVICE

server:
  port: 8085

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka

```

---

### Eureka Features We Should Know

#### Self-Healing

- Services are not removed immediately
- Prevents accidental removal due to temporary network issues

#### Client-Side Discovery

- Client decides which instance to call
- More scalable than server-side discovery

#### Status Updates

Services can publish:

- UP
- DOWN
- OUT_OF_SERVICE
- MAINTENANCE

##### Dashboard

- Visual view of registered services
- Health and metadata visibility

---

### Why Use Eureka in Spring Cloud?

- Easy to set up
- Cloud-native design
- Integrates well with:
    - API Gateway
    - LoadBalancer
    - Resilience patterns
- Avoids hard-coded service URLs

---

> Eureka is a service registry used in microservices to dynamically discover service instances. Services register themselves with Eureka, send heartbeats, and clients use Eureka along with a load balancer to locate and communicate with other services.

