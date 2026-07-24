---
title: Migration and Cleanup Notes
parent: Java-12
nav_order: 7
---

# Java 12 - Migration and Cleanup Notes

This note is about daily coding habits that make Java upgrades easier.

It is not only about `finalize()` being removed from some JDK classes.

The bigger developer lesson is:

> Write code that uses supported APIs, closes resources clearly, and does not depend on hidden JDK behavior.

Java 12 continued the direction started in Java 9:

- depend less on JDK-internal classes
- close files/resources clearly
- move away from `finalize()`
- use tools like `jdeps` to understand dependencies
- treat preview features carefully

These points matter a lot in backend projects because old applications often have hidden problems that appear only during Java upgrades.

---

## Daily Developer Focus

When writing normal backend code:

1. Use public Java APIs, not hidden JDK classes.
2. Use `try-with-resources` for anything that must be closed.
3. Do not write cleanup logic inside `finalize()`.
4. Use `AutoCloseable` when your own class owns a file, stream, socket, or connection.
5. Use `jdeps` during migration to find risky dependencies.
6. Use preview features only when compile, test, and runtime commands all enable preview.

This is the practical mindset behind this note.

---

## Quick Summary

| Topic                          | Developer Meaning                                |
|--------------------------------|--------------------------------------------------|
| Avoid old JDK-internal classes | Do not depend on hidden JDK implementation code  |
| Use try-with-resources         | Close files, streams, DB resources safely        |
| Avoid `finalize()`             | Do not depend on garbage collector for cleanup   |
| Use `jdeps`                    | Find module dependencies and internal API usage  |
| Use preview flags              | Preview features need explicit compile/run flags |

---

# 1. Do Not Depend on Old JDK-Internal Classes

## What are JDK-internal classes?

JDK-internal classes are classes used inside the JDK implementation.

They were not designed as public APIs for application developers.

Common examples:

```text
sun.*
most com.sun.*
most jdk.*
```

Not every class in these packages is always wrong, but as a backend developer, you should be careful when application code directly imports them.

---

## Why is this a problem?

Old code may compile and run for many years, then suddenly fail after a Java upgrade.

Reason:

Internal classes can be:

- moved
- hidden
- removed
- changed without warning
- blocked by the module system

Example of risky old code:

```java
import sun.misc.BASE64Encoder;

public class TokenUtil {
    public String encode(byte[] data) {
        return new BASE64Encoder().encode(data);
    }
}
```

This code depends on an internal JDK class.

Better:

```java
import java.util.Base64;

public class TokenUtil {
    public String encode(byte[] data) {
        return Base64.getEncoder().encodeToString(data);
    }
}
```

`java.util.Base64` is a supported public API.

---

## Daily backend examples

### Bad: internal Base64

```java
import sun.misc.BASE64Decoder;
import sun.misc.BASE64Encoder;
```

Good:

```java
import java.util.Base64;
```

---

### Bad: internal date/time classes

```java
import sun.util.calendar.ZoneInfo;
```

Good:

```java
import java.time.ZoneId;
import java.time.ZonedDateTime;
```

---

### Bad: internal compiler classes in normal app code

```java
import com.sun.tools.javac.tree.JCTree;
```

Better for tool/compiler-related code:

```java
import javax.tools.JavaCompiler;
import javax.tools.ToolProvider;
```

or use supported compiler tree APIs when you are building compiler tools.

---

## How to find internal API usage

Use `jdeps`:

```bash
jdeps --jdk-internals target/my-app.jar
```

Possible output may show:

```text
JDK internal API
```

That means your app or one of your libraries is using something from inside the JDK.

---

## Edge cases

### 1. A library may use internal APIs

Sometimes your code is clean, but a third-party dependency uses internal APIs.

What to do:

- upgrade the dependency
- check the library release notes
- avoid adding `--add-opens` or `--add-exports` as the first solution

### 2. `sun.misc.Unsafe` is a special case

Some frameworks use `sun.misc.Unsafe` for performance or low-level memory operations.

As application developers, we should not use it in normal business code.

Framework maintainers may use it carefully, but that is a different case.

### 3. `--add-opens` and `--add-exports` are temporary escape doors

