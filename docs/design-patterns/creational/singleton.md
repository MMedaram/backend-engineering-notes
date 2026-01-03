---
title: Singleton Pattern
parent: Creational Patterns
nav_order: 1

---

# Singleton Pattern

## 1. What is Singleton?

The **Singleton Design Pattern** means:

> A class is allowed to create **only one object** during the entire lifetime of the application, and that same object is reused everywhere.

So instead of:
- Creating a new object every time

We:
- Create one object once
- Share it across the application

Think of it like:
- One central authority
- Everyone refers to the same source

---

## 2. Why Do We Need Singleton?

To understand *why*, first understand the *problem*.

### Without Singleton (Problem)

In a large application (like a bank):

- Multiple services run at the same time
- Each service may create its own object
- Some objects are not meant to be duplicated

This leads to:
- Wasted memory
- Inconsistent data
- Unexpected behavior
- Performance issues

---

## 3. Example: Bank Configuration

A bank has:
- Interest rates
- Daily transaction limits
- Feature flags (enabled / disabled services)

These values:
- Are same for everyone
- Should be loaded once
- Should not change randomly

If every service loads its own configuration:
- One service may use old values
- Another may use new values

This causes **business inconsistency**.

👉 **Singleton ensures one shared configuration object**

---

## 4. Core Idea Behind Singleton (How It Works)

Singleton is based on three simple rules:

1. No one should create the object directly
2. Only the class itself creates the object
3. Everyone accesses the same object

This is achieved by:
- Making the constructor `private`
- Storing the object in a `static` variable
- Providing a `static` method to access it

---

## 5. Simple Example (Easy to Understand)

```java
public class BankConfig {

    private static BankConfig instance;

    private BankConfig() {
        // private constructor
    }

    public static BankConfig getInstance() {
        if (instance == null) {
            instance = new BankConfig();
        }
        return instance;
    }
}
```

### What is happening here?

- `private constructor` → prevents `new BankConfig()`

- `static instance` → shared across application

- `getInstance()` → controls object creation

---

## 6. Why This Simple Version Is NOT Enough

In real systems:

- Many threads run in parallel

- Two threads may enter getInstance() at the same time

- Both may create objects

This breaks the **only one instance**  rule
⚠️ Especially dangerous in banking systems

---

## 7. Thread-Safe Singleton (Real-World Ready)
```java
public class BankConfig {

    private static volatile BankConfig instance;

    private BankConfig() {}

    public static BankConfig getInstance() {
        if (instance == null) {
            synchronized (BankConfig.class) {
                if (instance == null) {
                    instance = new BankConfig();
                }
            }
        }
        return instance;
    }
}
```
- `synchronized` → only one thread enters at a time

- `double check` → avoids unnecessary locking

- `volatile` → ensures all threads see the same object

This version:

- Is safe

- Is efficient

- Is commonly discussed in interviews

----

## 8. Best and Cleanest Singleton in Java (Enum)
```java
public enum BankAuditLogger {
    INSTANCE;

    public void log(String message) {
        System.out.println("AUDIT: " + message);
    }
}
```
> Enum = Singleton ONLY if it has exactly ONE enum constant

❌ NOT Singleton

```java
public enum BankRole {
    ADMIN,
    CASHIER,
    MANAGER
}
```

#### Why Enum Singleton is Preferred

- Java guarantees only one enum instance

- Thread-safe by default

- Safe from reflection & serialization issues

- Very little code

👉 Many senior developers prefer this approach

---

## 9. Singleton in Spring Boot 

In Spring Boot:

```java
@Service
public class TransactionService {
}
```

By default:

- Spring creates only one instance

- Manages lifecycle for you

So:

- You rarely write Singleton manually

- You rely on Dependency Injection

👉 Singleton pattern is still important to understand, not always to implement

---

## 10. Alternatives to Singleton (Modern Approach)
### Dependency Injection (Preferred)

Instead of:

```
BankConfig.getInstance();
```

We use:

```java
@Autowired
private BankConfig bankConfig;
```

Why this is better:

- Easy to test

- Loose coupling

- Cleaner design

---

## 11. When NOT to Use Singleton

Avoid Singleton when:

- Object holds user-specific data

- Multiple configurations are required

- Unit testing becomes hard

- Global state causes hidden bugs

Singleton is **powerful**, but dangerous if misused.

---

## 12. Summary

- Singleton controls object creation

- Thread safety depends on implementation

- Enum Singleton is safest

- Spring beans are singleton by default

- Overuse of Singleton is considered a design smell

> Singleton ensures a single shared instance, but in modern Spring applications, dependency injection is preferred over manual Singleton implementation.
