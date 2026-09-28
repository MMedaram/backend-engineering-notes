# Backend Engineering Roadmap

This roadmap is designed for senior backend-engineering learning. Work through it in order, taking one topic at a time.

## 1. High-Level Design (HLD) Fundamentals

- [ ] **Functional requirements** — Define what a system must do: user actions, workflows, APIs, and expected outputs.
- [ ] **Non-functional requirements (NFRs)** — Define quality expectations: latency, availability, scalability, security, reliability, and cost.
- [ ] **Assumptions and constraints** — Identify unknowns early, such as expected users, budget, existing systems, compliance, or latency targets.
- [ ] **Capacity estimation** — Estimate requests per second, storage, bandwidth, concurrent users, and server/database capacity.
- [ ] **Latency, throughput, and concurrency** — Understand response time, requests processed per second, and active simultaneous requests.
- [ ] **Availability and reliability** — Design for uptime and predictable behavior even during partial failures.
- [ ] **Scalability** — Scale applications horizontally/vertically and identify bottlenecks before they become outages.
- [ ] **Consistency and durability** — Understand data correctness guarantees and when committed data must survive failures.
- [ ] **Monolith vs modular monolith vs microservices** — Choose architecture based on team, domain complexity, deployment needs, and operational cost.
- [ ] **Load balancers** — Distribute traffic across instances and remove unhealthy servers.
- [ ] **Reverse proxy** — Handle TLS termination, routing, caching, and request forwarding at the edge.
- [ ] **API Gateway** — Centralize routing, authentication, rate limiting, and API concerns for multiple services.
- [ ] **Backend for Frontend (BFF)** — Build API layers tailored for web, mobile, or other client needs.
- [ ] **Service discovery** — Allow services to locate each other dynamically in distributed deployments.
- [ ] **Configuration management** — Manage environment-specific configuration and secrets safely.
- [ ] **CDN** — Serve static content near users and reduce load on backend systems.
- [ ] **Object storage** — Store files, images, videos, and large blobs outside relational databases.

## 2. Data, Databases, Caching, and Messaging

- [ ] **SQL databases** — Use relational databases for transactions, relationships, constraints, and strong consistency.
- [ ] **NoSQL databases** — Use document, key-value, wide-column, or graph stores when data access patterns require them.
- [ ] **Database selection** — Choose a database based on consistency, query needs, scale, schema flexibility, and operational cost.
- [ ] **Data modelling** — Design tables, entities, relationships, keys, constraints, and access patterns.
- [ ] **Database indexing** — Speed up reads while understanding index size and write-performance trade-offs.
- [ ] **Database transactions** — Ensure a group of database operations succeeds or fails as one unit.
- [ ] **Isolation levels** — Understand dirty reads, non-repeatable reads, phantom reads, and transaction trade-offs.
- [ ] **Optimistic locking** — Detect conflicting updates without holding database locks for a long time.
- [ ] **Pessimistic locking** — Lock data during critical operations where conflicts are likely and correctness is paramount.
- [ ] **Replication** — Maintain copies of data for read scale, availability, and disaster recovery.
- [ ] **Partitioning and sharding** — Split large datasets to improve scale and manageability.
- [ ] **Database failover and backups** — Recover from infrastructure failure and prevent permanent data loss.
- [ ] **Redis fundamentals** — Use Redis for caching, counters, rate limiting, sessions, and lightweight distributed coordination.
- [ ] **Cache-aside pattern** — Read from cache first; on a miss, load from the database and populate the cache.
- [ ] **Write-through and write-behind caching** — Understand how writes interact with cache and persistent storage.
- [ ] **Cache invalidation** — Keep cached data sufficiently fresh after updates.
- [ ] **TTL and eviction policies** — Control cache lifetime and memory behavior.
- [ ] **Cache stampede and hot keys** — Prevent traffic spikes from overwhelming the database when a popular cache entry expires.
- [ ] **Message queues** — Decouple services and process work asynchronously.
- [ ] **Kafka fundamentals** — Learn topics, partitions, offsets, producers, consumers, consumer groups, and retention.
- [ ] **RabbitMQ fundamentals** — Understand exchanges, queues, routing keys, acknowledgements, and dead-letter queues.
- [ ] **Synchronous vs asynchronous communication** — Decide when a request needs an immediate response versus background processing.
- [ ] **Event-driven architecture** — Publish domain events so other systems react without direct tight coupling.
- [ ] **Event ordering and duplicate delivery** — Build consumers that safely handle retries, reordered events, and duplicates.
- [ ] **Dead-letter queues (DLQ)** — Isolate repeatedly failing messages for investigation and controlled replay.
- [ ] **Schema evolution** — Change event/API schemas without breaking existing producers or consumers.

