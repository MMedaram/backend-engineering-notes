---
title: Capacity Estimation
parent: System Design
nav_order: 3
---

# Capacity Estimation

> Capacity estimation converts business-scale statements into approximate engineering numbers that guide architecture decisions.

For example, “handle one million notifications per hour” is not yet an architecture. It must become requests per second, peak traffic, storage, bandwidth, queue backlog, worker count, and failure headroom.

Capacity estimation is **not exact forecasting**. It helps rule out unsuitable designs, exposes unknowns early, and provides a defensible starting point. Replace important assumptions with load-test and production data before final capacity planning.

---

## 1. Why estimate capacity before choosing technology?

Architecture choices depend on scale.

| Scale observation              | Possible design consequence                                             |
|--------------------------------|-------------------------------------------------------------------------|
| A few requests per second      | A simple synchronous application may be sufficient.                     |
| Thousands of events per second | Queueing, batching, and horizontally scalable workers become important. |
| Read-heavy traffic             | Caching, read replicas, indexes, and a CDN may be useful.               |
| Large retained audit data      | Partitioning, archiving, retention jobs, and storage cost matter.       |
| Large campaign spikes          | Rate limits, queue buffering, and load shedding are required.           |

Do not start by choosing Kafka, Redis, sharding, or microservices. First learn whether the traffic, latency, availability, and retention needs justify them.

---

## 2. Useful conversions

### Time

| Period            |    Seconds |
|-------------------|-----------:|
| 1 minute          |         60 |
| 1 hour            |      3,600 |
| 1 day             |     86,400 |
| 1 month (30 days) |  2,592,000 |
| 1 year (365 days) | 31,536,000 |

### Storage

| Unit | Approximate decimal value |
|------|--------------------------:|
| 1 KB |               1,000 bytes |
| 1 MB |                  1,000 KB |
| 1 GB |                  1,000 MB |
| 1 TB |                  1,000 GB |

In HLD discussions, round numbers are better than false precision. State your convention and keep calculations consistent.

---

## 3. Core estimation workflow

1. Start from a business number: users, requests/day, orders/day, messages/hour, or files/day.
2. Convert it to average requests/events per second.
3. Estimate peak rate and bursts.
4. Split reads and writes.
5. Estimate request, response, event, and database-record sizes.
6. Calculate storage and bandwidth.
7. Size queues and workers, including dependency limits.
8. Add growth and failure headroom.
9. Record every important assumption for validation.

---

## 4. Requests per second (RPS)

### Formula

```text
Average RPS = total requests in period / seconds in period
```

### Example: one million notifications per hour

```text
Average events/second = 1,000,000 / 3,600
                      ≈ 278 events/second
```

### Daily example

```text
100 million requests/day / 86,400 seconds/day
≈ 1,157 RPS average
```

Average traffic helps with storage and cost. It is not enough for live-service sizing because traffic is rarely uniform.

---

## 5. Average traffic, peak traffic, and bursts

### Peak traffic

Peak traffic is the highest sustained rate expected during busy periods.

```text
Peak RPS = average RPS × peak multiplier
```

For an average of 278 events/second and a four-times multiplier:

```text
Peak events/second = 278 × 4
                   ≈ 1,112 events/second
```

Choose the multiplier from actual usage patterns when possible. Consumer products may peak in evenings; e-commerce can peak during sales; billing systems may peak in scheduled batch windows.

### Bursts

A burst is a short spike beyond normal peak. A queue lets the upstream system accept work quickly while workers process it at a controlled rate.

Example: a campaign creates 5,000 events/second for two minutes, but downstream providers accept only 1,500/second.

```text
Backlog added/second = 5,000 - 1,500 = 3,500
Backlog after 120 seconds = 3,500 × 120 = 420,000 messages
```

The system must have enough queue capacity and surplus worker throughput to drain this backlog within the allowed delivery delay.

---

## 6. Read/write ratio

```text
Read/write ratio = read operations : write operations
```

| System               | Typical pattern                                                                              |
|----------------------|----------------------------------------------------------------------------------------------|
| Product catalog      | Read-heavy; often 100:1 or higher.                                                           |
| URL shortener        | Extremely read-heavy; redirects dominate link creation.                                      |
| Chat application     | Writes are high during active conversations; history reads can also be high.                 |
| Notification Service | Write/event-heavy during ingestion; reads are mostly preferences, status, and audit queries. |

| Dominant load | Common responses                                                      |
|---------------|-----------------------------------------------------------------------|
| Read-heavy    | Caching, read replicas, CDN, indexes, denormalised read models        |
| Write-heavy   | Queues, batching, partitioning, append-oriented storage, backpressure |

---

## 7. Storage estimation

### Formula

```text
Storage = records per period × average stored record size × retention period
```

Include more than the business payload:

- Database row and engine overhead
- Primary and secondary indexes
- Audit fields and status history
- Replication and backups
- Log retention, where relevant

### Notification Service example

Assume:

- 1,000,000 notification requests/hour
- 24 hours/day
- 1.5 KB average audit record
- 90-day retention

