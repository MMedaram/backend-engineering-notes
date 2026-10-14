---
title: Reverse Proxy
parent: System Design
nav_order: 10
---

# Reverse Proxy

> A reverse proxy receives a client's request, forwards it to a backend server,
> and returns the backend's response to the client.

The client knows the application's public address. The reverse proxy knows how
to reach the backend application.

## Learning outcomes

- Explain a reverse proxy using a simple request example.
- Distinguish proxying, load balancing, service discovery, and API Gateway responsibilities.
- Understand routing, TLS termination, forwarded headers, and path rewriting.
- Recognize common timeout, caching, and streaming problems.
- Connect the concept to an on-premises Spring Boot application.

---

## 1. Start with a simple example

Your Order Service runs on a private server:

```text
Backend application: http://10.20.1.11:8080
```

Customers use a friendly address:

```text
Public application: https://api.company.com
```

The reverse proxy connects these two addresses. A customer requests:

```http
GET https://api.company.com/orders/123
```

The proxy forwards the request to:

```http
GET http://10.20.1.11:8080/orders/123
```

The Order Service responds to the proxy. The proxy sends that response to the
customer. The customer's browser keeps using `api.company.com`.

**Simple analogy:** a reception desk receives a visitor's request, passes it to
the right department, and brings back the answer. The visitor does not need to
know the department's internal room number.

---

## 2. Request flow, step by step

| Step | What happens                                                    |
|------|-----------------------------------------------------------------|
| 1    | The client resolves the public hostname through DNS.            |
| 2    | The client connects to the reverse proxy's endpoint.            |
| 3    | The proxy matches the hostname/path to a routing rule.          |
| 4    | It forwards the request through a backend connection.           |
| 5    | The backend executes its business logic and returns a response. |
| 6    | The proxy returns the response to the client.                   |

For a typical HTTP reverse proxy, the client connection and backend connection
are separate. Backend connections can be reused; every request does not
necessarily require a new connection.

The backend is often called the **upstream** in proxy configuration and logs.

### Proxying is different from an HTTP redirect

| Behavior      | What the client does                                                        |
|---------------|-----------------------------------------------------------------------------|
| Reverse proxy | Receives the result through the same public endpoint.                       |
| HTTP redirect | Receives a redirect response and makes another request to the supplied URL. |

A reverse proxy normally forwards the request itself; it does not tell the
client to connect directly to the private backend.

---

## 3. Forward proxy vs reverse proxy

| Type          | Represents                          | Simple example                                                                  |
|---------------|-------------------------------------|---------------------------------------------------------------------------------|
| Forward proxy | Clients making outbound requests    | Employees access external websites through a company proxy.                     |
| Reverse proxy | Servers receiving incoming requests | Customers access private Spring Boot applications through a public entry point. |

With a forward proxy, the client often explicitly configures a proxy. With a
reverse proxy, the client normally uses the application's regular URL.

---

## 4. Reverse proxy, load balancer, API Gateway, and Eureka

These roles can coexist in one application or infrastructure product.

| Role              | Question it answers                                | Example                                                |
|-------------------|----------------------------------------------------|--------------------------------------------------------|
| Reverse proxy     | Who forwards the request and returns the response? | NGINX forwards a request to Spring Boot.               |
| Load balancer     | Which backend instance receives the work?          | Select one of three Order Service instances.           |
| API Gateway       | What API route and policies apply?                 | Route `/orders/**`, validate a token, enforce a quota. |
| Service discovery | Where are the available service instances?         | Eureka provides registered addresses.                  |

### Example A: one backend

NGINX forwards every request to one Spring Boot instance. It is acting as a
reverse proxy, without distributing traffic across multiple backends.

### Example B: three backends

NGINX forwards requests to a group of three instances and selects one for each
assignment. It performs both reverse proxying and load balancing.

### Example C: your gateway and Eureka

If your API Gateway receives a request, discovers an instance, calls it, and
relays its response, it performs a reverse-proxy role as well as gateway duties.
You do not automatically need a separate NGINX layer.

For a discovery-based gateway route, the responsibilities are:

1. Gateway matches the incoming route.
2. Discovery client supplies registered instance addresses from Eureka information.
3. Load-balancing component selects an instance.
4. Gateway proxies the request and response.

Eureka is not normally in the request's data path. Clients cache registrations,
so they do not need to query Eureka for every request. Internal service calls
may also bypass the gateway and use their own discovery/balancing clients.

---

## 5. Why use a reverse proxy?

| Benefit                      | Simple explanation                                                            |
|------------------------------|-------------------------------------------------------------------------------|
| Stable address               | Clients keep using one hostname while backends change.                        |
| Private backend access       | Backend applications can be reachable only through the intended network path. |
| Central TLS handling         | Certificates can be managed at the entry point.                               |
| Routing                      | Requests can go to different applications based on host or path.              |
| Traffic policies             | Supported products can apply request limits and access rules.                 |
| Optional caching/compression | Eligible responses can use less backend work or bandwidth.                    |

