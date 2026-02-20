---
title: Prototype Pattern
parent: Creational Patterns
nav_order: 5
---

# Prototype Pattern

The **Prototype Pattern** is a creational design pattern that creates
new objects by **copying an existing object (prototype)** rather than
creating a new instance from scratch.

---

## Why Prototype Pattern Exists

Prototype is useful when:
- Object creation is expensive
- Object setup is complex
- Many similar objects are needed
- Performance matters

> **Core idea:**  
> *Clone existing objects instead of rebuilding them.*

---

### Simple Example (Without Prototype)

```
Report report = new Report();
report.loadTemplate();
report.loadConfig();
report.loadMetadata();
```

#### Problems:

- Expensive setup repeated
- Slow performance
- Hard to scale

---

## Prototype Pattern Solution

### Step 1️⃣ Make Object Cloneable

```java
public class Report implements Cloneable {

    private String format;
    private String dataSource;

    @Override
    protected Report clone() throws CloneNotSupportedException {
        return (Report) super.clone();
    }
}
```

### Step 2️⃣ Use Prototype

```
Report prototype = new Report();
prototype.setFormat("PDF");
prototype.setDataSource("DB");

Report copy1 = prototype.clone();
Report copy2 = prototype.clone();
```

✔ Faster creation  
✔ Same base configuration  

---

### Shallow vs Deep Copy

#### Shallow Copy

- Any changes made to cloned object will be reflected in original object or vice versa.
- Preferred if an object has only primitive fields.


#### Deep Copy
- Any changes made to cloned object will not be reflected in original object or vice versa.
- Preferred if an object has references to other objects as fields.

> Prototype is dangerous if deep copy is not handled correctly.

---


## Prototype Pattern in Spring

Spring supports prototype scope:

```java 

@Scope("prototype")
@Component
public class ReportGenerator {
}

```
Each request:

- Returns a new instance
- Based on prototype definition

---

## When to Use Prototype

Use when:  
✔ Object creation is expensive  
✔ Many similar objects needed  
✔ Cloning is safe   
✔ Performance critical   

## When NOT to Use Prototype

Avoid when:  

❌ Object creation is cheap  
❌ Deep cloning is complex  
❌ Object graph is large  
❌ Simpler patterns suffice  


---

## Banking / Enterprise Context

Prototype is rare in banking apps, but may appear in:

- Report templates
- Rule engines
- Simulation engines
- Caching + cloning scenarios

---

> Prototype pattern creates new objects by cloning existing ones.
> It’s rarely used directly in business applications but appears in scenarios like cloning, prototype-scoped Spring beans, and performance-sensitive systems.

> Prototype is a specialized pattern.  
> Use it only when object creation cost is high and cloning is safe.