---
title: Default & Static Methods in Java Interfaces
parent: Java-8
nav_order: 4
---

# Default And Static Methods In Interfaces

Java 8 allowed interfaces to have:

- Default methods.
- Static methods.

Before Java 8, interfaces mainly had abstract methods.

---

## Why Were Default Methods Introduced?

Before Java 8, adding a new method to an interface could break all existing implementations.

Example:

```java
interface Vehicle {
    void start();
}
```

If we add:

```java
void stop();
```

then every class implementing `Vehicle` must implement `stop`.

Java 8 default methods solve this.

---

## Default Method

```java
interface Vehicle {
    void start();

    default void stop() {
        System.out.println("Vehicle stopped");
    }
}
```

Now old implementations do not break.

They get default behavior automatically.

---

## Override Default Method

```java
class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }

    @Override
    public void stop() {
        System.out.println("Car stopped");
    }
}
```

Implementing class can keep default behavior or override it.

---

## Diamond Problem

If two interfaces have same default method, class must resolve conflict.

```java
interface A {
    default void show() {
        System.out.println("A");
    }
}

interface B {
    default void show() {
        System.out.println("B");
    }
}

class C implements A, B {
    @Override
    public void show() {
        A.super.show();
// Or        B.super.show();
        System.out.println("Resolved");
    }
}
```

If class does not override `show`, compiler error occurs.

---

## Static Methods In Interfaces

Static methods belong to the interface itself.

```java
interface MathUtil {
    static int add(int a, int b) {
        return a + b;
    }
}
```

Call using interface name:

```java
int result = MathUtil.add(10, 20);
```

You cannot call it through implementation object.

---

## Default vs Static

| Feature           | Default Method                | Static Method         |
|-------------------|-------------------------------|-----------------------|
| Belongs to        | Implementing object           | Interface             |
| Can be overridden | Yes                           | No                    |
| Called using      | Object reference              | Interface name        |
| Main use          | API evolution/common behavior | Utility/helper method |

---

## Daily Use Case

Java collections used default methods to add new behavior without breaking old classes.

Example:

```
list.sort(Comparator.naturalOrder());
```

This became possible because Java 8 allowed interfaces to evolve more safely.

---

## Best Practices

- Use default methods for backward-compatible interface evolution.
- Do not put too much business logic in interfaces.
- Use static methods for small related helper methods.
- Resolve default method conflicts explicitly.
- Prefer classes/abstract classes when shared state is needed.

---

## Quick Summary

Default methods let interfaces provide behavior without breaking old implementations. Static methods let interfaces keep related helper logic.
