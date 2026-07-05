---
title: Single-File Source-Code Programs
parent: Java-11
nav_order: 4
---

# Single-File Source-Code Programs

Java 11 allows you to run a single `.java` file directly.

Before Java 11:

```bash
javac Hello.java
java Hello
```

Java 11:

```bash
java Hello.java
```

The compiler runs in memory. No `.class` file is written to your working directory.

---

## Why Was It Introduced?

Java is great for large applications, but small scripts and examples had too much ceremony.

For learning or quick tools, this was annoying:

1. Write `.java` file.
2. Compile with `javac`.
3. Run with `java`.
4. Clean generated `.class` files.

Java 11 made small Java programs easier to run.

---

## Basic Example

Create `Hello.java`:

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello Java 11");
    }
}
```

Run:

```bash
java Hello.java
```

---

## Passing Arguments

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello " + args[0]);
    }
}
```

Run:

```bash
java Hello.java Mohan
```

Output:

```text
Hello Mohan
```

### Edge Case

If no argument is passed:

```text
ArrayIndexOutOfBoundsException
```

Safer code:

```java
public class Hello {
    public static void main(String[] args) {
        String name = args.length > 0 ? args[0] : "Guest";
        System.out.println("Hello " + name);
    }
}
```

---

## Using --source

You can specify source version:

```bash
java --source 11 Hello.java
```

This is useful when you want to force Java 11 language rules.

---

## Shebang Scripts

On Unix-like systems, Java source files can be used as scripts.

Example file:

```java
#!/usr/bin/env java --source 11

public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello from script");
    }
}
```

Then:

```bash
chmod +x hello
./hello
```

This is useful for small CLI scripts.

---

## Daily Coding Use Cases

### 1. Quick Java Demo

```bash
java OptionalDemo.java
```

### 2. Small Utility Script

```bash
java CleanTempFiles.java
```

### 3. Learning New APIs

```bash
java HttpClientExample.java
```

---

## What It Is Not For

Do not use this for:

- Spring Boot applications.
- Multi-file projects.
- Maven or Gradle applications.
- Production service packaging.

Use normal build tools for real applications.

---

## Common Edge Cases

### Compile Error

If the source file has a compile error, the program does not run.

```text
error: cannot find symbol
```

### External Dependencies

For dependencies, you still need classpath/module-path setup.

```bash
java -cp "libs/*" MyTool.java
```

### Java Version Mismatch

If your file uses Java 11 features but runs on Java 8:

```text
Unrecognized option: --source
```

or the command may not support source-file mode.

---

## Best Practices

- Use it for learning, demos, and simple tools.
- Use normal Maven/Gradle builds for real applications.
- Validate command-line arguments.
- Avoid putting too much logic into one source file.
- Use `--source 11` when you want predictable Java 11 behavior.

---

## Quick Summary

Java 11 can run a single `.java` file directly using `java File.java`. It is useful for learning, examples, scripts, and small tools, but not for full backend applications.

---

