---
title: Comparable vs Comparator
parent: Collections
nav_order: 10
---

# Comparable vs Comparator
 
- **Comparable** defines the **default (natural) ordering** of a class
- **Comparator** defines **custom ordering** outside the class

---

## Core Difference

| Point | Comparable | Comparator |
|-----|------------|------------|
| Package | java.lang | java.util |
| Method | compareTo() | compare() |
| Where logic lives | Inside the class | Outside the class |
| Number of sort orders | One | Many |
| Modifies class | ✅ Yes | ❌ No |
| Use case | Natural order | Custom / multiple orders |

---

## Comparable

`Comparable` is used when a class has a **natural order**.

### Characteristics
- Sorting logic is part of the class
- Only **one default sorting rule**
- Used automatically by `Collections.sort()` and `Arrays.sort()`

> **String and all wrapper classes implement Comparable   
> Collections rely on Comparator or Comparable elements for sorting.**

---

## Comparator

`Comparator` is used to define **custom sorting logic**.

### Characteristics
- Sorting logic is external to the class
- Supports **multiple sorting strategies**
- Very flexible

---

> **Comparable is used to define the natural ordering of a class, whereas Comparator is used to define custom ordering externally.**

---

