---
title: Kafka Storage and Internals
parent: Kafka
nav_order: 14
---

# Kafka Storage & Internals

---

## 1. Where Does Kafka Store Data?

Kafka stores messages on disk (not in memory).

Example path: defined in `server.properties`

```
/var/lib/kafka/data/
```

Inside:

```
account.transaction.completed.v1-0/
account.transaction.completed.v1-1/
account.transaction.completed.v1-2/
```

Each folder = one partition.

---

## 2. Partition = Append-Only Log

A partition is an append-only log.

Example:

Offset 0 → Event A  
Offset 1 → Event B  
Offset 2 → Event C  

Key Points:
- Messages are never updated
- Messages are only appended
- Reads are sequential (very fast)

---

## 3. Log Segments

#### Problem

If partition grows:

`Millions of messages → file becomes huge ❌`

#### Solution

Kafka splits partitions into segments.

Example:
```
00000000000000000000.log
00000000000000001000.log
00000000000000002000.log
```

Config:

```
log.segment.bytes=1073741824
(1 GB per segment)
```

Meaning:
- Each segment holds a range of offsets

---

## 4. Index Files

Kafka maintains:
- .log → actual data
- .index → offset mapping
- .timeindex → timestamp mapping

Purpose:
- Fast lookup without scanning full file

---

## 5. Retention Policy

Controls how long data is stored.

Config:

```
log.retention.hours=168
```

Behavior:
- Old segments deleted after retention
- Deletion is segment-based
- Kafka deletes segments, not individual messages

---

## 6. Retention Types

### 1. Time-based:

`log.retention.hours=168`

- Keep data for X hours

### 2. Size-based:

```
log.retention.bytes=10737418240
```

Behavior:
- Old segments deleted when size exceeded

---

## 7. Cleanup Policy

## delete (default)

```
cleanup.policy=delete
```

- Deletes old segments based on retention

---

## compact

```
cleanup.policy=compact
```

- Keeps latest record per key

Example:

Key=A → 100  
Key=A → 200  
Key=A → 300  

After compaction:
Key=A → 300

---

## combined
```
cleanup.policy=delete,compact
```

- Compaction + retention both applied

---

## 8. Message Lifecycle

```
Producer sends
   ↓
Message written to log
   ↓
Consumers read
   ↓
Retention deletes old data
```

Important:
- Kafka does NOT delete after consumption

---

## 9. Segment Deletion Behavior

### With delete policy

Retention reached → old segments deleted

### With compact policy

- Messages compacted per key
- Segments rewritten, not simply deleted

---

# Important Note

Retention reached ≠ immediate deletion

Reason:
- Log cleaner(thread) runs periodically

---

## 10. Replication

Each partition has:
- Leader
- Followers

Config:
```
replication.factor=3
```

Flow:
`Producer → Leader → Followers replicate`

---

## 11. Failure Handling

Broker crash:
- Follower becomes leader

Result:
- No data loss (if configured properly)

---

## 12. Consumer Read

Consumers read using offsets.

Flow:

`Consumer → offset → read from log`

---

## 13. Banking Example

Topic:
`account.transaction.completed.v1`

Partition usage:
- Different accounts distributed across partitions

Retention:
- Keep transactions for 7 days

Compaction use case:

`account.balance.v1 → latest balance only`

---

# 14. Why Kafka Is Fast

- Sequential disk writes
- OS page cache
- Zero-copy (sendfile)
- Batching

---

# 15. Summary Table

| Concept | Meaning |
|--------|--------|
| Partition | Append-only log |
| Segment | Chunk of log |
| Offset | Position |
| Retention | Data lifetime |
| Compaction | Keep latest per key |
| Replication | Fault tolerance |

---


## Final Understanding

Kafka = Distributed log storage system

---

# Key Insight

Kafka deletes SEGMENTS (not messages),
and behavior depends on cleanup.policy

