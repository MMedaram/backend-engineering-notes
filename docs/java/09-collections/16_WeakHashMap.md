---
title: WeakHashMap (C)
parent: Collection Framework
nav_order: 16
---

# WeakHashMap 

## Overview

- `WeakHashMap` is a **Map implementation where keys are weakly referenced**
- Entries are automatically removed when the **key is no longer in use**
- Part of `java.util`

---

## Key Idea

👉 Normal `HashMap`:
- Strong reference to keys → NOT garbage collected

👉 `WeakHashMap`:
- Uses **WeakReference for keys**
- If no strong reference exists → key is eligible for **Garbage Collection (GC)**

---

## How It Works

```
WeakHashMap<K, V>
```

Internally:

Keys are wrapped as: `WeakReference<K>`

### What is a Weak Reference?

👉 A weak reference means:

> "If no strong reference exists, GC can remove this object anytime"

#### Example

```java

import java.util.*;

public class Test {
    public static void main(String[] args) throws Exception {
        WeakHashMap<Object, String> map = new WeakHashMap<>();

        Object key = new Object();
        map.put(key, "Value");

        System.out.println(map); // {key=Value}

        key = null; // remove strong reference

        System.gc(); // suggest GC
        Thread.sleep(1000);

        System.out.println(map); // {} (entry removed)
    }
}
```


### Behavior Summary

| Scenario                    | Result               |
| --------------------------- | -------------------- |
| Key has strong reference    | Entry stays          |
| Key has NO strong reference | Entry removed by GC  |
| Value has reference         | ❌ Does NOT matter    |
| Key is weak                 | ✅ Controls lifecycle |


## Important Characteristics

| Feature            | Description                |
| ------------------ | -------------------------- |
| **Reference Type** | Weak reference (keys only) |
| **Auto Cleanup**   | Yes (via GC)               |
| **Thread Safety**  | Not synchronized           |
| **Order**          | Not guaranteed             |


### Why Use WeakHashMap?

**Main Use Case: Memory-Sensitive Caching**

👉 Example:

- Cache data without preventing GC

> WeakHashMap<Object, String> cache = new WeakHashMap<>();

If key is no longer used:

- Entry disappears automatically
- Prevents memory leaks


### Real-World Use Cases

- Caches
- Metadata storage
- Listeners / observers
- Canonical mappings



-------------


## WeakHashMap vs HashMap

| Feature     | HashMap         | WeakHashMap              |
| ----------- | --------------- | ------------------------ |
| Reference   | Strong          | Weak (keys)              |
| GC behavior | Keys stay       | Keys can be removed      |
| Memory      | Can leak        | Auto cleanup             |
| Use case    | General purpose | Cache / memory-sensitive |


## When to Use

When you want:
- Automatic memory cleanup
- Cache without memory leaks

## When NOT to Use
When you need:
- Persistent data
- Predictable lifecycle
- When keys must NEVER disappear 

### Developer Notes
- GC timing is unpredictable
- Do NOT rely on exact removal time
- Values are NOT weak → only keys matter