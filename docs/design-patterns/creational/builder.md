---
title: Builder Pattern
parent: Creational Patterns
nav_order: 4
---

# Builder Pattern

The **Builder Pattern** is used to construct **complex objects step by step**
without using large constructors or confusing parameter lists.

It separates **object construction** from **object representation**.

---

## Why Builder Pattern Exists

Without Builder, object creation often leads to:
- Huge constructors
- Confusing parameter order
- Optional parameters becoming messy
- Poor readability
- Hard-to-maintain code

> **Core idea:**  
> *Make object creation readable, safe, and flexible.*

---

## Problem Without Builder

### Banking Example: Account Creation

```java
Account account = new Account(
    "SAVINGS",
    "ACTIVE",
    5000,
    true,
    false,
    "INR",
    LocalDate.now(),
    null
);
```

#### Problems ❌

- What does each parameter mean?
- Easy to swap values accidentally
- Hard to add new optional fields
- Constructor explosion

This is a code smell.

---

### Naive Fix: Multiple Constructors (Still Bad)

```
Account(String type, String status)
Account(String type, String status, double balance)
Account(String type, String status, double balance, boolean kyc)
```

❌ Still confusing  
❌ Hard to scale  
❌ Breaks SRP   

---
## Builder Pattern Solution ✅

### Step 1️⃣ Create the Product

```java 
public class Account {

    private String type;
    private String status;
    private double balance;
    private boolean kycVerified;
    private String currency;

    private Account(AccountBuilder builder) {
        this.type = builder.type;
        this.status = builder.status;
        this.balance = builder.balance;
        this.kycVerified = builder.kycVerified;
        this.currency = builder.currency;
    }

// ### Step 2️⃣ Create the Builder
    public static class AccountBuilder {
    
        private String type;
        private String status;
        private double balance;
        private boolean kycVerified;
        private String currency;
    
        public AccountBuilder type(String type) {
            this.type = type;
            return this;
        }
    
        public AccountBuilder status(String status) {
            this.status = status;
            return this;
        }
    
        public AccountBuilder balance(double balance) {
            this.balance = balance;
            return this;
        }
    
        public AccountBuilder kycVerified(boolean kycVerified) {
            this.kycVerified = kycVerified;
            return this;
        }
    
        public AccountBuilder currency(String currency) {
            this.currency = currency;
            return this;
        }
    
        public Account build() {
            return new Account(this);
        }
    }
}
```

### Step 3️⃣ Use the Builder (Clean & Readable)

```java
Account account = new Account.AccountBuilder()
.type("SAVINGS")
.status("ACTIVE")
.balance(5000)
.kycVerified(true)
.currency("INR")
.build();
```

✔ Self-documenting  
✔ No parameter confusion  
✔ Easy to extend  

---

## Key Benefits of Builder Pattern

✔ Readable object creation  
✔ Handles optional parameters cleanly  
✔ Avoids constructor overloads  
✔ Immutable object support  
✔ Reduces bugs  

---

### Builder vs Constructor

| Aspect          | Constructor | Builder    |
| --------------- | ----------- | ---------- |
| Readability     | Poor        | Excellent  |
| Optional fields | Hard        | Easy       |
| Parameter order | Critical    | Irrelevant |
| Extensibility   | Poor        | High       |
| Immutability    | Hard        | Easy       |

---

### Very common use cases:

- Request / Response objects
- DTOs
- Configuration objects
- Domain models with many optional fields

---

## Builder in Java Ecosystem

Lombok Builder

```java
@Builder
public class Account {
    private String type;
    private String status;
    private double balance;
}
```

✔ Same pattern  
✔ Less boilerplate  
⚠️ Still understand the pattern internally
---

### Builder vs Factory (Clarify Difference)

| Pattern | Focus                            |
| ------- | -------------------------------- |
| Factory | *Which object to create*         |
| Builder | *How to create a complex object* |


---

### When to Use Builder

Use Builder when:  
✔ Object has many fields  
✔ Many fields are optional  
✔ You want immutability  
✔ Readability matters  

### When NOT to Use Builder

Avoid when:  
❌ Object is simple (2–3 fields)  
❌ No optional parameters  
❌ Overhead not justified   

> Don’t over-engineer.

---

### Banking / Enterprise Example

Common builder usage in banking:  

- Account creation requests
- Loan applications
- Transaction filters
- Report configuration objects

## Interview Answer

> I used the Builder pattern to create complex domain objects with many optional fields. It improved readability, avoided constructor overloads, and made object creation safer.

