---
title: Scalability
parent: System Design
nav_order: 6
---

# Scalability

> Scalability is a system’s ability to handle growth in users, traffic, data, or
> work while maintaining acceptable performance, reliability, and cost.

A system that handles 100 requests per second today may need to handle 10,000
requests per second later. Scalability means understanding what grows, where
bottlenecks appear, and how to expand capacity without making other parts of the
system fail.

-----

## 1. What can grow?

When someone says “the system must scale,” clarify what is growing:

- **Traffic:** requests, messages, events, or background jobs per second
- **Users:** registered, active, or concurrently active users
- **Data:** database records, files, indexes, logs, or audit history
- **Work complexity:** more operations per request, larger payloads, or more downstream calls
- **Geographic reach:** users farther away or in more regions
- **Team and development scale:** more engineers, services, or independent releases

Each growth dimension can create a different bottleneck. More users do not
necessarily mean more concurrent requests; more data does not necessarily mean
more writes.

---

## 2. Vertical scaling

**Vertical scaling** means giving one machine more resources: CPU, memory, disk,
or network capacity.

```text
Before: 4 CPU, 16 GB RAM
After:  16 CPU, 64 GB RAM
```

### Advantages

- Simple to understand and operate.
- Often works without application changes.
- Useful for prototypes, small services, and early growth.
- Can improve performance when the bottleneck is local to one machine.

### Limitations

- There is a maximum machine size and cost.
- Larger machines can become expensive quickly.
- One machine may remain a single point of failure.
- Upgrades can require restarts or maintenance.
- It does not automatically solve architecture bottlenecks, such as a slow query or serialized workload.

Vertical scaling is often a good first move, but it has a ceiling.

---

## 3. Horizontal scaling

**Horizontal scaling** means adding more machines or application instances and
distributing work across them.

```text
Clients
  → Load balancer
      → App instance 1
      → App instance 2
      → App instance 3
```

### Advantages

- Capacity can grow incrementally.
- Can improve availability if instances fail independently.
- Often works well for stateless application servers.
- Makes it possible to scale only the busy component.

### Challenges

- Requests must be distributed.
- Shared state must be handled outside individual instances.
- Databases and downstream services may become bottlenecks.
- Deployments, configuration, observability, and networking become more complex.
- Some workloads are difficult to split or parallelize.

Horizontal scaling increases capacity only if dependencies can keep up.

---

## 4. Stateless vs stateful services

A **stateless application instance** does not keep important user or session
state only in its local memory or disk between requests. Any instance can handle
the next request.

```text
Request 1 → Instance A
Request 2 → Instance C
```

This makes horizontal scaling and failover easier. Avoid relying on local-only
state for:

- User sessions
- Notification status
- Idempotency records
- Job progress
- Shared rate limits

Store shared state in an appropriate external system, such as a database, cache,
or durable queue. “Stateless” does not mean the overall system has no state; it
means state is not trapped inside one application instance.

---

## 5. Scaling the Notification Service

Suppose a Notification Service handles:

```text
1 million notifications/hour ≈ 278/second average
Peak factor 4× → about 1,112/second
```

A first architecture might be:

```text
Order/Product systems
        ↓
Notification API
        ↓
Durable queue
        ↓
Delivery workers
        ↓
Email/SMS/Push providers
```

Different components may need different scaling strategies:

| Component            | Growth pressure               | Possible scaling approach                               |
|----------------------|-------------------------------|---------------------------------------------------------|
| API                  | More incoming requests        | Add stateless instances behind a load balancer          |
| Queue                | Bursts and downstream outages | Increase partitions/queue capacity; monitor backlog     |
| Workers              | More delivery work            | Add consumers, bounded by provider limits               |
| Preferences database | More reads and writes         | Index, cache hot preferences, tune database capacity    |
| Audit storage        | Retention over time           | Partition by date, archive, apply retention policy      |
| Provider integration | Provider rate limits          | Throttle, queue, use approved alternatives if justified |

Scaling the API does not solve provider throughput. If an SMS provider allows
only 300 requests per second, adding application instances cannot make SMS
delivery exceed that limit safely.

---

## 6. Find the bottleneck before scaling

The system’s overall throughput is often constrained by its slowest or most
limited component.

```text
API capacity:       5,000 requests/sec
Database capacity:  1,500 writes/sec
Provider capacity:    300 SMS/sec
```

If each request requires an SMS, end-to-end SMS throughput is limited to roughly
300 per second, regardless of API capacity.

### Common bottlenecks

