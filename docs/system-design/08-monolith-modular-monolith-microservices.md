---
title: Monolith vs Modular Monolith vs Microservices
parent: System Design
nav_order: 8
---

# Monolith vs Modular Monolith vs Microservices

> Choose an architecture to fit the product, team, and operational constraints.
> A system is not better simply because it has more services.

Architecture style affects how an application is developed, deployed, scaled,
tested, operated, and changed. The central choice is not “old versus modern.”
It is how much separation the product needs and how much distributed-systems
complexity the organization can operate.

---

## 1. The three architecture styles

### 1.1 Monolith

A monolith is an application built and deployed as one unit. Its code can be
well-structured or poorly structured; “monolith” describes the deployment unit,
not necessarily the quality of its internal design.

```text
Clients → One application deployment → One or more data stores
```

Typical traits:

- One application is built and released as a unit.
- Features usually run in the same process or deployment boundary.
- Modules may share runtime resources and database access.
- Calls between modules can be ordinary in-process calls.

A monolith can be internally modular and can scale horizontally by running
multiple instances. Its common limitation is that the application is generally
scaled and released as a whole rather than scaling or releasing one business
area independently.

### 1.2 Modular monolith

A modular monolith is still deployed as one application, but its code is divided
into modules with explicit responsibilities and boundaries.

```text
One deployment
├── Order module
├── Payment module
├── Inventory module
└── Notification module
```

Modules communicate through defined interfaces or internal events. They avoid
reaching into one another’s tables or implementation details. Modules may share
one database server, but should have clear ownership of their tables and data.

It is often a strong default when a product needs internal structure but does
not yet need independently deployed services.

### 1.3 Microservices

Microservices split an application into independently deployable services,
usually organized around business capabilities or bounded contexts. Each service
owns its data and communicates with other services through network APIs or
messages.

```text
Clients → Edge/API layer
             ├── Order Service → Order data
             ├── Payment Service → Payment data
             └── Notification Service → Notification data
```

Services can be deployed and scaled independently, but that independence must be
real. If every change requires coordinated releases or every request calls a
long chain of services, the system may be distributed in deployment but still
tightly coupled in practice.

---

## 2. Important distinction: deployment vs code structure

These are separate dimensions:

- A **monolith** can have clean modules or tangled code.
- **Microservices** can have clear boundaries or be tightly coupled.
- A single deployment does not automatically mean poor scalability.
- Multiple deployments do not automatically mean independent teams or fault isolation.

Avoid using “monolith” as shorthand for “bad design.” A well-structured
monolith may be easier to evolve than a poorly structured microservices system.

---

## 3. Comparison at a glance

| Area                  | Monolith                                  | Modular monolith                                       | Microservices                                                      |
|-----------------------|-------------------------------------------|--------------------------------------------------------|--------------------------------------------------------------------|
| Deployment            | One application unit                      | One application unit                                   | Multiple independent service deployments                           |
| Internal boundaries   | May be weak or strong                     | Explicit module boundaries                             | Service boundaries across processes                                |
| Communication         | In-process calls                          | In-process interfaces/events                           | Network APIs and/or messaging                                      |
| Scaling               | Usually scale the application as a whole  | Usually scale the application as a whole               | Scale selected services independently                              |
| Data ownership        | Often shared; depends on design           | Modules should own their tables/data                   | Each service owns its data; no direct shared-database access       |
| Transactions          | Straightforward local transactions        | Local transactions across modules may share a database | Cross-service workflows need coordination and eventual consistency |
| Failure isolation     | Process failures may affect the whole app | Similar deployment-level failure boundary              | Can isolate failures if dependencies are designed well             |
| Testing               | Simpler end-to-end setup                  | Good module tests plus app integration tests           | Contract, integration, and end-to-end testing across services      |
| Deployment complexity | Lower                                     | Lower to moderate                                      | Higher: pipelines, routing, observability, versioning, operations  |
| Team autonomy         | Lower as codebase/team grows              | Better code ownership; one release unit                | Can be high with clear ownership and independent delivery          |
| Operational cost      | Usually lowest                            | Usually low to moderate                                | Usually highest due to distributed infrastructure and support      |

These are typical tendencies, not guarantees. A modular monolith can be highly
scalable at the application tier. Microservices can be poorly isolated or
expensive if their boundaries and operations are weak.

---

## 4. Advantages and costs

### Monolith

**Advantages**

