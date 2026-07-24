---
title: Collectors teeing
parent: Java-12
nav_order: 4
---

# Java 12 - Collectors.teeing()

Java 12 added:

```
Collectors.teeing(downstream1, downstream2, merger)
```

Simple meaning:

It lets one stream collect two different results, then combine them into one final result.

Think of it like this:

```text
same stream data
    -> collector 1
    -> collector 2
    -> merge both results
```

---

## Why was it introduced?

Before Java 12, if we wanted two results from the same stream, we often had to:

- stream the data twice
- write manual loops
- collect one result first, then calculate another result separately

`teeing()` makes this cleaner when both results can be calculated from the same data.

---

## Basic example: count and sum

```java
List<Integer> numbers = List.of(10, 20, 30);

String result = numbers.stream()
        .collect(Collectors.teeing(
                Collectors.counting(),
                Collectors.summingInt(Integer::intValue),
                (count, sum) -> "count=" + count + ", sum=" + sum
        ));
```

Output:

```text
count=3, sum=60
```

One stream pass produced two results.

---

## Daily backend use case: order summary

```java
public class Order {
    private final String status;
    private final int amount;

    public Order(String status, int amount) {
        this.status = status;
        this.amount = amount;
    }

    public String getStatus() {
        return status;
    }

    public int getAmount() {
        return amount;
    }
}
```

Summary class:

```java
public class OrderSummary {
    private final long orderCount;
    private final int totalAmount;

    public OrderSummary(long orderCount, int totalAmount) {
        this.orderCount = orderCount;
        this.totalAmount = totalAmount;
    }

    @Override
    public String toString() {
        return "OrderSummary{orderCount=" + orderCount + ", totalAmount=" + totalAmount + "}";
    }
}
```

Using `teeing()`:

```
List<Order> orders = List.of(
        new Order("PAID", 100),
        new Order("PAID", 200),
        new Order("CANCELLED", 50)
);

OrderSummary summary = orders.stream()
        .filter(order -> "PAID".equals(order.getStatus()))
        .collect(Collectors.teeing(
                Collectors.counting(),
                Collectors.summingInt(Order::getAmount),
                OrderSummary::new
        ));

System.out.println(summary);
```

Output:

```text
OrderSummary{orderCount=2, totalAmount=300}
```

---

## Daily backend use case: min and max

```
List<Integer> responseTimes = List.of(120, 80, 300, 200);

String range = responseTimes.stream()
        .collect(Collectors.teeing(
                Collectors.minBy(Integer::compareTo),
                Collectors.maxBy(Integer::compareTo),
                (min, max) -> "min=" + min.orElse(0) + ", max=" + max.orElse(0)
        ));

System.out.println(range);
```

Output:

```text
min=80, max=300
```

---

## Daily backend use case: average with count

```java
List<Integer> ratings = List.of(5, 4, 5, 3);

String report = ratings.stream()
        .collect(Collectors.teeing(
                Collectors.counting(),
                Collectors.averagingInt(Integer::intValue),
                (count, average) -> "ratings=" + count + ", average=" + average
        ));
```

Useful for:

- dashboard cards
- report summaries
- API response metadata
- analytics calculations

---

## How teeing works internally

For every stream element:

1. Collector 1 receives the element.
2. Collector 2 receives the same element.
3. After the stream ends, collector 1 produces result 1.
4. Collector 2 produces result 2.
5. Merger combines result 1 and result 2.

---

## Empty stream behavior

```java
List<Integer> numbers = List.of();

String result = numbers.stream()
        .collect(Collectors.teeing(
                Collectors.counting(),
                Collectors.summingInt(Integer::intValue),
                (count, sum) -> "count=" + count + ", sum=" + sum
        ));

```

Output:

```text
count=0, sum=0
```

But this depends on the downstream collectors.

Example:

```
Collectors.minBy(Integer::compareTo)
```

returns:

```text
Optional.empty
```

for an empty stream.

---

## Edge cases and exceptions

### 1. First collector is null

```
List<Integer> numbers = List.of(1, 2, 3);

numbers.stream()
        .collect(Collectors.teeing(null, Collectors.counting(), (a, b) -> a));
```

Output:

```text
java.lang.NullPointerException
```

---

### 2. Second collector is null

```
List<Integer> numbers = List.of(1, 2, 3);

numbers.stream()
        .collect(Collectors.teeing(Collectors.counting(), null, (a, b) -> a));
```

Output:

```text
java.lang.NullPointerException
```

---

### 3. Merger is null

```
List<Integer> numbers = List.of(1, 2, 3);

numbers.stream()
        .collect(Collectors.teeing(
                Collectors.counting(),
                Collectors.counting(),
                null
        ));
```

Output:

```text
java.lang.NullPointerException
```

---

### 4. Merger throws exception

```java
String result = numbers.stream()
        .collect(Collectors.teeing(
                Collectors.counting(),
                Collectors.summingInt(Integer::intValue),
                (count, sum) -> {
                    throw new IllegalStateException("Cannot merge");
                }
        ));
```

Output:

```text
java.lang.IllegalStateException: Cannot merge
```

---

### 5. Both collectors process all elements

Do not use `teeing()` if one result needs only a small part but the other result is very expensive for every element.

Both downstream collectors receive every element.

---

## teeing() vs summarizingInt()

Sometimes Java already has a better collector.

For count, sum, min, max, and average of numbers:

```java
IntSummaryStatistics stats = orders.stream()
        .collect(Collectors.summarizingInt(Order::getAmount));
```

Use `teeing()` when the two results are different enough that one built-in summary collector is not enough.

---

## Best practices

1. Use `teeing()` when you need two results from the same stream.
2. Keep the merger simple.
3. Use a clear result class for business summaries.
4. For numeric stats, consider `summarizingInt`, `summarizingLong`, or `summarizingDouble` first.
5. Remember empty stream behavior depends on downstream collectors.
6. Do not use `teeing()` if a simple loop is more readable.

---

## Interview one-liner

Java 12 added `Collectors.teeing()` to run two downstream collectors on the same stream and merge their results into one final value.