## 3. HLD Case Studies

For every system-design problem, follow this format: requirements → estimates → APIs → data model → architecture → bottlenecks → failure handling → trade-offs.

- [ ] **URL shortener** — Key generation, redirects, caching, analytics, high read traffic, and database partitioning.
- [ ] **File upload and media service** — Object storage, pre-signed URLs, CDN, metadata, security, and asynchronous processing.
- [ ] **Notification service** — Email, SMS, push notifications, templates, preferences, retries, rate limiting, and delivery tracking.
- [ ] **E-commerce product catalog** — Product data, search, inventory visibility, caching, and price updates.
- [ ] **Shopping cart service** — Session/cart storage, expiry, concurrency, and cart-to-order conversion.
- [ ] **Order management system** — Order lifecycle, state transitions, payment, inventory, shipment, and audit history.
- [ ] **Payment system** — Idempotency, provider integration, reconciliation, fraud checks, refunds, and strong auditability.
- [ ] **Inventory management system** — Reservations, stock deduction, overselling prevention, and eventual consistency.
- [ ] **Chat application** — WebSockets, message ordering, online presence, message persistence, and fan-out.
- [ ] **News feed system** — Fan-out on write/read, ranking, caching, timelines, and high-volume reads.
- [ ] **Rate limiter** — Token bucket, leaky bucket, fixed/sliding windows, Redis implementation, and distributed limits.
- [ ] **Search system** — Indexing pipeline, Elasticsearch/OpenSearch, relevance, filters, and eventual consistency.

## 4. Low-Level Design (LLD) and Java

- [ ] **Object-oriented design** — Model real business concepts through classes, responsibilities, and collaboration.
- [ ] **SOLID principles** — Create code that is easier to extend, test, and maintain.
- [ ] **Single Responsibility Principle** — Keep each class focused on one meaningful reason to change.
- [ ] **Open/Closed Principle** — Add new behavior through extension rather than repeatedly changing stable code.
- [ ] **Liskov Substitution Principle** — Ensure subclasses can safely replace their parent types.
- [ ] **Interface Segregation Principle** — Prefer small, focused interfaces over large general-purpose ones.
- [ ] **Dependency Inversion Principle** — Depend on abstractions, enabling flexibility and testability.
- [ ] **Composition over inheritance** — Reuse behavior by assembling objects rather than creating deep class hierarchies.
- [ ] **Encapsulation and immutability** — Protect object state and make data safer in concurrent systems.
- [ ] **Domain-driven design basics** — Use domain language, bounded contexts, entities, value objects, and aggregates.
- [ ] **DTOs and mapping** — Separate API request/response models from domain and database entities.
- [ ] **Exception design** — Create meaningful business and technical exceptions with consistent API error responses.
- [ ] **Strategy pattern** — Switch behavior cleanly, such as payment methods or notification channels.
- [ ] **Factory pattern** — Create the correct implementation based on configuration or input.
- [ ] **Builder pattern** — Construct complex, immutable objects safely and readably.
- [ ] **Adapter pattern** — Wrap external APIs behind your own stable interfaces.
- [ ] **Observer pattern / domain events** — Trigger follow-up actions after important domain changes.
- [ ] **Decorator pattern** — Add behavior such as logging, retrying, or auditing without changing core logic.
- [ ] **State pattern** — Represent workflows with valid transitions, such as order states.
- [ ] **Chain of Responsibility** — Build extensible processing or validation pipelines.
- [ ] **Java Collections Framework** — Choose correct collections and understand time/space complexity.
- [ ] **Generics** — Write type-safe reusable Java components.
- [ ] **Java Streams and Optional** — Use functional style appropriately without making code obscure.
- [ ] **Java concurrency** — Threads, executors, synchronization, locks, thread safety, and race conditions.
- [ ] **CompletableFuture** — Compose asynchronous operations and handle failures correctly.
- [ ] **JVM fundamentals** — Heap, stack, garbage collection, class loading, and common memory issues.
- [ ] **Performance profiling** — Diagnose CPU, memory, thread, database, and connection-pool problems.
- [ ] **Unit testing** — Test business logic in isolation.
- [ ] **Integration testing** — Test real interactions with databases, Kafka, Redis, and HTTP dependencies.
- [ ] **Testcontainers** — Run realistic infrastructure dependencies during automated tests.
- [ ] **Code review and refactoring** — Improve readability, maintainability, correctness, and performance.

