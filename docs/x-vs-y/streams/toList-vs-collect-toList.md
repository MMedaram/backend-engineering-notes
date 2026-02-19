---
title: toList() vs collect(Collectors.toList())
parent: Streams
nav_order: 8
---

# toList() vs collect(Collectors.toList())

Java provides two common ways to convert a stream into a List:

1. `stream().collect(Collectors.toList())`
2. `stream().toList()`  (introduced in Java 16)

Although both produce a List, they are **not identical**.

---

## Basic Difference

| Feature | toList() | collect(Collectors.toList()) |
|----------|----------|------------------------------|
| Introduced in | Java 16 | Java 8 |
| Mutability | ❌ Unmodifiable | ✅ Usually mutable |
| Performance | Slightly optimized | Standard collector |
| Simplicity | Cleaner syntax | Verbose |

---

## collect(Collectors.toList())

```java
List<String> result = names.stream().collect(Collectors.toList());
```

### Characteristics

✔ Returns a mutable list (usually ArrayList)    
✔ Available since Java 8     
✔ Can modify result      

#### Example:

> result.add("New Value"); // ✅ Works

---

## toList()

```
List<String> result = names.stream().toList();
```

### Characteristics

✔ Shorter and cleaner    
✔ Returns an unmodifiable list    
✔ Cannot add/remove elements    

#### Example:

> result.add("New Value"); // ❌ Throws UnsupportedOperationException

---

> `collect(Collectors.toList())` creates a mutable list and has been available since Java 8, 
> while `toList()` was introduced in Java 16 and returns an unmodifiable list. toList() is cleaner and preferred when immutability is desired.

