---
title: Collection vs Collections
parent: Collections
nav_order: 11
---

# Collection vs Collections

- **Collection** is an **interface** that represents a group of objects
- **Collections** is a **utility class** that provides helper methods for collections

---

## Core Difference

| Point | Collection | Collections |
|-----|------------|-------------|
| Type | Interface | Utility class |
| Package | java.util | java.util |
| Purpose | Store and manage elements | Operate on collections |
| Contains methods | add, remove, size, iterator | sort, reverse, shuffle |
| Can be instantiated | ❌ No | ❌ No |
| Used for | Data structure | Helper operations |

---

## Collection

`Collection` is the **root interface** of the Java Collections Framework.

### Characteristics
- Represents a group of objects
- Provides basic operations like add, remove, iterate
- Parent of:
    - List
    - Set
    - Queue

---

## Collections

`Collections` is a **utility class** with only **static methods**.

### Characteristics
- Cannot be instantiated
- Used to perform operations on collections
- Provides algorithms and wrappers

### Common methods
- `sort()`
- `reverse()`
- `shuffle()`
- `synchronizedList()`
- `unmodifiableList()`
- 
---
 
> **Collection is an interface used to store groups of objects, whereas Collections is a utility class that provides static methods to operate on collections.**
