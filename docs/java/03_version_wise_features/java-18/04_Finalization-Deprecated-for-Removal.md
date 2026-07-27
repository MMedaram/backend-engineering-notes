---
title: Finalization Deprecated for Removal
parent: Java-18
nav_order: 4
---

# Finalization Deprecated for Removal (`finalize()`)

## What is Finalization?

Finalization is a mechanism that allows Java to execute a special method called **`finalize()`** before an object is removed from memory by the Garbage Collector (GC).

The idea was:

> "Before an object is destroyed, Java gives it one last chance to clean up resources."

The special method is:

```java
protected void finalize() throws Throwable{}
```

---

# What is `finalize()`?

Suppose you have a class.

```java
public class Employee {

    @Override
    protected void finalize() throws Throwable {

        System.out.println("Cleaning resources...");
    }
}
```

When the object becomes unreachable, Java **may** call `finalize()` before removing the object.

Notice the word **may**.

It is **not guaranteed**.

---

# Why was Finalization introduced?

When Java was first released, developers needed a way to clean resources like:

* files
* sockets
* database connections
* native resources

The idea was:

```text
Object becomes unused

↓

Garbage Collector runs

↓

finalize()

↓

Object removed
```

At that time, it looked like a good solution.

---

# What is the problem?

The biggest problem is:

**Nobody knows when `finalize()` will run.**

It depends on the Garbage Collector.

It may run:

* immediately
* after 5 minutes
* after 1 hour
* never before the program exits

Your program cannot control it.

---

# Example

```java
public class Demo {

    @Override
    protected void finalize() throws Throwable {

        System.out.println("Finalizer called");
    }

    public static void main(String[] args) {

        Demo demo = new Demo();

        demo = null;

        System.gc();

        System.out.println("Program Finished");
    }
}
```

Possible Output

```text
Program Finished
Finalizer called
```

Another run

```text
Program Finished
```

No finalizer at all.

Both are valid.

---

# Why?

Because

```text
System.gc()
```

is only a **request**.

Java is free to ignore it.

Even if GC runs, it may still decide not to execute `finalize()` immediately.

---

# Banking Example

Suppose a banking application opens a database connection.

```java
Connection connection = getConnection();
```

Developer writes

```java
@Override
protected void finalize() {

    connection.close();
}
```

Imagine

10,000 customers connect.

Since `finalize()` doesn't run immediately,

all 10,000 database connections stay open.

Eventually

```text
Database Connection Pool Full
```

New users cannot log in.

Production outage.

---

# Another Banking Example

Suppose

A file is opened.

```java
FileInputStream stream =
        new FileInputStream("customers.csv");
```

Developer expects

```java
finalize(){}
```

to close it.

If GC doesn't run,

the file remains open.

Now another process tries to update the file.

Result

```text
File is locked
```

Production issue.

---

# Why is Finalization Bad?

## 1. Unpredictable

Nobody knows when it runs.

---

## 2. Poor Performance

Objects with `finalize()` require extra work from the Garbage Collector.

GC becomes slower.

---

## 3. Resource Leaks

Resources remain open longer than expected.

Examples

* database connections
* files
* sockets

---

## 4. Security Problems

Malicious code could abuse finalization to "resurrect" objects that should have been destroyed.

This created security risks.

---

## 5. Exceptions are Ignored

If

```java
protected void finalize() {

    throw new RuntimeException();
}
```

Java ignores the exception.

No compile error.

No application failure.

Debugging becomes difficult.

---

## 6. Not Guaranteed

Java specification never guarantees that `finalize()` will execute.

Your cleanup code may never run.

---

# What happened in Java 18?

Java 18 officially marked Finalization as

> **Deprecated for Removal**

Meaning

Java recommends

**Do not use it.**

It may disappear in a future Java release.

---

# What should we use instead?

## 1. try-with-resources (Recommended)

Best solution.

```
try (FileInputStream stream =
        new FileInputStream("customers.csv")) {

    // use file

}
```

When the block finishes,

Java automatically closes the resource.

No GC needed.

---

## 2. AutoCloseable

Create classes that implement

```
AutoCloseable
```

Example

```java
public class CustomerService
        implements AutoCloseable {

    @Override
    public void close() {

        System.out.println("Closed");
    }
}
```

Now

```
try(CustomerService service =
        new CustomerService()) {

}
```

Java automatically calls

```
close()
```

---

## 3. Cleaner API

Java provides

```
java.lang.ref.Cleaner
```

Cleaner is safer than Finalization.

Mostly used by

* JVM developers
* library developers
* native memory libraries

Normal application developers rarely need it.

---

# Before Java 18

Many old applications used

```
finalize()
```

for cleanup.

---

# Java 18+

Use

```text
try-with-resources
```

instead.

---

# Comparison

| Finalize          | try-with-resources |
|-------------------|--------------------|
| May never execute | Always executes    |
| Depends on GC     | Runs immediately   |
| Slow              | Fast               |
| Unpredictable     | Predictable        |
| Deprecated        | Recommended        |

---

# Daily Developer Usage

Modern Spring Boot applications almost never use

```
finalize()
```

Instead they use

* try-with-resources
* AutoCloseable
* Spring lifecycle methods
* Connection pools
* Resource management frameworks

---

# Negative Cases / Common Mistakes

## 1. Closing database connections in finalize()

Wrong.

Close them immediately.

---

## 2. Closing files in finalize()

Wrong.

Use

```
try-with-resources
```

---

## 3. Calling `System.gc()` expecting finalize()

Wrong.

GC is not guaranteed.

---

## 4. Writing important business logic inside finalize()

Very dangerous.

Business logic should never depend on Garbage Collection.

---

# Best Practices

✅ Never override `finalize()` in new applications.

✅ Always use `try-with-resources` for files and streams.

✅ Implement `AutoCloseable` for custom resources.

✅ Close database connections immediately.

✅ Release resources explicitly.

✅ Let Garbage Collector manage only memory, not business resources.

---

# Quick Revision

* `finalize()` was used for object cleanup.
* Java never guaranteed when or whether it would run.
* It caused performance, reliability, and security problems.
* Java 18 deprecated Finalization for future removal.
* Use `try-with-resources` instead.
* Use `AutoCloseable` for custom resources.
* Use `Cleaner` only in advanced library scenarios.

---

# One-line Summary

Finalization (`finalize()`) was deprecated in Java 18 because it is unpredictable, slow, and unreliable for resource cleanup. Modern Java applications should use `try-with-resources` and `AutoCloseable` to release resources safely and immediately.