Example:

```bash
java --add-opens java.base/java.lang=ALL-UNNAMED -jar app.jar
```

This can unblock a migration temporarily.

But it should not become the long-term design.

Best practice:

Move to supported APIs and newer library versions.

---

# 2. Prefer try-with-resources for Closing Files and Resources

## Why do we need it?

Many objects hold external resources:

- files
- sockets
- database connections
- input streams
- output streams
- ZIP files

These resources should be closed after use.

If they are not closed, the application can leak file handles, memory, or database connections.

---

## Old style: finally block

```
FileInputStream input = null;

try {
    input = new FileInputStream("data.txt");
    int firstByte = input.read();
    System.out.println(firstByte);
} finally {
    if (input != null) {
        input.close();
    }
}
```

This works, but it is noisy.

It is also easy to forget the null check or close the wrong resource.

---

## Better: try-with-resources

```
try (FileInputStream input = new FileInputStream("data.txt")) {
    int firstByte = input.read();
    System.out.println(firstByte);
}
```

Java automatically calls `input.close()` after the block.

This happens whether the code succeeds or fails.

---

## Daily backend use case: file reading

```
try (BufferedReader reader = Files.newBufferedReader(Path.of("users.csv"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}
```

---

## Daily backend use case: JDBC

```
String sql = "select id, name from users where status = ?";

try (Connection connection = dataSource.getConnection();
     PreparedStatement statement = connection.prepareStatement(sql)) {

    statement.setString(1, "ACTIVE");

    try (ResultSet resultSet = statement.executeQuery()) {
        while (resultSet.next()) {
            System.out.println(resultSet.getString("name"));
        }
    }
}
```

All resources are closed automatically.

Closing order is reverse order:

```text
ResultSet
PreparedStatement
Connection
```

---

## Edge cases

### 1. Resource creation fails

```
try (FileInputStream input = new FileInputStream("missing.txt")) {
    System.out.println(input.read());
}
```

Possible exception:

```text
java.io.FileNotFoundException
```

The block does not run if the resource cannot be created.

### 2. Exception inside block and exception during close

If the main block throws an exception and `close()` also throws an exception, Java keeps the main exception and stores the close exception as a suppressed exception.

Example:

```
catch (IOException e) {
    for (Throwable suppressed : e.getSuppressed()) {
        System.out.println(suppressed.getMessage());
    }
}
```

This is useful when debugging resource cleanup failures.

### 3. Resource can be null

If a resource variable is null, Java does not call `close()` on it.

But best practice is simple:

Do not intentionally put null resources inside try-with-resources.

### 4. Only works with AutoCloseable

The resource must implement:

```
AutoCloseable
```

or:

```
Closeable
```

Otherwise, code will not compile.

---

## Best practice

Use try-with-resources for anything that must be closed.

Examples:

- `InputStream`
- `OutputStream`
- `Reader`
- `Writer`
- `Connection`
- `PreparedStatement`
- `ResultSet`
- `ZipFile`

---

# 3. Do Not Rely on finalize() for Cleanup

## What is finalize()?

`finalize()` was an old method that Java could call before garbage collecting an object.

Example:

```
public class FileHolder {
    @Override
    protected void finalize() throws Throwable {
        System.out.println("cleanup");
    }
}
```

This looks like automatic cleanup, but it is not reliable.

---

## Why is finalize() bad?

Problems:

- no guarantee when it will run
- may not run before the application exits
- can delay garbage collection
- can create security and performance problems
- exceptions thrown from `finalize()` are ignored
- cleanup happens too late for files, sockets, and DB connections

For backend systems, this is dangerous.

Resources should be released immediately after use, not sometime later.

---

## Java 12 migration point

Java 12 removed old `finalize()` methods from some JDK classes, including:

- `FileInputStream`
- `FileOutputStream`
- `ZipFile`
- `Inflater`
- `Deflater`

Developer meaning:

Do not depend on finalizers to close files, streams, ZIP files, or compression objects.

Use explicit cleanup.

---

## Bad code

