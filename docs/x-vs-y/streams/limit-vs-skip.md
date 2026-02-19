---
title: limit() vs skip()
parent: Streams
nav_order: 9
---

# limit() vs skip()

Both `limit()` and `skip()` are intermediate operations in Java Streams.
They are mainly used for **slicing streams**, especially in pagination scenarios.

---

##  Basic Difference

| limit(n) | skip(n) |
|-----------|----------|
| Takes first n elements | Skips first n elements |
| Reduces stream size | Removes elements from start |
| Used for pagination | Used for pagination offset |
| Preserves encounter order | Preserves encounter order |

---

## limit()

`limit(n)` returns the **first n elements** from the stream.

### Example

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

List<Integer> result =  numbers.stream().limit(3).toList();
```

#### Output:

> [1, 2, 3]

✔ Stops after n elements     
✔ Useful for page size       

---

## skip()

skip(n) skips the first n elements and processes the rest.

### Example

```java
List<Integer> result = numbers.stream().skip(2).toList();
```

#### Output:

> [3, 4, 5]

✔ Useful for page offset     

---

> `limit(n)` returns the first n elements of a stream
> `skip(n)` ignores the first n elements.
