---
title: HashMap vs WeakHashMap vs IdentityHashMap
parent: Collections
nav_order: 17
---

# HashMap vs WeakHashMap vs IdentityHashMap

- **HashMap** uses `equals()` for key comparison and keeps entries strongly
- **WeakHashMap** allows entries to be removed by GC when keys are no longer referenced
- **IdentityHashMap** uses `==` instead of `equals()` for key comparison

---

## Core Difference

| Point | HashMap | WeakHashMap | IdentityHashMap |
|-----|--------|-------------|-----------------|
| Key comparison | equals() | equals() | == (reference) |
| Reference type | Strong | Weak (keys) | Strong |
| GC behavior | Entries not GCed | Keys can be GCed | Entries not GCed |
| Allows null key | ✅ One | ✅ One | ✅ Multiple |
| Allows null values | ✅ Yes | ✅ Yes | ✅ Yes |
| Typical use | General-purpose map | Cache / memory-sensitive | Reference-based keys |
| Commonly used | ✅ Very common | ⚠️ Less common | ⚠️ Rare |

---

## HashMap

A **HashMap** is the most commonly used `Map` implementation.

### Characteristics
- Uses `hashCode()` and `equals()` for key comparison
- Holds **strong references** to keys and values
- Entries remain until explicitly removed

### When to use
- General-purpose key–value storage
- When you control lifecycle of keys

---

## WeakHashMap

A **WeakHashMap** stores keys using **weak references**.

### Characteristics
- If a key is **no longer strongly referenced**, GC can remove the entry
- Helps avoid memory leaks
- Values are removed automatically when key is GCed

### Typical use cases
- Caches
- Metadata storage
- Listener registries

> “WeakHashMap entries can be garbage collected when keys are no longer referenced.”

---

## IdentityHashMap

An **IdentityHashMap** compares keys using **reference equality (`==`)**.

### Characteristics
- Ignores `equals()` method
- Two different objects with same content are treated as different keys
- Allows multiple null keys (because comparison is reference-based)

### Typical use cases
- Framework internals
- Object graph processing
- When identity matters more than equality

---

- **HashMap** → Normal map
- **WeakHashMap** → GC-aware map
- **IdentityHashMap** → Reference-based map

---

> **HashMap uses equals() for key comparison, WeakHashMap allows entries to be garbage collected when keys are weakly referenced, and IdentityHashMap uses reference equality (==) for keys.**

