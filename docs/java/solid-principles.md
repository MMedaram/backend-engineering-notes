---
title: SOLID Principles
parent: Java
nav_order: 13
---

# SOLID Principles

SOLID is a set of five object-oriented design principles that help in writing:

- Clean code
- Maintainable code
- Scalable systems
- Testable and extensible applications

These principles are extremely important for backend engineers, especially when working with Java, Spring Boot, Microservices, and enterprise systems.

> **SOLID** is not about writing more code — it is about writing code that survives change.

---
## S — Single Responsibility Principle (SRP)

> A class should have only one reason to change.

A class should do one thing and do it well.

### ❌ Violation (Without SRP)

```java
public class UserService {

    public void createUser(User user) {
        // save user to DB
    }

    public void sendEmail(User user) {
        // send email
    }

    public void generateReport(User user) {
        // generate report
    }
}

```

#### Problems

- Multiple responsibilities
- Hard to test
- Any change affects the whole class

### ✅ Correct (With SRP)

```java

public class UserService {
    public void createUser(User user) {
        // save user
    }
}

public class EmailService {
    public void sendEmail(User user) {
        // send email
    }
}

public class ReportService {
    public void generateReport(User user) {
        // generate report
    }
}

```
#### Benefits

- Easier to maintain
- Clear ownership
- Better testability

---

## 🧠 Backend Insight

In Spring Boot, SRP usually maps to:

- Controller → HTTP handling
- Service → Business logic
- Repository → Data access

---

## O — Open/Closed Principle (OCP)

> Software entities should be open for extension but closed for modification.

You should add new behavior without changing existing code.

### ❌ Violation (If-else hell)

```java
public class InterestCalculator {

    public double calculate(String accountType, double balance) {
        if ("SAVINGS".equals(accountType)) {
            return balance * 0.04;
        } else if ("CURRENT".equals(accountType)) {
            return balance * 0.02;
        }
        return 0;
    }
}
```
### Problem

- Every new account type requires modifying this class
- Risky in production

### ✅ Correct (Using Polymorphism)

```java 

public interface InterestCalculator {
    double calculate(double balance);
}

public class SavingsInterestCalculator implements InterestCalculator {
    public double calculate(double balance) {
        return balance * 0.04;
    }
}

public class FixedDepositInterestCalculator implements InterestCalculator {
    public double calculate(double balance) {
        return balance * 0.06;
    }
}

```

### Benefits

- Add new account types safely
- No existing code touched
- Production-friendly

#### 🧠 Backend Insight

Spring uses OCP heavily:

- JpaRepository
-  @Profile
- @AutoConfiguration
- ControllerAdvice

---

## L — Liskov Substitution Principle (LSP)

> Subtypes must be substitutable for their base types without breaking behavior.

If class B extends class A, then B should behave like A.

### ❌ Violation Example

```java

public class Account {
    public void withdraw(double amount) {
        // withdraw logic
    }
}

public class FixedDepositAccount extends Account {
    @Override
    public void withdraw(double amount) {
        throw new UnsupportedOperationException("Withdraw not allowed");
    }
}

```

### Problem

- Code expecting Account may crash
- Breaks substitution

### ✅ Correct Design

```java

public interface Withdrawable {
    void withdraw(double amount);
}

public class SavingsAccount implements Withdrawable {
    public void withdraw(double amount) {
        // allowed
    }
}

public class FixedDepositAccount {
    // no withdraw method
}

```
### Benefits

- No unexpected behavior
- Clear contracts
- Safer polymorphism

### 🧠 Rule of Thumb

>If you need to throw UnsupportedOperationException, your design is wrong.


---

## I — Interface Segregation Principle (ISP)

> Clients should not be forced to depend on methods they do not use.

Prefer many small interfaces over one large interface.

### ❌ Violation

```java
public interface AccountOperations {
    void deposit();
    void withdraw();
    void calculateInterest();
}

public class FixedDepositAccount implements AccountOperations {
    public void withdraw() {
        // not applicable
    }
}

```
### ✅ Correct (Segregated Interfaces)

```java
public interface Depositable {
    void deposit();
}

public interface Withdrawable {
    void withdraw();
}

public interface InterestBearing {
    void calculateInterest();
}

public class FixedDepositAccount implements Depositable, InterestBearing {
    // only required methods
}

```

### 🧠 Backend Insight

ISP improves:

- API clarity
- Microservice contracts
- Feign clients
- Kafka event consumers

---

## D — Dependency Inversion Principle (DIP)

> High-level modules should not depend on low-level modules.
Both should depend on abstractions.

### ❌ Violation (Tight Coupling) 

```java
public class AccountService {
    private EmailNotificationService emailService = new EmailNotificationService();
}
```

#### Problems
- Hard to switch notification type
- Difficult to test

### ✅ Correct (Using Abstraction)

```java

public interface NotificationService {
    void notifyUser(String message);
}

```

```java
public class EmailNotificationService implements NotificationService {
    public void notifyUser(String message) {
        // email logic
    }
}
```

```java 
public class AccountService {

    private final NotificationService notificationService;

    public AccountService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }
}
```

#### 🧠 Spring Boot Connection

Spring Dependency Injection is a direct implementation of DIP:

- @Autowired
- Constructor injection
- @Qualifier

---
### Summary

- SOLID is not academic — it directly impacts:
  - Production bugs
  - Change safety
  - Team velocity
- Overusing SOLID is bad, ignoring SOLID is worse
- Aim for balance and clarity

