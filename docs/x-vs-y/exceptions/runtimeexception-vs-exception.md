---
title:  RuntimeException vs Exception
parent: Exceptions
nav_order: 7
---

# RuntimeException vs Exception

- **Exception** represents conditions that a program is **expected to handle**
- **RuntimeException** represents **programming mistakes** that usually occur at runtime

---

##  Core Difference

| Point | Exception | RuntimeException |
|-----|----------|------------------|
| Type | Checked | Unchecked |
| Checked by compiler | ✅ Yes | ❌ No |
| Must handle with try/catch or throws | ✅ Mandatory | ❌ Optional |
| Occurs | Compile time / Runtime | Runtime |
| Typical cause | External issues | Programming errors |

---

## Exception 

- Compiler **forces handling**
- Usually caused by **external factors**
- Program can continue if handled properly


## RuntimeException

- Compiler does **not force handling**
- Usually indicates a **bug**
- Can be avoided by proper coding


## ⚠️ Important Interview Rules 

- All `RuntimeException`s are **Exceptions**
- But not all `Exception`s are **RuntimeExceptions**
- Checked → must handle
- Unchecked → optional to handle
