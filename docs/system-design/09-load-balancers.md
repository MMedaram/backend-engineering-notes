---
title: Load Balancers
parent: System Design
nav_order: 9
---

# Load Balancers

> A load balancer gives clients a stable entry point and distributes incoming
> traffic across a set of backend instances.

It can help spread work, route around unhealthy instances, and scale application
instances without exposing each backend directly. It does not, by itself, make
every part of a system highly available: the load balancer, applications,
databases, and downstream dependencies all need appropriate reliability.

---

## 1. Basic model

Instead of clients choosing a server, they connect to a stable endpoint. The
load balancer selects a backend from a configured backend set.

```text
Clients
   ↓
Load balancer
   ├── App instance A
   ├── App instance B
   └── App instance C
```

Common responsibilities include:

- Distributing requests or connections across backends
- Checking backend health and routing around failed instances
- Providing a stable frontend address while the backend pool changes
- Terminating TLS, depending on the design
- Routing by protocol, host, path, or other request attributes when supported
- Managing backend connections and timeouts

---

## 2. Request path

A typical internet-facing application request may travel through:

```text
Client
  → DNS
  → CDN or edge service (optional)
  → Public load balancer
  → Application backend
  → Database/cache/other services
  → Response
```

A larger system may also include an API Gateway, ingress controller, or internal
load balancer. These roles can overlap in one product, but their common focus
differs:

- A **load balancer** mainly distributes traffic among backends.
- An **API Gateway** commonly handles API policies such as authentication,
  quotas, request transformation, and API composition.
- A **reverse proxy** forwards requests and may also provide TLS termination,
  caching, routing, or security controls.

One product can provide more than one of these functions.

---


## 3. Layer 4 vs Layer 7 load balancing

### Layer 4: transport-level balancing

Layer 4 operates mainly on connection and network information such as IP
addresses, ports, and TCP/UDP protocols.

```text
Client TCP/UDP flow → selected backend flow
```

Typical characteristics:

- Does not need to understand the full HTTP request.
- Can handle TCP/UDP and other supported IP traffic.
- Often has lower processing overhead.
- Routing is generally based on connection/flow information.

### Layer 7: application-level balancing

Layer 7 understands application protocols such as HTTP/HTTPS and can inspect
request metadata. It can route based on:

- Host, such as `api.example.com`
- Path, such as `/orders` or `/payments`
- HTTP headers or cookies
- HTTP method
- Other supported request attributes

Example:

```text
/orders/*   → Order backend set
/payments/* → Payment backend set
```

Layer 7 offers more routing flexibility, but the balancer needs to process
application traffic. OCI's Load Balancer service supports proxy behavior at
Layer 4 and Layer 7; OCI Network Load Balancer operates at Layers 3/4 and
distributes client connections using IP protocol information.

---

## 4. Load-balancing algorithms

An algorithm chooses which healthy backend receives work. For the examples,
assume three backends: **A**, **B**, and **C**.

### 4.1 Round Robin

Round Robin cycles through the configured backends:

```text
Request/connection 1 → A
Request/connection 2 → B
Request/connection 3 → C
Request/connection 4 → A
Request/connection 5 → B
Request/connection 6 → C
```

**Good fit:** backends have similar capacity and requests take roughly similar
time.

**Weakness:** it counts assignments, not the work each assignment creates. If
one request takes 2 ms and another takes 20 seconds, both count as one turn.
Likewise, a smaller backend may be overwhelmed if it receives the same share as
a larger one.

OCI documents Round Robin as its default policy for the classic Load Balancer.

### 4.2 Weighted Round Robin

Assign each backend a relative weight. For example:

```text
A weight 2
B weight 1
C weight 1
```

Across a sufficiently large sample, A should receive about twice as much work as
B or C. Out of 100 assignments, the approximate distribution is:

```text
A ≈ 50
B ≈ 25
C ≈ 25
```

This helps when backend sizes differ or when gradually moving traffic to a new
version. Short-term counts may vary, depending on the implementation and traffic
volume. OCI supports backend weights with its documented policies; a weight of 3
is intended to receive three times the traffic of a weight of 1.

### 4.3 Least Connections

Choose the backend with the fewest active connections. Suppose:

```text
A: 8 active connections
B: 3 active connections
C: 5 active connections
```

The next connection is routed to **B**.

**Good fit:** requests or sessions last for different lengths of time. A backend
with several long-running connections is less likely to keep receiving new
ones.

**Limitations:** an open connection is only an estimate of workload. An idle
keep-alive connection may count despite doing little work, while one expensive
request may be harder than several cheap ones. OCI notes that active-but-idle
TCP connections may not be a good load measure. Weights can also refine
distribution when capacities differ.

### 4.4 IP Hash

Hash the client source IP to choose a backend. Requests from the same IP tend to
go to the same backend while that backend is available.

```text
Client IP 203.0.113.10 → hash → Backend B
Next request from same IP → Backend B
```

