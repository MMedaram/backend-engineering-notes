---
title: Map vs Dictionary
parent: Collections
nav_order: 12
---

# Map vs Dictionary

- **Map** is a **modern interface** used to store key–value pairs
- **Dictionary** is a **legacy abstract class** and is obsolete

---

## Core Difference

| Point | Map | Dictionary |
|-----|-----|------------|
| Type | Interface | Abstract class |
| Introduced in | Java 1.2 | Java 1.0 |
| Status | Actively used | Legacy / obsolete |
| Thread safety | ❌ Not by default | ✅ Yes (synchronized) |
| Allows null keys | Depends on implementation | ❌ No |
| Allows null values | Depends on implementation | ❌ No |
| Modern alternatives | HashMap, TreeMap, ConcurrentHashMap | Hashtable |
| Recommended for use | ✅ Yes | ❌ No |

---

## Map 

`Map` is part of the **Java Collections Framework** used to store data in **key–value pairs**.

### Characteristics
- Keys are unique
- Values can be duplicated
- Flexible implementations available
- Not synchronized by default

### Common Implementations
- HashMap
- TreeMap
- LinkedHashMap
- ConcurrentHashMap

---

## Dictionary 

`Dictionary` is a **legacy abstract class** introduced in early Java versions.

### Characteristics
- Thread-safe by default
- Does not allow null keys or values
- Limited API
- Replaced by Map interface

### Known Implementation
- Hashtable

---

> **Map is a modern interface for key–value storage, whereas Dictionary is a legacy abstract class that is no longer recommended for use.**
