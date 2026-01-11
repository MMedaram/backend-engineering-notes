---
title: Abstract Factory Pattern
parent: Creational Patterns
nav_order: 3
---

# Abstract Factory Pattern

The **Abstract Factory Pattern** provides an interface to create **families of related objects**
without specifying their concrete classes.

If **Factory Method** creates **one product**,  
**Abstract Factory** creates **a group of related products** that are meant to work together.

---

## Why Abstract Factory Exists

Factory Method solves **object creation**, but problems appear when:

- Objects are **related**
- Objects must be **consistent with each other**
- You want to switch **entire product families** at once

Abstract Factory solves this by:
- Grouping related object creation
- Enforcing compatibility
- Avoiding scattered `if-else` logic

> **Core idea:**  
> *Create families of related objects, not individual ones.*

---

## Simple Way to Remember

| Pattern | Creates |
|------|--------|
| Factory Method | One object |
| Abstract Factory | A family of objects |

---

## Real Banking Scenario

### Scenario: Payment + Notification + Audit

Different payment channels require:
- Payment processor
- Notification service
- Audit logger

Example families:
- **UPI family**
- **CARD family**
- **NET_BANKING family**

Each family has **its own compatible implementations**.

---

## ❌ Problem Without Abstract Factory (Bad Design)

```
if (type.equals("UPI")) {
    new UpiPayment();
    new SmsNotification();
    new UpiAuditLogger();
} else if (type.equals("CARD")) {
    new CardPayment();
    new EmailNotification();
    new CardAuditLogger();
}
```

#### Problems

❌ Tight coupling   
❌ Hard to extend   
❌ Inconsistent combinations possible  
❌ Violates Open/Closed Principle  

---

## ✅ Abstract Factory Solution

### Step 1️⃣ Define Product Interfaces

```java 
public interface PaymentProcessor {
void pay();
}

public interface NotificationService {
void notifyUser();
}

public interface AuditLogger {
void log();
}
```

### Step 2️⃣ Create Abstract Factory Interface

```java
public interface BankingServiceFactory {

    PaymentProcessor createPaymentProcessor();
    NotificationService createNotificationService();
    AuditLogger createAuditLogger();
}
```

### Step 3️⃣ Create Concrete Factories

#### UPI Factory

```java 
public class UpiBankingFactory implements BankingServiceFactory {

    public PaymentProcessor createPaymentProcessor() {
        return new UpiPayment();
    }

    public NotificationService createNotificationService() {
        return new SmsNotification();
    }

    public AuditLogger createAuditLogger() {
        return new UpiAuditLogger();
    }
}
```

#### Card Factory

```java 
public class CardBankingFactory implements BankingServiceFactory {

    public PaymentProcessor createPaymentProcessor() {
        return new CardPayment();
    }

    public NotificationService createNotificationService() {
        return new EmailNotification();
    }

    public AuditLogger createAuditLogger() {
        return new CardAuditLogger();
    }
}
```

### Step 4️⃣ Use the Factory

```
BankingServiceFactory factory = new UpiBankingFactory();

factory.createPaymentProcessor().pay();
factory.createNotificationService().notifyUser();
factory.createAuditLogger().log();
```

✔ No concrete classes  
✔ Family consistency guaranteed  

---
## How This Appears in Spring

Spring implicitly uses Abstract Factory concepts:

- ApplicationContext
- BeanFactory
- Auto-configuration modules

#### Example:

- JPA starter → creates DataSource + EntityManager + TxManager
- Kafka starter → creates Producer + Consumer + Admin

👉 Starters = Abstract Factories

---

## Factory Method vs Abstract Factory
| Aspect      | Factory Method | Abstract Factory           |
| ----------- | -------------- | -------------------------- |
| Creates     | Single object  | Object families            |
| Complexity  | Low            | Medium                     |
| Flexibility | Moderate       | High                       |
| Usage       | Very common    | Less frequent but powerful |

---

### When to Use Abstract Factory

Use it when:  
✔ Objects must work together  
✔ Multiple configurations exist  
✔ You want to switch entire behavior sets  
✔ Consistency matters more than simplicity   

### When NOT to Use It

Avoid when:  
❌ Only one object is created  
❌ No product families exist  
❌ Over-engineering risk  

> Most applications don’t need it — but when they do, nothing else fits.

---

## Banking / Enterprise Examples

Real usages:

- Payment ecosystems
- Report generation (PDF / Excel / CSV families)
- Environment-based infra (DEV / PROD clients)
- Cloud provider integrations

---

### Interview Answer

> I used Abstract Factory to create related services like payment, notification, and audit components together. It ensured consistent combinations and made switching payment channels easy without modifying business logic.

Common Interview Mistakes:    

❌ Confusing it with Factory Method  
❌ Using it when simple factory is enough  
❌ Over-designing small systems  

---

## Summary

- Abstract Factory creates families of objects
- Ensures compatibility
- Promotes Open/Closed Principle
- Used heavily in frameworks like Spring
- Powerful but should be used carefully

