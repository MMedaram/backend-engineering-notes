---
title: Pattern Matching instanceof Preview
parent: Java-14
nav_order: 2
---

# Java 14 - Pattern Matching for instanceof Preview

Java 14 introduced **pattern matching for `instanceof`** as a **preview feature**.

Simple meaning:

It lets us check the type and create a typed variable in one step.

---

## Preview Feature Status

| Version | Status                      |
|---------|-----------------------------|
| Java 14 | Preview                     |
| Java 15 | Second preview              |
| Java 16 | Standard production feature |

Important:

In Java 14, this feature needs preview flags.

In modern Java projects, use Java 16+ pattern matching for `instanceof` as the final version.

---

## Why was it introduced?

Before Java 14, code using `instanceof` often repeated the same type.

Old style:

```
if (value instanceof String) {
    String text = (String) value;
    System.out.println(text.toUpperCase());
}
```

Problems:

- `String` is written twice.
- The cast is manual.
- A wrong cast can create bugs.
- The code is noisy when repeated many times.

---

## Java 14 preview style

```
if (value instanceof String text) {
    System.out.println(text.toUpperCase());
}
```

This means:

```text
If value is a String, create a String variable named text.
```

Inside the `if` block, `text` is already a `String`.

No manual cast is needed.

---

## Daily backend use case: validation

```java
public void validate(Object input) {
    if (input instanceof String text && !text.isBlank()) {
        System.out.println("Valid text: " + text);
        return;
    }

    throw new IllegalArgumentException("Input must be a non-empty string");
}
```

Useful when working with:

- generic request maps
- event payloads
- deserialized data
- validation utilities
- old APIs that return `Object`

---

## Daily backend use case: equals method

Old style:

```java
@Override
public boolean equals(Object o) {
    if (!(o instanceof CustomerId)) {
        return false;
    }
    CustomerId other = (CustomerId) o;
    return value.equals(other.value);
}
```

Pattern matching style:

```java
@Override
public boolean equals(Object o) {
    return o instanceof Customer other
            && value.equals(other.value);
}
```

This is shorter and removes the manual cast.

---

## Daily backend use case: event handling

```java
public void handle(Object event) {
    if (event instanceof OrderCreated orderCreated) {
        processCreated(orderCreated);
    } else if (event instanceof OrderCancelled orderCancelled) {
        processCancelled(orderCancelled);
    } else {
        throw new IllegalArgumentException("Unsupported event: " + event);
    }
}
```

This is useful when code receives a base type or `Object`.

For large event systems, prefer a proper interface, sealed hierarchy, or visitor-style design where suitable.

---

## Flow scoping

The pattern variable is available only where Java knows the match succeeded.

```
if (value instanceof String text) {
    System.out.println(text.length());
}

// text is not available here
```

This is called flow scoping.

Simple meaning:

Java keeps the variable in scope only in the code path where it is safe to use.

---

## Works nicely with &&

```
if (value instanceof String text && text.length() > 5) {
    System.out.println(text.toUpperCase());
}
```

Why this is safe:

The right side of `&&` runs only if the left side is true.

So `text.length()` runs only after `value instanceof String text` has matched.

---

## Does not work the same way with ||

Bad:

```
if (value instanceof String text || text.length() > 5) {
    System.out.println(text);
}
```

Possible result:

```text
compile-time error
```

Why:

With `||`, the second part may run when the first part is false.

If the first part is false, `text` was never created.

---

## Useful negation pattern

```java
public void printUpperCase(Object value) {
    if (!(value instanceof String text)) {
        throw new IllegalArgumentException("Expected String");
    }

    System.out.println(text.toUpperCase());
}
```

After the guard clause, Java knows `text` is available.

This style is useful for clean validation code.

---

## Null behavior

`null` does not match any type pattern.

```
Object value = null;

if (value instanceof String text) {
    System.out.println(text);
} else {
    System.out.println("Not a String");
}
```

Output:

```text
Not a String
```

No `NullPointerException` is thrown by the `instanceof` check itself.

---

## Compile and run Java 14 preview code

Compile:

```bash
javac --release 14 --enable-preview PatternDemo.java
```

Run:

```bash
java --enable-preview PatternDemo
```

Run JAR:

```bash
java --enable-preview -jar app.jar
```

---

## Edge cases and negative cases

### 1. Missing preview flag

```bash
javac --release 14 PatternDemo.java
```

Possible result:

```text
error: pattern matching in instanceof is a preview feature and is disabled by default
```

---

### 2. Pattern variable is not available outside the safe area

Bad:

```
if (value instanceof String text) {
    System.out.println(text.length());
}

System.out.println(text);
```

Possible result:

```text
cannot find symbol
```

---

### 3. Pattern variable with ||

Bad:

```
if (value instanceof String text || text.isBlank()) {
    System.out.println(text);
}
```

Possible result:

```text
cannot find symbol
```

Use `&&` when the second condition depends on the pattern variable.

---

### 4. Pattern does not match null

```
Object value = null;

System.out.println(value instanceof String text);
```

Output:

```text
false
```

This is the same general null behavior as normal `instanceof`.

---

### 5. Do not hide poor design with too many instanceof checks

If a method has many checks like this:

```
if (event instanceof A a) {
    ...
} else if (event instanceof B b) {
    ...
} else if (event instanceof C c) {
    ...
}
```

it may be a sign that the model needs improvement.

Possible alternatives:

- common interface method
- enum-driven command
- sealed classes in Java 17+
- polymorphism
- pattern matching `switch` in later Java versions

---

### 6. Pattern variable can shadow a field

```java
class Demo {
    private String text = "field";

    void print(Object value) {
        if (value instanceof String text) {
            System.out.println(text);
        }
    }
}
```

Inside the `if`, `text` means the pattern variable, not the field.

Best practice:

Use clear variable names to avoid confusion.

---

## Best practices

1. Use pattern matching for `instanceof` to remove manual casts.
2. Use it in `equals()` methods and validation helpers.
3. Prefer `&&` when checking extra conditions on the pattern variable.
4. Use guard clauses for clean validation.
5. Do not overuse `instanceof` when polymorphism is better.
6. Keep pattern variable names clear and short.
7. Remember that `null` does not match.
8. In Java 14, compile and run with preview flags.
9. In modern projects, use Java 16+ final behavior.

---

## Interview one-liner

Java 14 introduced pattern matching for `instanceof` as a preview feature so Java can check a type and create a typed variable in one step. It removes repeated casts and became standard in Java 16.

---

