---
title: server.properties
parent: Kafka
nav_order: 15
---

# Kafka Broker Configuration (server.properties)

---

## 1. Broker Basics

```properties
broker.id=1
listeners=PLAINTEXT://localhost:9092
log.dirs=/var/lib/kafka/data
```

- broker.id → unique broker identifier  
- listeners → host/port Kafka listens on  
- log.dirs → storage location for Kafka data  

 If `log.dirs` disk is full → Kafka stops working

---

## 2. Topic Defaults

```properties
num.partitions=3
default.replication.factor=3
min.insync.replicas=2
```

- partitions → parallelism  
- replication.factor → fault tolerance  
- min.insync.replicas → durability guarantee  

---

## 3. Storage & Retention

```properties
log.segment.bytes=1073741824
log.retention.hours=168
log.retention.bytes=10737418240
log.cleanup.policy=delete
```

- segment size → file chunking  
- retention → data lifetime  
- cleanup.policy → delete / compact  

---

## 4. Compaction Support

```properties
log.cleaner.enable=true
log.cleaner.threads=2
```

Used when:
```
cleanup.policy=compact
```

---

## 5. Replication Safety

```properties
unclean.leader.election.enable=false
```

- prevents data loss during leader election  

---

## 6. Message Size Limits

```properties
message.max.bytes=1048576
replica.fetch.max.bytes=1048576
```

- max message size Kafka accepts  

---

## 7. Network & Threads

```properties
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
```

- controls throughput and performance  

---

## 8. Offset Topic

```properties
offsets.topic.replication.factor=3
```

- ensures consumer offset safety  

---

## 9. Topic Creation Control

```properties
auto.create.topics.enable=false
```

- prevents accidental topic creation  

---

## 10. Optional Flush Config

```properties
log.flush.interval.messages=10000
log.flush.interval.ms=1000
```

- usually not required (Kafka optimized already)  

---

## 11. Zookeeper / KRaft

## Zookeeper mode
```properties
zookeeper.connect=localhost:2181
```

## KRaft mode
- no zookeeper required  

---

## 12. Recommended Production Configuration

```properties
# Reliability
default.replication.factor=3
min.insync.replicas=2
unclean.leader.election.enable=false

# Storage
log.retention.hours=168
log.segment.bytes=1073741824

# Safety
auto.create.topics.enable=false

# Offsets
offsets.topic.replication.factor=3
```

---

## 13. Producer Mapping

| Producer Config | Broker Config |
|----------------|--------------|
| acks=all | min.insync.replicas=2 |
| retries | replication.factor |
| idempotence | durability |

---

# Final Understanding

Kafka Broker Config (server.properties) controls:

- durability  
- storage  
- replication  
- system safety  

Application config controls:

- how your app interacts with Kafka  
