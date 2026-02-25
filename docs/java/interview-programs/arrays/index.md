---
title: Array
parent: Programs – Java
nav_order: 1
---

# Array 

An array is a **fixed-size**, **indexed** collection of elements of the **same data type**.

```java
int[] arr = new int[5];
int[] nums = {1, 2, 3, 4};
```

- Stored in contiguous memory
- Index-based access (0-based)
- Length is fixed once created

## 🧠 Key Characteristics

- Fast access: O(1)
- Fixed size (cannot grow/shrink)
- Can store primitives or objects
- Supports single-dimensional & multi-dimensional arrays

## 📏 Array Length    

> arr.length

⚠️ length is a property, not a method.

## 🔁 Traversing an Array

#### Classic For Loop

```
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
```

#### Enhanced For Loop

```
for (int num : arr) {
    System.out.println(num);
}
```


## ⚙️ Common Arrays Utility Methods (java.util.Arrays)

```text
Arrays.sort(arr)
Arrays.binarySearch(arr, key) // Works only on sorted arrays :: Time: O(log n)
Arrays.equals(arr1, arr2)
Arrays.copyOf(arr, newLength)
Arrays.copyOfRange(arr, from, to)
Arrays.fill(arr, value)
Arrays.toString(arr)
```

📌 These are frequently asked in interviews.    

## 🧩 Java 8+ Array Enhancements

#### Streams from Arrays

> Arrays.stream(arr)

##### Examples:

```java

int sum = Arrays.stream(arr).sum();

int max = Arrays.stream(arr).max().orElse(0);

long count = Arrays.stream(arr)
                   .filter(n -> n == 0)
                   .count();

```

⚠️ Streams are not in-place.

## 🔄 Converting Array ↔ List

### Array to List

> List<Integer> list = Arrays.asList(1, 2, 3);

⚠️ Fixed-size list (cannot add/remove).

### List to Array

Integer[] arr = list.toArray(new Integer[0]);


