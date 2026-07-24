---
title: HexFormat
parent: Java-17
nav_order: 2
---

# Java 17 - HexFormat Notes

## What is HexFormat?

`HexFormat` is a Java 17 utility class used to **convert bytes to hex text** and **hex text back to bytes**.

Hex text means numbers and letters like:

`0A`, `1F`, `FF`, `4B`

It is useful when you want to show binary data in a readable form.

---

## Why do we need it?

Computer data is often stored as bytes.

Bytes are not easy for humans to read.

Example:

```java
byte[] data = {10, 20, 30};
```

If you print this directly, it is not useful for debugging or logs.

`HexFormat` helps convert it into a clean hex string.

---

## Basic Use

```
HexFormat hexFormat = HexFormat.of();

byte[] bytes = {10, 20, 30};

String hex = hexFormat.formatHex(bytes);

System.out.println(hex);
```

Output:

```
0a141e
```

---

## Convert Hex String Back to Bytes

```java
HexFormat hexFormat = HexFormat.of();

byte[] bytes = hexFormat.parseHex("0a141e");
```

Now the hex text is converted back into bytes.

---

## Where is it used?

`HexFormat` is useful in:

* security code
* hashing
* encryption
* digital signatures
* token handling
* log debugging
* API troubleshooting
* transaction fingerprints

---

## Simple Banking Example

Suppose a bank stores a hash value.

Hash values are usually byte arrays.

```java
byte[] hash = messageDigest.digest(input.getBytes());
```

To store or display it clearly, you can use:

```java
HexFormat hexFormat = HexFormat.of();
String hashText = hexFormat.formatHex(hash);
```

This makes the hash readable.

---

## Important Point

`HexFormat` does **not** create security by itself.

It only converts data between:

* bytes
* hex string

So it is a **formatting tool**, not an encryption tool.

---

## Methods to Know

### `HexFormat.of()`

Creates a default hex formatter.

```java
HexFormat hexFormat = HexFormat.of();
```

---

### `formatHex(byte[])`

Converts bytes to hex text.

```java
String hex = hexFormat.formatHex(bytes);
```

---

### `parseHex(String)`

Converts hex text back to bytes.

```java
byte[] bytes = hexFormat.parseHex("0a141e");
```

---

## Optional Formatting

`HexFormat` can also add:

* prefix
* suffix
* delimiter
* uppercase output

Example:

```java
HexFormat hexFormat = HexFormat.ofDelimiter(":").withUpperCase();

String result = hexFormat.formatHex(new byte[]{10, 20, 30});
```

Possible output:

```
0A:14:1E
```

This is useful when you want a more readable style.

---

## Advantages

* easy to use
* built into Java
* cleaner than manual hex conversion
* good for debugging
* good for security-related logs
* readable output

---

## Limitations

* only for hex conversion
* does not encrypt data
* not useful for business logic directly
* not needed in every project

---

## Negative Cases / Common Mistakes

### 1. Thinking HexFormat is encryption

Wrong.

It only changes the display format.

---

### 2. Using it for random numbers

Wrong use.

It is not for generating numbers.

---

### 3. Passing invalid hex text

Example:

```
hexFormat.parseHex("0xZZ");
```

This will fail because the text is not valid hex.

---

### 4. Using odd-length invalid input

Hex text must be correct and well-formed.

Bad input will cause an error.

---

## Exceptions

If the hex string is invalid, Java throws:

* `IllegalArgumentException`

This usually happens when:

* text has invalid characters
* text is not proper hex format
* text does not match the expected rules

---

## Better Approach

Use `HexFormat` when:

* you need to show bytes clearly
* you need to convert hash bytes to text
* you need to convert hex text back to bytes

Do not use it when:

* you are doing real encryption
* you need secure random values
* you just want a business ID

---

## Daily Developer View

As a developer, you should know:

* how to convert byte array to hex string
* how to convert hex string back to byte array
* where this is useful in logs and security
* that invalid hex text can throw an exception

That is enough for normal development.

---

## Quick Revision

* `HexFormat` is a Java 17 utility class
* It converts bytes to hex and hex to bytes
* It is useful for hashes, tokens, logs, and security data
* It is not encryption
* Main methods: `of()`, `formatHex()`, `parseHex()`
* Invalid input can throw `IllegalArgumentException`

---

## One-line Summary

`HexFormat` is a simple Java 17 helper class used to convert byte data into readable hex text and back again.