## 5. Production Spring Boot

- [ ] **Spring Boot fundamentals** — Auto-configuration, starters, dependency injection, profiles, and application lifecycle.
- [ ] **Dependency Injection** — Use constructor injection and design components around clear dependencies.
- [ ] **Spring MVC / REST APIs** — Build controllers, requests, responses, validation, and exception handling.
- [ ] **REST API design** — Resource naming, HTTP methods, status codes, pagination, filtering, and versioning.
- [ ] **Input validation** — Validate requests early and return useful, safe error messages.
- [ ] **Global exception handling** — Standardize API errors using `@ControllerAdvice`.
- [ ] **OpenAPI / Swagger** — Document and test APIs clearly.
- [ ] **Layered architecture** — Separate web, application, domain, and infrastructure concerns.
- [ ] **Hexagonal architecture** — Keep business logic independent from frameworks, databases, and external providers.
- [ ] **Spring Data JPA** — Build repositories and manage relational persistence.
- [ ] **Hibernate internals** — Understand lazy loading, dirty checking, entity state, and persistence context.
- [ ] **N+1 query problem** — Detect and eliminate inefficient database query patterns.
- [ ] **Transactions with `@Transactional`** — Define proper transactional boundaries and propagation.
- [ ] **Database migration** — Use Flyway or Liquibase to version schema changes safely.
- [ ] **Pagination and large data processing** — Avoid loading excessive data into memory.
- [ ] **External service integration** — Use HTTP clients, timeouts, error handling, and API contracts.
- [ ] **Resilience4j** — Apply retries, circuit breakers, rate limiters, bulkheads, and time limiters.
- [ ] **Idempotency APIs** — Safely process client retries without duplicate side effects.
- [ ] **Spring Security** — Secure endpoints, authenticate users, and enforce authorization.
- [ ] **OAuth2 and JWT** — Implement token-based authentication and resource-server security.
- [ ] **Role-based and permission-based authorization** — Control access based on user roles and fine-grained permissions.
- [ ] **Secure configuration and secrets** — Avoid exposing credentials and sensitive configuration.
- [ ] **Structured logging** — Produce searchable logs with useful context.
- [ ] **Correlation IDs** — Trace a request across APIs and services.
- [ ] **Metrics with Micrometer** — Measure application health, latency, errors, JVM, and custom business metrics.
- [ ] **Distributed tracing with OpenTelemetry** — Follow one transaction across multiple services.
- [ ] **Health checks and graceful shutdown** — Deploy safely and avoid dropping in-flight requests.

## 6. Microservices and Distributed Systems

