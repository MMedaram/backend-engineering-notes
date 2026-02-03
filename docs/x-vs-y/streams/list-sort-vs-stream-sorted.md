---
title:  List.sort() vs stream().sorted()
parent: Streams
nav_order: 7
---

# List.sort() vs stream().sorted()

- Use **List.sort()** when you want to **sort the existing list**
- Use **stream().sorted()** when you want to **create a new sorted list**

---

## Key Difference

| Point | List.sort() | stream().sorted() |
|------|------------|------------------|
| Works on | Existing List | Stream pipeline |
| Modifies original list | ✅ Yes | ❌ No |
| Creates new list | ❌ No | ✅ Yes |
| In-place sorting | ✅ Yes | ❌ No |
| Memory usage | Less | More |
| Java version | Java 8+ | Java 8+ |

---

## When to Use `List.sort()`
- You want to **sort the same list**
- You don’t need immutability
- You care about **performance & memory**

---

## When to Use `stream().sorted()`
- You want to keep original list unchanged
- You are already using streams
- You want a **new sorted result**
- Functional / immutable style

---


> **List.sort() sorts the existing list in place, whereas stream().sorted() returns a new sorted result without modifying the original list.**


