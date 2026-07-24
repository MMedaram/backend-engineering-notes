---
title: NIO and XML API Improvements
parent: Java-13
nav_order: 3
---

# Java 13 - NIO and XML API Improvements

Java 13 added a few smaller API improvements that are useful for backend developers in specific situations.

Main topics:

1. `FileSystems.newFileSystem(Path, ...)`
2. Absolute bulk `ByteBuffer` get/put methods
3. DOM/SAX namespace-aware factory methods

These are not as famous as text blocks, but they are useful when working with files, ZIP/JAR data, buffers, or XML.

---

# 1. FileSystems.newFileSystem(Path, ...)

## What does it do?

Java can treat some files as file systems.

Common example:

```text
.zip
.jar
```

Java 13 added more convenient `FileSystems.newFileSystem(...)` overloads that accept a `Path`.

This makes it easier to open a ZIP/JAR file and read files inside it using normal `Path` APIs.

---

## Daily backend use case: read file inside ZIP

```
Path zipPath = Path.of("reports.zip");

try (FileSystem zipFs = FileSystems.newFileSystem(zipPath)) {
    Path reportPath = zipFs.getPath("/monthly/report.csv");
    List<String> lines = Files.readAllLines(reportPath);

    lines.forEach(System.out::println);
}
```

Useful for:

- reading uploaded ZIP files
- inspecting JAR contents
- processing packaged reports
- reading files from archive-like formats

---

## With environment map

```
Path zipPath = Path.of("reports.zip");
Map<String, String> env = Map.of("create", "true");

try (FileSystem zipFs = FileSystems.newFileSystem(zipPath, env)) {
    Path reportPath = zipFs.getPath("/report.txt");
    Files.writeString(reportPath, "Generated report");
}
```

`create=true` tells the ZIP file system provider to create the file system if needed.

---

## Important edge case: ambiguous null

Java 13 added a new overload:

```
newFileSystem(Path, Map<String, ?>)
```

Because another overload already existed:

```
newFileSystem(Path, ClassLoader)
```

This can make old code ambiguous:

```java
FileSystem fs = FileSystems.newFileSystem(path, null);
```

Possible compile error:

```text
reference to newFileSystem is ambiguous
```

Fix by casting:

```java
FileSystem fs = FileSystems.newFileSystem(path, (ClassLoader) null);
```

or:

```java
FileSystem fs = FileSystems.newFileSystem(path, Map.of());
```

---

## Other edge cases

| Case                              | Result                                        |
|-----------------------------------|-----------------------------------------------|
| Path is null                      | `NullPointerException`                        |
| Provider not found                | `ProviderNotFoundException`                   |
| File cannot be opened             | `IOException`                                 |
| ZIP file already open in same JVM | `FileSystemAlreadyExistsException` may happen |
| File system is not closed         | Resource leak                                 |

Best practice:

Always use try-with-resources.

---

# 2. Absolute Bulk ByteBuffer get/put Methods

## What changed?

Java 13 added absolute bulk `get` and `put` methods to `ByteBuffer` and related buffer classes.

Simple meaning:

You can copy multiple values from/to a buffer starting at a given index without changing the buffer's current position.

---

## Why is this useful?

Old buffer code often depends on `position()`.

If one helper method changes the position by mistake, later code can break.

Absolute methods are safer for read-only inspection and fixed-offset parsing.

---

## Example: read bytes without changing position

```
ByteBuffer buffer = ByteBuffer.wrap(new byte[] {10, 20, 30, 40, 50});
byte[] target = new byte[3];

buffer.get(1, target);

System.out.println(Arrays.toString(target));
System.out.println(buffer.position());
```

Output:

```text
[20, 30, 40]
0
```

The buffer position stays unchanged.

---

## Example: write bytes without changing position

```
ByteBuffer buffer = ByteBuffer.allocate(5);
byte[] source = new byte[] {7, 8, 9};

buffer.put(1, source);

byte[] all = buffer.array();
System.out.println(Arrays.toString(all));
System.out.println(buffer.position());
```

Output:

```text
[0, 7, 8, 9, 0]
0
```

---

## Daily backend use case

Useful when working with:

- binary protocols
- file headers
- network packet parsing
- custom serialization
- compression/encryption buffers

Most normal CRUD backend code will not need this often.

But it is good to know if you work near IO internals.

---

## Edge cases

| Case                             | Result                                  |
|----------------------------------|-----------------------------------------|
| Negative index                   | `IndexOutOfBoundsException`             |
| Read/write crosses buffer limit  | `IndexOutOfBoundsException`             |
| Destination/source array is null | `NullPointerException`                  |
| Read-only buffer write           | `ReadOnlyBufferException`               |
| Buffer position                  | Not changed by absolute bulk operations |

---

# 3. DOM/SAX Namespace-Aware Factory Methods

## What changed?

Java 13 added factory methods for creating DOM and SAX parser factories with namespace support enabled by default.

Examples:

```
DocumentBuilderFactory.newDefaultNSInstance()
SAXParserFactory.newDefaultNSInstance()
```

`NS` means:

```text
NamespaceAware
```

---

## Why is this useful?

Before Java 13:

```
DocumentBuilderFactory factory = DocumentBuilderFactory.newDefaultInstance();
factory.setNamespaceAware(true);
DocumentBuilder builder = factory.newDocumentBuilder();
```

Java 13:

```java
DocumentBuilder builder = DocumentBuilderFactory
        .newDefaultNSInstance()
        .newDocumentBuilder();
```

Cleaner and harder to forget namespace awareness.

---

## Daily backend use case: XML with namespaces

```java
DocumentBuilder builder = DocumentBuilderFactory
        .newDefaultNSInstance()
        .newDocumentBuilder();

Document document = builder.parse(xmlFile);
```

Useful for:

- SOAP integrations
- XML feeds
- payment gateway XML
- enterprise file exchange
- government/banking integrations

---

## Edge cases and exceptions

| Case                           | Possible Exception                           |
|--------------------------------|----------------------------------------------|
| Parser cannot be configured    | `ParserConfigurationException`               |
| XML file invalid               | `SAXException`                               |
| File cannot be read            | `IOException`                                |
| Custom factory class not found | `FactoryConfigurationError` or related error |

Best practice:

For XML from outside systems, also configure secure XML parsing settings to avoid XML External Entity issues.

---

## Best practices

1. Use `FileSystems.newFileSystem(Path)` when reading ZIP/JAR content as paths.
2. Always close a `FileSystem` with try-with-resources.
3. Be careful with `newFileSystem(path, null)` because Java 13 can make it ambiguous.
4. Use absolute buffer methods when you do not want to change buffer position.
5. Use namespace-aware XML factory methods when parsing XML with namespaces.
6. For XML from users or partners, also think about secure parser settings.

---

## Interview one-liner

Java 13 added smaller NIO and XML conveniences: easier `FileSystems.newFileSystem(Path, ...)` overloads, absolute bulk buffer get/put methods that do not change buffer position, and namespace-aware DOM/SAX factory methods.

