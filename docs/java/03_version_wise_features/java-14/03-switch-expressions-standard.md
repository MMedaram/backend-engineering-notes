---
title: Switch Expressions Standard
parent: Java-14
nav_order: 3
---

# Java 14 - Switch Expressions Standard

Java 14 made **switch expressions** a **standard production feature**.

Simple meaning:

`switch` can now return a value cleanly and safely.

This feature was preview in Java 12 and Java 13.

In Java 14, it became final.

---

## Feature Status

| Version | Status                             |
|---------|------------------------------------|
| Java 12 | Preview, used `break value`        |
| Java 13 | Second preview, changed to `yield` |
| Java 14 | Standard production feature        |

Important:

In Java 14, switch expressions do **not** need preview flags.

---

## Why was it introduced?

Old `switch` statements had common problems:

- too many `break` statements
- accidental fall-through bugs
- repeated assignment
- bigger variable scope than needed
- hard to use directly as a value

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

## Java 14 switch expression

```java
String label = switch (status) {
    case "NEW" -> "New order";
    case "PAID" -> "Paid order";
    case "CANCELLED" -> "Cancelled order";
    default -> "Unknown order";
};
```

This directly produces a value.

---

## Daily backend use case: map status to HTTP code

```java
int httpStatus = switch (result) {
    case "SUCCESS" -> 200;
    case "CREATED" -> 201;
    case "VALIDATION_FAILED" -> 400;
    case "NOT_FOUND" -> 404;
    default -> 500;
};
```

Useful for:

- status mapping
- response code mapping
- workflow decisions
- enum-to-label conversion
- error classification

---

## Daily backend use case: enum mapping

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

When all enum values are covered, a `default` is not always required.

This is useful because if a new enum constant is added later, the compiler can help catch missing cases after recompilation.

---

## Multiple labels in one case

```java
String dayType = switch (day) {
    case "SATURDAY", "SUNDAY" -> "Weekend";
    case "MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY" -> "Weekday";
    default -> "Invalid day";
};
```

This avoids repeated case blocks.

---

## Block case with yield

Use a block when one case needs multiple statements.

```java
int discount = switch (customerType) {
    case "REGULAR" -> 0;
    case "PREMIUM" -> 10;
    case "PARTNER" -> {
        int baseDiscount = 15;
        int campaignBonus = 5;
        yield baseDiscount + campaignBonus;
    }
    default -> throw new IllegalArgumentException("Unknown customer type: " + customerType);
};
```

`yield` returns the value from that case block.

---

## Throw from switch expression

```java
String priority = switch (severity) {
    case 1 -> "LOW";
    case 2 -> "MEDIUM";
    case 3 -> "HIGH";
    default -> throw new IllegalArgumentException("Invalid severity: " + severity);
};
```

This is better than silently returning a wrong default when unknown input is a real bug.

---

## Switch statement vs switch expression

| Switch Statement                     | Switch Expression                     |
|--------------------------------------|---------------------------------------|
| Executes code                        | Produces a value                      |
| Can be non-exhaustive                | Must cover all possible values        |
| Traditional `case:` can fall through | Arrow `case ->` does not fall through |
| Good for side effects                | Good for input-to-output mapping      |
| Uses `break` for control flow        | Uses `yield` inside block cases       |

---

## Arrow case does not fall through

```
switch (status) {
    case "NEW" -> System.out.println("New order");
    case "PAID" -> System.out.println("Paid order");
    default -> System.out.println("Unknown order");
}
```

Only the matching case runs.

No accidental fall-through.

---

## Edge cases and negative cases

### 1. Missing default for non-enum input

Bad:

```java
String label = switch (status) {
    case "NEW" -> "New";
    case "PAID" -> "Paid";
};
```

Possible result:

```text
the switch expression does not cover all possible input values
```

Better:

```java
String label = switch (status) {
    case "NEW" -> "New";
    case "PAID" -> "Paid";
    default -> "Unknown";
};
```

---

### 2. Block case without yield

Bad:

```java
int value = switch (type) {
    case "A" -> {
        int temp = 10;
    }
    default -> 0;
};
```

Possible result:

```text
switch rule completes without providing a value
```

Use `yield`:

```java
int value = switch (type) {
    case "A" -> {
        int temp = 10;
        yield temp;
    }
    default -> 0;
};
```

---

### 3. Java 12 `break value` is invalid in Java 14

Bad:

```
int value = switch (type) {
    case "A" -> {
        break 10;
    }
    default -> 0;
};
```

Possible result:

```text
compile-time error
```

Use `yield` in Java 14:

```java
int value = switch (type) {
    case "A" -> {
        yield 10;
    }
    default -> 0;
};
```

---

### 4. Null switch value

```java
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

Java 14 switch does not support `case null`.

`case null` comes later with pattern matching for switch.

---

### 5. Duplicate case labels

Bad:

```java
String label = switch (status) {
    case "NEW" -> "New order";
    case "NEW" -> "Duplicate";
    default -> "Unknown";
};
```

Possible result:

```text
duplicate case label
```

---

### 6. Mixed result types can become unclear

```java
Object result = switch (type) {
    case "COUNT" -> 10;
    case "NAME" -> "Mohan";
    default -> null;
};
```

This compiles because the result type can be `Object`, but it may be poor design.

Better:

Return one clear type from a switch expression.

---

### 7. Avoid side effects inside switch expressions

Possible but not ideal:

```java
String result = switch (status) {
    case "NEW" -> {
        audit("new order");
        yield "New";
    }
    default -> "Unknown";
};
```

Best practice:

Use switch expressions mainly for mapping input to output.

If most branches do side effects, a switch statement may be clearer.

---

## Best practices

1. Use switch expressions for clean input-to-output mapping.
2. Prefer arrow cases to avoid fall-through bugs.
3. Use `yield` only when a case block needs multiple statements.
4. Use `default` for external or user-provided input.
5. Throw an exception in `default` when unknown input is a real bug.
6. Keep result types consistent.
7. Avoid heavy side effects inside switch expressions.
8. For enum switches, covering all enum values can be better than adding a lazy `default`.
9. In Java 14+, use final `yield` syntax.

---

## Summary one-liner

Java 14 made switch expressions standard, allowing `switch` to return a value, use arrow labels without fall-through, support multiple labels, and use `yield` from block cases.

