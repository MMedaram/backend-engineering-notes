---
title:  StackOverflowError vs OutOfMemoryError
parent: Exceptions
nav_order: 4
---

# StackOverflowError vs OutOfMemoryError
 
- **StackOverflowError** happens when the **call stack is full**
- **OutOfMemoryError** happens when the **heap memory is exhausted**

---


| Point | StackOverflowError | OutOfMemoryError |
|-----|-------------------|-----------------|
| Memory area | Stack | Heap |
| Caused by | Too many method calls | Too many objects |
| Typical reason | Infinite / deep recursion | Memory leak / large objects |
| Occurs during | Method execution | Object allocation |


---


## StackOverflowError

Occurs when **too many method calls** are added to the **call stack** and there is **no space left**.

### Common Causes
- Infinite recursion
- Very deep recursion
- No proper base condition in recursive methods

### Example Scenario
```text
methodA() → methodA() → methodA() → ...

methodA() --> methodB() --> methodA()...
```

---

## OutOfMemoryError

Occurs when the JVM heap cannot allocate more memory for objects.

### Common Causes

- Creating too many objects
- Memory leaks
- Loading huge data into memory
- Large collections not released

### Example Scenario

> List keeps growing and never cleared

---


