---
title: Everyday API Improvements
parent: Java-11
nav_order: 1
---

# Everyday API Improvements

Java 11 added small but useful methods that developers use often.

Important APIs:

- `String.isBlank()`
- `String.strip()`
- `String.stripLeading()`
- `String.stripTrailing()`
- `String.lines()`
- `String.repeat()`
- `Files.readString()`
- `Files.writeString()`
- `Optional.isEmpty()`
- `Predicate.not()`
- `Collection.toArray(IntFunction)`

These are not huge language features, but they make normal coding cleaner.

---

## 1. String.isBlank

### Why Was It Introduced?

Before Java 11, we often wrote:

```
if (name == null || name.trim().isEmpty()) {
    throw new IllegalArgumentException("Name is required");
}
```

Java 11 added:

```
if (name == null || name.isBlank()) {
    throw new IllegalArgumentException("Name is required");
}
```

`isBlank()` returns true when the string is empty or contains only white space.

```
"".isBlank();       // true
"   ".isBlank();    // true
"\n\t".isBlank();   // true
"Mohan".isBlank();  // false
```

### Edge Case: Null String

```
String name = null;
name.isBlank(); // NullPointerException
```

If the value can be null, check null first.

---

## 2. strip, stripLeading, stripTrailing

`strip()` removes white space from both sides.

```
"  hello  ".strip(); // "hello"
```

`stripLeading()` removes from the beginning.

```
"  hello  ".stripLeading(); // "hello  "
```

`stripTrailing()` removes from the end.

```
"  hello  ".stripTrailing(); // "  hello"
```

### strip vs trim

`trim()` is older and handles a smaller set of whitespace characters.

`strip()` is Unicode-aware.

Developer meaning:

- Use `strip()` for modern text handling.
- Use `trim()` only when old behavior is required.

---

## 3. String.lines

`lines()` splits a string into a stream of lines.

```
String text = "first\nsecond\nthird";

List<String> lines = text.lines()
    .collect(Collectors.toList());

System.out.println(lines); // [first, second, third]
```

Daily use case:

```java
long nonEmptyLines = requestBody.lines()
    .filter(Predicate.not(String::isBlank))
    .count();
```

### Edge Cases

Line separators are not included in the returned lines.

```
"a\nb".lines().collect(Collectors.toList()); // [a, b]
```

Empty string returns zero lines.

```
"".lines().count(); // 0
```

---

## 4. String.repeat

Repeats a string.

```
"=".repeat(10); // "=========="
```

Daily use case:

```java
String separator = "-".repeat(40);
```

### Edge Cases

```
"abc".repeat(0); // ""
```

Negative count:

```
"abc".repeat(-1); // IllegalArgumentException
```

Very large repeat count can cause:

```text
OutOfMemoryError
```

So do not repeat user-controlled values without a limit.

---

## 5. Files.readString

Reads the full file into a String.

```java
Path path = Path.of("config.json");

String content = Files.readString(path);
```

By default, it reads using UTF-8.

With charset:

```java
String content = Files.readString(path, StandardCharsets.ISO_8859_1);
```

### Edge Cases

If file does not exist:

```text
NoSuchFileException
```

For other read problems:

```text
IOException
```

For huge files, avoid `readString()` because it loads the whole file into memory.

Use streaming instead:

```
try (Stream<String> lines = Files.lines(path)) {
    lines.forEach(System.out::println);
}
```

---

## 6. Files.writeString

Writes text to a file.

```
Path path = Path.of("output.txt");

Files.writeString(path, "Hello Java 11");
```

With charset:

```
Files.writeString(path, "Hello", StandardCharsets.UTF_8);
```

Append:

```
Files.writeString(
    path,
    "New line\n",
    StandardOpenOption.CREATE,
    StandardOpenOption.APPEND
);
```

### Edge Cases

By default, it creates or overwrites the file depending on options.

If parent directory does not exist:

```text
NoSuchFileException
```

If permission is missing:

```text
AccessDeniedException
```

---

## 7. Optional.isEmpty

Before Java 11:

```
if (!user.isPresent()) {
    throw new UserNotFoundException();
}
```

Java 11:

```
if (user.isEmpty()) {
    throw new UserNotFoundException();
}
```

This is easier to read.

Daily use:

```
Optional<User> user = userRepository.findById(id);

if (user.isEmpty()) {
    return;
}
```

Best practice:

- Use `isEmpty()` for readability.
- Prefer `orElseThrow()` when absence is an error.
- Prefer `ifPresent()` when absence is normal.

---

## 8. Predicate.not

Before Java 11:

```java
List<String> names = values.stream()
    .filter(value -> !value.isBlank())
    .collect(Collectors.toList());
```

Java 11:

```java
List<String> names = values.stream()
    .filter(Predicate.not(String::isBlank))
    .collect(Collectors.toList());
```

This is useful when using method references.

### Edge Case

If the predicate is null:

```
Predicate.not(null); // NullPointerException
```

---

## 9. Collection.toArray(IntFunction)

Java 11 added a cleaner typed array conversion.

```java
List<String> names = List.of("A", "B");

String[] array = names.toArray(String[]::new);
```

Before this, common style was:

```java
String[] array = names.toArray(new String[0]);
```

Both are valid, but `String[]::new` is cleaner.

### Edge Case

If the generator is null:

```
names.toArray(null); // NullPointerException
```

---

## Best Practices

- Use `isBlank()` for empty-or-whitespace validation.
- Use `strip()` instead of `trim()` for Unicode-aware trimming.
- Use `Files.readString()` only for small or medium files.
- Use `Files.lines()` for large files.
- Use `Files.writeString()` for simple text output.
- Use `Optional.isEmpty()` when it improves readability.
- Use `Predicate.not()` to keep stream filters clean.
- Put limits around `String.repeat()` when count comes from user input.

---

## Quick Summary

Java 11 added practical API methods that remove boilerplate from string handling, file reading/writing, Optional checks, stream filters, and typed array conversion.

