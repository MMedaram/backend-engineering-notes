---
title: Files and IO Enhancements
parent: Java-8
nav_order: 15
---

# Files and IO Enhancements

Java 8 added stream-friendly file APIs.

Important methods:

- `Files.lines`
- `Files.walk`
- `Files.find`
- `BufferedReader.lines`

---

## Files.lines

Reads file lines as a stream.

```
Path path = Paths.get("data.txt");

try (Stream<String> lines = Files.lines(path)) {
    lines.forEach(System.out::println);
}
```

Important:

The stream must be closed.

Use try-with-resources.

---

## Files.lines Edge Cases

If file does not exist:

```text
NoSuchFileException
```

If access is denied:

```text
AccessDeniedException
```

If there is an IO problem while reading during stream processing:

```text
UncheckedIOException
```

Why unchecked?

Because stream operations cannot easily throw checked `IOException`.

---

## Files.walk

Walks through a directory tree.

```
try (Stream<Path> paths = Files.walk(Paths.get("src"))) {
    paths.filter(Files::isRegularFile)
        .forEach(System.out::println);
}
```

Use for:

- Finding files.
- Counting files.
- Cleaning directories.

Always close the stream.

---

## Files.find

Finds files using a predicate.

```
try (Stream<Path> paths = Files.find(
        Paths.get("src"),
        5,
        (path, attrs) -> attrs.isRegularFile() && path.toString().endsWith(".java"))) {

    paths.forEach(System.out::println);
}
```

Parameters:

- Start path.
- Max depth.
- Predicate with path and file attributes.

---

## BufferedReader.lines

```
try (BufferedReader reader = Files.newBufferedReader(path)) {
    reader.lines()
        .filter(line -> !line.trim().isEmpty())
        .forEach(System.out::println);
}
```

The reader should be closed.

---

## Daily Coding Example

Count error lines:

```
try (Stream<String> lines = Files.lines(Paths.get("app.log"))) {
    long errorCount = lines
        .filter(line -> line.contains("ERROR"))
        .count();

    System.out.println(errorCount);
}
```

---

## Best Practices

- Always close file streams using try-with-resources.
- Use `Files.lines` for large files instead of reading everything into memory.
- Handle `IOException` when opening files.
- Remember stream processing may throw `UncheckedIOException`.
- Be careful with `Files.walk` on huge directories.
- Avoid modifying a directory while walking it unless you understand the behavior.

---

## Quick Summary

Java 8 made file processing stream-friendly. Use `Files.lines`, `Files.walk`, and `Files.find`, but always close the returned stream.

