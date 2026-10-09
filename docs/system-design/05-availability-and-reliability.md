---
title: Availability and Reliability
parent: System Design
nav_order: 5
---

# Availability and Reliability

> Availability and reliability are closely related, but they are not the same.

- **Availability:** Can users access the system when they need it?
- **Reliability:** Does the system continue to behave correctly and predictably over time?

A service can be available but unreliable. For example, an order API may return
`200 OK` every time but occasionally create two orders for one request. The API
is reachable, but its behavior is not reliable.

---

## Learning outcomes

After this topic, you should be able to:

- Distinguish availability from reliability.
- Calculate availability targets and understand “nines.”
- Identify single points of failure and failure domains.
- Explain redundancy, health checks, and failover.
- Design graceful degradation for optional or failing dependencies.
- Use timeouts, retries, and circuit breakers safely.
- Understand SLI, SLO, SLA, error budgets, RTO, and RPO.
- Walk through a dependency failure in the Notification Service.

---

## 1. Availability

Availability is the proportion of time a system is usable.

```text
Availability = uptime / (uptime + downtime)
```

Availability is often expressed as a percentage or in “nines.” Approximate
downtime for a 30-day month:

| Availability | Approximate downtime/month |
|-------------:|---------------------------:|
|          99% |         7 hours 18 minutes |
|        99.9% |      43 minutes 50 seconds |
|       99.99% |       4 minutes 23 seconds |
|      99.999% |                 26 seconds |

These are approximate values. Exact downtime budgets depend on the measurement
period and how availability is defined. Each additional nine generally costs
more and requires more operational complexity. Do not promise five nines without
first understanding the actual business and customer need.

### Availability is user-visible

A server being “up” does not necessarily mean the system is available:

- The API process is running, but the database is unreachable.
- The endpoint returns responses, but checkout cannot complete.
- Notification requests are accepted, but the queue is stuck and no messages are delivered.
- One region is healthy, but all customers are routed to a failed region.

Define availability from the customer’s point of view. For checkout, ask
“Can a customer successfully place an order?” rather than only “Is the web server
alive?”

---

## 2. Reliability

Reliability is the ability of a system to perform its required functions
correctly over time, including when faults occur.

A reliable system should:

- Produce correct results.
- Avoid losing accepted work.
- Avoid unintended duplicate side effects.
- Handle expected errors and failures predictably.
- Recover or degrade in a controlled way.
- Make failures visible to operators.

Examples:

- An order is not charged twice when a client retries.
- An accepted notification request is not silently lost.
- If an email provider is down, work is retried or reported as failed rather than disappearing.
- A database failover does not return contradictory order states.

Reliability is broader than uptime. It includes **correctness, durability,
recovery, and predictable behavior**.

---

## 3. Reliability vocabulary

| Term           | Meaning                                         | Example                                                           |
|----------------|-------------------------------------------------|-------------------------------------------------------------------|
| **Fault**      | A defect or abnormal condition in a component   | A disk fails; a provider times out                                |
| **Error**      | An incorrect internal state caused by a fault   | A handler believes a payment completed when its status is unknown |
| **Failure**    | User-visible deviation from expected behavior   | The customer is charged, but the order remains unpaid             |
| **Resilience** | Ability to withstand and recover from faults    | Continue accepting notifications while a provider is down         |
| **Redundancy** | Extra components or capacity that can take over | Multiple application instances across zones                       |
| **Failover**   | Switching to a healthy component after failure  | Promote a database replica or route traffic to another zone       |
| **Recovery**   | Restoring normal or acceptable operation        | Replay queued messages after a provider outage                    |

A fault does not always become a user-visible failure. Good design contains
faults and prevents them from spreading.

---

## 4. Single points of failure

A **single point of failure (SPOF)** is a component whose failure stops the
whole system or a critical feature.

```text
Users → Load Balancer → One Application Instance → One Database
```

Even with a load balancer, one application instance and one database can remain
SPOFs. A more fault-tolerant baseline could use multiple stateless application
instances across failure domains and a highly available database.

Consider all dependencies, not just application servers:

- DNS and certificates
- Load balancers
- Application instances
- Database primary and replicas
- Cache clusters
- Queues and brokers
- External providers
- Identity provider
- Network paths between zones or regions

Do not merely draw “multiple instances.” Ask where they run, what they depend
on, and whether they can fail independently.

---

## 5. Redundancy and failure domains

Redundancy means having extra components or capacity so one failure does not
immediately stop the service.

Examples:

- Multiple stateless application instances
- Database replicas
- Multiple availability zones
- Multiple queue brokers
- Backup network paths
- A secondary provider, when justified

### Failure domains

Two instances on the same host or in the same availability zone can fail
together. Redundancy helps only when components do not share the same failure
domain.

Common failure domains include:

- Process or container
- Host or virtual machine
- Rack
- Availability zone
- Region
- Cloud provider
- External vendor