- [ ] **Microservice boundaries** — Split services around business capabilities, not technical layers.
- [ ] **Bounded contexts** — Prevent different domains from becoming tightly coupled through shared models.
- [ ] **Database per service** — Keep ownership of data clear and reduce direct cross-service database access.
- [ ] **Inter-service communication** — Use REST/gRPC for immediate responses and events for asynchronous workflows.
- [ ] **API contracts** — Version APIs safely and prevent breaking changes.
- [ ] **Service-to-service authentication** — Secure internal calls using trusted identity mechanisms.
- [ ] **Distributed tracing** — Diagnose failures across a multi-service request path.
- [ ] **Eventual consistency** — Accept temporary inconsistency where distributed transactions are impractical.
- [ ] **Distributed transactions** — Understand why traditional ACID transactions do not scale well across services.
- [ ] **Saga pattern** — Coordinate multi-service business workflows with compensating actions.
- [ ] **Saga choreography** — Services react to events without a central coordinator.
- [ ] **Saga orchestration** — A workflow coordinator directs participating services.
- [ ] **Transactional outbox pattern** — Reliably publish events after database changes.
- [ ] **Inbox / idempotent consumer pattern** — Prevent duplicated event processing.
- [ ] **CQRS basics** — Separate write models from optimized read models when complexity justifies it.
- [ ] **Distributed locks** — Understand when locks are needed and why they are risky.
- [ ] **Leader election** — Select one active worker for jobs that must not run concurrently.
- [ ] **Rate limiting in distributed systems** — Enforce limits consistently across multiple application instances.
- [ ] **Feature flags** — Release behavior safely without full redeployment.
- [ ] **Multi-region and disaster recovery** — Plan for regional outages, data replication, and recovery objectives.

## 7. DevOps, Containers, and Cloud Fundamentals

- [ ] **Linux and networking basics** — Processes, ports, DNS, HTTP, TLS, sockets, and environment variables.
- [ ] **Docker** — Package applications with their dependencies in reproducible containers.
- [ ] **Docker Compose** — Run a local multi-service environment with PostgreSQL, Redis, Kafka, and services.
- [ ] **Container security** — Use minimal images, non-root users, vulnerability scanning, and secret-safe builds.
- [ ] **Kubernetes fundamentals** — Pods, deployments, services, namespaces, ingress, and configuration.
- [ ] **Readiness and liveness probes** — Let Kubernetes route traffic only to healthy applications.
- [ ] **ConfigMaps and Secrets** — Manage deployment configuration separately from application code.
- [ ] **Autoscaling** — Scale workloads based on CPU, memory, or custom metrics.
- [ ] **CI/CD** — Automate build, test, scan, deployment, and rollback stages.
- [ ] **Deployment strategies** — Rolling, blue-green, and canary deployments.
- [ ] **Infrastructure as Code basics** — Provision repeatable cloud infrastructure using declarative configuration.
- [ ] **Cloud primitives** — Compute, managed databases, object storage, load balancers, messaging, monitoring, and IAM.
- [ ] **Cost awareness** — Balance performance, availability, and operational cost.

## 8. Engineering Practices for Senior Roles

- [ ] **Architecture Decision Records (ADRs)** — Document decisions, alternatives, consequences, and review dates.
- [ ] **RFC/design documents** — Write proposals that teams can review before implementation.
- [ ] **Trade-off communication** — Clearly explain why one approach was selected over alternatives.
- [ ] **Production incident handling** — Triage, mitigate, communicate, investigate root cause, and prevent recurrence.
- [ ] **Postmortems** — Write blameless analysis and concrete preventive actions after incidents.
- [ ] **SLOs, SLIs, and error budgets** — Measure reliability from the customer’s perspective.
- [ ] **Security review mindset** — Identify authentication, authorization, validation, data exposure, and abuse risks.
- [ ] **Performance testing** — Use load, stress, soak, and spike tests to expose real bottlenecks.
- [ ] **Technical leadership** — Break down work, guide design reviews, mentor engineers, and make pragmatic decisions.