**Useful for:** temporary session affinity or backend-local cache locality when
the application cannot yet avoid it.

**Risks:**

- Many users behind one proxy or NAT can appear as one source IP and be sent to
  the same backend.
- A user's IP may change, causing a different backend selection.
- If a backend becomes unhealthy, mappings may change.
- Uneven client populations can create uneven load.

OCI specifically warns about proxy/NAT clients sharing a source address. Prefer
stateless application instances with shared session storage when possible,
rather than relying on client-IP affinity.

### 4.5 Least response time (common algorithm concept)

Choose a backend that appears to respond fastest, possibly considering active
connections too.

```text
A recent response time: 80 ms
B recent response time: 35 ms
C recent response time: 120 ms
```

A policy using these measurements might prefer B. This can help when backends
perform differently, but measurements are noisy: a brief spike can skew the
choice, and a slow shared dependency may make every backend look bad. Do not
assume this is a built-in option in a particular OCI product without checking
that product's current policy support.

### 4.6 Random and “power of two choices” (common concepts)

A random policy selects a backend randomly. A variation selects two candidates
and sends work to the less-loaded one. This can reduce hotspots without needing
to keep a perfectly synchronized ranking of every backend.

Support varies by load-balancing product; treat this as a general algorithm
concept, not a guarantee of an OCI configuration option.

### 4.7 Consistent hashing (common concept)

Consistent hashing maps a key, such as a user ID, to a backend. When a backend
is added or removed, the design aims to remap only some keys rather than
reshuffling almost all of them.

It is useful in certain cache and distributed-storage designs. It can still
create hot backends if a key is very popular. It is more general than IP Hash,
which uses the source IP as the hashing key. Verify support before expecting it
as a configurable OCI load-balancer policy.

---

## 5. Which algorithm should I choose?

| Workload                                       | Reasonable starting point              | Main caveat                                           |
|------------------------------------------------|----------------------------------------|-------------------------------------------------------|
| Similar backend sizes; short, similar requests | Round Robin                            | Ignores how much work each request creates            |
| Backend capacities differ                      | Weighted Round Robin                   | Weights need measurements and adjustment              |
| Connections have very different durations      | Least Connections                      | Active connection count may not reflect actual work   |
| Temporary client affinity is required          | IP Hash                                | Proxies/NAT can concentrate many users on one backend |
| Highly variable performance                    | Measure first; test supported policies | Response-time or load metrics may be noisy            |

No algorithm can compensate for a saturated database, a slow external provider,
or a badly sized backend pool.

---

## 6. Health checks

Health checks help determine whether a backend should receive traffic.

### Active health checks

The load balancer periodically calls a health endpoint or attempts a connection:

```text
GET /health/ready
```

After configured failures, it can remove the backend from rotation; it can
return the backend after it becomes healthy again.

### Passive health checks

The load balancer observes real requests and detects repeated errors, timeouts,
or connection failures.

### Liveness vs readiness

- **Liveness:** Should this process be restarted?
- **Readiness:** Is this instance ready to receive traffic?

An instance can be alive but not ready—for example, it is warming up. Health
checks should avoid making every downstream outage restart all application
instances or remove the entire backend pool.

---

## 7. TLS termination and connection handling

### TLS termination at the load balancer

```text
Client --HTTPS--> Load balancer --HTTP or HTTPS--> Application
```

The load balancer decrypts client traffic. This can simplify certificate
management on application instances. Whether traffic is encrypted again between
the load balancer and backend depends on the security requirements and network
design.

### TLS pass-through

```text
Client --encrypted connection passes through--> Backend terminates TLS
```

The load balancer does not decrypt application traffic. This preserves backend
control of TLS but limits HTTP-aware routing unless the product can inspect
enough metadata without terminating the connection.

### Connection reuse and timeouts

A load balancer may reuse connections to backends, reducing connection setup
overhead. Align idle timeouts and keep-alive settings across clients, balancer,
application, and downstream services to avoid unexpected disconnects.

---

## 8. Session stickiness

**Sticky sessions** try to route a client repeatedly to the same backend. They
can support legacy applications that store sessions in local memory, but have
drawbacks:

- Traffic can become uneven.
- Backend failure can lose local session state.
- Scaling and deployments become harder.
- A user's IP or cookie can change.

Prefer stateless application instances and shared session storage. Use stickiness
only when necessary and document its failure behavior.

---

## 9. Load balancers and autoscaling

A load balancer distributes traffic, but it does not decide by itself how many
instances should exist. An autoscaling system may add instances based on:

- CPU or memory use
- Request rate or active requests
- Response latency
- Queue lag
- Custom application metrics

The load balancer then detects when new instances are ready and begins routing
traffic to them. Consider:

- Instance warm-up time and readiness checks
- Graceful connection draining during scale-in
- Maximum scaling limits and cost
- Database/provider capacity when application instances increase

---

## 10. High availability and load-balancer failure

