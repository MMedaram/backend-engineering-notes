---
title: Factory Method Pattern
parent: Creational Patterns
nav_order: 2
---

# Factory Method Pattern

The **Factory Method Pattern** provides an interface for creating objects,
but allows subclasses or a factory class to decide **which concrete class to instantiate**.

Instead of creating objects using `new`, object creation is **delegated to a factory**.

---

## Why Factory Method Exists

Direct object creation causes:
- Tight coupling
- Difficult changes
- Violations of Open/Closed Principle
- Hard-to-test code

Factory Method solves this by:
- Hiding object creation logic
- Decoupling client code from concrete classes
- Allowing easy extension

> **Core idea:**  
> *Separate object creation from object usage.*

---

## Problem Without Factory

### Banking Example: Payment Processing

```java
public class PaymentService {

    public void pay(String type) {
        if ("UPI".equals(type)) {
            new UpiPayment().process();
        } else if ("CARD".equals(type)) {
            new CardPayment().process();
        }
    }
}
```

### Problems ❌

- if-else grows with new types
- Class must be modified every time
- Violates Open/Closed Principle
- Hard to test and extend

---

## Factory Method Solution

### Step 1️⃣ Define a Common Interface

```java 
public interface PaymentProcessor {
void process();
}
```

### Step 2️⃣ Create Implementations

```java
public class UpiPayment implements PaymentProcessor {
    public void process() {
        System.out.println("Processing UPI payment");
    }
}
```

```java
public class CardPayment implements PaymentProcessor {
    public void process() {
        System.out.println("Processing Card payment");
    }
}

```

### Step 3️⃣ Create the Factory

```java

public class PaymentFactory {

    public static PaymentProcessor getPaymentProcessor(String type) {
        return switch (type) {
            case "UPI" -> new UpiPayment();
            case "CARD" -> new CardPayment();
            default -> throw new IllegalArgumentException("Invalid payment type");
        };
    }
}

```

### Step 4️⃣ Use the Factory

```java
public class PaymentService {

    public void pay(String type) {
        PaymentProcessor processor =   PaymentFactory.getPaymentProcessor("UPI");
        processor.process();
    }
}

```

### Benefits Achieved ✅

- Client code does not know concrete classes
- Object creation logic is centralized
- Easy to add new payment types
- Better readability and testability

---
## How This Relates to Spring

You use Factory Method daily in Spring without realizing it.

### Example: `BeanFactory`

```
ApplicationContext context = ...
MyService service = context.getBean(MyService.class);
```

#### Spring:

- Decides which implementation to return
- Manages creation
- Manages lifecycle

> 👉 Spring itself is a massive Factory implementation
---

## Factory Method vs Simple `new`

| Aspect        | new      | Factory  |
| ------------- | -------- | -------- |
| Coupling      | Tight    | Loose    |
| Extensibility | Poor     | High     |
| Testability   | Low      | High     |
| OCP           | Violated | Followed |

---

## When to Use Factory Method

Use it when:

- Object creation logic is complex
- Multiple implementations exist
- You expect new types in future
- Creation should be centralized

---

## When NOT to Use Factory

Avoid when:

- Only one implementation
- No variation expected

> Do not over-engineer.

---

## Banking / Enterprise Usage

Common factory usages:

- Payment processors
- Notification channels (Email/SMS/Kafka)
- Interest calculation strategies
- Report generators
- File parsers

---

> I used Factory Method pattern to create different payment processors based on payment type. It helped decouple object creation from business logic and allowed easy extension without modifying existing code.

---
## Summary

- Factory Method hides object creation
- Improves extensibility
- Reduces coupling
- Widely used in Spring internally
- Very common in real projects

