---
title:  Checked Exceptions vs Unchecked Exceptions
parent: Exceptions
nav_order: 2
---

# Checked Exceptions vs Unchecked Exceptions

- **Checked exceptions** are checked at **compile time**
- **Unchecked exceptions** are checked at **runtime**


---

| Point | Checked Exception | Unchecked Exception |
|-----|------------------|--------------------|
| Checked by compiler | ✅ Yes | ❌ No |
| When it occurs | Compile time | Runtime |
| Must handle with try/catch or throws | ✅ Mandatory | ❌ Not mandatory |
| Extends | Exception | RuntimeException |
| Programmer responsibility | Yes | Yes (but optional handling) |
| Common use | Recoverable issues | Programming errors |

---

##  Checked Exceptions

Checked exceptions represent **recoverable situations** that a program is expected to handle.

### Examples

- IOException
- FileNotFoundException
- EOFException
- InterruptedIOException


- MalformedURLException
- SocketException
- UnknownHostException


- SQLException
- BatchUpdateException


- InterruptedException
- CloneNotSupportedException


- ClassNotFoundException
- InstantiationException
- IllegalAccessException
- NoSuchMethodException
- InvocationTargetException


- ParseException
- UnsupportedEncodingException

### Key Rule
> You **must** handle them, otherwise code will not compile.

---

## Unchecked Exceptions

Unchecked exceptions represent **programming mistakes** that usually cannot be recovered at runtime.

### Examples

- NullPointerException
- ArrayIndexOutOfBoundsException
- StringIndexOutOfBoundsException
- ArithmeticException
- ClassCastException


- ConcurrentModificationException
- NoSuchElementException
- UnsupportedOperationException


- IllegalArgumentException
- IllegalStateException
- NumberFormatException


- IllegalMonitorStateException
- RejectedExecutionException
- StackOverflowError ❗ (Error, but often mentioned)
- OutOfMemoryError ❗ (Error, not Exception)




### Key Rule
> You **do NOT have to handle them explicitly**.

> **Checked = must handle  
Unchecked = optional to handle**

---

## Summary

- **Checked exceptions** are enforced by the compiler.
- **Unchecked exceptions** occur due to logical or programming errors.
- **RuntimeException and its subclasses** are unchecked exceptions.

