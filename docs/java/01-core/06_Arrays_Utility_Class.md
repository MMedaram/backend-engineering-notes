---
title: Arrays Class
parent: Core Concepts
nav_order: 6
---

# Arrays Utility Class in Java

`java.util.Arrays` is a utility class for working with arrays.

In simple words, it helps us do common array operations without writing manual loops every time.

It is useful for:

- printing arrays
- sorting arrays
- searching arrays
- comparing arrays
- copying arrays
- filling arrays with default values
- converting arrays to list
- creating streams from arrays
- validating array differences

Arrays are fixed-size containers. Once an array is created, its length cannot change.

`Arrays` does not make arrays dynamic. It only gives helper methods to work with them.

---

## Common Imports Used in Examples

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;
import java.util.stream.Collectors;
```

---

## Version Timeline

| Java Version | What changed                                                                              |
|--------------|-------------------------------------------------------------------------------------------|
| Java 1.2     | `Arrays` utility class introduced                                                         |
| Java 5       | Many convenience methods became more useful with generics/autoboxing                      |
| Java 8       | Added streams, spliterators, `setAll`, `parallelSetAll`, `parallelSort`, `parallelPrefix` |
| Java 9       | Added `compare`, `compareUnsigned`, `mismatch`, and range-based comparison helpers        |

There are no preview features here. These are normal production APIs.

---

## Quick Method Map

| Method                                    | Daily Use                                      |
|-------------------------------------------|------------------------------------------------|
| `Arrays.toString(array)`                  | Print one-dimensional array values             |
| `Arrays.deepToString(array)`              | Print nested array values                      |
| `Arrays.equals(a, b)`                     | Compare one-dimensional arrays                 |
| `Arrays.deepEquals(a, b)`                 | Compare nested arrays                          |
| `Arrays.hashCode(array)`                  | Create hash code for one-dimensional array     |
| `Arrays.deepHashCode(array)`              | Create hash code for nested array              |
| `Arrays.sort(array)`                      | Sort array in ascending order                  |
| `Arrays.sort(array, comparator)`          | Sort object array using custom rule            |
| `Arrays.parallelSort(array)`              | Sort large arrays in parallel                  |
| `Arrays.binarySearch(array, key)`         | Search in sorted array                         |
| `Arrays.copyOf(array, newLength)`         | Copy array with new length                     |
| `Arrays.copyOfRange(array, from, to)`     | Copy part of array                             |
| `Arrays.fill(array, value)`               | Fill array with same value                     |
| `Arrays.asList(values)`                   | Convert object array/values to fixed-size list |
| `Arrays.stream(array)`                    | Convert array to stream                        |
| `Arrays.setAll(array, generator)`         | Set each element using its index               |
| `Arrays.parallelSetAll(array, generator)` | Set elements in parallel                       |
| `Arrays.parallelPrefix(array, operator)`  | Build running prefix values                    |
| `Arrays.compare(a, b)`                    | Java 9 lexicographic comparison                |
| `Arrays.mismatch(a, b)`                   | Java 9 first mismatch index                    |

---

# 1. Arrays.toString()

## Why do we need it?

If we print an array directly, Java does not print the values.

Bad:

```
int[] numbers = {10, 20, 30};

