---
title:  ClassNotFoundException vs NoClassDefFoundError
parent: Exceptions
nav_order: 3
---

# ClassNotFoundException vs NoClassDefFoundError

- **ClassNotFoundException** occurs when JVM **cannot find a class at runtime**
- **NoClassDefFoundError** occurs when JVM **found the class earlier but cannot load it now**

---

##  Core Difference

| Point | ClassNotFoundException | NoClassDefFoundError |
|-----|------------------------|---------------------|
| Type | Exception | Error |
| Checked / Unchecked | Checked | Unchecked |
| Occurs when | Class is not found at runtime | Class was present earlier but now missing |
| Thrown by | ClassLoader | JVM |
| Recoverable | ✅ Yes (can handle) | ❌ No (fix configuration) |
| Common cause | Missing class in classpath | Missing dependency / class init failure |

---

## ClassNotFoundException

### What it is
Occurs when:
- JVM tries to load a class **dynamically**
- The class is **not present in classpath**

### Typical Scenarios
- Using `Class.forName()`
- Using custom class loaders
- Missing JAR at runtime

### Key Point
👉 This is an **exception**, so it **must be handled**.

---

## NoClassDefFoundError

### What it is
Occurs when:
- Class was **present at compile time**
- JVM **cannot find or load it at runtime**

### Typical Scenarios
- Missing dependent JAR
- Static initialization failure
- Classpath issues during deployment

### Key Point
👉 This is an **Error**, so it **should not be caught**.


> **ClassNotFoundException → class was never found  
NoClassDefFoundError → class was found earlier but is missing now**
