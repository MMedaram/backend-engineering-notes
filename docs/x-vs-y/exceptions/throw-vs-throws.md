---
title:  throw vs throws
parent: Exceptions
nav_order: 5
---

# throw vs throws

- **throw** is used to **explicitly throw an exception**
- **throws** is used to **declare an exception**

---

## Core Difference

| Point | throw | throws |
|-----|------|--------|
| Used for | Throwing an exception | Declaring an exception |
| Location | Inside method body | In method signature |
| Number of exceptions | Only one at a time | Multiple exceptions |
| Object required | ✅ Yes | ❌ No |
| Compile-time role | Creates exception | Informs caller |
| Common usage | Custom / validation errors | Propagating exceptions |

---

## `throw` 

### What it does
- Used to **manually create and throw an exception**
- Stops normal flow of program

### Key Points
- Must be followed by an **exception object**
- Can throw **checked or unchecked** exceptions
- Usually used for **validation failures**

### Example idea
> “If age is invalid, I throw an exception.”

---

## `throws` 

### What it does
- Used to **declare that a method may throw an exception**
- Responsibility is passed to the **caller**

### Key Points
- Written in method signature
- Can declare **multiple exceptions**
- Mostly used with **checked exceptions**

### Example idea
> “This method might fail, caller should handle it.”

---


- `throw` → **inside method**
- `throws` → **method declaration**
- `throw` → **one exception**
- `throws` → **multiple exceptions**

---


> **`throw` is used to explicitly throw an exception, whereas `throws` is used to declare exceptions that a method may pass to the caller.**
