---
title: Pattern Matching for instanceof
parent: Java-16
nav_order: 2
---

# Pattern Matching for `instanceof`

## Introduction

Java 16 standardized pattern matching for `instanceof`: a type check and a flow-scoped typed binding in one expression. It was previewed in Java 14 and Java 15.

## Why it exists

Traditional type handling repeated the type and manual cast, making boundary code verbose and creating opportunities for unsafe or incorrectly scoped casts.

## Problem it solves

The pattern variable exists only on paths where the runtime type test has succeeded, removing repeated casts while keeping the control flow explicit.

## Before and after

```java
public void test() {
// Before Java 16
    if (payload instanceof PaymentCommand) {
        PaymentCommand command = (PaymentCommand) payload;
        process(command);
    }

// Java 16+
    if (payload instanceof PaymentCommand command) {
        process(command);
    }    
}

```

## Syntax

```
if (value instanceof String text && !text.isBlank()) {
    return text.trim();
}
```

The binding is flow-scoped: it is available only after a successful test. It is not record deconstruction and it does not match `null`.

## Internal Working

The runtime performs the familiar type test. If it succeeds, the reference is bound to the pattern variable without a source-level repeated cast. This is a language-level convenience, not reflection or object transformation.

## Compiler Behavior

The compiler uses definite-assignment and flow analysis to prevent use of the variable on an unmatched path. It can extend the binding after a negated guard only when the remaining path is provably matched:

```
if (!(value instanceof String text)) {
    return "";
}
return text.trim();
```

It rejects patterns that are unconditional for the expression's static type because they do not provide a meaningful test.


## Real-world Usage

Use it at boundaries: translating exceptions, adapting legacy payloads, implementing a small internal command/result family, or safely inspecting values from extensibility points. Use polymorphism when behavior belongs on the type and the hierarchy will grow.


## Best Practices

- Keep a binding close to its guard.
- Use meaningful pattern-variable names.
- Combine with a simple guard only when it remains readable.
- Prefer sealed hierarchies or polymorphism for finite or behavior-rich variants.
- Keep adapters defensive when values originate outside your service.

## Common Mistakes

1. Expecting the variable to exist outside its proven scope.
2. Assuming `null` matches the pattern.
3. Using a large `else if` chain where a domain abstraction is clearer.
4. Assuming proxy classes behave exactly like concrete entities.
5. Confusing Java 16 `instanceof` patterns with later switch-pattern or record-pattern features.

## Edge Cases

`null instanceof Type` is `false`, so no variable is bound. A variable declared in `a instanceof Type value || otherCondition` is not safely available after the `||`, because the right side may make the condition true without a successful match. Be deliberate with `&&`, `||`, negation, and early returns.

## Daily coding sample

```java
public ProblemDetail toProblem(Throwable error) {
    if (error instanceof IllegalArgumentException invalid) {
        return ProblemDetail.forStatusAndDetail(400, invalid.getMessage());
    }
    if (error instanceof AccessDeniedException denied) {
        return ProblemDetail.forStatusAndDetail(403, denied.getMessage());
    }
    return ProblemDetail.forStatusAndDetail(500, "Unexpected server error");
}
```

Keep this mapping centralized, avoid exposing internal exception details, and test every supported exception type.

---

## Version Timeline

| Version | Status         | Change                                      |
|---------|----------------|---------------------------------------------|
| Java 14 | Preview        | Initial type-test and binding syntax.       |
| Java 15 | Second preview | Refined preview.                            |
| Java 16 | Standard       | Final production feature; no preview flags. |
