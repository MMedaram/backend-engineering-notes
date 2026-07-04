---
title: Annotations and Parameter Reflection
parent: Java-8
nav_order: 18
---

# Annotations and Parameter Reflection

Java 8 improved annotations and reflection.

Important features:

- Repeating annotations.
- Type annotations.
- Method parameter name reflection.

These are not daily business-code features, but frameworks and tools use them heavily.

---

## Repeating Annotations

Before Java 8, the same annotation could not be applied multiple times directly.

Java 8 allows it if the annotation is designed for repeat use.

```java
@Schedule(day = "MONDAY")
@Schedule(day = "FRIDAY")
public void sendReport() {
}
```

Why useful?

- Scheduling.
- Validation rules.
- Framework metadata.

---

## How Repeating Annotation Works

Repeatable annotation:

```java
@Repeatable(Schedules.class)
public @interface Schedule {
    String day();
}
```

Container annotation:

```java
public @interface Schedules {
    Schedule[] value();
}
```

---

## Type Annotations

Java 8 allows annotations on type usage.

```java
List<@NonNull String> names;
```

Developer meaning:

- Mostly useful with static analysis tools.
- Helps tools catch null and type-related issues earlier.

Note:

`@NonNull` is usually from external libraries/tools, not core Java.

---

## Method Parameter Reflection

Java 8 added support to read method parameter names using reflection.

But parameter names are not stored by default.

Compile with:

```bash
javac -parameters UserController.java
```

Then:

```
Method method = UserController.class.getMethod("findUser", Long.class);

for (Parameter parameter : method.getParameters()) {
    System.out.println(parameter.getName());
}
```

---

## Edge Cases

Without `-parameters`, Java may show names like:

```text
arg0
arg1
```

Frameworks may use parameter names for:

- Dependency injection.
- Request mapping.
- Serialization.
- Validation.

If parameter names are missing, some framework features may need explicit annotations.

---

## Best Practices

- Use repeating annotations when the same metadata naturally appears multiple times.
- Use type annotations with tools that understand them.
- Add `-parameters` when your framework benefits from real parameter names.
- Do not rely on parameter names unless your build is configured to keep them.

---

## Quick Summary

Java 8 improved annotations for frameworks and tools. Repeating annotations allow the same annotation multiple times, type annotations allow annotations on type usage, and `-parameters` lets reflection see real method parameter names.

