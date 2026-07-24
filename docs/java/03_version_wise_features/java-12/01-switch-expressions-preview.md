---
title: Switch Expressions Preview
parent: Java-12
nav_order: 1
---

# Java 12 - Switch Expressions Preview

Java 12 introduced **switch expressions** as a **preview feature**.

Simple meaning:

Old `switch` was mainly used to execute code.

Java 12 preview allowed `switch` to also return a value.

This made many `switch` blocks shorter, safer, and easier to read.

---

## Preview Feature Status

| Version | Status                                         |
|---------|------------------------------------------------|
| Java 12 | Preview, used `break value` inside block cases |
| Java 13 | Preview again, changed to `yield`              |
| Java 14 | Standard production feature                    |

Important:

For modern Java projects, use the final Java 14+ syntax with `yield`.

For Java 12 learning, understand that Java 12 used `break value` in block cases.

---

## Why was it introduced?

Old `switch` had common problems:

- too many `break` statements
- accidental fall-through bugs
- repeated assignment code
- bigger scope for variables than expected
- not easy to use directly as a value

Old style:

```
String label;

switch (status) {
    case "NEW":
        label = "New order";
        break;
    case "PAID":
        label = "Paid order";
        break;
    case "CANCELLED":
        label = "Cancelled order";
        break;
    default:
        label = "Unknown order";
}
```

This works, but it is noisy.

If we forget one `break`, behavior can be wrong.

---

## Java 12 preview style

```
String label = switch (status) {
    case "NEW" -> "New order";
    case "PAID" -> "Paid order";
    case "CANCELLED" -> "Cancelled order";
    default -> "Unknown order";
};
```

Now `switch` directly returns a value.

---

## Arrow cases do not fall through

```
switch (status) {
    case "NEW" -> System.out.println("New order");
    case "PAID" -> System.out.println("Paid order");
    default -> System.out.println("Unknown order");
}
```

Only the matching case runs.

There is no accidental fall-through.

---

## Multiple labels in one case

```
String type = switch (day) {
    case "SATURDAY", "SUNDAY" -> "Weekend";
    case "MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY" -> "Weekday";
    default -> "Invalid day";
};
```

This avoids writing repeated case blocks.

---

## Daily backend use case: map status to message

```
String message = switch (orderStatus) {
    case "CREATED" -> "Order created";
    case "PAID" -> "Payment received";
    case "SHIPPED" -> "Order shipped";
    case "DELIVERED" -> "Order delivered";
    case "CANCELLED" -> "Order cancelled";
    default -> "Unknown order status";
};
```

Good for:

- status mapping
- API response labels
- error messages
- workflow decisions
- enum-based business logic

---

## Daily backend use case: enum switch

```java
enum PaymentStatus {
    PENDING, SUCCESS, FAILED
}

String action = switch (paymentStatus) {
    case PENDING -> "Retry later";
    case SUCCESS -> "Send invoice";
    case FAILED -> "Notify customer";
};
```

For enum switches, if all enum constants are covered, a `default` may not be needed.

---

## Block case in Java 12 preview

When a case needs multiple lines, use a block.

In Java 12 preview, block cases returned a value using `break value`.

```
int charge = switch (customerType) {
    case "REGULAR" -> 100;
    case "PREMIUM" -> 80;
    case "PARTNER" -> {
        int discount = 30;
        break 100 - discount;
    }
    default -> throw new IllegalArgumentException("Unknown customer type: " + customerType);
};
```

Important:

This Java 12 preview syntax changed later.

In Java 14+, use:

```
int charge = switch (customerType) {
    case "REGULAR" -> 100;
    case "PREMIUM" -> 80;
    case "PARTNER" -> {
        int discount = 30;
        yield 100 - discount;
    }
    default -> throw new IllegalArgumentException("Unknown customer type: " + customerType);
};
```

---

## Throw from a switch expression

```
int httpCode = switch (result) {
    case "OK" -> 200;
    case "CREATED" -> 201;
    case "BAD_REQUEST" -> 400;
    default -> throw new IllegalArgumentException("Unknown result: " + result);
};
```

This is useful when unknown input is a real error.

---

## Exhaustive switch is required

A switch expression must return a value for every possible input.

Bad:

```
String label = switch (status) {
    case "NEW" -> "New order";
    case "PAID" -> "Paid order";
};
```

Possible compile error:

```text
the switch expression does not cover all possible input values
```

Better:

```
String label = switch (status) {
    case "NEW" -> "New order";
    case "PAID" -> "Paid order";
    default -> "Unknown order";
};
```

---

## Variable scope is cleaner

Old switch blocks can make variable scope confusing.

With arrow cases, variables declared inside a block stay inside that block.

```
String result = switch (type) {
    case "A" -> {
        String message = "Type A";
        break message;
    }
    case "B" -> {
        String message = "Type B";
        break message;
    }
    default -> "Unknown";
};
```

Each `message` belongs only to its own block.

---

## Compile and run Java 12 preview code

Compile:

```bash
javac --release 12 --enable-preview SwitchDemo.java
```

Run:

```bash
java --enable-preview SwitchDemo
```

If running a JAR:

```bash
java --enable-preview -jar app.jar
```

---

## Edge cases and negative cases

### 1. Missing preview flag

Compile without preview flag:

```bash
javac --release 12 SwitchDemo.java
```

Possible result:

```text
error: switch expressions are a preview feature and are disabled by default
```

---

### 2. Null switch value

```
String status = null;

String label = switch (status) {
    case "NEW" -> "New";
    default -> "Unknown";
};
```

Possible result:

```text
java.lang.NullPointerException
```

Java 12 switch does not support `case null`.

---

### 3. Missing value from block

Bad:

```
int value = switch (type) {
    case "A" -> {
        System.out.println("A");
    }
    default -> 0;
};
```

Possible compile error:

```text
switch rule completes without providing a value
```

In Java 12 preview, use `break value`.

In Java 14+, use `yield value`.

---

### 4. Duplicate case labels

Bad:

```
String value = switch (status) {
    case "NEW" -> "New";
    case "NEW" -> "Again";
    default -> "Unknown";
};
```

Possible compile error:

```text
duplicate case label
```

---

### 5. Do not jump through switch expression

Inside a switch expression, control flow must produce a value or throw.

Using `continue`, `return`, or unlabeled flow in confusing ways can fail compilation.

Keep switch expression branches simple.

---

## Best practices

1. Use switch expressions when you are mapping one input to one result.
2. Use `default` when input can come from outside the system.
3. Throw an exception from `default` when unknown input is a bug.
4. Keep each case small.
5. Avoid side effects inside switch expressions.
6. For Java 12 learning, remember `break value`.
7. For real modern projects, use Java 14+ final syntax with `yield`.
8. Do not use preview features in production unless the team has clearly decided it.

---

## Interview one-liner

Java 12 introduced switch expressions as a preview feature so `switch` could return a value, reduce fall-through bugs, support arrow labels, and make mapping logic cleaner. It became standard in Java 14.

---