System.out.println(numbers);
```

Possible output:

```text
[I@5acf9800
```

This is not useful for developers.

It shows array type and memory-related identity text, not the actual values.

---

## Better way

```
int[] numbers = {10, 20, 30};

System.out.println(Arrays.toString(numbers));
```

Output:

```text
[10, 20, 30]
```

---

## Daily backend use case

```
String[] roles = {"ADMIN", "REPORT_VIEWER"};

log.info("User roles: {}", Arrays.toString(roles));
```

Useful for:

- debugging
- logs
- test output
- simple audit messages

---

## Edge cases

| Code                                        | Result      |
|---------------------------------------------|-------------|
| `Arrays.toString(new int[] {})`             | `[]`        |
| `Arrays.toString((int[]) null)`             | `"null"`    |
| `Arrays.toString(new String[] {"A", null})` | `[A, null]` |

Important:

`Arrays.toString()` is good for one-dimensional arrays.

For nested arrays, use `Arrays.deepToString()`.

---

# 2. Arrays.deepToString()

## Problem with nested arrays

```
int[][] matrix = {
        {1, 2},
        {3, 4}
};

System.out.println(Arrays.toString(matrix));
```

Possible output:

```text
[[I@36baf30c, [I@7a81197d]
```

This still does not show nested values properly.

---

## Better way

```
int[][] matrix = {
        {1, 2},
        {3, 4}
};

System.out.println(Arrays.deepToString(matrix));
```

Output:

```text
[[1, 2], [3, 4]]
```

---

## Edge cases

| Code                        | Result                              |
|-----------------------------|-------------------------------------|
| `Arrays.deepToString(null)` | `"null"`                            |
| Nested array contains null  | Prints `null` at that position      |
| Self-referencing array      | Shows `[...]` to avoid endless loop |

Best practice:

Use `deepToString()` only when the array can contain arrays inside it.

---

# 3. Arrays.equals()

## Why do we need it?

Using `==` with arrays checks whether both variables point to the same array object.

It does not compare values.

Bad:

```
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

System.out.println(a == b);
```

Output:

```text
false
```

Reason:

`a` and `b` are two different array objects.

---

## Better way

```
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

System.out.println(Arrays.equals(a, b));
```

Output:

```text
true
```

---

## Daily backend use case

```
byte[] oldChecksum = fileService.calculateChecksum(oldFile);
byte[] newChecksum = fileService.calculateChecksum(newFile);

if (Arrays.equals(oldChecksum, newChecksum)) {
    System.out.println("File content is same");
}
```

Useful for:

- checksum comparison
- byte array comparison
- test assertions
- small config array comparison

---

## Edge cases

| Case                         | Result  |
|------------------------------|---------|
| Both arrays are null         | `true`  |
| One array is null            | `false` |
| Same values, same order      | `true`  |
| Same values, different order | `false` |
| Different length             | `false` |

Example:

```
int[] a = {1, 2, 3};
int[] b = {3, 2, 1};

System.out.println(Arrays.equals(a, b));
```

Output:

```text
false
```

Order matters.

---

# 4. Arrays.deepEquals()

## Why do we need it?

`Arrays.equals()` is not enough for nested arrays.

```
int[][] first = {
        {1, 2},
        {3, 4}
};

int[][] second = {
        {1, 2},
        {3, 4}
};

System.out.println(Arrays.equals(first, second));
```

Output:

```text
false
```

Reason:

The outer array values are inner array references.

---

## Better way

```
System.out.println(Arrays.deepEquals(first, second));
```

Output:

```text
true
```

---

## Edge cases

| Case                                   | Result  |
|----------------------------------------|---------|
| Both nested arrays are null            | `true`  |
| One nested array is null               | `false` |
| Same nested values                     | `true`  |
| Same values but different nested order | `false` |

Best practice:

Use `deepEquals()` for nested arrays.

Use normal `Arrays.equals()` for normal one-dimensional arrays.

---

# 5. Arrays.hashCode() and deepHashCode()

## Why do we need it?

If an object has an array field, normal `Objects.hash(arrayField)` may not behave as expected because arrays use reference-based `hashCode()`.

Example:

```java
public class FileData {
    private byte[] content;

    @Override
    public int hashCode() {
        return Arrays.hashCode(content);
    }
}
```

This creates a hash code based on array values.

---

## deepHashCode()

Use `deepHashCode()` for nested arrays.

```
String[][] permissions = {
        {"READ", "WRITE"},
        {"EXPORT"}
};

System.out.println(Arrays.deepHashCode(permissions));
```

---

## Edge cases

| Code                            | Result                      |
|---------------------------------|-----------------------------|
| `Arrays.hashCode((int[]) null)` | `0`                         |
| Same values in same order       | Same hash code              |
| Same values in different order  | Different hash code usually |

Best practice:

If `equals()` uses `Arrays.equals(array)`, then `hashCode()` should use `Arrays.hashCode(array)`.

If `equals()` uses `Arrays.deepEquals(array)`, then `hashCode()` should use `Arrays.deepHashCode(array)`.

---

# 6. Arrays.sort()

## Why do we need it?

Sorting is one of the most common array operations.

```
int[] numbers = {30, 10, 20};

Arrays.sort(numbers);

System.out.println(Arrays.toString(numbers));
```

Output:

```text
[10, 20, 30]
```

Important:

`Arrays.sort()` modifies the original array.

It does not create a new sorted array.

---

## Sort strings

```
String[] names = {"Mohan", "Anil", "Kiran"};

Arrays.sort(names);

System.out.println(Arrays.toString(names));
```

Output:

```text
[Anil, Kiran, Mohan]
```

---

## Sort with custom rule

```
String[] names = {"Mohan", "Anil", "Kiran"};

Arrays.sort(names, Comparator.comparingInt(String::length));

System.out.println(Arrays.toString(names));
```

Output:

```text
[Anil, Mohan, Kiran]
```

Here, sorting is based on name length.

---

## Sort descending

```
Integer[] numbers = {30, 10, 20};

Arrays.sort(numbers, Comparator.reverseOrder());

System.out.println(Arrays.toString(numbers));
```

Output:

```text
[30, 20, 10]
```

Important:

For `Comparator.reverseOrder()`, use wrapper arrays like `Integer[]`, not primitive arrays like `int[]`.

---

## Edge cases

### 1. Null array

```
int[] numbers = null;

Arrays.sort(numbers);
```

Output:

```text
java.lang.NullPointerException
```

### 2. Object array with null values

```
String[] names = {"Mohan", null, "Anil"};

Arrays.sort(names);
```

Possible output:

```text
java.lang.NullPointerException
```

Better:

```
Arrays.sort(names, Comparator.nullsLast(String::compareTo));

System.out.println(Arrays.toString(names));
```

Output:

```text
[Anil, Mohan, null]
```

### 3. Mixed non-comparable objects

If objects do not implement `Comparable`, natural sorting can fail.

Possible exception:

```text
java.lang.ClassCastException
```

### 4. Bad comparator

If the comparator breaks comparison rules, sorting may fail.

Possible exception:

```text
java.lang.IllegalArgumentException: Comparison method violates its general contract
```

Best practice:

Always make comparator logic consistent and predictable.

---

# 7. Arrays.parallelSort()

`parallelSort()` was added in Java 8.

It sorts arrays using parallel processing.

```
int[] numbers = {50, 10, 40, 20, 30};

Arrays.parallelSort(numbers);

System.out.println(Arrays.toString(numbers));
```

Output:

```text
[10, 20, 30, 40, 50]
```

---

## When to use parallelSort()

Good cases:

- very large arrays
- CPU-heavy sorting
- server has enough CPU cores
- sorting is not already inside another heavy parallel workflow

Not useful for:

- small arrays
- request-response code where predictability matters more
- already busy production servers

Best practice:

Use normal `Arrays.sort()` by default.

Use `parallelSort()` only when you have measured that it helps.

---

# 8. Arrays.binarySearch()

## Why do we need it?

`binarySearch()` searches faster than a normal linear search.

But it works correctly only when the array is already sorted.

```
int[] numbers = {10, 20, 30, 40};

int index = Arrays.binarySearch(numbers, 30);

System.out.println(index);
```

Output:

```text
2
```

---

## Key not found

```
int[] numbers = {10, 20, 30, 40};

int index = Arrays.binarySearch(numbers, 25);

System.out.println(index);
```

Output:

```text
-3
```

Meaning:

```text
insertion point = 2
return value = -(insertion point) - 1
return value = -2 - 1 = -3
```

So `25` should be inserted at index `2` to keep the array sorted.

---

## How to calculate insertion point

```
int result = Arrays.binarySearch(numbers, 25);

if (result < 0) {
    int insertionPoint = -result - 1;
    System.out.println(insertionPoint);
}
```

Output:

```text
2
```

---

## Edge cases

| Case                   | Result                                        |
|------------------------|-----------------------------------------------|
| Array is not sorted    | Result is unpredictable/wrong                 |
| Key found              | Returns index                                 |
| Key not found          | Returns negative value                        |
| Duplicate values exist | No guarantee which matching index is returned |
| Null array             | `NullPointerException`                        |

Example of wrong usage:

```
int[] numbers = {30, 10, 20};

System.out.println(Arrays.binarySearch(numbers, 20));
```

The array is not sorted, so the result should not be trusted.

Best practice:

Sort first, then search.

```
Arrays.sort(numbers);
int index = Arrays.binarySearch(numbers, 20);
```

---

# 9. Arrays.copyOf()

## Why do we need it?

`copyOf()` creates a new array.

It is useful when:

- you want to resize-like copy
- you want to protect internal array state
- you want to return a copy instead of original array

```
int[] oldNumbers = {10, 20, 30};

int[] newNumbers = Arrays.copyOf(oldNumbers, 5);

System.out.println(Arrays.toString(newNumbers));
```

Output:

```text
[10, 20, 30, 0, 0]
```

New positions get default values.

---

## Default values by type

| Array Type    | Default value |
|---------------|---------------|
| `int[]`       | `0`           |
| `long[]`      | `0`           |
| `double[]`    | `0.0`         |
| `boolean[]`   | `false`       |
| `char[]`      | `\u0000`      |
| Object arrays | `null`        |

---

## Daily backend use case: defensive copy

```java
public class Report {
    private final byte[] content;

    public Report(byte[] content) {
        this.content = Arrays.copyOf(content, content.length);
    }

    public byte[] getContent() {
        return Arrays.copyOf(content, content.length);
    }
}
```

This prevents outside code from directly changing internal array data.

---

## Edge cases

| Case                   | Result                          |
|------------------------|---------------------------------|
| New length is bigger   | Extra values are default values |
| New length is smaller  | Array is truncated              |
| Original array is null | `NullPointerException`          |
| New length is negative | `NegativeArraySizeException`    |

---

# 10. Arrays.copyOfRange()

## Why do we need it?

`copyOfRange()` copies part of an array.

`from` is inclusive.

`to` is exclusive.

```
int[] numbers = {10, 20, 30, 40, 50};

int[] middle = Arrays.copyOfRange(numbers, 1, 4);

System.out.println(Arrays.toString(middle));
```

Output:

```text
[20, 30, 40]
```

Index `1` is included.

Index `4` is not included.

---

## Edge cases

### 1. To index can be greater than length

```
int[] numbers = {10, 20, 30};

int[] copy = Arrays.copyOfRange(numbers, 1, 5);

System.out.println(Arrays.toString(copy));
```

Output:

```text
[20, 30, 0, 0]
```

### 2. From index greater than to index

```
Arrays.copyOfRange(numbers, 3, 1);
```

Output:

```text
java.lang.IllegalArgumentException
```

### 3. From index outside original length

```
Arrays.copyOfRange(numbers, 5, 6);
```

Output:

```text
java.lang.ArrayIndexOutOfBoundsException
```

### 4. Negative from index

```
Arrays.copyOfRange(numbers, -1, 2);
```

Output:

```text
java.lang.ArrayIndexOutOfBoundsException
```

---

# 11. Arrays.fill()

## Why do we need it?

`fill()` assigns the same value to all positions.

```
int[] scores = new int[5];

Arrays.fill(scores, -1);

System.out.println(Arrays.toString(scores));
```

Output:

```text
[-1, -1, -1, -1, -1]
```

---

## Fill a range

```
int[] scores = {0, 0, 0, 0, 0};

Arrays.fill(scores, 1, 4, 9);

System.out.println(Arrays.toString(scores));
```

Output:

```text
[0, 9, 9, 9, 0]
```

`fromIndex` is inclusive.

`toIndex` is exclusive.

---

## Daily backend use case

```
boolean[] processed = new boolean[10];

Arrays.fill(processed, false);
```

Useful for:

- default statuses
- reset logic
- test setup
- initializing buffers

---

## Edge cases

| Case                  | Result                           |
|-----------------------|----------------------------------|
| Array is null         | `NullPointerException`           |
| `fromIndex > toIndex` | `IllegalArgumentException`       |
| Index out of range    | `ArrayIndexOutOfBoundsException` |

---

## Important object-array warning

When you fill an object array, the same object reference is placed in all positions.

```
StringBuilder builder = new StringBuilder("A");

StringBuilder[] values = new StringBuilder[3];
Arrays.fill(values, builder);

values[0].append("1");

System.out.println(values[1]);
```

Output:

```text
A1
```

Reason:

All positions point to the same `StringBuilder` object.

Best practice:

For mutable objects, create separate objects for each index.

---

# 12. Arrays.asList()

## Why do we need it?

`asList()` converts values or an object array into a list view.

```
List<String> names = Arrays.asList("Mohan", "Anil", "Kiran");

System.out.println(names);
```

Output:

```text
[Mohan, Anil, Kiran]
```

---

## Important: fixed-size list

The returned list is fixed-size.

You can update existing positions:

```
List<String> names = Arrays.asList("Mohan", "Anil");

names.set(0, "Ravi");

System.out.println(names);
```

Output:

```text
[Ravi, Anil]
```

But you cannot add or remove elements:

```
names.add("Kiran");
```

Output:

```text
java.lang.UnsupportedOperationException
```

---

## Backed by original array

```
String[] namesArray = {"Mohan", "Anil"};

List<String> names = Arrays.asList(namesArray);

names.set(0, "Ravi");

System.out.println(Arrays.toString(namesArray));
```

Output:

```text
[Ravi, Anil]
```

Changing the list changes the array.

Changing the array also changes the list.

---

## How to create a normal mutable list

```
List<String> names = new ArrayList<>(Arrays.asList("Mohan", "Anil"));

names.add("Kiran");

System.out.println(names);
```

Output:

```text
[Mohan, Anil, Kiran]
```

---

## Primitive array trap

```
int[] numbers = {1, 2, 3};

List<int[]> list = Arrays.asList(numbers);

System.out.println(list.size());
```

Output:

```text
1
```

Reason:

`int[]` is treated as one object.

It does not become `List<Integer>`.

Better in Java 8:

```
int[] numbers = {1, 2, 3};

List<Integer> list = Arrays.stream(numbers)
        .boxed()
        .collect(Collectors.toList());

System.out.println(list);
```

Output:

```text
[1, 2, 3]
```

---

## Edge cases

| Case                             | Result                          |
|----------------------------------|---------------------------------|
| `Arrays.asList("A", null)`       | List with null value            |
| `Arrays.asList((String) null)`   | List with one null value        |
| `Arrays.asList((String[]) null)` | `NullPointerException`          |
| Add/remove on returned list      | `UnsupportedOperationException` |

Best practice:

Use `Arrays.asList()` when you need a quick fixed-size list.

Use `new ArrayList<>(Arrays.asList(...))` when you need to add or remove values.

---

# 13. Arrays.stream()

`stream()` was added in Java 8.

It converts an array into a stream.

```
String[] names = {"Mohan", "Anil", "Kiran"};

List<String> result = Arrays.stream(names)
        .filter(name -> name.startsWith("M"))
        .collect(Collectors.toList());

System.out.println(result);
```

Output:

```text
[Mohan]
```

---

## Primitive streams

```
int[] numbers = {10, 20, 30};

int sum = Arrays.stream(numbers)
        .sum();

System.out.println(sum);
```

Output:

```text
60
```

For primitive arrays:

| Array      | Stream Type    |
|------------|----------------|
| `int[]`    | `IntStream`    |
| `long[]`   | `LongStream`   |
| `double[]` | `DoubleStream` |

For object arrays:

```java
String[] names = {"A", "B"};
```

Result is:

```text
Stream<String>
```

---

## Stream a range

```
int[] numbers = {10, 20, 30, 40, 50};

int sum = Arrays.stream(numbers, 1, 4)
        .sum();

System.out.println(sum);
```

Output:

```text
90
```

Indexes used:

```text
1, 2, 3
```

Values used:

```text
20, 30, 40
```

---

## Edge cases

| Case                            | Result                           |
|---------------------------------|----------------------------------|
| Array is null                   | `NullPointerException`           |
| `startInclusive > endExclusive` | `ArrayIndexOutOfBoundsException` |
| Index out of range              | `ArrayIndexOutOfBoundsException` |
| Empty array                     | Empty stream                     |

Best practice:

Use `Arrays.stream()` when you want filtering, mapping, grouping, or simple calculations.

Do not use streams just to make one simple array access.

---

# 14. Arrays.setAll()

`setAll()` was added in Java 8.

It fills an array by using each index.

```
int[] numbers = new int[5];

Arrays.setAll(numbers, index -> index * 10);

System.out.println(Arrays.toString(numbers));
```

Output:

```text
[0, 10, 20, 30, 40]
```

---

## Daily backend use case

```
String[] labels = new String[3];

Arrays.setAll(labels, index -> "column-" + index);

System.out.println(Arrays.toString(labels));
```

Output:

```text
[column-0, column-1, column-2]
```

---

## Edge cases

| Case                       | Result                         |
|----------------------------|--------------------------------|
| Array is null              | `NullPointerException`         |
| Generator is null          | `NullPointerException`         |
| Generator throws exception | Array may be partially updated |

Example:

```
int[] numbers = new int[5];

Arrays.setAll(numbers, index -> {
    if (index == 3) {
        throw new RuntimeException("Failed at index 3");
    }
    return index;
});
```

Indexes `0`, `1`, and `2` may already be updated before the exception.

---

# 15. Arrays.parallelSetAll()

`parallelSetAll()` was added in Java 8.

It is like `setAll()`, but Java may calculate values in parallel.

```
int[] squares = new int[5];

Arrays.parallelSetAll(squares, index -> index * index);

System.out.println(Arrays.toString(squares));
```

Output:

```text
[0, 1, 4, 9, 16]
```

Best practice:

Use `setAll()` by default.

Use `parallelSetAll()` only for large arrays and expensive calculations.

Keep the generator independent for each index.

Bad:

```
int[] numbers = new int[100];
int[] counter = {0};

Arrays.parallelSetAll(numbers, index -> counter[0]++);
```

This is unsafe because multiple threads may update `counter` at the same time.

---

# 16. Arrays.parallelPrefix()

`parallelPrefix()` was added in Java 8.

It calculates running results.

Example:

```
int[] numbers = {1, 2, 3, 4};

Arrays.parallelPrefix(numbers, Integer::sum);

System.out.println(Arrays.toString(numbers));
```

Output:

```text
[1, 3, 6, 10]
```

Meaning:

```text
1
1 + 2 = 3
1 + 2 + 3 = 6
1 + 2 + 3 + 4 = 10
```

---

## Daily use case

Useful for running totals:

- cumulative amount
- cumulative count
- prefix sums
- report totals

---

## Edge cases

| Case                      | Result                         |
|---------------------------|--------------------------------|
| Array is null             | `NullPointerException`         |
| Operator is null          | `NullPointerException`         |
| Empty array               | No change                      |
| Operator throws exception | Array may be partially updated |

Best practice:

The operator should be associative.

Good:

```
Integer::sum
```

Risky:

```
(a, b) -> a - b
```

Reason:

Parallel calculation may group operations differently. Non-associative operations can give confusing results.

---

# 17. Java 9: Arrays.compare()

`compare()` compares two arrays lexicographically.

In simple words, it compares arrays like dictionary order:

1. Compare first element.
2. If same, compare second element.
3. Continue until a difference is found.
4. If all common values are same, shorter array is smaller.

```
int[] first = {1, 2, 3};
int[] second = {1, 2, 4};

System.out.println(Arrays.compare(first, second));
```

Output:

```text
-1
```

Reason:

At index `2`, `3` is smaller than `4`.

---

## Compare equal arrays

```
int[] first = {1, 2, 3};
int[] second = {1, 2, 3};

System.out.println(Arrays.compare(first, second));
```

Output:

```text
0
```

---

## Null behavior

```
int[] first = null;
int[] second = {1, 2};

System.out.println(Arrays.compare(first, second));
```

Output:

```text
Negative value
```

Java treats null array as smaller than non-null array.

Two null arrays are treated as equal.

---

## Edge cases

| Case                              | Result         |
|-----------------------------------|----------------|
| Same values                       | `0`            |
| First array smaller               | Negative value |
| First array greater               | Positive value |
| First array null, second non-null | Negative value |
| Both arrays null                  | `0`            |

Best practice:

Use `compare()` when you really need array ordering.

Use `equals()` when you only need to know same or not same.

---

## Java 9: compareUnsigned()

`compareUnsigned()` is available for numeric primitive arrays like `byte[]`, `short[]`, `int[]`, and `long[]`.

It compares values as unsigned numbers.

This is useful in low-level cases like:

- binary data
- network packets
- file bytes
- protocol parsing

For normal business numbers, use `compare()`.

---

## Object array null element behavior

For object arrays using natural comparison:

```
String[] first = {null, "B"};
String[] second = {"A", "B"};

System.out.println(Arrays.compare(first, second));
```

Output:

```text
Negative value
```

Reason:

Java treats a null array element as smaller than a non-null array element.

If you use the comparator-based overload, the comparator must know how to handle null values.

Example:

```
Arrays.compare(first, second, Comparator.nullsFirst(String::compareTo));
```

---

# 18. Java 9: Arrays.mismatch()

`mismatch()` returns the first index where two arrays are different.

```
int[] first = {10, 20, 30, 40};
int[] second = {10, 20, 99, 40};

int index = Arrays.mismatch(first, second);

System.out.println(index);
```

Output:

```text
2
```

Index `2` is the first mismatch.

---

## Equal arrays

```
int[] first = {10, 20};
int[] second = {10, 20};

System.out.println(Arrays.mismatch(first, second));
```

Output:

```text
-1
```

`-1` means no mismatch.

---

## Different lengths

```
int[] first = {10, 20};
int[] second = {10, 20, 30};

System.out.println(Arrays.mismatch(first, second));
```

Output:

```text
2
```

Reason:

First two values are same, but one array ends at index `2`.

---

## Edge cases

| Case                             | Result                    |
|----------------------------------|---------------------------|
| Arrays are equal                 | `-1`                      |
| First different value found      | Index of first difference |
| Same prefix but different length | Length of smaller array   |
| One array is null                | `NullPointerException`    |
| Both arrays are null             | `NullPointerException`    |

Daily use case:

```
int mismatchIndex = Arrays.mismatch(expectedBytes, actualBytes);

if (mismatchIndex >= 0) {
    log.warn("File mismatch at index {}", mismatchIndex);
}
```

---

# 19. Java 9: Range-Based equals(), compare(), mismatch()

Java 9 also added range-based methods.

Example:

```
int[] first = {10, 20, 30, 40, 50};
int[] second = {99, 20, 30, 88};

boolean sameMiddle = Arrays.equals(first, 1, 3, second, 1, 3);

System.out.println(sameMiddle);
```

Output:

```text
true
```

Compared ranges:

```text
first[1], first[2]   -> 20, 30
second[1], second[2] -> 20, 30
```

---

## Edge cases

| Case                             | Result                           |
|----------------------------------|----------------------------------|
| From index greater than to index | `IllegalArgumentException`       |
| Index outside array length       | `ArrayIndexOutOfBoundsException` |
| Array is null                    | `NullPointerException`           |

Best practice:

Use range-based methods when you only care about a part of an array.

This is useful in:

- byte buffer checks
- file chunk comparison
- protocol parsing
- low-level utility code

---

# Daily Coding Examples

## 1. Print request IDs for debugging

```
String[] requestIds = {"REQ-1", "REQ-2"};

log.info("Request ids: {}", Arrays.toString(requestIds));
```

## 2. Compare byte arrays

```java
boolean sameContent = Arrays.equals(oldBytes, newBytes);
```

## 3. Sort values before binary search

```
int[] allowedCodes = {400, 200, 500};

Arrays.sort(allowedCodes);

int index = Arrays.binarySearch(allowedCodes, 200);
```

## 4. Defensive copy in immutable class

```java
public byte[] getContent() {
    return Arrays.copyOf(content, content.length);
}
```

## 5. Convert primitive array to list

```java
List<Integer> ids = Arrays.stream(idArray)
        .boxed()
        .collect(Collectors.toList());
```

## 6. Create default values by index

```
String[] columns = new String[5];

Arrays.setAll(columns, index -> "column_" + index);
```

## 7. Find where two arrays differ

```java
int mismatch = Arrays.mismatch(expected, actual);
```

---

# When Not to Use Arrays

## 1. Do not use arrays when size changes often

Arrays have fixed length.

If you need frequent add/remove operations, use:

```java
List<String> names = new ArrayList<>();
```

---

## 2. Do not use Arrays.asList() when you need add/remove

Bad:

```
List<String> names = Arrays.asList("A", "B");
names.add("C");
```

Output:

```text
java.lang.UnsupportedOperationException
```

Better:

```
List<String> names = new ArrayList<>(Arrays.asList("A", "B"));
names.add("C");
```

---

## 3. Do not use binarySearch() on unsorted arrays

Bad:

```
int[] numbers = {30, 10, 20};

Arrays.binarySearch(numbers, 20);
```

Better:

```
Arrays.sort(numbers);
Arrays.binarySearch(numbers, 20);
```

---

## 4. Do not expose internal arrays directly

Bad:

```java
public byte[] getContent() {
    return content;
}
```

Better:

```java
public byte[] getContent() {
    return Arrays.copyOf(content, content.length);
}
```

---

# Best Practices

1. Use `Arrays.toString()` for one-dimensional array logging.
2. Use `Arrays.deepToString()` for nested arrays.
3. Use `Arrays.equals()` for array value comparison.
4. Use `Arrays.deepEquals()` for nested array comparison.
5. Use `Arrays.hashCode()` with `Arrays.equals()`.
6. Use `Arrays.deepHashCode()` with `Arrays.deepEquals()`.
7. Remember that `Arrays.sort()` changes the original array.
8. Sort before using `Arrays.binarySearch()`.
9. Use `Arrays.copyOf()` for defensive copies.
10. Be careful with `Arrays.asList()` because it returns a fixed-size list.
11. Use `Arrays.stream()` for Java 8 stream operations.
12. Use parallel methods only after checking that they help.
13. Use Java 9 `mismatch()` when you need the exact first difference.
14. Use Java 9 `compare()` when arrays need ordering.

---

# Interview One-Liner

`java.util.Arrays` is a utility class for common array operations like printing, sorting, searching, comparing, copying, filling, converting to list/stream, and Java 9 comparison/mismatch checks.

---

# Summary

1. `Arrays` helps avoid manual loops for common array tasks.
2. Arrays are fixed-size; `Arrays` does not make them dynamic.
3. `toString`, `equals`, `sort`, `binarySearch`, `copyOf`, `fill`, and `asList` are very common in daily coding.
4. Java 8 added stream and parallel array helpers.
5. Java 9 added better array comparison and mismatch helpers.
6. Be careful with null arrays, fixed-size lists, primitive arrays, and methods that modify the original array.

