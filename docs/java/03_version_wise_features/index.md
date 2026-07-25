---
title: Java Versions
parent: Java
nav_order: 3
---

# Java Version-wise Features
---

## Java 8 (LTS) - The Biggest Modern Java Release

### Must Know
- Lambda Expressions
- Functional Interfaces
- Stream API
- Method References
- Optional
- Default Methods
- Static Interface Methods
- Date & Time API
- CompletableFuture

### Good to Know
- base 64
- repeated annotations
- `String.join` , `StringJoiner`

---

## Java 9 - Modular Java
### Must Know
- Module System (Java Platform Module System (JPMS))
- JShell
- Collection Factory Methods (`List.of`, `Set.of`, `Map.of`)

### Good to Know
- Private Interface Methods
- Try-with-Resources enhancement
- Stream updates (`takeWhile`, `dropWhile`, `ofNullable`, improved `iterate`)
- Optional updates (`ifPresentOrElse`, `or`, `stream`)
- G1 as default GC

---


## Java 10 - Small but Useful
### Must Know
- `var`

### Good to Know
- `List.copyOf`, `Set.copyOf`, `Map.copyOf`
- `Collectors.toUnmodifiableList`, `toUnmodifiableSet`, `toUnmodifiableMap`
- `Optional.orElseThrow()`

---

## Java 11 - Practical Everyday Updates
### Must Know
- HTTP Client API
- String methods (`isBlank`, `lines`, `repeat`, `strip`)
- Files methods (`readString`, `writeString`)

### Good to Know
- `var` in lambda parameters
- Single-file source execution
- `Optional.isEmpty`
- `Predicate.not`
- Nest-based access control

### Awareness Only
- Epsilon GC (Experimental)
- ZGC (Experimental)

---

## Java 12 - Preview and Utility Updates
### Good to Know
- Switch Expressions (Preview)
- `String.indent(int)`
- `String.transform(Function)`
- `Files.mismatch(Path, Path)`
- `Collectors.teeing(...)`

### Awareness Only
- Shenandoah GC (Experimental)
- Finalize-related cleanup changes

---

## Java 13 - Transition Release
### Good to Know
- Text Blocks (Preview)
- Switch expression improvements (Preview)

### Awareness Only
- ZGC experimental


---

## Java 14 - Important Language Step
### Must Know
- Switch Expressions (Standard)
- Helpful NullPointerException

### Good to Know
- Records (Preview)
- Pattern Matching for `instanceof` (Preview)

### Awareness Only
- CMS GC removed

---

## Java 15 - More Language Maturity
### Must Know
- Text Blocks (Standard)

### Good to Know
- Records (Second Preview)
- Sealed Classes (Preview)
- Pattern Matching for `instanceof` (Second Preview)

### Awareness Only
- ZGC production
- Shenandoah GC production

---

## Java 16 - Very Important for Developers
### Must Know
- Records
- Pattern Matching for `instanceof`
- `Stream.toList()`
- `mapMulti()`

### Good to Know
- Sealed Classes (Second Preview)

---

## Java 17 - LTS and Very Important
### Must Know
- Sealed Classes
- HexFormat

### Good to Know
- RandomGenerator API

### Awareness Only
- Pattern Matching for `switch` (Preview)


---

# LTS Releases

| Java Version | LTS |
|--------------|-----|
| Java 8       | ✅   |
| Java 11      | ✅   |
| Java 17      | ✅   |