- Straightforward local development and debugging
- Simple deployment and fewer infrastructure components
- In-process calls avoid network latency and remote-call failures
- Easier transactions within one database
- Often efficient for a small team or early product

**Costs and risks**

- Modules can become tightly coupled without discipline
- A release may require deploying the whole application
- A busy feature can require scaling the whole application
- Large builds and tests may slow delivery as the codebase grows
- A process-level failure can affect multiple features

### Modular monolith

**Advantages**

- Clear code ownership and internal boundaries with one deployment
- Easier refactoring while product boundaries are still changing
- In-process communication and local database transactions remain available
- Lower operational burden than many independently deployed services
- Creates a useful foundation for extracting a service later, if needed

**Costs and risks**

- Requires discipline to enforce module boundaries
- Modules still share deployment and process-level failure boundaries
- Usually cannot scale or release only one module independently
- Shared database access can create hidden coupling if ownership is not enforced

### Microservices

**Advantages**

- Independent deployment when APIs and ownership are designed well
- Selective scaling for services with different traffic profiles
- Clear business and operational ownership can support autonomous teams
- A failure can be contained when callers use timeouts, bulkheads, and fallbacks
- Different implementation choices are possible when there is a real need

**Costs and risks**

- Network calls introduce latency, timeouts, partial failures, and retries
- Data is distributed; cross-service workflows are eventually consistent
- Requires service discovery/routing, observability, security, and automation
- Testing and local development require multiple components or realistic substitutes
- Versioning and compatibility must be managed across independently deployed services
- On-call and operational load increase
- More services can increase infrastructure and engineering cost

---

## 5. When each architecture fits

### A monolith is often a good fit when

- The product is new and the domain is still changing.
- One small team owns most of the application.
- The system has moderate scale and no strong need for independent scaling.
- Fast development and simple operations are important.
- Cross-feature transactions are common and need simple consistency.

### A modular monolith is often a good fit when

- The codebase has multiple business domains but one coordinated release is acceptable.
- The team wants clearer boundaries without distributed-system overhead.
- Domain boundaries are not yet stable enough to split into services.
- The organization wants a path to extract only proven boundaries later.
- Strong internal ownership and testing can be established.

### Microservices may be justified when

- Business capabilities have stable, well-understood boundaries.
- Multiple teams need to build, deploy, and operate independently.
- Some capabilities have materially different scaling requirements.
- A service needs a distinct availability, security, or compliance boundary.
- Independent release cadence provides real business value.
- Teams have the automation, observability, security, and on-call maturity to operate distributed systems.

High traffic alone does not automatically justify microservices. A well-designed
monolith can serve high traffic by scaling horizontally, tuning the database,
using caches, and isolating background work. Split services when independent
ownership, scaling, deployment, or fault boundaries are worth the added cost.

---

## 6. Decision framework

Before choosing microservices, answer these questions:

1. **Domain:** Are the business boundaries understood and relatively stable?
2. **Team:** Do separate teams need independent ownership and release schedules?
3. **Scaling:** Is one part of the system a proven bottleneck with distinct scaling needs?
4. **Reliability:** Does a capability require a separate failure or availability boundary?
5. **Data:** Can the capability own its data without frequent cross-service transactions?
6. **Operations:** Can the organization monitor, secure, deploy, and support the services?
7. **Economics:** Does the benefit justify additional infrastructure and engineering cost?

If the domain is uncertain, the team is small, and independent scaling is not
proven, start with a modular monolith and measure real behavior. Do not treat
this as a rule that services must never be extracted; it is a way to delay
irreversible complexity until there is evidence for it.

### Simple scoring prompt

For each candidate service boundary, rate the following from low to high:

| Question                             | Low score suggests                       | High score suggests                     |
|--------------------------------------|------------------------------------------|-----------------------------------------|
| Is the boundary stable and cohesive? | Keep it in a module while learning       | Candidate for a separate service        |
| Does it need independent deployment? | Shared release is acceptable             | Independent release has clear value     |
| Does it have distinct scaling needs? | Scale the application together           | Separate scaling may justify extraction |
| Can it own its data?                 | Strong cross-domain transaction coupling | Clear data ownership is feasible        |
| Can the team operate it?             | Added service may be a burden            | Operational readiness exists            |

This is a discussion aid, not a mechanical score that decides architecture.

---

## 7. Service boundaries and data ownership

Good service boundaries align with business capabilities, not technical layers.

