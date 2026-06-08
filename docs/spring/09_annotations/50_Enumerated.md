---
title: Enumerated 
parent: Annotations
nav_order: 50
---

# Spring JPA `@Enumerated`

## What is `@Enumerated`?

`@Enumerated` is a JPA annotation used to specify how a Java `enum` should be stored in the database.

Without `@Enumerated`, Hibernate/JPA must decide how to persist the enum value.

---

# Why Do We Need It?

Consider an enum:

```java
public enum Status {
    ACTIVE,
    INACTIVE,
    PENDING
}
```

And an entity:

```java
@Entity
public class User {

    @Id
    private Long id;

    private Status status;
}
```

Question:

How should Hibernate store the value?

```text
ACTIVE
INACTIVE
PENDING
```

or

```text
0
1
2
```

This is controlled by `@Enumerated`.

---

# EnumType.STRING (Recommended)

```java
@Entity
public class User {

    @Id
    private Long id;

    @Enumerated(EnumType.STRING)
    private Status status;
}
```

Example:

```
user.setStatus(Status.ACTIVE);
```

Database:

| ID   | STATUS   |
|------|----------|
| 1    | ACTIVE   |

Hibernate stores the enum name.

---

# Benefits of STRING

### Readable Database

Database value:

```text
ACTIVE
```

instead of:

```text
0
```

Much easier to understand and debug.

---

### Safe for Future Changes

Original enum:

```java
public enum Status {
    ACTIVE,
    INACTIVE,
    PENDING
}
```

Later:

```java
public enum Status {
    ACTIVE,
    SUSPENDED,
    INACTIVE,
    PENDING
}
```

Database values remain:

```text
ACTIVE
INACTIVE
PENDING
```

No data corruption.

---

# EnumType.ORDINAL

```java
@Entity
public class User {

    @Id
    private Long id;

    @Enumerated(EnumType.ORDINAL)
    private Status status;
}
```

Enum:

```java
public enum Status {
    ACTIVE,
    INACTIVE,
    PENDING
}
```

Stored values:

| Enum       | Stored Value   |
|------------|----------------|
| ACTIVE     | 0              |
| INACTIVE   | 1              |
| PENDING    | 2              |

Database:

| ID   | STATUS   |
|------|----------|
| 1    | 0        |

Hibernate stores the enum position (index).

---

# Why ORDINAL is Dangerous

Version 1:

```java
public enum Status {
    ACTIVE,
    INACTIVE,
    PENDING
}
```

Database:

```text
ACTIVE = 0
INACTIVE = 1
PENDING = 2
```

Later another developer adds:

```java
public enum Status {
    ACTIVE,
    SUSPENDED,
    INACTIVE,
    PENDING
}
```

Now:

```text
ACTIVE = 0
SUSPENDED = 1
INACTIVE = 2
PENDING = 3
```

Existing database records become incorrect.

Old value:

```text
1
```

Previously meant:

```text
INACTIVE
```

Now means:

```text
SUSPENDED
```

This causes data corruption.

---

# Industry Best Practice

Always use:

```
@Enumerated(EnumType.STRING)
```

unless there is a very specific performance or storage requirement.

Most Spring Boot projects use STRING.

---

# Real-World Example

```java
public enum PaymentStatus {
    INITIATED,
    SUCCESS,
    FAILED,
    REFUNDED
}
```

Entity:

```java
@Entity
public class Payment {

    @Id
    private Long id;

    @Enumerated(EnumType.STRING)
    private PaymentStatus status;
}
```

Database:

```text
INITIATED
SUCCESS
FAILED
REFUNDED
```

Very easy to debug production issues.

---

# What Happens Without @Enumerated?

```java
private Status status;
```

JPA traditionally defaults to:

```
EnumType.ORDINAL
```

Because of this, many teams always explicitly write:

```
@Enumerated(EnumType.STRING)
```

to avoid surprises.

---

# Complete Example

```java
public enum OrderStatus {
    CREATED,
    PROCESSING,
    SHIPPED,
    DELIVERED
}
```

```java
@Entity
public class Order {

    @Id
    private Long id;

    @Enumerated(EnumType.STRING)
    private OrderStatus status;
}
```

Database:

| ID   | STATUS      |
|------|-------------|
| 1    | CREATED     |
| 2    | SHIPPED     |
| 3    | DELIVERED   |

---

# Interview Questions

### What does `@Enumerated` do?

It tells JPA/Hibernate how to store enum values in the database.

---

### Available Types

```
EnumType.STRING
EnumType.ORDINAL
```

---

### Which One Should Be Preferred?

```
EnumType.STRING
```

because it is safer and more readable.

---

### Why is ORDINAL Dangerous?

Because changing enum order changes stored numeric values and can corrupt existing data.

---

# Memory Trick

### ORDINAL

Stores Position

```text
ACTIVE   -> 0
INACTIVE -> 1
PENDING  -> 2
```

Think:

```text
ORDINAL = ORDER/POSITION
```

---

### STRING

Stores Name

```text
ACTIVE
INACTIVE
PENDING
```

Think:

```text
STRING = ENUM NAME
```

---

# Interview One-Liner

`@Enumerated` is used to persist Java enums in a database. `EnumType.STRING` stores the enum name and is recommended because it is readable and safe against enum reordering, whereas `EnumType.ORDINAL` stores the enum position and can lead to data corruption if enum constants are reordered.