Three replicas in one zone may protect against a process failure, but not
necessarily against a zone outage.

### Active-active and active-passive

- **Active-active:** Multiple instances serve traffic at the same time. This can
  improve capacity and availability, but data consistency and routing are more complex.
- **Active-passive:** A standby component takes over after failure. This can be
  simpler for some stateful systems, but failover can take time and the standby may be under-tested.

Neither is universally better. Choose based on recovery objectives, cost, and
consistency requirements.

---

## 6. Health checks and failover

Health checks help determine whether a component can receive traffic.

### Liveness check

Answers:

> Is this process alive, or should it be restarted?

It should usually check the process itself, not every dependency. If liveness
fails whenever a database is temporarily down, restarting every application
instance can make the outage worse.

### Readiness check

Answers:

> Is this instance ready to receive requests?

An instance may be alive but not ready because it is warming up, applying a
migration, or unable to serve its core function.

### Dependency health

A dependency check can report database, cache, or broker status. Be careful not
to make every downstream outage restart the application or remove all instances
from service.

### Failover design questions

- How is failure detected?
- How quickly does routing or promotion happen?
- Can failover cause data loss?
- Are in-flight requests retried safely?
- Can clients retry without duplicating side effects?
- Is standby capacity sufficient for full traffic?
- How will the failover process be tested?

Failover that has never been tested is an assumption, not a guarantee.

---

## 7. Graceful degradation

Graceful degradation means preserving the most important user capability when
optional components fail.

Examples:

- Product recommendations are unavailable, but customers can still browse and buy.
- Notification delivery is delayed, but order placement succeeds.
- The cache is down, so reads go to the database at a limited rate.
- Analytics are delayed without blocking the customer request.
- A dashboard shows slightly stale data rather than failing entirely.

Prioritize features explicitly:

| Priority | Notification Service example       | Degradation behavior                              |
|----------|------------------------------------|---------------------------------------------------|
| Critical | Accept an order-confirmation event | Persist to a queue; do not lose the request       |
| High     | Deliver shipment/status alert      | Retry with bounded backoff; track delivery status |
| Lower    | Send product recommendation        | Delay, batch, or temporarily pause                |

Graceful degradation is not “ignore the error.” It is a deliberate decision
about what continues, what is delayed, and what is temporarily unavailable.

---

## 8. Timeouts, retries, and circuit breakers

### Timeouts

Every network call should have a reasonable timeout. Without one, a request may
wait indefinitely and consume a thread or connection.

Set timeouts according to the user-facing latency budget and the dependency’s
expected behavior.

### Retries

Retry only errors that may be temporary, such as a connection reset or a `503`
response. Use:

- A maximum retry count
- Exponential backoff
- Random jitter to avoid synchronized retry spikes
- Idempotency or deduplication for side effects

Example backoff:

```text
Retry 1: 200 ms
Retry 2: 400 ms
Retry 3: 800 ms
```

Exact values depend on the dependency and the request deadline. Avoid retrying
permanent errors such as invalid input. Also avoid retries at every layer:
retries across multiple services can multiply traffic dramatically.

### Circuit breaker

A circuit breaker temporarily stops calls to a dependency that is consistently
failing.

Typical states:

- **Closed:** Calls flow normally.
- **Open:** Calls are rejected or fail fast, possibly using a fallback.
- **Half-open:** A small number of test calls are allowed to check recovery.

Circuit breakers prevent a struggling dependency from exhausting resources in
callers. They do not fix the dependency; they contain impact and allow recovery.

---

## 9. SLI, SLO, SLA, and error budget

- **SLI (Service Level Indicator):** What you measure.
- **SLO (Service Level Objective):** The target for that measurement.
- **SLA (Service Level Agreement):** A formal commitment, often with consequences if missed.

Example:

```text
SLI: percentage of order requests that complete successfully
SLO: 99.9% successful over a rolling 30-day window
SLA: contractual availability commitment to customers
```

An SLO should use a meaningful user-visible indicator. “Application CPU is
below 70%” is an operational metric, not a user-facing availability SLI.

### Error budget

If an SLO is 99.9% over 30 days, the approximate error budget is 0.1% of that
period—about 43 minutes and 50 seconds of unavailability, depending on the
measurement rules.

An error budget helps teams balance reliability work and feature delivery. If
the budget is being consumed too quickly, the team may slow risky releases and
focus on reliability.

---

## 10. RTO and RPO

Disaster-recovery planning often uses two targets:

- **RTO (Recovery Time Objective):** How long it may take to restore service.
- **RPO (Recovery Point Objective):** How much data loss, measured in time, is acceptable.

Example:

```text
RTO = 30 minutes
RPO = 5 minutes
```

The service should be restored within 30 minutes, and losing more than five
minutes of data is unacceptable. Lower RTO and RPO generally require more cost
and complexity: replication, backups, automation, multi-region systems, and
regular recovery testing.

---

## 11. Notification Service failure walkthrough

