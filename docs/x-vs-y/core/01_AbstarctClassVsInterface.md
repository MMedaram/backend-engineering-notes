---
title: Abstract Class vs Interface
parent: OOPS
nav_order: 1
---

# Interface vs Abstract Class (Java)

## 1. Overview

| Feature | Interface | Abstract Class |
|--------|----------|----------------|
| Purpose | Define contract (what to do) | Provide partial implementation (how + what) |
| Keyword | `interface` | `abstract class` |

---

## 2. Methods

| Feature | Interface | Abstract Class |
|--------|----------|----------------|
| Abstract methods | Yes (default) | Yes |
| Concrete methods | Yes (default/static) | Yes |
| Default methods | Yes (`default` keyword) | Not needed (normal methods) |
| Static methods | Yes | Yes |
| Private methods | Yes (Java 9+) | Yes |

---

## 3. Variables

| Feature | Interface | Abstract Class |
|--------|----------|----------------|
| Fields | `public static final` (constants only) | Instance + static variables allowed |
| Default modifier | public static final | No default restriction |

---

## 4. Constructors

| Feature | Interface | Abstract Class |
|--------|----------|----------------|
| Constructor | ❌ Not allowed | ✅ Allowed |

---

## 5. Initialization Blocks

| Abstract Class | Interface |
|----------------|----------|
| ✅ Instance and static blocks allowed | ❌ Not allowed |

---

## 6. Inheritance

| Feature | Interface | Abstract Class |
|--------|----------|----------------|
| Multiple inheritance | ✅ Yes (implements multiple) | ❌ No (only one class) |
| Extends | Can extend multiple interfaces | Can extend one class |
| Implements | N/A | Can implement interfaces |

---

## 7. Object Creation

| Feature | Interface | Abstract Class |
|--------|----------|----------------|
| Instantiation | ❌ Cannot instantiate | ❌ Cannot instantiate |

---

## 8. Access Modifiers

| Feature | Interface | Abstract Class |
|--------|----------|----------------|
| Methods | public by default | Any (public, protected, private) |
| Variables | public static final only | Any |

---

## 9. When to Use

| Use Case | Interface | Abstract Class |
|---------|----------|----------------|
| Define contract | ✅ Yes | ❌ Less suitable |
| Shared code | ❌ Limited | ✅ Yes |
| Multiple inheritance needed | ✅ Yes | ❌ No |
| Common base class | ❌ No | ✅ Yes |

---

