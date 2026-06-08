---
title: Java transient vs JPA Transient
parent: X vs Y
nav_order: 23
---

# Java `transient` vs JPA `@Transient`

## Quick Difference

| Feature                  | `transient`                           | `@Transient`                            |
| ------------------------ | ------------------------------------- | --------------------------------------- |
| Type                     | Java keyword                          | JPA/Hibernate annotation                |
| Purpose                  | Exclude field from Java Serialization | Exclude field from Database Persistence |
| Affects                  | `ObjectOutputStream` serialization    | JPA/Hibernate ORM                       |
| Package                  | Java language                         | `jakarta.persistence.Transient`         |
| Database Column Created? | No (JPA ignores it)                   | No                                      |
| Serialized?              | No                                    | Yes                                     |

---

# 1. `transient` Keyword

Used when you do NOT want a field to participate in Java Serialization.

```java
import java.io.Serializable;

public class User implements Serializable {

    private String username;

    private transient String password;
}
```

Example:

```
User user = new User();
user.setUsername("mohan");
user.setPassword("secret123");
```

After serialization and deserialization:

```
user.getUsername(); // mohan
user.getPassword(); // null
```

Reason:

```java
private transient String password;
```

is skipped during serialization.

### Common Use Cases

#### Passwords

```java
private transient String password;
```

#### Caches

```java
private transient Map<String, Object> cache;
```

#### Temporary Runtime Data

```java
private transient Connection connection;
```

---

# 2. `@Transient` Annotation

Used when JPA/Hibernate should NOT persist a field to the database.

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String firstName;

    private String lastName;

    @Transient
    private String fullName;
}
```

Database Table:

```sql
EMPLOYEE
------------------
ID
FIRST_NAME
LAST_NAME
```

Notice:

```sql
FULL_NAME
```

is NOT created.

Reason:

```java
@Transient
private String fullName;
```

tells Hibernate to ignore it.

---

# Common Use Case

Computed Fields

```java
@Entity
public class Employee {

    private BigDecimal salary;

    private BigDecimal bonus;

    @Transient
    private BigDecimal totalCompensation;

    public BigDecimal getTotalCompensation() {
        return salary.add(bonus);
    }
}
```

No need to store computed values in the database.

---

# Interview Trick Questions

## Question 1

```java
@Entity
public class User {

    private transient String password;
}
```

Will Hibernate persist password?

### Answer

No.

Hibernate respects Java's `transient` keyword and ignores the field.

---

## Question 2

```java
@Entity
public class User {

    @Transient
    private String password;
}
```

Will password be serialized?

### Answer

Yes.

`@Transient` only affects JPA persistence.

Java Serialization still includes the field.

---

## Question 3

```java
@Transient
private transient String password;
```

### Database?

Ignored.

### Serialization?

Ignored.

---

# Example Showing the Difference

```java
@Entity
public class User implements Serializable {

    @Id
    private Long id;

    private String username;

    @Transient
    private String fullName;

    private transient String password;
}
```

## Database Columns

```sql
ID
USERNAME
```

Both `fullName` and `password` are ignored by JPA.

---

## Serialization Result

| Field    | Serialized? |
| -------- | ----------- |
| id       | Yes         |
| username | Yes         |
| fullName | Yes         |
| password | No          |

Notice:

```java
@Transient
private String fullName;
```

is still serialized because `@Transient` only affects JPA.

---

# Memory Trick

## `transient`

Think:

```text
Java Object
     ↓
File / Network
```

Serialization concern.

---

## `@Transient`

Think:

```text
Java Object
     ↓
Database
```

Persistence concern.

---

# Spring Boot Real-World Examples

### Full Name

```java
@Transient
private String fullName;
```

### Age Calculated From DOB

```java
@Transient
private Integer age;
```

### UI Flag

```java
@Transient
private boolean editable;
```

These values are calculated at runtime and should not be stored in the database.

---

# Interview One-Liner

`transient` prevents a field from participating in Java serialization, whereas `@Transient` prevents a field from being persisted by JPA/Hibernate. A `transient` field is also ignored by JPA, but an `@Transient` field is still included in Java serialization.