Weak split:

```text
Controller Service → Business Logic Service → Database Service
```

This often creates a distributed version of a layered monolith: one user action
must synchronously cross several services with tightly coordinated changes.

More cohesive split:

```text
Order Service owns order lifecycle and order data
Payment Service owns payment lifecycle and payment data
Notification Service owns notification preferences and delivery state
```

With microservices, one service should not directly query or update another
service’s tables. Use an API or event contract. Data duplication for local reads
can be appropriate if the ownership and synchronization rules are clear.

---

## 8. Transactions and consistency

Inside a monolith or modular monolith, modules may often use one database
transaction. This makes atomic updates simpler, though module ownership should
still be respected.

Across microservices, a single ACID transaction usually cannot safely span
multiple service databases. Distributed workflows commonly use:

- Events and eventual consistency
- Transactional outbox/inbox patterns
- Idempotent consumers
- Sagas with compensating actions
- Explicit workflow state and reconciliation

Example order flow:

```text
Order created → Payment requested → Inventory reserved → Order confirmed
```

If inventory reservation fails after payment succeeds, the workflow needs a
defined compensation, such as refunding or voiding the payment. This complexity
must be justified by the business and organizational benefits of separation.

---

## 9. Failure isolation is not automatic

Separate processes can fail independently, but service dependencies can still
spread failures. For example, if every API request synchronously calls five
services and one is slow, the entire user request may be slow or unavailable.

Microservices need deliberate resilience:

- Timeouts on network calls
- Bounded retries with backoff and jitter
- Circuit breakers where appropriate
- Bulkheads and concurrency limits
- Graceful degradation and fallbacks
- Queues for work that can be asynchronous
- Clear ownership of error handling and recovery

If services must always be deployed together, share a database, and make
synchronous calls through long chains, they may be a **distributed monolith**.

---

## 10. Common failure patterns

### Distributed monolith

Services are separately deployed but tightly coupled through shared schemas,
synchronous call chains, coordinated releases, or shared database tables.

**Improve it:** Clarify ownership, stabilize contracts, reduce unnecessary
synchronous dependencies, and extract only boundaries that can be independently
changed.

### Premature microservices

Services are created before domain boundaries, traffic needs, or team ownership
are understood.

**Improve it:** Start with a modular monolith, record boundaries, measure
bottlenecks, and extract when a concrete need appears.

### Shared database across services

Several services read and write the same tables, creating hidden coupling and
unsafe schema changes.

**Improve it:** Give each service ownership of its data. Migrate consumers to
APIs or events; use replicated/read-model data where justified.

### Too-small services

Every entity or CRUD operation becomes its own service, creating excessive
network calls and operational overhead.

**Improve it:** Group behavior around cohesive business capabilities and data
ownership, not class or table boundaries.

### One giant service

A service owns unrelated domains, teams, and workloads, so its boundary provides
little autonomy or scaling advantage.

**Improve it:** Revisit bounded contexts and ownership based on evidence, then
extract only where the boundary is stable and useful.

---

## 11. Migration: monolith toward services

Avoid a large “big-bang” rewrite when a gradual migration can reduce risk.

### Strangler Fig approach

1. Identify a cohesive business capability to extract.
2. Define its API/event contract and data ownership.
3. Route one part of the workload to the new service.
4. Keep old and new behavior observable and validate results.
5. Move more traffic when correctness and operations are proven.
6. Remove the old implementation after consumers have migrated.

### Preparation that helps

- Create module boundaries before extraction.
- Avoid cross-module database access.
- Add tests around the behavior to be moved.
- Track API latency, failure rate, and business outcomes.
- Plan data migration, backfill, rollback, and reconciliation.
- Define how old and new versions coexist during deployment.

Do not extract a service solely because the code is large. Extract when an
independent boundary solves a real deployment, scaling, ownership, compliance,
or reliability problem.

---

## 12. Worked example: order and notification platform

Suppose a team is building an e-commerce product with these capabilities:

- Orders
- Payments
- Inventory
- Customer notifications

### Option A: Modular monolith

```text
Commerce application
├── Order module
├── Payment module
├── Inventory module
└── Notification module
```

Good starting choice if one team owns the product, workflows change frequently,
and shared transactions simplify correctness. Enforce module interfaces and
table ownership so future separation remains possible.

### Option B: Microservices

```text
Order Service → Order DB
Payment Service → Payment DB
Inventory Service → Inventory DB
Notification Service → Notification DB + delivery queue
```

