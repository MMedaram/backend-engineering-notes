---
title: findFirst() vs findAny()
parent: Streams
nav_order: 7
---

# findFirst() vs findAny()

Both `findFirst()` and `findAny()` are terminal operations used to
retrieve a single element from a stream.

They return:

```
Optional<T>
```

However, their behavior differs — especially in parallel streams.

---

| findFirst()               | findAny()                    |
| ------------------------- | ---------------------------- |
| Returns first element     | Returns any element          |
| Preserves encounter order | Does not guarantee order     |
| May be slower in parallel | Faster in parallel           |
| Deterministic             | Non-deterministic (parallel) |

---

## findFirst()?

`findFirst()` returns the first element in encounter order.

```text
List<Integer> numbers = List.of(10, 20, 30, 40);

Optional<Integer> result =
    numbers.stream()
           .findFirst();

System.out.println(result.get());

```

> Result :: 10

✔ Always returns first element      
✔ Order preserved       

---

## findAny()?

`findAny()` returns any element from the stream.

```text
Optional<Integer> result =
    numbers.stream()
           .findAny();
```

> Result :: 10

In sequential streams:

- Often behaves like `findFirst()`    
- But not guaranteed by contract  

---

> `findFirst()` returns the first element in encounter order and guarantees ordering, even in parallel streams. findAny() returns any element and is more efficient in parallel streams because it does not require order preservation.

