---
title: BigDecimal
parent: Core Concepts
nav_order: 4
---

# BigDecimal vs Double/Float and Wrapper Classes

## 1. Why Do We Need BigDecimal?

### Problem with float and double

`float` and `double` use IEEE 754 binary floating-point representation.

Many decimal numbers cannot be represented exactly in binary.

Example:

```
System.out.println(0.1 + 0.2);
```

Expected:

```text
0.3
```

Actual:

```text
0.30000000000000004
```

This happens because:

```text
0.1
0.2
0.3
```

cannot be represented exactly in binary format.

---

## Why Is This a Problem?

Imagine a banking system:

```
double balance = 100.00;

balance += 0.10;
balance += 0.10;
balance += 0.10;
```

Expected:

```text
100.30
```

Possible result:

```text
100.29999999999998
```

Small errors accumulate over thousands or millions of transactions.

For financial applications, this is unacceptable.

---

# BigDecimal Solution

```
BigDecimal a = new BigDecimal("0.1");
BigDecimal b = new BigDecimal("0.2");

System.out.println(a.add(b));
```

Output:

```text
0.3
```

BigDecimal provides exact decimal arithmetic.

---

# Important Rule

Never do:

```
new BigDecimal(0.1);
```

Because the double value is already imprecise.

Bad:

```java
BigDecimal amount = new BigDecimal(0.1);
```

Good:

```java
BigDecimal amount = new BigDecimal("0.1");
```

Or:

```java
BigDecimal amount = BigDecimal.valueOf(0.1);
```

---

# Common BigDecimal Operations

### Addition

```
a.add(b);
```

### Subtraction

```
a.subtract(b);
```

### Multiplication

```
a.multiply(b);
```

### Division

```
a.divide(b, 2, RoundingMode.HALF_UP);
```

Division often requires specifying rounding.

---

# BigDecimal Comparison

Wrong:

```
BigDecimal a = new BigDecimal("10.0");
BigDecimal b = new BigDecimal("10.00");

System.out.println(a.equals(b));
```

Output:

```text
false
```

Reason:

`equals()` compares:

* value
* scale

```text
10.0  -> scale 1
10.00 -> scale 2
```

Use:

```
a.compareTo(b) == 0
```

Output:

```text
true
```

---

# When to Use BigDecimal

Use BigDecimal for:

* Banking
* Accounting
* Tax calculations
* Currency conversion
* Interest calculations
* Invoices
* Financial reports

Examples:

```java
BigDecimal amount = new BigDecimal("199.99");
BigDecimal taxRate = new BigDecimal("0.18");

BigDecimal tax = amount.multiply(taxRate);
```

---

# When to Use double

Use double for:

* Scientific calculations
* Physics calculations
* Distance measurements
* Graphics programming
* Machine learning
* Sensor readings

Small precision errors are usually acceptable.

---

# Interview One-Liner

"double is faster but stores decimal values approximately using binary floating-point representation. BigDecimal provides exact decimal arithmetic and is therefore preferred for monetary and financial calculations."

---

# Wrapper Classes

## What Is a Wrapper Class?

A wrapper class converts a primitive type into an object.

| Primitive | Wrapper Class |
|-----------|---------------|
| byte      | Byte          |
| short     | Short         |
| int       | Integer       |
| long      | Long          |
| float     | Float         |
| double    | Double        |
| char      | Character     |
| boolean   | Boolean       |

Example:

```java
int age = 25;

Integer ageObj = age;
```

Integer wraps the primitive int.

---

# Why Wrapper Classes Exist

Collections can store only objects.

Invalid:

```java
List<int> numbers;
```

Valid:

```java
List<Integer> numbers;
```

Wrapper classes also provide utility methods.

Example:

```
Integer.parseInt("123");
Double.parseDouble("12.5");
```

---

# Autoboxing and Unboxing

### Autoboxing

Primitive → Object

```java
Integer num = 10;
```

Java automatically converts:

```
int → Integer
```

---

### Unboxing

Object → Primitive

```java
Integer num = 10;

int value = num;
```

Java automatically converts:

```
Integer → int
```

---

# Is BigDecimal a Wrapper Class?

No.

BigDecimal is NOT a wrapper class.

Reason:

Java does not have a primitive type called:

```
decimal
```

or

```
bigdecimal
```

Therefore, BigDecimal is simply a regular class.

---

# BigDecimal Hierarchy

```text
Object
  └── Number
        ├── Integer
        ├── Double
        ├── Long
        └── BigDecimal
```

BigDecimal extends:

```
java.lang.Number
```

but it does not wrap a primitive type.

---

# BigDecimal vs Wrapper Classes

| Feature                  | Integer/Double  | BigDecimal  |
|--------------------------|-----------------|-------------|
| Wrapper Class            | Yes             | No          |
| Wraps Primitive          | Yes             | No          |
| Supports Autoboxing      | Yes             | No          |
| Exact Decimal Arithmetic | No              | Yes         |
| Financial Calculations   | Not Recommended | Recommended |

---

# Example

Wrapper Class:

```java
Integer num = 10;
```

BigDecimal:

```java
BigDecimal amount = BigDecimal.valueOf(10);
```

This will NOT work:

```java
BigDecimal amount = 10;
```

Compilation Error.

Because BigDecimal is not a wrapper class and does not support autoboxing.

---

# Easy Memory Trick

### Wrapper Classes

```text
Primitive
    ↓
Object
```

Example:

```text
int → Integer
double → Double
```

---

### BigDecimal

```text
Precise Decimal Arithmetic
```

Used for money and financial calculations.

---

# Summary

1. Wrapper classes wrap primitive types into objects.
2. BigDecimal is NOT a wrapper class.
3. BigDecimal is a regular class in `java.math`.
4. BigDecimal is used when precision matters.
5. Always create BigDecimal using String or `valueOf()`.
6. Use `compareTo()` for value comparison.
7. Use BigDecimal for money, tax, interest, and financial calculations.