These features require configuration. A proxy does not automatically provide
authentication, correct authorization, durable writes, or safe retries.

---

## 6. Routing examples

### Host-based routing

| Public hostname       | Backend               |
|-----------------------|-----------------------|
| `orders.company.com`  | Order application     |
| `reports.company.com` | Reporting application |

### Path-based routing

| Public path         | Backend              |
|---------------------|----------------------|
| `/orders/**`        | Order Service        |
| `/products/**`      | Product Service      |
| `/notifications/**` | Notification Service |

### Path rewriting

Suppose the public API accepts:

```text
/api/orders/123
```

But the backend controller accepts:

```text
/orders/123
```

The proxy must remove `/api` before forwarding. If it preserves the wrong path,
the backend may return `404`, even though the backend is running correctly.

---

## 7. TLS termination: where HTTPS ends

The client can use HTTPS while the proxy uses HTTP to the backend:

```text
Client → HTTPS → Proxy → HTTP → Spring Boot
```

The proxy owns the public certificate and decrypts the request. This is **TLS
termination**.

If the backend connection also uses HTTPS:

```text
Client → HTTPS → Proxy → HTTPS → Spring Boot
```

there are two encrypted connections. This is commonly called TLS re-encryption.
Choose the backend encryption and certificate validation according to your
security requirements.

**Remember:** HTTPS on the public connection does not automatically encrypt
every internal hop.

---

## 8. Forwarded headers: preserving the original request details

Consider:

```text
Customer URL: https://api.company.com/orders
Backend URL:  http://10.20.1.11:8080/orders
```

Without proxy information, Spring Boot might see an internal hostname, HTTP,
and the proxy's IP rather than the original request details.

This can cause incorrect redirects, generated links, or client-address logs.

| Header              | Purpose                                                 |
|---------------------|---------------------------------------------------------|
| `X-Forwarded-Proto` | Original protocol, such as `https`                      |
| `X-Forwarded-Host`  | Original public hostname                                |
| `X-Forwarded-Port`  | Original public port                                    |
| `X-Forwarded-For`   | Client address and, depending on setup, the proxy chain |
| `Forwarded`         | Standardized forwarding information                     |

Spring Boot can be configured to interpret forwarding information. The exact
strategy must fit the web server, framework version, and trusted proxy setup.

### Why trust matters

A client can submit a fake header:

```http
X-Forwarded-For: 127.0.0.1
```

That does not prove the request came from localhost. At the boundary of trust,
the proxy must remove or overwrite untrusted forwarding headers and supply the
intended values. The backend should accept traffic through the approved proxy
path and use forwarding information according to that trust configuration.

---

## 9. Small NGINX example

This learning example forwards HTTP requests to one Spring Boot instance:

```nginx
server {
    listen 80;
    server_name api.example.internal;

    location /api/ {
        proxy_pass http://10.20.1.11:8080;
        proxy_set_header Host $host;
    }
}
```

| Configuration           | Meaning                                                  |
|-------------------------|----------------------------------------------------------|
| `listen 80`             | Accept HTTP connections on port 80.                      |
| `server_name`           | Identify the hostname this server configuration handles. |
| `location /api/`        | Match this path prefix.                                  |
| `proxy_pass`            | Forward matching requests to this backend.               |
| `proxy_set_header Host` | Set the Host header sent to the backend.                 |

With this form of `proxy_pass`, `/api/orders/123` remains `/api/orders/123` at
the backend. A URI-bearing form such as `proxy_pass http://10.20.1.11:8080/;`
has different path-replacement behavior. Trailing slashes matter.

This example shows forwarding; a deployed configuration also needs the relevant
TLS, access, timeout, header-trust, and failure settings.

---

## 10. Buffering, caching, and streaming

### Buffering

A proxy can temporarily store request or response data:

- **Request buffering:** Receive an upload before sending it to the backend.
- **Response buffering:** Receive the backend response, then serve it to a slower client.

Buffering can protect backend resources from slow clients, but uses proxy memory
or disk. Large uploads and streaming responses need special attention.

### Caching

Caching a public product description can reduce repeated backend work. Caching
`/my-orders` requires much more care because the response belongs to a specific
user. Define cache keys, eligibility, freshness, and invalidation to avoid
returning one customer's data to another.

### WebSockets and Server-Sent Events

Long-lived and streaming connections need suitable idle timeouts and buffering
behavior. WebSockets also require correct protocol-upgrade handling. A
configuration suitable for short REST calls may delay streamed events or close
a long-lived connection too early.

---

## 11. Timeouts and error diagnosis

| Setting or signal         | Meaning                                                                                |
|---------------------------|----------------------------------------------------------------------------------------|
| Connect timeout           | Time allowed to establish a backend connection                                         |
| Read timeout              | Often the permitted inactivity while waiting for response data; product semantics vary |
| Idle timeout              | How long an unused connection may remain open                                          |
| `502 Bad Gateway`         | Typically the proxy could not obtain a valid upstream response                         |
| `504 Gateway Timeout`     | Typically an upstream operation exceeded a proxy timeout                               |
| `503 Service Unavailable` | May indicate unavailable capacity or backends, or come from the application            |

