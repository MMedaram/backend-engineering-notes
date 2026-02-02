---
title:  equals() vs hashCode()
parent: X vs Y
nav_order: 22
---

# equals() vs hashCode() in Java

`equals()` and `hashCode()` are fundamental methods defined in the
`java.lang.Object` class.

They play a **critical role** in:
- Object comparison
- Collections (`HashMap`, `HashSet`)
- Correct behavior of domain objects

---

## equals()

> `equals()` is used to **check logical equality** between two objects.

Default implementation in `Object`:

```java
public boolean equals(Object obj) {
    return this == obj;
}
```

>**This means:**    
> Default equals() checks **reference equality, not content**.

---

## hashCode()

> hashCode() returns an integer hash value representing the object.

Used by:

- HashMap
- HashSet
- Hashtable

Default implementation:

- Usually derived from memory address
- Different objects → different hash codes

---

## Why equals() and hashCode() Are Related

Java defines a strict contract between **equals() and hashCode()**.

### The Contract

#### 1️⃣ If two objects are equal according to equals()     
    👉 they must have the same hashCode()

#### 2️⃣ If two objects have the same hashCode()    
    👉 they may or may not be equal


---

## Why This Contract Exists

Hash-based collections work in two steps:

- Use `hashCode()` to find the bucket
- Use `equals()` to find the exact object

If this contract is broken:

- Objects become unreachable
- Data loss happens
- Bugs are very hard to detect

---


## Why It Is Recommended to Override BOTH

> Overriding only one breaks the contract.

### Case 1️⃣ Override equals() ONLY ❌

```java
@Override
public boolean equals(Object o) {
return this.id == ((Customer)o).id;
}
```

But hashCode() is still default.

Result:

- equals() → true
- hashCode() → different

HashSet treats them as different objects ❌

### Case 2️⃣ Override hashCode() ONLY ❌

```java
@Override
public int hashCode() {
return id;
}
```
But equals() still uses reference comparison.

Result:

- Same bucket
- equals() fails

Duplicate objects ❌

---

### Case 3️⃣ Override BOTH (Correct) ✅

```java
@Override
public boolean equals(Object o) {

if (this == o) return true;

if (!(o instanceof Customer)) return false;

Customer c = (Customer) o;
return id == c.id;
}

@Override
public int hashCode() {
return Objects.hash(id);
}
```

✔ Correct behavior    
✔ Works in all collections     

#### Where This Matters the Most

- HashMap
- HashSet
- ConcurrentHashMap
- Hibernate entities
- Cache keys

In enterprise apps:
- Incorrect equals/hashCode = production bugs

---

## Where equals() DOES content comparison by default 

- 1️⃣ String
- 2️⃣ Wrapper Classes
- 3️⃣ Enum
- 4️⃣ Record (Java 16+)
- 5️⃣ Collection Implementations

