---
title: Latency, Throughput, and Concurrency
parent: System Design
nav_order: 4
---

# Latency, Throughput, and Concurrency

> These metrics are related, but they describe different things. Confusing them
> leads to poor capacity estimates and systems that become slow under load.

| Metric          | Question it answers                       | Example                                          |
|-----------------|-------------------------------------------|--------------------------------------------------|
| **Latency**     | How long does one operation take?         | “Create-order API responds in 180 ms.”           |
| **Throughput**  | How much work completes per unit time?    | “The service processes 2,000 requests/sec.”      |
| **Concurrency** | How much work is in progress at one time? | “500 requests are being handled simultaneously.” |

A system can have high throughput and poor latency, or low concurrency and good
latency. For a useful performance discussion, consider all three.

---

## Learning outcomes

After this topic, you should be able to:

- Define latency, throughput, and concurrency and distinguish them.
- Interpret latency percentiles such as p50, p95, and p99.
- Estimate in-flight work using Little’s Law.
- Explain how queue buildup increases waiting time and latency.
- Reason about synchronous versus asynchronous work.
- Identify thread-pool, connection-pool, and downstream bottlenecks.
- Choose production metrics that reveal saturation and slow dependencies.

---

## 1. Latency

Latency is the time taken for one operation from start to finish. For an API
request, end-to-end latency may include:

```text
Client sends request
→ network travel
→ load balancer
→ application processing
→ database/cache/external service
→ response returns to client
```

### Example breakdown

```text
Network to API Gateway        20 ms
Load balancer routing          5 ms
Application processing        25 ms
Redis lookup                   3 ms
Database lookup               40 ms
Response network time         20 ms
-----------------------------------
Total latency                113 ms
```

### Common latency terms

| Term                   | Meaning                                                                 |
|------------------------|-------------------------------------------------------------------------|
| **End-to-end latency** | Total time from the client request until the client receives a response |
| **Service latency**    | Time spent inside one application or service                            |
| **Database latency**   | Time spent waiting for and executing database work                      |
| **Network latency**    | Time spent travelling across the network                                |
| **Queue latency**      | Time a message waits before a consumer processes it                     |
| **Processing latency** | Time spent executing CPU/business logic, excluding waiting              |

### Percentiles: average latency is not enough

Imagine 100 requests:

- 95 requests finish in 100 ms.
- 5 requests finish in 5 seconds.

The average can hide the poor experience of those five users. Production systems
therefore track latency percentiles.

| Metric    | Meaning                                                                                 |
|-----------|-----------------------------------------------------------------------------------------|
| **p50**   | 50% of requests are faster than this; the median                                        |
| **p95**   | 95% of requests are faster than this                                                    |
| **p99**   | 99% of requests are faster than this                                                    |
| **p99.9** | 99.9% of requests are faster than this; useful for very high-volume or critical systems |

Example:

```text
p50 = 80 ms
p95 = 200 ms
p99 = 1,500 ms
```

Most requests are fast, but 1% take 1.5 seconds or more. Investigate the slow
tail. Common causes include slow database queries, cache misses, garbage
collection pauses, external dependencies, lock contention, and queueing.

### Make latency targets specific

```text
GET /products/{id}: p95 under 200 ms
POST /orders: p95 under 2 seconds
Payment provider call: timeout after 3 seconds
Transactional notification: queued within 1 second
Promotional notification: may be delayed up to 10 minutes
```

The correct targets depend on the user experience and business requirement.

---

## 2. Throughput

Throughput is the amount of work completed per unit of time.

Common measures include:

- Requests per second (RPS) or queries per second (QPS)
- Messages per second
- Orders per minute
- Payments per second
- Records processed per hour
- Megabytes or gigabytes transferred per second

### Example: notification throughput

```text
1,000,000 notifications/hour
= 1,000,000 / 3,600
≈ 278 notifications/second on average
```

Traffic is rarely uniform. A sale, campaign, or retry storm can create a burst.
If peak load is four times the average:

```text
278 × 4 ≈ 1,112 notifications/second peak
```

Use realistic peak throughput—not just average throughput—when reasoning about
live-service capacity.

### Throughput is not the same as capacity

- **Current throughput** is the work the system is processing now.
- **Maximum tested capacity** is the rate the system can sustain before latency,
  errors, or backlog become unacceptable.
- **Safe operating capacity** is usually lower than the tested maximum, leaving
  room for spikes, failures, deployments, and slower dependencies.

Example:

```text
Current traffic:          500 requests/sec
Maximum tested capacity: 1,500 requests/sec
Safe operating limit:   perhaps 1,000 requests/sec
```

The safe limit should be established by load testing and operational goals, not
chosen as a universal percentage.

---

## 3. Concurrency

Concurrency is the number of operations currently in progress.

For a service, this can mean:

- Requests currently being handled
- Active application threads or event-loop tasks
- Open database queries or connections in use
- In-flight calls to a payment or messaging provider
- Running background jobs
- Messages currently being processed by consumers

