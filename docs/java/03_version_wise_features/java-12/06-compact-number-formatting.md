---
title: Compact Number Formatting
parent: Java-12
nav_order: 6
---

# Java 12 - Compact Number Formatting

Java 12 added compact number formatting through:

```
NumberFormat.getCompactNumberInstance()
NumberFormat.getCompactNumberInstance(Locale locale, NumberFormat.Style style)
```

Simple meaning:

It formats big numbers in a short human-readable way.

Examples:

```text
1,000      -> 1K
1,000,000  -> 1M
10,000     -> 10K
```

Actual output depends on locale and style.

---

## Why was it introduced?

Applications often need to show large numbers in limited space:

- dashboard counts
- notification counts
- analytics cards
- mobile UI
- admin reports
- social-style counts

Before Java 12, developers often wrote custom logic like:

```
if (count >= 1_000_000) {
    return count / 1_000_000 + "M";
}
if (count >= 1_000) {
    return count / 1_000 + "K";
}
return String.valueOf(count);
```

This is simple but not locale-friendly.

Java 12 gives a built-in API.

---

## Basic example

```
NumberFormat formatter = NumberFormat.getCompactNumberInstance();

System.out.println(formatter.format(1_000));
System.out.println(formatter.format(1_000_000));
```

Possible output in US locale:

```text
1K
1M
```

Important:

The default locale can change the output.

---

## Use a specific locale

```
NumberFormat formatter = NumberFormat.getCompactNumberInstance(
        Locale.US,
        NumberFormat.Style.SHORT
);

System.out.println(formatter.format(10_000));
```

Output:

```text
10K
```

Best practice:

For backend-generated text, pass the locale explicitly.

Do not depend on server default locale unless that is intentional.

---

## SHORT vs LONG style

```
NumberFormat shortFormat = NumberFormat.getCompactNumberInstance(
        Locale.US,
        NumberFormat.Style.SHORT
);

NumberFormat longFormat = NumberFormat.getCompactNumberInstance(
        Locale.US,
        NumberFormat.Style.LONG
);

System.out.println(shortFormat.format(10_000));
System.out.println(longFormat.format(10_000));
```

Possible output:

```text
10K
10 thousand
```

Use `SHORT` for compact UI.

Use `LONG` for more readable text.

---

## Daily backend use case: dashboard API

```java
public String formatTotalUsers(long userCount, Locale locale) {
    NumberFormat formatter = NumberFormat.getCompactNumberInstance(
            locale,
            NumberFormat.Style.SHORT
    );
    return formatter.format(userCount);
}
```

Example:

```
formatTotalUsers(1_250_000, Locale.US);
```

Possible output:

```text
1M
```

---

## Daily backend use case: notification count

```java
public String notificationLabel(long unreadCount) {
    NumberFormat formatter = NumberFormat.getCompactNumberInstance(
            Locale.US,
            NumberFormat.Style.SHORT
    );

    return formatter.format(unreadCount) + " unread";
}
```

Possible output:

```text
2K unread
```

---

## Add decimal digits

By default, compact formatting may not show fraction digits.

```
NumberFormat formatter = NumberFormat.getCompactNumberInstance(
        Locale.US,
        NumberFormat.Style.SHORT
);

formatter.setMaximumFractionDigits(1);

System.out.println(formatter.format(1_250));
```

Possible output:

```text
1.2K
```

Rounding can affect the final value.

---

## Rounding mode

`CompactNumberFormat` uses rounding rules.

By default, it uses:

```text
RoundingMode.HALF_EVEN
```

You can change it:

```
NumberFormat formatter = NumberFormat.getCompactNumberInstance(
        Locale.US,
        NumberFormat.Style.SHORT
);

formatter.setMaximumFractionDigits(1);
formatter.setRoundingMode(RoundingMode.HALF_UP);
```

Use explicit rounding rules when output matters.

---

## Parsing compact numbers

```
NumberFormat formatter = NumberFormat.getCompactNumberInstance(
        Locale.US,
        NumberFormat.Style.SHORT
);

Number value = formatter.parse("1K");

System.out.println(value);
```

Possible output:

```text
1000
```

Important:

Parsing is locale-dependent.

`"1K"` may not mean the same thing in every locale.

---

## Edge cases and exceptions

### 1. Null locale

```
NumberFormat.getCompactNumberInstance(null, NumberFormat.Style.SHORT);
```

Output:

```text
java.lang.NullPointerException
```

---

### 2. Null style

```
NumberFormat.getCompactNumberInstance(Locale.US, null);
```

Output:

```text
java.lang.NullPointerException
```

---

### 3. Formatting null

```
formatter.format(null);
```

Possible output:

```text
java.lang.IllegalArgumentException
```

---

### 4. Formatting non-number object

```
formatter.format("1000");
```

Possible output:

```text
java.lang.IllegalArgumentException
```

Pass a `Number`, not a numeric string.

---

### 5. RoundingMode.UNNECESSARY

```
formatter.setMaximumFractionDigits(0);
formatter.setRoundingMode(RoundingMode.UNNECESSARY);
formatter.format(1_250);
```

Possible output:

```text
java.lang.ArithmeticException
```

Reason:

Rounding is needed, but the rounding mode says rounding is not allowed.

---

### 6. Output can change by locale

```
NumberFormat us = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);
NumberFormat india = NumberFormat.getCompactNumberInstance(new Locale("hi", "IN"), NumberFormat.Style.SHORT);

System.out.println(us.format(50_000_000));
System.out.println(india.format(50_000_000));
```

Output can be different because compact patterns are locale-specific.

---

## Thread-safety note

`NumberFormat` objects are mutable.

Do not share one formatter instance across many threads and keep changing settings like fraction digits or rounding mode.

Good simple approach:

```java
public String formatCount(long count, Locale locale) {
    NumberFormat formatter = NumberFormat.getCompactNumberInstance(
            locale,
            NumberFormat.Style.SHORT
    );
    return formatter.format(count);
}
```

Create it where needed, or manage formatter instances carefully.

---

## When not to use compact formatting

Do not use it for:

- money calculations
- database values
- API machine fields
- exact audit values
- values that must be parsed by another system

Use it for human display only.

Bad API response:

```json
{
  "totalUsers": "1M"
}
```

Better API response:

```json
{
  "totalUsers": 1250000,
  "displayTotalUsers": "1.2M"
}
```

---

## Best practices

1. Use compact formatting for display text, not exact data.
2. Pass locale explicitly.
3. Choose `SHORT` or `LONG` based on UI need.
4. Set fraction digits if you need `1.2K` style output.
5. Set rounding mode when rounding matters.
6. Do not share mutable formatter instances carelessly across threads.
7. Keep raw numeric values in APIs and databases.

---

## Summary

Java 12 added compact number formatting so large numbers can be formatted in locale-aware short or long forms like `1K`, `1M`, or `10 thousand` for human-readable display.

---
