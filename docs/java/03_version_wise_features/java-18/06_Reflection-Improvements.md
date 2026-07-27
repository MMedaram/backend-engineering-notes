---
title: Reflection Improvements
parent: Java-18
nav_order: 6
---

# Java 18 - Reflection Improvements

## What is Reflection?

Reflection is a Java feature that allows a program to **inspect and use classes while the application is running**.

Normally, we write code like this:

```
Employee employee = new Employee();
employee.display();
```

The class and method are known at compile time.

With Reflection, Java can discover this information at runtime.

---

# Simple Meaning

Think of Reflection as Java asking questions like:

* Which class is this?
* What methods does it have?
* What fields does it have?
* Can I call this method?
* Can I create an object?

All of this happens while the program is running.

---

# Example

Suppose you have

```java
public class Employee {

    public void display() {
        System.out.println("Employee");
    }
}
```

Using Reflection

```
Class<?> clazz = Employee.class;

System.out.println(clazz.getName());
```

Output

```
Employee
```

Java discovers information about the class while the application is running.

---

# Where is Reflection used?

Reflection is heavily used by frameworks.

Examples

* Spring Boot
* Spring MVC
* Spring Security
* Hibernate
* Jackson
* JUnit
* Mockito

Most enterprise Java applications use Reflection indirectly.

---

# Banking Example

Suppose you create

```java
@RestController
public class CustomerController {

}
```

You never tell Spring

```
new CustomerController();
```

Spring finds this class automatically.

How?

Using Reflection.

---

Another example

```java
@Entity
public class Customer {

}
```

Hibernate discovers

* fields
* annotations
* constructors

using Reflection.

---

# What changed in Java 18?

Java 18 internally **reimplemented Reflection using Method Handles**.

This change is completely internal.

Application code did **not** change.

The Reflection APIs remain the same.

---

# Why was it changed?

The goals were:

* improve maintainability
* simplify JDK internals
* reduce duplicate implementations
* improve future performance
* make Reflection easier to maintain

---

# Did developers need to change code?

No.

Before Java 18

```java
Method method =
        Employee.class.getMethod("display");
```

After Java 18

Exactly the same.

No code changes.

---

# What are Method Handles?

Method Handles are another Java API used to access methods and fields.

They are generally faster and more flexible than traditional Reflection.

Java 18 now uses Method Handles internally to implement Reflection.

As a normal backend developer, you do not need to learn Method Handles in detail.

---

# Real-world Usage

As a Spring Boot developer

You rarely write Reflection code yourself.

Instead,

Spring uses Reflection internally for

* Dependency Injection
* Bean Creation
* Annotation Processing
* Controller Discovery
* Entity Mapping
* Validation

So you benefit from Reflection every day without writing it yourself.

---

# Banking Example

Suppose you create

```java
@Service
public class AccountService {

}
```

Spring automatically creates the bean.

Reflection is used behind the scenes.

You don't have to call

```
new AccountService();
```

---

# Advantages of the Java 18 Improvement

* Cleaner JDK implementation
* Easier maintenance
* Better internal design
* Possible performance improvements
* No impact on existing applications

---

# Limitations

* No new Reflection APIs
* No syntax changes
* No new methods for developers
* Mostly an internal JDK improvement

---

# Negative Cases / Common Mistakes

## 1. Thinking Reflection changed

Wrong.

Only the internal implementation changed.

---

## 2. Thinking application code must change

Wrong.

Existing Reflection code continues to work.

---

## 3. Thinking this is a new feature for everyday coding

Wrong.

Most developers simply benefit from the internal improvement.

---

# Should Backend Developers Learn It?

Only basic awareness.

Know:

* what Reflection is
* where frameworks use it
* Java 18 improved its internal implementation
* no application code changes are required

That is enough.

---


# Quick Revision

* Reflection allows Java to inspect classes at runtime.
* Spring and Hibernate use Reflection extensively.
* Java 18 reimplemented Reflection using Method Handles.
* This is an internal JDK improvement.
* Existing Reflection code continues to work.
* No code changes are required.
* Basic awareness is enough for most backend developers.

---

# One-line Summary

Java 18 internally reimplemented Reflection using Method Handles to improve the JDK's maintainability and performance, without changing how developers write Reflection code.