### Example

If 100 requests arrive every second and each takes 2 seconds:

```text
100 requests/sec × 2 sec = 200 concurrent requests
```

About 200 requests are active at a time on average.

If latency increases to 10 seconds while traffic remains 100 requests/sec:

```text
100 requests/sec × 10 sec = 1,000 concurrent requests
```

Incoming traffic did not change, but the system now has five times as much work
in progress. That consumes more memory, threads, connections, and queue slots.
Slow dependencies can therefore trigger cascading failures.

---

## 4. Little’s Law

Little’s Law relates average concurrency, throughput, and latency:

```text
Concurrency = Throughput × Latency
L = λ × W
```

Where:

- `L` = average number of in-progress operations
- `λ` = average throughput (operations per second)
- `W` = average time in the system (seconds per operation)

### Example: Order API

```text
Throughput = 500 requests/sec
Average latency = 0.2 sec

Concurrency = 500 × 0.2
            = 100 in-flight requests
```

If a dependency slows the API:

```text
Throughput = 500 requests/sec
Latency = 3 sec

Concurrency = 500 × 3
            = 1,500 in-flight requests
```

The equation uses averages over the same stable system boundary and time
period. It is a useful estimate, not a substitute for measuring actual resource
use or tail latency.

---

## 5. Queueing: where systems become slow

When work arrives faster than it can be processed, it waits in a queue:

```text
Incoming rate > processing rate
→ queue grows
→ waiting time grows
→ end-to-end latency grows
→ timeouts and failures increase
```

### Notification Service example: backlog growth

Assume:

```text
Incoming notifications: 1,200/sec
Worker processing capacity: 1,000/sec
```

The backlog grows by:

```text
1,200 - 1,000 = 200 messages/sec
```

After one minute:

```text
200 × 60 = 12,000 queued messages
```

### Backlog recovery

If traffic drops to 800 messages/sec while workers can process 1,000/sec:

```text
Recovery rate = 1,000 - 800 = 200 messages/sec
```

For a backlog of 12,000 messages:

```text
12,000 / 200 = 60 seconds to recover
```

Queue depth and the age of the oldest message are important signals. A queue is
not healthy merely because it is accepting messages; consumers must also catch
up within the required delivery target.

---

## 6. Synchronous vs asynchronous throughput

### Synchronous request flow

```text
Client → Order Service → Payment Provider → Response
```

The client waits for the downstream work to finish.

Good for:

- Immediate validation
- Returning a final result the user needs now
- Operations that require confirmation before continuing

Risks:

- A slow provider increases user-facing latency.
- Many in-flight calls can exhaust threads or connections.
- A provider outage can spread failure to the calling service.

### Asynchronous request flow

```text
Order Service → Queue → Notification Workers → Provider
```

The producer can return after the work is durably accepted; workers deliver it
later.

Good for:

- Email, SMS, and push notifications
- Analytics and reporting
- Media processing
- Non-critical follow-up work

Risks and responsibilities:

- Monitor queue depth and message age.
- Handle retries and duplicate delivery safely.
- Define ordering and dead-letter behaviour.
- Make delayed completion visible to users or downstream systems where needed.

Choose the model from the business requirement, not fashion. Asynchronous work
can improve request latency, but it does not eliminate processing latency or
capacity limits.

---

## 7. Threads, connection pools, and concurrency limits

Every in-flight request consumes resources, such as:

- Application thread or event-loop capacity
- Heap memory
- Database or HTTP connection
- File handle
- CPU time
- Queue-worker slot

### Database connection-pool example

Assume:

```text
Application instances: 10
Database connections per instance: 30
```

Potential application connections:

```text
10 × 30 = 300 connections
```

If all connections are busy, new requests wait. Waiting increases latency; if
the wait exceeds a request timeout, requests fail. Increasing the pool is not
always the answer: too many concurrent queries can overload the database and
make every query slower.

Use **bounded concurrency**: limit expensive work to a level that downstream
systems can safely handle. Common tools include:

- Connection pools and thread pools
- Semaphores and bulkheads
- Rate limiters
- Queue consumers with controlled concurrency
- Timeouts and circuit breakers

---

## 8. Performance trade-offs

| Change                      | Possible benefit                     | Possible risk                                           |
|-----------------------------|--------------------------------------|---------------------------------------------------------|
| Add application instances   | Higher application throughput        | Database or provider may become the bottleneck          |
| Increase worker concurrency | Faster queue processing              | Provider rate limits, CPU/memory pressure, retry storms |
| Add cache                   | Lower read latency and database load | Stale data and invalidation complexity                  |
| Batch writes/events         | Higher throughput                    | Added waiting time and larger failure scope             |
| Use asynchronous processing | Better user-facing latency           | Eventual consistency and queue-backlog risk             |
| Increase timeout            | Fewer immediate timeout errors       | More threads/connections held; cascading failure risk   |
| Increase connection pool    | More parallel database work          | Database overload and worse latency                     |

