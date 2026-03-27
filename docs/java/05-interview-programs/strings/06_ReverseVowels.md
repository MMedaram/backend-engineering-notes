---
title: ReverseVowels
parent: Strings
nav_order: 6
---

# Reverse only vowels (a, e, i, o, u) & Keep consonants in the same position

## Example

```
Input:  "hello"
Output: "holle"
```

```java
package exercise.strings;

import java.util.Set;

public class ReverseVowels {

    public static String reverseVowels(String s) {

        // Edge case: if string is null or too short, no reversal needed
        if (s == null || s.length() < 2) return s;

        // Define a set of vowels (both lowercase and uppercase for safety)
        Set<Character> vowels = Set.of(
                'a','e','i','o','u',
                'A','E','I','O','U'
        );

        // Convert string to character array (since String is immutable)
        char[] arr = s.toCharArray();

        // Initialize two pointers: one from start, one from end
        int left = 0, right = arr.length - 1;

        // Loop until both pointers meet
        while (left < right) {

            // If left character is NOT a vowel → move left pointer forward
            if (!vowels.contains(arr[left])) {
                left++;

                // If right character is NOT a vowel → move right pointer backward
            } else if (!vowels.contains(arr[right])) {
                right--;

            } else {
                // Both characters are vowels → swap them

                char temp = arr[left];     // store left vowel
                arr[left] = arr[right];   // replace left with right vowel
                arr[right] = temp;        // replace right with stored left vowel

                // Move both pointers inward after swap
                left++;
                right--;
            }
        }

        // Convert modified char array back to String and return
        return new String(arr);
    }

    public static void main(String[] args) {
        System.out.println(reverseVowels("hello"));    // holle
        System.out.println(reverseVowels("Medarametla Mohan Naidu")); // Mudiramatlo Mahen Naade
    }
}
```