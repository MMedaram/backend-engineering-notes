---
title: MissingNumberInArray 
parent: Array
nav_order: 3
---

# 1️. missingNumber(int[] arr)

✔ Example:

- input :[1,2,4,5] 
- Result → missing = 3

# 2️. missingNumbersInAnArray(int[] arr)

find multiple missing numbers


```java

package exercise.arrays;

import java.util.Arrays;
import java.util.List;
import java.util.Set;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

public class MissingNumberInArray {

    public static void main(String[] args) {

        int[] arr = {1, 2, 4, 5, 9, 7, 8, 6};

        // Case 1: Find single missing number (assumes 1..n sequence)
        missingNumber(arr);

        int[] arr2 = {1,6,3,8,9,0};
        // Case 2: Find all missing numbers in range [min, max]
        missingNumbersInAnArray(arr2);
    }

    // Finds ONE missing number (works only if numbers are from 1 to n and exactly one is missing)
    public static void missingNumber(int[] arr) {

        // Total numbers should be length + 1 (since one number is missing)
        int n = arr.length + 1;

        // Sum of first n natural numbers → n * (n + 1) / 2
        int expectedSum = (n * (n + 1)) / 2;

        // Sum of elements present in array
        int actualSum = Arrays.stream(arr).sum();

        // Missing number = expected sum - actual sum
        int missing = expectedSum - actualSum;

        System.out.println("Missing Number is = " + missing);
    }

    // Finds ALL missing numbers between min and max
    public static void missingNumbersInAnArray(int[] arr) {

        // Edge case: null or empty array
        if (arr == null || arr.length == 0) {
            System.out.println("Invalid input");
            return;
        }

        // Find minimum value in array
        int min = Arrays.stream(arr)
                .min()
                .orElseThrow(() -> new IllegalArgumentException("Array is empty"));

        // Find maximum value in array
        int max = Arrays.stream(arr)
                .max()
                .orElseThrow(() -> new IllegalArgumentException("Array is empty"));

        // Convert array into Set for O(1) lookup
        Set<Integer> inputSet = Arrays.stream(arr)
                .boxed()   // convert int → Integer
                .collect(Collectors.toSet());

        // Generate full range from min → max
        // Filter numbers that are NOT present in inputSet
        List<Integer> missingNumbers = IntStream.rangeClosed(min, max)
                .filter(n -> !inputSet.contains(n)) // missing ones
                .boxed()  // convert int → Integer for List
                .toList();

        // Print result
        System.out.println("Missing Numbers: " + missingNumbers);
    }
}
```