Do not optimize one metric blindly. Protect the whole request path and its
dependencies.

---

## 9. Worked example: Notification Service

### 9.1 Traffic assumptions

```text
Notification requests: 1,000,000/hour
Average throughput:    1,000,000 / 3,600 ≈ 278/sec
Peak factor:           4×
Peak throughput:       278 × 4 ≈ 1,112/sec
```

Suppose 20% are transactional and 80% are promotional:

| Type          | Share | Approximate peak rate | Typical handling                                         |
|---------------|------:|----------------------:|----------------------------------------------------------|
| Transactional |   20% |               222/sec | High priority; queue quickly and deliver to its SLO      |
| Promotional   |   80% |               890/sec | Lower priority; may be delayed within its allowed window |

These figures are illustrative. Real traffic mix and peak factors must be
confirmed from business forecasts or measured traffic.

### 9.2 Provider limits

Suppose:

```text
SMS provider capacity:   300 calls/sec
Email provider capacity: 2,000 calls/sec
Push provider capacity:  5,000 calls/sec
```

If 400 notifications per second must use SMS:

```text
Incoming SMS work: 400/sec
Provider capacity: 300/sec
Backlog growth:    100/sec
```

Possible responses:

- Rate-limit calls to the provider and queue the excess.
- Use another channel only if customer preferences and business rules allow it.
- Add a second approved provider if the cost and operational complexity are justified.
- Alert when queue age breaches the transactional delivery objective.

### 9.3 Initial worker estimate

If one worker safely processes 50 provider calls per second:

```text
Peak work:       1,112 calls/sec
One worker:      50 calls/sec
Minimum workers: 1,112 / 50 ≈ 23 workers
```

With approximately 30–50% spare capacity, an initial target might be about
30–35 workers. This estimate assumes work is evenly distributed and the
provider can accept the resulting rate. It must be validated with load tests,
per-channel limits, deployment headroom, and failure scenarios.

---

## 10. What to monitor in production

### Latency

- API p50, p95, and p99
- Database query latency
- External-provider latency
- Queue waiting time
- End-to-end notification delivery time

### Throughput

- Requests and messages per second
- Successful deliveries per second
- Failed deliveries and retries per second
- Provider-specific throughput

### Concurrency and saturation

- Active HTTP requests
- Thread-pool active count and queue size
- Database connection-pool usage and wait time
- Active provider calls
- Kafka consumer lag or queue depth
- CPU, memory, and garbage-collection pauses

### Errors and backlog

- Timeout and error rates
- Retry exhaustion
- Circuit-breaker state
- Dead-letter queue count
- Oldest queued message age

Low CPU does not guarantee a healthy system. A service may be blocked waiting
for a database or provider while CPU remains low.

---

## 11. Common mistakes

- Using average latency instead of p95/p99.
- Designing for average traffic instead of peak and burst traffic.
- Increasing thread or connection pools without checking downstream capacity.
- Treating queue depth as harmless and ignoring message age.
- Increasing timeouts to hide slow dependencies.
- Confusing more concurrent requests with more completed work.
- Forgetting retries can multiply traffic during an outage.
- Ignoring capacity needs during an instance failure or deployment.
- Assuming asynchronous processing removes the need for latency targets.

---

## 12. Quick reference

```text
Latency      = how long one operation takes
Throughput   = how much work completes per unit of time
Concurrency  = how much work is active right now

Concurrency = Throughput × Latency
```

If latency increases while throughput remains constant, concurrency rises. If
incoming throughput exceeds processing throughput, queues grow. As queues grow,
waiting time increases and latency can eventually violate the service-level
objective (SLO).

---

## Practice exercise

A notification system receives **600 requests/second** during a campaign. Each
request takes **250 ms on average** end-to-end.

1. Estimate average in-flight requests using Little’s Law.
2. If latency rises to 1.5 seconds while throughput remains 600 requests/sec,
   estimate the new concurrency.
3. Workers process 500 notifications/second. How quickly does the backlog grow?
4. If the traffic drops to 350 requests/second and worker capacity stays at 500
   per second, how long will it take to drain a backlog of 90,000 messages?
5. Name three metrics you would alert on for this service.

### Reference answer

1. Convert latency to seconds: `250 ms = 0.25 sec`.

   ```text
   Concurrency = 600 × 0.25 = 150 in-flight requests
   ```

2. At 1.5 seconds:

   ```text
   Concurrency = 600 × 1.5 = 900 in-flight requests
   ```

3. Backlog growth:

   ```text
   600 incoming/sec - 500 processed/sec = 100 messages/sec
   ```

4. Recovery rate:

   ```text
   500 processed/sec - 350 incoming/sec = 150 messages/sec
   ```

   Drain time:

   ```text
   90,000 / 150 = 600 seconds = 10 minutes
   ```

5. Useful alert candidates include p95/p99 delivery latency, oldest message age,
   queue depth or consumer lag, provider error/timeout rate, and worker saturation.
   Choose thresholds from the system's SLOs and normal operating baseline.

