---
title: Java Streams (Java 8+)
parent: Java
nav_order: 30
---

# Java Streams (Java 8+)

Java Streams were introduced in **Java 8** to simplify **data processing on collections**
by providing a **declarative, functional-style API**.

> Streams are **not data structures** —  
> they are **operations performed on data**. 

---

## Why Streams Were Introduced

Before Java 8:
- Data processing required loops
- Code was verbose and error-prone
- Parallel processing was complex

Streams solve this by providing:
- Clean and readable code
- Internal iteration
- Built-in parallelism
- Functional programming style

---

## What Is a Stream?

> A Stream is a **sequence of elements from a source**
> that supports **data processing operations**.

### Important Clarification
- Streams **do not store data**
- Streams **operate on data from a source**
- Source can be:
    - Collection
    - Array
    - I/O channel

---

## Characteristics of Streams 

- Streams do **not modify the source**
- Streams are **not reusable**
- Streams support **lazy evaluation**
- Streams process elements **only when needed**
- Streams support **parallel execution**

---

## Internal Iteration

Collections:

> for (String s : list) { }


Streams:

> list.stream().filter(...).map(...);

✔ Iteration handled internally    
✔ Less boilerplate     
✔ Safer parallel execution    

---

## Stream Pipeline Structure


### A stream pipeline consists of:

- Source     
- Intermediate operation(s)      
- Terminal operation       

> Source → Intermediate Ops → Terminal Op      

Without a terminal operation:

- Nothing executes.

