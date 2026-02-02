---
title:  IllegalArgumentException vs IllegalStateException
parent: Exceptions
nav_order: 8
---

# IllegalArgumentException vs IllegalStateException

- **IllegalArgumentException** → method received an **invalid input**
- **IllegalStateException** → method was called at the **wrong time / wrong state**

---

## Core Difference

| Point | IllegalArgumentException | IllegalStateException |
|-----|--------------------------|----------------------|
| Problem type | Invalid method argument | Invalid object / system state |
| Cause | Caller passed wrong value | Method invoked in wrong state |
| Checked / Unchecked | Unchecked | Unchecked |
| Extends | RuntimeException | RuntimeException |
| When to throw | Input validation fails | State validation fails |

---

## IllegalArgumentException

The **argument passed to the method is invalid**, even though the method is allowed to be called.

### Think like this
> “The method is correct, but the input is wrong.”

### Typical situations
- Negative number where only positive is allowed
- Null passed where value is required
- Invalid range or format

> “IllegalArgumentException is thrown when a method argument does not meet the expected criteria.”

---

## IllegalStateException

The **method call is not valid at the current state of the object**, even if arguments are correct.

### Think like this
> “The input is fine, but the object is not ready.”

### Typical situations
- Calling `start()` twice
- Accessing data before initialization
- Calling close-dependent methods after close

> “IllegalStateException is thrown when a method is invoked at an inappropriate time.”

---

> **Argument problem → IllegalArgumentException  
State problem → IllegalStateException**

