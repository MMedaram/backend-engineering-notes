---
title: Files mismatch
parent: Java-12
nav_order: 3
---

# Java 12 - Files.mismatch()

Java 12 added:

```
Files.mismatch(Path path, Path path2)
```

Simple meaning:

It compares two files and returns the first byte position where they are different.

If files are the same, it returns:

```text
-1
```

---

## Why was it introduced?

Before Java 12, if we wanted to compare file content, we usually had to:

- read both files manually
- compare byte by byte
- calculate checksums
- use external libraries

Java 12 made this common task easier.

---

## Basic example

```
Path file1 = Path.of("old-report.txt");
Path file2 = Path.of("new-report.txt");

long mismatchPosition = Files.mismatch(file1, file2);

System.out.println(mismatchPosition);
```

Possible output:

```text
-1
```

`-1` means both files match.

---

## Files are different

Imagine:

```text
file1: Java
file2: Jaxa
```

Code:

```
long mismatchPosition = Files.mismatch(file1, file2);

System.out.println(mismatchPosition);
```

Output:

```text
2
```

Reason:

Index `2` is the first different byte:

```text
J a v a
J a x a
    ^
```

---

## Different file sizes

Imagine:

```text
file1: Java
file2: Java12
```

Code:

```java
long mismatchPosition = Files.mismatch(file1, file2);
```

Output:

```text
4
```

Reason:

The first 4 bytes match, but the smaller file ends at position `4`.

---

## Daily backend use case: avoid duplicate upload

```java
public boolean hasSameContent(Path existingFile, Path uploadedFile) throws IOException {
    return Files.mismatch(existingFile, uploadedFile) == -1;
}
```

Useful for:

- uploaded documents
- generated reports
- config files
- backup comparison
- file sync logic

---

## Daily backend use case: log first difference

```
long mismatch = Files.mismatch(expectedFile, actualFile);

if (mismatch == -1) {
    log.info("Files match");
} else {
    log.warn("Files differ at byte position {}", mismatch);
}
```

This is useful in testing and debugging generated files.

---

## What does it compare?

`Files.mismatch()` compares file **content bytes**.

It does not compare:

- file name
- file owner
- file permissions
- last modified time
- file attributes

So two files with the same content but different names can still match.

---

## Same path behavior

```java
long result = Files.mismatch(path, path);
```

Output:

```text
-1
```

A file always matches itself.

---

## Return value map

| Return Value      | Meaning                             |
|-------------------|-------------------------------------|
| `-1`              | Files match                         |
| `0`               | First byte is different             |
| positive number   | First mismatch byte position        |
| smaller file size | Same prefix, but one file is longer |

---

## Edge cases and exceptions

### 1. File does not exist

```
Files.mismatch(Path.of("missing-a.txt"), Path.of("missing-b.txt"));
```

Possible exception:

```text
java.nio.file.NoSuchFileException
```

This is a subclass of `IOException`.

---

### 2. Path points to a directory

```
Files.mismatch(Path.of("folder1"), Path.of("folder2"));
```

Possible exception:

```text
java.io.IOException
```

Exact exception can depend on the operating system and file system provider.

---

### 3. No read permission

Possible exception:

```text
java.nio.file.AccessDeniedException
```

or:

```text
java.lang.SecurityException
```

depending on environment/security manager.

---

### 4. Null path

```
Files.mismatch(null, Path.of("file.txt"));
```

Output:

```text
java.lang.NullPointerException
```

---

### 5. File changes while comparing

The method is not atomic with respect to other file operations.

Simple meaning:

If another process changes the file while comparison is happening, result may not represent a stable snapshot.

Best practice:

For critical comparison, compare files that are not being modified.

---

## Files.mismatch() vs checksum

| Option             | Good For                                          |
|--------------------|---------------------------------------------------|
| `Files.mismatch()` | Find first difference between two available files |
| Checksum/hash      | Store and compare file identity later             |

Example:

Use `Files.mismatch()` when both files are present now.

Use checksum when you want to store a fingerprint in database and compare later.

---

## Best practices

1. Use `Files.mismatch()` for direct file content comparison.
2. Check `-1` for same files.
3. Treat any non-negative value as different files.
4. Handle `IOException`.
5. Do not use it to compare file metadata.
6. Avoid comparing files that are actively being written.
7. Use checksums when you need persistent file fingerprints.

---

## Summary

Java 12 added `Files.mismatch()` to compare two files byte by byte and return the first mismatch position, or `-1` when both files have the same content.

---