```java
public class ReportWriter {
    private FileOutputStream output;

    public ReportWriter(String fileName) throws IOException {
        this.output = new FileOutputStream(fileName);
    }

    @Override
    protected void finalize() throws Throwable {
        output.close();
    }
}
```

Problem:

The file may stay open for a long time.

---

## Better: AutoCloseable

```java
public class ReportWriter implements AutoCloseable {
    private final FileOutputStream output;

    public ReportWriter(String fileName) throws IOException {
        this.output = new FileOutputStream(fileName);
    }

    public void write(byte[] data) throws IOException {
        output.write(data);
    }

    @Override
    public void close() throws IOException {
        output.close();
    }
}
```

Use it like this:

```
try (ReportWriter writer = new ReportWriter("report.txt")) {
    writer.write("hello".getBytes(java.nio.charset.StandardCharsets.UTF_8));
}
```

Now cleanup is clear and immediate.

---

## What about Cleaner?

`java.lang.ref.Cleaner` can be used as a backup cleanup mechanism.

But for daily backend code, prefer:

```text
try-with-resources + AutoCloseable
```

Use `Cleaner` only when you are building low-level libraries and you really understand the lifecycle.

---

## Edge cases

### 1. System.gc() does not guarantee cleanup

Bad thinking:

```
System.gc();
```

This does not guarantee that `finalize()` will run immediately.

### 2. finalize() exception is ignored

If `finalize()` throws an exception, Java ignores it and cleanup may remain incomplete.

### 3. Object resurrection

`finalize()` can accidentally make an object reachable again.

This is called object resurrection.

It makes cleanup behavior harder to understand.

Best practice:

Do not write new `finalize()` methods.

---

# 4. Use jdeps for Module and Dependency Checks

## What is jdeps?

`jdeps` is a JDK tool that analyzes Java class and JAR dependencies.

In simple words:

It tells you what your application depends on.

This is helpful during Java migration because old applications may depend on:

- removed Java EE modules
- JDK-internal APIs
- missing libraries
- modules that should be added explicitly

---

## Common commands

### Check JDK-internal API usage

```bash
jdeps --jdk-internals target/my-app.jar
```

### Print module dependencies

```bash
jdeps --print-module-deps target/my-app.jar
```

### List dependencies

```bash
jdeps --list-deps target/my-app.jar
```

### Show reduced module dependencies

```bash
jdeps --list-reduce-deps target/my-app.jar
```

---

## Java 12 improvement

In Java 12, these `jdeps` options became smarter:

```text
--print-module-deps
--list-deps
--list-reduce-deps
```

By default, they analyze transitive module dependencies.

Simple meaning:

They do not only look at direct dependencies. They also look deeper into dependencies required by those dependencies.

---

## Missing dependency behavior

In Java 12, missing dependencies are reported as errors by default.

Example:

```bash
jdeps --print-module-deps target/my-app.jar
```

If some dependency is missing from classpath/module path, `jdeps` can fail.

Temporary option:

```bash
jdeps --ignore-missing-deps --print-module-deps target/my-app.jar
```

Important:

Use `--ignore-missing-deps` carefully.

It can hide real runtime problems.

---

## Daily migration use case

Before moving an old Java 8 or Java 11 app forward:

```bash
jdeps --jdk-internals target/my-app.jar
jdeps --print-module-deps target/my-app.jar
```

Then check:

- Are we using internal JDK APIs?
- Are any dependencies missing?
- Are Java EE APIs still expected from the JDK?
- Are module dependencies clear?

---

## Edge cases

### 1. Reflection may not be fully visible

If your app loads classes dynamically:

```
Class.forName("com.example.SomeClass");
```

`jdeps` may not fully understand that runtime behavior.

So do not use `jdeps` as the only migration test.

Also run automated tests.

### 2. Classpath matters

If you run `jdeps` without required libraries, results may be incomplete or fail.

Add classpath/module path when needed.

Example:

```bash
jdeps --class-path "libs/*" target/my-app.jar
```

### 3. Multi-release JARs

Some libraries contain version-specific classes.

Use:

```bash
jdeps --multi-release 12 target/my-library.jar
```

This tells `jdeps` which Java version view to analyze.

---

# 5. Use Preview Flags for Preview Features

## What is a preview feature?