Suppose the service receives an order-shipped event, but the SMS provider is
unavailable. A reliable flow might be:

1. Validate the event and the customer’s notification preferences.
2. Persist a notification job or publish it to a durable queue.
3. Acknowledge the work only after it is safely accepted.
4. Have a worker call the provider with a timeout.
5. On transient failure, retry with bounded exponential backoff and jitter.
6. Use an idempotency/deduplication key to avoid duplicate customer impact.
7. After the retry limit, move the job to a dead-letter queue and alert.
8. Track queue age, retry count, provider errors, and final status.
9. Continue accepting work while the queue has capacity; apply backpressure or reject safely if it is full.
10. Recover and replay dead-lettered items through a controlled process.

Important trade-offs:

- The service can be available for **accepting** requests while delivery is delayed.
- This does not mean notification delivery itself is currently available.
- Requirements should define whether delayed delivery is acceptable and for how long.
- If the provider remains down, the queue can fill; capacity and overload policy matter.

---

## 12. Common mistakes

- Equating “process is running” with “service is available.”
- Treating availability as the only measure of reliability.
- Claiming high availability without identifying SPOFs or failure domains.
- Adding retries without timeouts, backoff, jitter, and idempotency.
- Retrying at every layer and causing a retry storm.
- Making liveness checks depend on every external service.
- Assuming a replica is a backup or that failover is automatic and lossless.
- Building multi-region architecture without clear RTO/RPO requirements.
- Failing open in security-sensitive paths without an explicit policy.
- Never testing backups, failover, or recovery procedures.

---

## 13. Design checklist

For each critical user journey, ask:

- [ ] What does “available” mean from the user’s perspective?
- [ ] Which components are single points of failure?
- [ ] Which components share a failure domain?
- [ ] How is failure detected, and how quickly can failover happen?
- [ ] Which operations are safe to retry?
- [ ] Are timeouts, retry limits, backoff, and idempotency defined?
- [ ] What can degrade, and what must remain functional?
- [ ] What are the SLI, SLO, and (if applicable) SLA?
- [ ] What are the RTO and RPO?
- [ ] How are recovery and failover tested?
- [ ] What metrics and alerts reveal user-visible failure?

-----------------
## Practice Exercise

#### 1. What does “available” mean for request acceptance versus actual delivery?

These are separate availability measures:

- **Request-acceptance availability:** The service can validate and durably
  record a notification request or enqueue it for delivery. It should
  acknowledge the request only after it is safely stored.
- **Delivery availability:** The service can successfully hand the notification
  to the selected provider and, where supported, confirm delivery.

The service may accept requests while delivery is delayed. The API should report
that the request was **accepted or queued**, not claim it was delivered.

#### 2. What should happen if the SMS provider is down for 20 minutes?

- Keep accepted requests in a durable queue, subject to available queue capacity.
- Retry transient failures with bounded exponential backoff and jitter.
- Use idempotency or deduplication so retries do not create duplicate customer notifications.
- Monitor provider errors, queue depth, and the age of the oldest SMS.
- Alert operators if delivery targets are at risk.
- If the queue approaches capacity, apply backpressure or safely reject new requests rather than silently losing them.
- After the retry limit, move failed messages to a dead-letter queue for investigation and controlled replay.

Use another provider only if it is approved and the customer’s preferences and
business rules allow it.

#### 3. Which components are potential single points of failure?

Potential single points include:

- One application instance or one availability zone
- A single database primary without a tested failover plan
- One queue broker or unavailable queue cluster
- A shared cache if the application cannot operate without it
- A single SMS provider
- DNS, identity, or network dependencies without a fallback

A component is a practical single point of failure when its outage stops a
critical user journey. For example, Redis should not become one if the service
can safely fall back to a database with rate limits.

#### 4. Which notifications may be delayed or dropped first during overload?

Delay **promotional notifications** first: similar-item suggestions, product
launches, and marketing campaigns. They can use a lower-priority queue and be
paused or discarded according to product policy.

Preserve **transactional notifications** such as order placed, shipped, or
delivered at higher priority. If they cannot be delivered within their target,
keep them queued and surface the delay operationally rather than silently
dropping them.

#### 5. Suggest one SLI/SLO and reasonable RTO/RPO

**Assumptions:** The service accepts requests into durable storage before
acknowledging them. Transactional requests are more important than promotional
requests. The targets below are an initial proposal and should be confirmed with
product and operations teams.

- **SLI:** Percentage of valid transactional notification requests durably
  accepted by the service.
- **SLO:** At least **99.9%** of valid requests are durably accepted each calendar
  month.
- **RTO:** **30 minutes** to restore request acceptance after a major service
  outage.
- **RPO:** **5 minutes** maximum of accepted notification data may be lost after
  a disaster.

For delivery, define a separate SLO—for example, “95% of transactional
notifications are submitted to their provider within 30 seconds.” Provider
outages may affect that delivery SLO even while request acceptance remains
available.

