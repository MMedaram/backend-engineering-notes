---
title: Map Enhancements
parent: Java-8
nav_order: 11
---

# Map Enhancements

Java 8 added useful default methods to `Map`.

Important methods:

- `getOrDefault`
- `putIfAbsent`
- `computeIfAbsent`
- `computeIfPresent`
- `compute`
- `merge`
- `replaceAll`
- `forEach`

---

## getOrDefault

```java
int count = map.getOrDefault("apple", 0);
```

If key exists, returns value.

If key does not exist, returns default.

Edge case:

If key exists with null value, it returns null, not default.

---

## putIfAbsent

```
map.putIfAbsent("role", "USER");
```

Adds value only if key is missing or currently mapped to null.

---

## computeIfAbsent

Useful for cache-style logic.

```
Map<String, List<String>> usersByDept = new HashMap<>();

usersByDept.computeIfAbsent("IT", key -> new ArrayList<>())
    .add("Mohan");
```

Edge cases:

- Mapping function runs only if key is missing or value is null.
- If mapping function returns null, no mapping is added.
- If mapping function throws exception, no mapping is added.

---

## computeIfPresent

Runs only if key exists and current value is not null.

```
map.computeIfPresent("count", (key, value) -> value + 1);
```

If function returns null, mapping is removed.

---

## compute

Runs whether key exists or not.

```
map.compute("count", (key, value) -> value == null ? 1 : value + 1);
```

If remapping function returns null, mapping is removed or remains absent.

---

## merge

Good for counters and grouping.

```
Map<String, Integer> wordCount = new HashMap<>();

wordCount.merge("java", 1, Integer::sum);
```

Meaning:

- If key is missing, put 1.
- If key exists, add old value and new value.

If merge function returns null, key is removed.

---

## replaceAll

It updates every value in the map by applying a function to each key-value pair.

```
map.replaceAll((key, value) -> newValue);
```
- key → the current key
- value → the current value
- Returns the new value that should replace the old one

EX:

```
Map<String, Integer> prices = new HashMap<>();

prices.put("Apple", 100);
prices.put("Banana", 50);
prices.put("Orange", 80);
```

- Apple  -> 100
- Banana -> 50
- Orange -> 80

```
prices.replaceAll((item, price) -> price + 10);
```

The map becomes:

- Apple  -> 110
- Banana -> 60
- Orange -> 90

---

## forEach

- Use forEach() when you want to perform an action on every element.

```
map.forEach((key, value) -> {
    System.out.println(key + " = " + value);
});
```

---

## Common Exceptions

| Case                                           | Possible Exception                |
|------------------------------------------------|-----------------------------------|
| Null key/value in map that does not allow null | `NullPointerException`            |
| Unsupported map update                         | `UnsupportedOperationException`   |
| Modifying map incorrectly during traversal     | `ConcurrentModificationException` |
| Mapping/remapping function throws              | Same exception is propagated      |

---

## Best Practices

- Use `computeIfAbsent` for map-of-list patterns.
- Use `merge` for counters.
- Keep mapping functions simple.
- Avoid modifying the same map inside mapping functions.
- Be careful with null values.
- Prefer `ConcurrentHashMap` for concurrent updates.

---

## Quick Summary

Java 8 Map methods reduce manual `containsKey` and null-check code. `computeIfAbsent` is great for lazy creation, and `merge` is great for counters.