```text
Notifications/day = 1,000,000 × 24 = 24,000,000

Records in 90 days = 24,000,000 × 90 = 2,160,000,000

Raw storage = 2,160,000,000 × 1.5 KB
            ≈ 3.24 TB
```

If indexes and database overhead add 50%:

```text
Primary storage ≈ 3.24 TB × 1.5 = 4.86 TB
```

With three total copies of data:

```text
Total replicated storage ≈ 4.86 TB × 3 = 14.58 TB
```

These are first-pass estimates. Measure representative records before committing to storage design or cost.

---

## 8. Bandwidth estimation

### Formula

```text
Bandwidth/second = requests/second × average request or response size
```

If an internal event is 2 KB and peak throughput is 1,112 events/second:

```text
1,112 × 2 KB ≈ 2,224 KB/second ≈ 2.2 MB/second
```

Include all relevant paths in real planning:

- Producer to broker
- Broker replication
- Broker to consumer
- API request and response traffic
- Retries and dead-letter handling
- TLS and protocol overhead

For Kafka, replication increases network and storage cost. The precise amount depends on cluster topology, replication factor, and acknowledgement configuration.

---

## 9. Queue capacity and backlog

Queues decouple producers from consumers and protect the system when downstream dependencies are slow or unavailable.

### Formula

```text
Backlog = (incoming rate - processing rate) × duration
```

Example: providers are unavailable for 15 minutes while the service continues accepting 1,100 messages/second.

```text
15 minutes = 900 seconds
Backlog = 1,100 × 900 = 990,000 messages
```

If each queued message is 2 KB:

```text
990,000 × 2 KB ≈ 1.98 GB raw queue data
```

Ask:

- How long may downstream providers be unavailable?
- How much queue retention is needed?
- How quickly must delayed work be caught up?
- Which lower-priority traffic may be delayed or dropped?
- What happens when the queue approaches its limit?

### Recovery capacity

After an outage, normal-rate processing is not enough; there must be surplus capacity to drain backlog.

```text
Incoming rate = 1,100/second
Worker capacity = 1,650/second
Extra drain rate = 1,650 - 1,100 = 550/second

Drain time = 990,000 / 550 = 1,800 seconds = 30 minutes
```

---

## 10. Worker and instance estimation

### Formula

```text
Workers needed = peak required throughput / proven throughput per worker
```

Assume one worker safely processes 100 notification attempts/second after load testing and peak required throughput is 1,112/second.

```text
Workers needed = 1,112 / 100 = 11.12
Minimum workers = 12
```

Add resilience headroom. For example, use 14 or more workers if one or two worker instances must fail without breaching latency targets.

Worker throughput depends on:

- Provider rate limits
- Connection-pool limits
- CPU and memory per task
- I/O wait and network latency
- Retry volume
- Consumer concurrency
- Instance or zone failure

> A guessed throughput per worker is not a fact. Prove it with representative load and provider-sandbox tests.

---

## 11. Kafka partition estimation

Within one consumer group, a partition is processed by at most one consumer at a time. Therefore, partitions set the maximum useful consumer parallelism for that group.

### First-pass approach

1. Estimate peak event rate.
2. Benchmark safe processing rate per consumer.
3. Calculate required consumer parallelism.
4. Add growth and recovery headroom.
5. Choose enough partitions for that parallelism.

Example:

```text
Peak incoming rate = 1,112 events/second
Safe consumer rate = 100 events/second
Required consumers = ceil(1,112 / 100) = 12
```

With about 50% growth/recovery headroom, **18 partitions** is a reasonable starting estimate. It is not a universal formula. Partition count affects ordering, broker load, rebalancing, file handles, and operational cost. Validate it using actual event size, processing time, retention, and broker configuration.

---

## 12. Cache memory estimation

### Formula

```text
Cache memory = hot keys × average entry size × overhead factor
```

Example: cache 5 million user-preference records, averaging 500 bytes, with a 2× overhead factor for keys, metadata, allocator overhead, and safety margin.

```text
5,000,000 × 500 bytes × 2
= 5,000,000,000 bytes
≈ 5 GB
```

Also consider TTL, eviction policy, replication, hot keys, cache stampede, restart behaviour, and whether the primary database can survive a cache outage.

---

## 13. Headroom and failure planning

Do not size exactly at estimated peak. Leave capacity for:

- Growth and inaccurate forecasts
- Retries, batch jobs, and backfills
- Traffic bursts
- Deployments and consumer rebalances
- One instance, zone, or broker failure

A common first pass is 30% to 50% headroom, then refine it from actual saturation and SLO measurements. For critical systems, explicitly ask:

> Can the system still meet its target if one instance or one availability zone fails?

---

## 14. Fully worked Notification Service estimate

### Inputs

| Input                     |                                   Assumption |
|---------------------------|---------------------------------------------:|
| Notification requests     |                               1,000,000/hour |
| Average rate              |             `1,000,000 / 3,600 ≈ 278/second` |
| Peak multiplier           |                                           4× |
| Peak rate                 |                     `278 × 4 ≈ 1,112/second` |
| Average queued event size |                                         2 KB |
| Average audit record size |                                       1.5 KB |
| Audit retention           |                                      90 days |
| Safe throughput/worker    | 100 notifications/second; benchmark required |
| Provider outage tolerance |                                   15 minutes |