- CPU-bound business logic
- Slow or unindexed database queries
- Database connection limits
- Lock contention
- External API rate limits
- Queue consumer lag
- Network bandwidth
- Large payloads
- Garbage-collection pauses
- Thread-pool or connection-pool exhaustion
- Hot keys or uneven partition distribution

### Bottleneck-finding process

1. Define the target: throughput, latency, and error-rate objective.
2. Measure each major component under realistic traffic.
3. Identify where requests wait or fail.
4. Check whether the bottleneck is CPU, I/O, locks, connections, or a dependency limit.
5. Change the narrowest relevant part.
6. Load-test again to see whether the bottleneck moved elsewhere.

Do not scale based on CPU alone. A service can have low CPU while waiting on a
database or provider.

---

## 7. Scale up vs scale out by component

Different parts of a system may use different scaling approaches.

### Application tier

Stateless web/API instances usually scale horizontally.

### Database

Possible options include:

- Vertical scaling for more CPU, memory, and I/O
- Read replicas for read-heavy workloads
- Partitioning for large tables or write distribution
- Sharding when a single database cannot meet capacity needs

Database scaling is more complex because relationships, transactions,
consistency, and query patterns matter.

### Workers

Workers can often scale horizontally, but only up to the capacity of their
downstream dependencies. Bound worker concurrency and respect provider rate
limits.

### Cache

A cache may scale vertically, horizontally, or through clustering/sharding,
depending on the product and access pattern. Consider memory limits, hot keys,
and what happens if the cache is unavailable.

---

## 8. Database scaling progression

Prefer the simplest effective approach first. A common progression is:

1. **Fix inefficient queries** — Remove unnecessary database work.
2. **Add correct indexes** — Support real query patterns; indexes also add write and storage costs.
3. **Reduce round trips** — Batch or combine queries where appropriate.
4. **Cache suitable reads** — Define freshness and invalidation rules.
5. **Scale the database vertically** — Add resources if the database is resource-constrained.
6. **Add read replicas** — Offload eligible reads while accounting for replication lag.
7. **Partition large tables** — Often by time or another access-pattern-aligned key.
8. **Shard across databases** — Use only when simpler options no longer satisfy requirements.

Sharding adds significant complexity: routing, rebalancing, cross-shard queries,
global uniqueness, migrations, and operational tooling. Do not shard simply
because the system is “large.”

---

## 9. Caching and scalability

Caching can reduce database load and latency for data that is read often and can
tolerate defined freshness behavior.

```text
Read cache
  ├─ hit → return cached value
  └─ miss → read database → populate cache → return
```

Before caching, decide:

- How stale can data be?
- What invalidates or refreshes it?
- What happens during cache outage?
- Can a hot key overload one cache node?
- Can many simultaneous misses cause a cache stampede and overload the database?

A cache can improve scalability, but it introduces another dependency and
consistency behavior.

---

## 10. Queues and backpressure

Queues help systems absorb bursts and separate producers from consumers. They do
not create unlimited capacity.

If:

```text
Incoming rate = 1,200 messages/sec
Processing rate = 1,000 messages/sec
```

Then backlog grows by 200 messages each second. Eventually storage, delivery
deadlines, or queue limits can be exceeded.

Use **backpressure** to prevent overload from spreading:

- Slow or pause producers.
- Limit worker concurrency.
- Reject low-priority work.
- Apply rate limits.
- Return a retryable response when work cannot be safely accepted.
- Preserve capacity for critical operations.

Define queue limits, maximum message age, and behavior when capacity is
exhausted.

---

## 11. Scalability and partitioning

Partitioning divides data or work into smaller parts.

Examples:

- Kafka topics split into partitions
- Database tables partitioned by date
- User data distributed by a hash of `userId`
- Jobs assigned to worker groups

Partitioning can improve parallelism, but the partition key matters. A useful
key tends to:

- Distribute work evenly.
- Keep related operations together when ordering is needed.
- Avoid a small number of extremely hot partitions.
- Support common access patterns.

### Hot partition example

If all events for a very popular user or product go to one partition, that
partition can become overloaded while others remain idle. Average cluster
capacity may look fine even though one partition is the bottleneck.

---

## 12. Scalability vs elasticity

- **Scalability:** The system can handle increased load by adding resources or changing design.
- **Elasticity:** Capacity automatically or quickly grows and shrinks with demand.

Adding worker instances to handle a campaign is scaling. Automatically adding
workers when queue lag rises and removing them after the backlog drains is
elasticity.

Autoscaling should use meaningful signals such as queue lag, request rate, or
saturation—not just CPU. It also needs cooldowns and maximum limits to avoid
oscillation or runaway cost.

