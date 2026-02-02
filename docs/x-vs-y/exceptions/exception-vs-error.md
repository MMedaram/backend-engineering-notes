_---
title:  Exception vs Error
parent: Exceptions
nav_order: 1
---_

# Exception vs Error

- **Exception** represents problems that an application **can handle**
- **Error** represents serious problems that an application **should not handle**


---

| Point | Exception | Error |
|-----|----------|-------|
| Caused by | Application logic / external issues | JVM / system issues |
| Recoverable | ✅ Yes (most cases) | ❌ No |
| Handling | try-catch / throws | Should NOT be caught |


---

## Exception

An **Exception** occurs due to problems in the program or environment that **can be handled**.

### Examples
- File not found
- Database connection issue
- Null value access
- Invalid input

### Types of Exceptions
- **Checked Exceptions** (compile-time)
- **Unchecked Exceptions** (runtime)

>  Applications are expected to **handle exceptions** properly.

---

## Error

An **Error** occurs due to **serious JVM or system problems** that the application **cannot recover from**.

### Examples
- OutOfMemoryError
- StackOverflowError
- VirtualMachineError

> These indicate **fatal issues**, not coding mistakes to be caught.

---

