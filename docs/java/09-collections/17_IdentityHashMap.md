---
title: IdentityHashMap (C)
parent: Collection Framework
nav_order: 17
---



# IdentityHashMap 

## Overview

- `IdentityHashMap` is a **Map implementation that uses reference equality (`==`) instead of `equals()`**
- Part of `java.util`
- Used when **object identity matters more than logical equality**

---

## Key Difference 

👉 Normal `HashMap`:

> key1.equals(key2)


👉 IdentityHashMap:

> key1 == key2

### What does this mean?

Two objects:

```
String a = new String("A");
String b = new String("A");

a.equals(b) → ✅ true
a == b → ❌ false
```

### Behavior

```java
public static void main(String[] args) {
    Map<String, String> map = new IdentityHashMap<>();

    map.put(a, "First");
    map.put(b, "Second");

    System.out.println(map);
}
```

👉 Output:

> {A=First, A=Second}

✔ Both entries exist (treated as different keys)


### Key Characteristics

| Feature            | Description                    |
| ------------------ | ------------------------------ |
| **Equality Check** | Uses `==` (reference equality) |
| **Duplicates**     | Allowed (if different objects) |
| **Order**          | Not guaranteed                 |
| **Thread Safety**  | Not synchronized               |
| **Null Keys**      | Allowed                        |


### Internal Working

- Uses array-based structure, not standard buckets like HashMap
- Stores keys and values in alternating slots:
> [ key1, value1, key2, value2, ... ]

- Uses System.identityHashCode() instead of hashCode()

### Important Methods Behavior

| Method Signature                  | Description     | Behavior / Edge Case                         |
| --------------------------------- | --------------- | -------------------------------------------- |
| `V put(K key, V value)`           | Adds entry      | Allows logically equal but different objects |
| `V get(Object key)`               | Retrieves value | Works only if same reference used            |
| `boolean containsKey(Object key)` | Checks key      | Uses `==`, not equals()                      |
| `V remove(Object key)`            | Removes entry   | Removes only exact reference                 |


## Code Examples

### 1. Identity vs Equality
   
```java
public static void main(String[] args) {
    Map<String, String> map = new IdentityHashMap<>();

    String a = new String("A");
    String b = new String("A");

    map.put(a, "First");

    System.out.println(map.get(b)); // null ❌
    
}
```

👉 Because a != b (different references)

### 2. Normal HashMap Comparison
   
```java
public static void main(String[] args) {
    Map<String, String> map = new HashMap<>();

    String a = new String("A");
    String b = new String("A");

    map.put(a, "First");

    System.out.println(map.get(b)); // First ✅
    
}
```

### When to Use

When object identity matters

Examples:

- Object graph processing
- Serialization frameworks
- Tracking object references (not values)
- Proxy/debugging tools

### When NOT to Use

- General-purpose mapping ❌
- When logical equality matters ❌
- Most business applications ❌

### IdentityHashMap vs HashMap

| Feature        | HashMap     | IdentityHashMap             |
| -------------- | ----------- | --------------------------- |
| Equality       | equals()    | ==                          |
| Hashing        | hashCode()  | identityHashCode()          |
| Duplicate keys | Not allowed | Allowed (if different refs) |
| Use case       | General     | Special cases               |


## Important Gotchas ⚠️

1. Retrieval issue

> map.put(new String("A"), "Value");
> 
> map.get(new String("A")); // null ❌

2. Breaks Map contract (subtle point)
    
- Map contract expects equals() usage
- IdentityHashMap violates this expectation intentionally
  
## Developer Notes

- Rarely used in normal applications
- Useful in framework-level programming
- Do NOT replace HashMap with this casually