---

## 13. Scalability vs availability

- **Scalability** is about handling more load.
- **Availability** is about remaining usable when components fail.

Adding instances can improve both if they are independent and traffic can route
around failures. However, scaling can also reduce availability if it overloads a
shared database or creates configuration errors.

---

## 14. Common scaling mistakes

- Scaling the application tier while ignoring the database or providers.
- Adding instances without checking whether requests are stateless.
- Increasing connection pools until the database is overloaded.
- Assuming queues remove capacity limits.
- Introducing caching without defining freshness and invalidation.
- Sharding before measuring query and database bottlenecks.
- Ignoring hot partitions and uneven load.
- Autoscaling on CPU alone.
- Ignoring retry traffic and failure scenarios.
- Treating “supports 10× traffic” as meaningful without defining the workload.

---

## 15. Scalability design checklist

For each system, ask:

- [ ] What exactly is growing: requests, users, data, or processing complexity?
- [ ] What are average, peak, and burst rates?
- [ ] Which components are stateless and horizontally scalable?
- [ ] What is the current bottleneck under representative load?
- [ ] What limits the database, cache, queue, and external providers?
- [ ] Are requests and data distributed evenly?
- [ ] Could hot keys or hot partitions appear?
- [ ] What is the overload/backpressure behavior?
- [ ] What signals trigger autoscaling?
- [ ] What happens during a dependency outage while traffic continues?
- [ ] What are the cost and operational trade-offs?

---

## Practice exercise

For the Notification Service:

1. The API receives 2,000 notification requests/sec, while workers can process
   1,500/sec. How quickly does the backlog grow?
2. If SMS provider capacity is 400/sec and 50% of notifications require SMS,
   what is the maximum safe total throughput if all channels arrive in the same
   mix and no alternative provider is used?
3. Which components would you scale horizontally? Which may need a different
   scaling approach?
4. Name two metrics that could trigger worker autoscaling.
5. What would you do if one Kafka partition has much higher lag than the others?

### Reference answers

#### 1. Backlog growth

Assuming the rates remain constant:

```text
Backlog growth = incoming rate - processing rate
               = 2,000 - 1,500
               = 500 notifications/second
```

After one minute:

```text
500 × 60 = 30,000 notifications
```

The system needs more processing capacity, lower incoming traffic, or an
explicit overload policy. Otherwise, the queue keeps growing.

#### 2. SMS provider capacity

If half of all notifications use SMS, then at total throughput `T`:

```text
SMS rate = T × 50%
T × 0.5 ≤ 400
T ≤ 800 notifications/second
```

The theoretical maximum is **800 total notifications/second**, assuming the
other channel has enough capacity and traffic remains in that 50/50 mix. In
practice, leave headroom below the provider’s limit and account for retries and
bursts.

#### 3. What to scale

**Good candidates for horizontal scaling:**

- Stateless API instances behind a load balancer
- Notification workers, within provider rate limits
- Queue consumers, when partitions and downstream capacity allow it

**Other components need additional considerations:**

- **Database:** Optimize queries and indexes first; then consider vertical
  scaling, read replicas, partitioning, or sharding based on the bottleneck.
- **Queue/broker:** Add partitions or brokers as appropriate, while checking
  ordering, partition balance, and storage.
- **External providers:** Their capacity is controlled by provider limits; use
  throttling or an approved alternative rather than simply adding workers.
- **Cache:** Scale or cluster based on memory and traffic patterns; plan for hot
  keys and cache outages.

#### 4. Worker autoscaling metrics

Useful metrics include:

- **Queue lag or oldest-message age:** Scale out when work is accumulating or delivery is falling behind.
- **Worker utilization or processing rate:** Scale based on active workers and whether they are keeping up.

Set maximum worker counts and respect provider rate limits. Scaling beyond
downstream capacity can increase errors without increasing successful delivery.

#### 5. One Kafka partition has much higher lag

First investigate whether the partition has a **hot key**, uneven traffic, a
slow consumer, or repeatedly failing messages.

Possible actions:

- Fix the consumer or message issue causing slow processing.
- Check whether the key choice concentrates events on one partition.
- Increase partition count if safe for the topic’s ordering and keying requirements.
- Change the partitioning strategy if a key is persistently hot.
- Scale consumers only if partitions are available and the downstream provider can handle more work.

Adding consumers alone may not help: within a consumer group, a Kafka
partition is assigned to at most one consumer at a time, so the hot partition
still has one active consumer.