### Key results

| Area                     | First-pass result                          | Implication                                                               |
|--------------------------|--------------------------------------------|---------------------------------------------------------------------------|
| Peak ingest              | ~1,112 events/second                       | Use asynchronous queueing and horizontally scalable workers.              |
| Minimum workers          | 12                                         | Use at least 14 after failure/deployment headroom, subject to benchmarks. |
| 15-minute outage backlog | ~1,000,800 messages / ~2 GB raw            | Retain queue data and plan recovery capacity.                             |
| 90-day raw audit storage | ~3.24 TB                                   | Use time-based partitioning and retention management.                     |
| Replicated audit storage | ~14.58 TB with stated overhead/copies      | Storage cost and archival strategy need review.                           |
| Internal event bandwidth | ~2.2 MB/second before replication/overhead | Validate broker, network, and consumer capacity.                          |
| Consumer partitions      | ~18 as a starting point                    | Benchmark before production; respect ordering requirements.               |

This estimate suggests queues, provider throttling, worker autoscaling, delivery-lag monitoring, retention controls, and careful audit-storage design. It does **not** by itself prove that Kafka or a particular database is required.

---

## 15. HLD estimate vs production capacity planning

| HLD estimation                | Production capacity planning                                |
|-------------------------------|-------------------------------------------------------------|
| Quick and approximate         | Detailed and evidence-based                                 |
| Uses stated assumptions       | Uses real traffic, profiling, and load tests                |
| Identifies unsuitable designs | Sets actual infrastructure limits and budgets               |
| Uses rounded arithmetic       | Measures p95/p99 latency, saturation, and failure behaviour |
| Explains trade-offs           | Validates scaling rules, alarms, and runbooks               |

In an interview, explain your assumptions and arithmetic. In production, replace assumptions with observed measurements.

---

## 16. Common mistakes

- Sizing from average traffic only and ignoring peaks.
- Choosing a peak multiplier without explaining it.
- Forgetting retries, replication, indexes, backups, and retention.
- Treating queue capacity as unlimited.
- Assuming worker throughput without benchmarking.
- Picking Kafka partition count from a formula alone.
- Confusing storage capacity with database write throughput.
- Showing exact-looking numbers when inputs are guesses.
- Over-engineering a small system before real scale demands it.

---

## 17. Interview structure

1. State the source traffic assumption.
2. Convert it to average RPS.
3. State peak/burst multiplier and why.
4. Estimate read/write ratio and payload sizes.
5. Calculate storage and retention.
6. Identify queue, worker, and provider-rate implications.
7. Add growth and failure headroom.
8. Label every number that requires validation.

Example:

> One million notification requests per hour is roughly 278 events per second on average. I will plan for a four-times peak, around 1,100 events per second, because campaigns concentrate traffic. I will queue provider delivery, size workers from benchmarked throughput, and retain enough queue capacity for a 15-minute provider outage. I will validate event size, provider limits, and worker throughput before final capacity planning.

---

## 18. Capacity-estimation checklist

- [ ] What is the source business volume: users, requests, orders, messages, or files?
- [ ] What is the average RPS/events per second?
- [ ] What are expected peak and burst rates?
- [ ] What is the read/write ratio?
- [ ] What are average request, event, and stored-record sizes?
- [ ] How long is data retained?
- [ ] What indexes, replication, backups, and overhead apply?
- [ ] How long can downstream dependencies be unavailable?
- [ ] How quickly must backlog drain after recovery?
- [ ] What throughput can one worker safely handle in a benchmark?
- [ ] Can the system survive an instance or zone failure?
- [ ] Which estimates need validation before production?

---

## Practice exercise — answered

### Problem

A URL Shortener receives **50 million redirects per day**. Assume:

- 10% of daily traffic occurs in the busiest hour.
- A redirect lookup returns 500 bytes.
- A click-analytics event is 1 KB.
- Click analytics are retained for 30 days.
- The analytics stream uses three copies of data.

Estimate busiest-hour RPS, redirect-response bandwidth, and raw analytics storage for 30 days.

### Answer

#### Busiest-hour RPS

```text
Requests in busiest hour = 50,000,000 × 10% = 5,000,000
Peak-hour RPS = 5,000,000 / 3,600 ≈ 1,389 RPS
```

#### Redirect-response bandwidth

```text
1,389 responses/second × 500 bytes
= 694,500 bytes/second
≈ 0.7 MB/second
```

This excludes HTTP/TLS overhead, requests, cache misses, database reads, and replication traffic.

#### Raw analytics storage

```text
50,000,000 events/day × 1 KB × 30 days
= 1,500,000,000 KB
≈ 1.5 TB raw storage

With three copies: 1.5 TB × 3 ≈ 4.5 TB
```

Indexes, compression, metadata, backups, and the chosen storage engine change the final number. The important conclusion is that analytics volume is large enough to justify asynchronous ingestion plus a retention and partitioning strategy.

