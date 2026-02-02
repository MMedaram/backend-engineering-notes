---
title: Exception Hierarchy
parent: Java
nav_order: 20
---

# Exception Hierarchy

```php

Throwable
│
├── Error
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── VirtualMachineError
│
└── Exception
    │
    ├── Checked Exceptions
    │   ├── IOException
    │   │   ├── FileNotFoundException
    │   │   └── EOFException
    │   ├── SQLException
    │   ├── ClassNotFoundException
    │   ├── InterruptedException
    │   └── ParseException
    │
    └── RuntimeException (Unchecked)
        ├── NullPointerException
        ├── ArithmeticException
        ├── ArrayIndexOutOfBoundsException
        ├── StringIndexOutOfBoundsException
        ├── ClassCastException
        ├── NumberFormatException
        ├── IllegalArgumentException
        ├── IllegalStateException
        └── ConcurrentModificationException

```

## Level 1: Throwable

- Root of all errors and exceptions
- Only classes that can be thrown and caught


## Level 2: Error , Exception

### Error

Represents serious JVM-level problems.

#### Most used Errors

- OutOfMemoryError → Heap memory exhausted
- StackOverflowError → Deep / infinite recursion

> Errors should not be handled, they should be fixed.

### Exception

Represents problems an application can handle.

This splits into:

- Checked Exceptions
- Unchecked Exceptions (RuntimeException)

---

> Throwable is the root, Errors represent JVM failures, checked exceptions must be handled, and unchecked exceptions represent programming mistakes.