This may fit when separate teams own these capabilities, payment has distinct
security/compliance needs, notifications need independent scaling, and the
organization can operate asynchronous workflows and observability.

### Decision considerations

- **Notification delivery can scale independently:** A queue and workers may be
  enough initially; the whole notification domain need not immediately become a
  separate service.
- **Payment isolation may be valuable:** A separate security boundary can
  justify an independent service, but introduces distributed transaction and
  reconciliation work.
- **Order and inventory consistency matters:** Define reservation and compensation
  behavior if split across services.
- **Team ownership matters:** If one team must coordinate every change, separate
  services may add overhead without real autonomy.

The choice depends on evidence about teams, change rate, scaling, compliance,
and operations—not just anticipated traffic.

---

## 13. Interview answer structure

When asked “Should we use microservices?”, answer in this order:

1. Clarify product scope, teams, traffic, and independent availability needs.
2. State whether domain boundaries are already understood.
3. Recommend the simplest architecture that meets current needs.
4. Explain the operational and data-consistency costs of microservices.
5. Describe the evidence that would justify extracting a service later.

Example:

> I would begin with a modular monolith because the domain and traffic profile
> are still evolving and one team owns the product. I would enforce module and
> data boundaries, then extract notification delivery if its scaling or release
> requirements become distinct. I would choose microservices earlier only if
> independent team ownership, compliance, or availability requirements already
> justify the operational and consistency costs.

---

## 14. Design checklist

- [ ] What problem is the architecture choice solving?
- [ ] Are the business boundaries stable and cohesive?
- [ ] Can modules/services be changed independently?
- [ ] Who owns each module, service, and data set?
- [ ] Is independent scaling actually required and measured?
- [ ] Are cross-domain transactions and consistency needs understood?
- [ ] Can the team operate the number of deployments being proposed?
- [ ] Are monitoring, deployment automation, security, and on-call ready?
- [ ] Could a modular monolith meet the same requirements at lower cost?
- [ ] Is there a gradual migration and rollback path?

---

## Practice exercise

A small team of six engineers is building a new commerce platform. It expects
moderate traffic initially, but notification campaigns may create large bursts.
The domain is still changing. The team has CI/CD and container experience, but
limited production experience with Kafka and multi-service operations.

1. Which architecture would you start with, and why?
2. How would you structure module boundaries and data ownership?
3. How would you handle notification bursts without immediately splitting the
   whole application into microservices?
4. What evidence would justify extracting the Notification capability later?
5. Name two risks if the team starts with many microservices now.

### Reference answers

#### 1. Starting architecture

Start with a **modular monolith**. The domain is changing, the team is small,
initial traffic is moderate, and the team has limited experience operating
distributed systems. Clear modules provide structure while keeping deployment,
debugging, and transactions relatively simple.

#### 2. Module boundaries and data ownership

Create business-oriented modules such as `orders`, `payments`, `inventory`, and
`notifications`. Give each module clear interfaces and ownership of its tables.
Avoid direct access to another module’s tables; communicate through module APIs
or internal events. This preserves boundaries even though the application is
deployed as one unit.

#### 3. Handling notification bursts

Persist notification work to a durable queue or an appropriate asynchronous
work mechanism, then process it with bounded workers. Use priority for
transactional versus promotional messages, respect provider rate limits, and
monitor queue depth and oldest-message age. This isolates bursty delivery work
without requiring every business capability to become a separate service.

#### 4. Evidence for extraction

Consider extraction when there is a concrete, sustained need, such as:

- Notification delivery requires independent scaling or availability.
- A separate team needs to deploy and operate it independently.
- Provider integrations or compliance require a distinct boundary.
- The module has stable interfaces and can own its data.
- Operational tooling, monitoring, deployment, and on-call support are ready.

#### 5. Risks of starting with many microservices

- Increased operational and on-call burden before the team has experience.
- Distributed transactions, retries, partial failures, and eventual consistency
  across a domain that is still changing.
- Coordinated schema/API changes may create a distributed monolith.
- More pipelines, infrastructure, security configuration, and end-to-end testing.

Any two of these are valid; the key is connecting the risk to the team’s current
context.

---

## Key takeaway

> Start with clear boundaries. Deploy them separately only when independent
> scaling, ownership, reliability, compliance, or release cadence makes the
> extra distributed-systems cost worthwhile.

