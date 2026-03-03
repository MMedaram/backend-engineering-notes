---
title: Find First Repeating Char in a given String
parent: Strings
nav_order: 4
---

# Find First Repeating Char in a given String

```java
package exercise.strings;

import java.util.HashSet;
import java.util.Set;

public class FindFirstRepeatingChar {
    public static void main(String[] args) {

        String s = "MohanBabuNaidu";
        char c = findFirstRepeatingChar(s);
        System.out.println(c != '\0' ? "First Repeating Char: " + c : "No repeating char");

        char c1 = findFirstRepeatingCharUsingStreams("Mohan");
        System.out.println(c1 != '\0' ? "First Repeating Char: " + c1 : "No repeating char");
    }

    public static char findFirstRepeatingChar(String s) {

        HashSet<Character> result = new HashSet<>();

        for (char c : s.toCharArray()) {
            // If add returns false, it means 'c' is already in the set
            if (!result.add(c)) {
                return c;
            }
        }

        return '\0';
    }


    public static char findFirstRepeatingCharUsingStreams(String s) {

        Set<Integer> seen = new HashSet<>();

        Integer firstRepeat = s.chars()           // Creates an IntStream of characters
                .filter(c -> !seen.add(c))    // Stateful filter: returns true only for the 2nd occurrence
                .boxed()                          // Convert int to Integer
                .findFirst()                       // Stop and return as soon as the first duplicate is found
                .orElse(0);

        return (char) firstRepeat.intValue();
    }
}

```