---
title:  try-catch vs try-with-resources
parent: Exceptions
nav_order: 6
---

# try-catch vs try-with-resources

- **try-catch** is used to **handle exceptions**
- **try-with-resources** is used to **handle exceptions AND automatically close resources**

---

## Core Difference

| Point | try-catch | try-with-resources |
|-----|----------|-------------------|
| Purpose | Handle exceptions | Handle exceptions + close resources |
| Resource management | Manual | Automatic |
| finally block needed | ✅ Yes (usually) | ❌ No |
| Introduced in Java | Java 1.0 | Java 7 |
| Code length | More | Less |
| Risk of resource leak | Higher | Very low |

---

## `try-catch`

### What it does
- Catches and handles exceptions
- Does **NOT** close resources automatically

### Key Points
- You must close resources manually
- Usually combined with `finally`
- More boilerplate code

### Typical usage
> “I want to catch and handle an exception.”

---

## `try-with-resources`

### What it does
- Automatically closes resources after use
- Handles exceptions at the same time

### Key Points
- Resource must implement `AutoCloseable`
- Cleaner and safer
- Preferred in modern Java

### Typical usage
> “I want to use a resource safely and ensure it is closed.”

---

## ⚠️ Important 

- `try-with-resources` **always closes resources**
- Closing happens **even if exception occurs**
- Resources are closed in **reverse order of creation**

---

##  When to Use What (Practical)

- Use **try-catch** when:
    - No external resource is involved
    - You only want exception handling

- Use **try-with-resources** when:
    - Working with files, DB, streams, sockets
    - You want clean and safe code
    - Java 7+ is available

---


> **`try-catch` handles exceptions, while `try-with-resources` handles exceptions and automatically closes resources.**