A preview feature is complete enough for developers to try, but not final yet.

It can:

- change in the next Java version
- be previewed again
- become standard later
- be removed

Java 12 introduced switch expressions as a preview feature.

Switch expressions became standard later in Java 14.

---

## Important Java 12 switch expression note

Java 12 preview syntax used `break value` for returning from block cases.

Example Java 12 preview style:

```
int days = switch (month) {
    case 1, 3, 5, 7, 8, 10, 12 -> 31;
    case 4, 6, 9, 11 -> 30;
    case 2 -> {
        break 28;
    }
    default -> throw new IllegalArgumentException("Invalid month: " + month);
};
```

Later Java changed this direction.

The final Java 14 style uses `yield`:

```
int days = switch (month) {
    case 1, 3, 5, 7, 8, 10, 12 -> 31;
    case 4, 6, 9, 11 -> 30;
    case 2 -> {
        yield 28;
    }
    default -> throw new IllegalArgumentException("Invalid month: " + month);
};
```

So if you are learning today, understand Java 12 history, but write final-style switch expressions in Java 14+ projects.

---

## Compile Java 12 preview code

Use JDK 12 and enable preview:

```bash
javac --release 12 --enable-preview SwitchDemo.java
```

or:

```bash
javac -source 12 --enable-preview SwitchDemo.java
```

---

## Run Java 12 preview code

Use:

```bash
java --enable-preview SwitchDemo
```

If packaged as a JAR:

```bash
java --enable-preview -jar app.jar
```

---

## What happens if flags are missing?

### Compile without preview flag

```bash
javac --release 12 SwitchDemo.java
```

Possible result:

```text
error: switch expressions are a preview feature and are disabled by default
```

### Run without preview flag

```bash
java SwitchDemo
```

Possible result:

```text
UnsupportedClassVersionError
```

Reason:

The class file was compiled with preview features, but runtime preview support was not enabled.

---

## Edge cases

### 1. JDK version must match preview version

Java 12 preview features should be compiled with JDK 12.

Do not expect JDK 13 or JDK 14 to compile old Java 12 preview syntax.

Preview features are tied to the JDK release that introduced them.

### 2. Do not use preview features casually in production

Preview features are for feedback and learning.

If a company uses preview features in production, it should be a conscious technical decision.

### 3. CI/CD also needs preview flags

It is not enough to run preview code locally.

Build tools, CI, test commands, and runtime commands must also use preview flags.

### 4. Preview warnings are expected

Even when enabled, the compiler may warn that preview features are being used.

This is intentional.

It reminds developers that the feature is not final yet.

---

# Practical Migration Checklist

Use this checklist when moving an old app toward Java 12 or later:

1. Run tests on the newer JDK.
2. Run `jdeps --jdk-internals`.
3. Replace direct `sun.*`, `com.sun.*`, and `jdk.*` usage where possible.
4. Upgrade old dependencies that use internal JDK APIs.
5. Replace `finally` cleanup code with try-with-resources where suitable.
6. Remove custom `finalize()` cleanup logic.
7. Use `AutoCloseable` for classes that own resources.
8. Run `jdeps --print-module-deps` if creating module-aware builds or runtime images.
9. Avoid `--ignore-missing-deps` unless you know why dependencies are missing.
10. Use preview features only with explicit compile and run flags.

---

# Best Practices

1. Prefer supported Java APIs over internal JDK classes.
2. Treat `--add-opens` and `--add-exports` as temporary migration helpers.
3. Use try-with-resources for files, streams, sockets, JDBC objects, and ZIP files.
4. Do not write new `finalize()` methods.
5. Use `AutoCloseable` when your class owns a closeable resource.
6. Use `jdeps` before and during Java version upgrades.
7. Do not rely only on `jdeps`; run automated tests too.
8. Do not distribute preview-feature code as if it is stable.
9. For modern projects, use the final version of features when available.

---

# Summary One-Liner

Java 12 migration cleanup is mainly about safer upgrades: avoid JDK-internal APIs, close resources with try-with-resources, do not rely on `finalize()`, use `jdeps` to inspect dependencies, and enable preview features explicitly at compile time and runtime.