A single load balancer can itself be a single point of failure. Cloud load
balancing services usually offer managed redundancy, but the design still needs
to consider:

- Availability zones or other failure domains
- Failover behavior and health detection
- DNS and client-side caching
- Connection draining
- Configuration rollout and rollback
- Capacity limits and monitoring

A managed service reduces some operational work; it does not remove the need to
understand the availability model and limits.

---

## 11. Retries and failure handling

A load balancer can route around an unhealthy backend, but it cannot make an
unsafe retry safe.

Suppose a payment request reaches an application, the payment is processed, but
the response is lost. If a client or proxy retries the request at another
backend, payment may be processed twice unless the operation is idempotent.

For state-changing operations:

- Use idempotency keys where appropriate.
- Make retry policies explicit.
- Do not blindly retry requests with unknown outcomes.
- Align timeouts across the request path.
- Drain connections during deployment and scale-in.
- Monitor backend errors and load-balancer rejections.

---

## 12. Notification Service example

Suppose the Notification API runs on three instances:

```text
Producer systems
      ↓
Public load balancer
  ├── Notification API A
  ├── Notification API B
  └── Notification API C
      ↓
Durable queue → Delivery workers → Providers
```

The load balancer handles incoming API traffic. The queue and worker pool handle
asynchronous delivery separately.

If API instance B becomes unhealthy:

1. Health checks detect the failure.
2. The load balancer stops sending new traffic to B.
3. Requests continue to A and C.
4. B can rejoin after it passes readiness checks.
4. Accepted jobs remain safe only if they were durably stored before the API acknowledged them.

The load balancer does not solve provider outages. If SMS is down, the API
instances can remain healthy while SMS delivery queues up.

---

## 13. Common mistakes

- Assuming a load balancer alone guarantees high availability.
- Treating a direct public IP association as load balancing.
- Using round robin when backends have different capacities or request costs.
- Confusing liveness and readiness checks.
- Making readiness depend on every downstream service.
- Keeping important session state only on one backend and relying on stickiness.
- Automatically retrying non-idempotent requests.
- Ignoring connection draining during deployments.
- Scaling application instances without checking database/provider capacity.
- Treating an API Gateway, reverse proxy, and load balancer as identical.

---

## 14. Design checklist

- [ ] Is balancing needed at Layer 4, Layer 7, or both?
- [ ] What resource owns the public IP: compute, load balancer, or appliance?
- [ ] Does the listener route to the correct backend set and port?
- [ ] What algorithm fits request duration and backend capacity?
- [ ] Are backend weights based on measured capacity?
- [ ] Are instances stateless, or is session stickiness required?
- [ ] What health check determines readiness to receive traffic?
- [ ] How are failed instances removed and reintroduced?
- [ ] Is TLS terminated at the load balancer or backend?
- [ ] Are connection timeouts and keep-alive settings aligned?
- [ ] Are requests safe to retry?
- [ ] Can deployments drain connections gracefully?
- [ ] Can the load balancer itself fail over?
- [ ] What metrics show saturation or uneven routing?
- [ ] Can dependencies handle extra traffic when app instances scale?

---

## Practice exercise

A service has four backend instances. One instance has twice the capacity of
each of the other three. Requests also have very different processing times.

1. Why might plain Round Robin distribute load poorly?
2. Which balancing strategy could account for the capacity difference?
3. Why might Least Connections be more suitable than Round Robin for
   variable-duration requests, and what are its limits?
4. What should happen when a backend fails its readiness check?
5. Should the load balancer automatically retry a `POST /payments` request
   after a timeout? What must be true for that to be safe?

### Reference answers

#### 1. Round Robin with mixed capacity and request duration

Plain Round Robin gives each backend a similar number of assignments. The
stronger instance may be underused, while weaker instances can be overloaded.
It also does not account for request cost: a long-running operation and a quick
one each consume one turn.

#### 2. Account for capacity differences

Use **Weighted Round Robin** if supported by the selected load balancer. Assign
the double-capacity instance a larger weight, then validate the traffic split
with measurements. Weight is a relative share, not a guarantee that response
times or CPU utilization will be exactly equal.

#### 3. Least Connections for variable-duration work

Least Connections sends new work to the backend with fewer active connections.
This can help when some requests remain active much longer than others.

Its limit is that connection count only approximates work. Idle keep-alive
connections may count, and requests can have very different CPU or database
costs. Monitor actual latency and saturation too.

#### 4. Backend fails readiness

Stop routing new requests to that backend, allow healthy instances to continue
serving traffic, and alert if capacity or availability is at risk. Reintroduce
the backend only after it passes readiness checks. During deployment, drain
existing connections gracefully where possible.

#### 5. Retrying `POST /payments`

Do not blindly retry: the payment may have succeeded even though the response
timed out. Retrying is safe only if the payment operation is idempotent—for
example, the caller supplies a stable idempotency key and the payment system
ensures repeated requests with that key do not create another charge. The
outcome should be reconciled or queried when it is unknown.

