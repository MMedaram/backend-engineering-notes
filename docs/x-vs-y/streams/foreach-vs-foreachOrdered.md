---
title: forEach() vs forEachOrdered()
parent: Streams
nav_order: 4
---

# forEach() vs forEachOrdered()

In Java Streams, both `forEach()` and `forEachOrdered()` are terminal operations
used to iterate over elements.

However, their behavior differs significantly — especially when used with
**parallel streams**.

---

## Basic Difference

| Method | Order Guarantee | Performance | Best Used With |
|--------|-----------------|------------|----------------|
| forEach() | ❌ No order guarantee | Faster | Parallel streams |
| forEachOrdered() | ✅ Preserves encounter order | Slightly slower | When order matters |

---

## Using forEach() (Sequential Stream)

```text
List<Integer> list = List.of(1, 2, 3, 4);

list.stream().forEach(System.out::println);
```

### output

```text
1
2
3
4
```

✔ Order preserved    
✔ Single-thread execution    

---

## Using forEach() (Parallel Stream)

```
list.parallelStream().forEach(System.out::println);
```

### Possible Output:

```
3
1
4
2
```

#### Why?

- Elements are processed in parallel
- No guarantee of encounter order
- Faster execution

---

## Using forEachOrdered() (Parallel Stream)

```
list.parallelStream().forEachOrdered(System.out::println);
```
#### Output:

```
1
2
3
4
```
- ✔ Maintains encounter order
- ❌ Reduces parallel performance benefit

---

## What Is "Encounter Order"?

Encounter order means:
- The order in which elements appear in the source.

#### Examples:

| Source        | Encounter Order Exists? |
| ------------- | ----------------------- |
| List          | Yes                     |
| Array         | Yes                     |
| LinkedHashSet | Yes                     |
| HashSet       | No (unordered)          |

If the source is unordered, forEachOrdered() has no meaningful order to preserve.

---

## Performance Impact

### forEachOrdered():

- Forces synchronization
- Waits for lower-index tasks to complete
- Reduces parallel efficiency

### forEach():

- Processes elements as soon as they are ready
- Maximizes parallel performance