Status codes are clues. Check which component generated the response, proxy
logs, upstream timing, and application logs before deciding the cause.

### Payment timeout example

1. The proxy forwards a payment request.
2. The backend completes the payment.
3. The response is delayed or lost.
4. The proxy returns a timeout to the client.

The payment may already have succeeded. Blindly retrying can charge again. Use
stable idempotency keys and a way to reconcile unknown outcomes. Keep retries
bounded across clients, proxies, gateways, and applications.

---

## 12. Availability and operations

A single proxy process can become a single point of failure. A production design
must consider redundant proxy instances, how clients reach healthy instances,
configuration changes, connection draining, and failure monitoring.

Useful measurements include:

- Incoming request rate and latency
- Upstream connection and response timings
- Error rates by route and backend
- Active connections and resource use
- Request-size rejections and timeout counts
- Request/correlation IDs across proxy and application logs

Adding a proxy layer adds another operational component. Give it a clear role
and make its failures observable.

---

## 13. Notification Service example

A producer calls `POST /notifications` through the gateway. Responsibilities
are distributed as follows:

| Component        | Responsibility                                                          |
|------------------|-------------------------------------------------------------------------|
| Proxy/gateway    | Receive traffic, apply configured routing/policies, and relay responses |
| Notification API | Validate, enforce business rules, and durably accept work               |
| Queue            | Retain accepted work for asynchronous processing                        |
| Workers          | Send notifications with bounded concurrency and safe retry handling     |
| Provider         | Accept and deliver email/SMS/push requests                              |

If an SMS provider fails, the API may continue accepting work while durable
queue capacity allows it. The reverse proxy does not deliver queued messages
or guarantee their durability.

---

## 14. Common mistakes and revision checklist

- Confusing proxying with discovery or backend selection.
- Adding another proxy without a clear purpose.
- Misconfiguring path rewriting and causing backend `404` responses.
- Assuming public HTTPS encrypts the internal connection too.
- Trusting forwarding headers from arbitrary clients.
- Caching personalized responses without safe rules.
- Retrying state-changing operations without idempotency.
- Ignoring streaming buffering, WebSocket upgrades, or idle timeouts.
- Assuming one proxy instance provides high availability.
- Treating a proxy timeout as proof that the business operation failed.

Before finalizing a design:

- [ ] Identify the public endpoint and backend endpoints.
- [ ] Define routing and path-rewrite rules.
- [ ] Decide where TLS terminates and whether the backend hop is encrypted.
- [ ] Define trusted forwarding-header handling.
- [ ] Align timeouts and retry policies across the request path.
- [ ] Check caching, uploads, streaming, and WebSocket requirements.
- [ ] Provide failure handling, connection draining, and monitoring.

---

## Practice questions and reference answers

### 1. Is an API Gateway a reverse proxy if it forwards requests and relays responses?

**Answer:** Yes. It performs the reverse-proxy role for those requests. It may
also perform routing, authentication, quotas, and load balancing. The roles can
coexist in the same component.

### 2. A client uses HTTPS, but Spring Boot sees HTTP. What likely happened?

**Answer:** TLS likely terminated at the proxy, which used HTTP for the backend
connection. Trusted `Forwarded` or `X-Forwarded-Proto` information can preserve
the original protocol, with appropriate application configuration.

### 3. Why should the application avoid trusting arbitrary `X-Forwarded-For` headers?

**Answer:** A client can forge them. The trusted edge must remove or overwrite
untrusted values. Otherwise, logs, address-based policies, or rate limits could
use a false client address.

### 4. Public path: `/api/orders/123`. Backend path: `/orders/123`. What must happen?

**Answer:** Configure the proxy or gateway to remove `/api` before forwarding.
Verify the actual upstream URI and query parameters with tests or logs.

### 5. A payment request returns `504`. Why is blindly retrying unsafe?

**Answer:** The backend may have completed the payment before the proxy timed
out. Use a stable idempotency key and query/reconcile the existing operation
instead of treating the timeout as proof that no charge occurred.

### 6. Does a reverse proxy always need multiple backends?

**Answer:** No. Forwarding to one backend is enough to be a reverse proxy.
Selecting among multiple backends adds a load-balancing responsibility.

---

## Quick revision

| Concept           | Remember it as                                            |
|-------------------|-----------------------------------------------------------|
| Reverse proxy     | Receives, forwards, and returns the response              |
| Load balancing    | Chooses an instance                                       |
| Discovery         | Provides instance addresses                               |
| API Gateway       | Applies API routes and policies                           |
| TLS termination   | Public HTTPS ends at this component                       |
| Forwarded headers | Preserve original request details through trusted proxies |

