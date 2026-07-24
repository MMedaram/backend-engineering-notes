---
title: Switch Expressions Second Preview
parent: Java-13
nav_order: 2
---

# Java 13 - Switch Expressions Second Preview

Java 13 continued **switch expressions** as a **second preview feature**.

The biggest change from Java 12:

```text
break value
```

was replaced with:

```text
yield value
```

---

## Preview Feature Status

| Version | Status                             |
|---------|------------------------------------|
| Java 12 | First preview, used `break value`  |
| Java 13 | Second preview, introduced `yield` |
| Java 14 | Standard production feature        |

Important:

For modern Java projects, Java 13 switch expression syntax is very close to the final Java 14 syntax.

---

## Why was it improved?

Java 12 switch expressions worked, but `break value` was confusing because `break` already had an old meaning in Java switch statements.

Java 13 made the meaning clearer:

```java
yield result;
```

means:

```text
return this value from the switch expression
```

---

## Old switch statement style

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

Problems:

- repeated assignment
- many `break` statements
- accidental fall-through risk
- more lines of code

---

## Java 13 switch expression

```java
String label = switch (status) {
    case "NEW" -> "New order";
    case "PAID" -> "Paid order";
    case "CANCELLED" -> "Cancelled order";
    default -> "Unknown order";
};
```

This directly returns a value.

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
- workflow decisions
- error classification
- response message creation
- enum-to-code conversion

---

## Multiple labels in one case

```java
String type = switch (day) {
    case "SATURDAY", "SUNDAY" -> "Weekend";
    case "MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY" -> "Weekday";
    default -> "Invalid day";
};
```

This reduces repeated code.

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

`yield` gives the value back to the switch expression.

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

This is better than silently returning a wrong default when the input is invalid.

---

## Enum switch example

```java
enum PaymentStatus {
    PENDING, SUCCESS, FAILED
}

String nextAction = switch (paymentStatus) {
    case PENDING -> "Retry later";
    case SUCCESS -> "Send invoice";
    case FAILED -> "Notify customer";
};
```

When all enum values are covered, `default` is not always needed.

But if enum values may be added later, recompilation helps catch missing cases.

---

## Difference between switch statement and switch expression

| Switch Statement               | Switch Expression                |
|--------------------------------|----------------------------------|
| Executes code                  | Produces a value                 |
| `break` avoids fall-through    | `yield` returns value from block |
| Does not need to be exhaustive | Must cover all possible inputs   |
| Good for side effects          | Good for mapping input to output |

---

## Compile and run Java 13 preview code

Compile:

```bash
javac --release 13 --enable-preview SwitchDemo.java
```

Run:

```bash
java --enable-preview SwitchDemo
```

Run JAR:

```bash
java --enable-preview -jar app.jar
```

---

## Edge cases and negative cases

### 1. Using Java 12 `break value` style in Java 13

Bad in Java 13:

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

Use `yield` instead:

```java
int value = switch (type) {
    case "A" -> {
        System.out.println("A");
        yield 10;
    }
    default -> 0;
};
```

---

### 2. Missing preview flag

```bash
javac --release 13 SwitchDemo.java
```

Possible result:

```text
error: switch expressions are a preview feature and are disabled by default
```

---

### 3. Missing default for non-enum input

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

---

### 4. Block case without yield

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

---

### 5. Null input

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

Java 13 switch does not support `case null`.

---

### 6. Illegal jump through switch expression

Inside a switch expression, do not use `continue`, `break`, or `return` in ways that jump through the expression.

Keep switch expression branches simple.

---

## Best practices

1. Use switch expressions for clean input-to-output mapping.
2. Use `yield` only when a case block has multiple statements.
3. Prefer arrow cases for no fall-through behavior.
4. Throw an exception in `default` when unknown input is a real bug.
5. For external input, usually include a `default`.
6. Avoid side effects inside switch expressions.
7. In Java 13, compile and run with preview flags.
8. In modern projects, use Java 14+ standard switch expressions.

---

## Summary one-liner

Java 13 provided the second preview of switch expressions and replaced Java 12's `break value` syntax with `yield`, making switch expressions clearer before they became standard in Java 14.

---
