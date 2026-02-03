---
title:  map vs flatMap (Java 8 Streams)
parent: Streams
nav_order: 1
---

# map vs flatMap (Java 8 Streams)

- **map** transforms **one element into one element**
- **flatMap** transforms **one element into multiple elements** and flattens the result

If mapping creates a **nested structure**, use **flatMap**.

---

## 🔍 Top Differences

| Feature | map | flatMap |
|------|-----|---------|
| Output per input | One-to-one | One-to-many |
| Return type | Stream<T> | Stream<T> (flattened) |
| Common use | Simple transformation | Flattening collections |

---

### `map`
- Applies a `function` to each element
- Result size = input size
- Does **not** flatten nested structures

#### Example

```java
List<String> names = List.of("java", "spring");

List<String> result = names.stream()
        .map(String::toUpperCase)
        .toList();

// Output: ["JAVA", "SPRING"]
```

### `flatMap`
- Applies a function that returns a **stream**
- Flattens multiple streams into **one stream**
- Used when dealing with **collections inside collections**

#### Example

```java

class Employee {
    List<String> skills;
}
```

### ❌ Wrong (map)

```java 
List<List<String>> skills = employees.stream()
        .map(Employee::getSkills)
        .toList();

```

### ✅ Correct (flatMap)

```java
Set<String> skills = employees.stream()
        .flatMap(e -> e.getSkills().stream())
        .collect(Collectors.toSet());

```


## summary
- Use `map` for simple transformations
- Use `flatMap` when dealing with nested data

> Remember: flattening is the key difference
>