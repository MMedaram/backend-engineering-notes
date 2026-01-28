---
title: Run Length Encoding of a String
parent: Strings
nav_order: 1
---

# Run Length Encoding of a String


##  Problem Statement
Given a string, encode it by counting **consecutive repeating characters** and appending the character followed by its count.

Characters appearing again later should be treated as a **new group**.

### Input
> aaaabbdddeaaff

### Output
> a4b2d3e1a2f2

### Explanation
This is a **run-length encoding** problem.

We:
- Traverse the string from left to right
- Maintain a count of consecutive characters
- When the character changes, append the previous character and its count
- Reset count for the new character

This handles repeated characters appearing again later correctly.

```java
package exercise.strings;

public class RunLengthEncoding {

    public static String encode(String input) {
        if (input == null || input.isEmpty()) {
            return input;
        }

        StringBuilder result = new StringBuilder();
        int count = 1;

        for (int i = 1; i <= input.length(); i++) {

            if (i < input.length() && input.charAt(i) == input.charAt(i - 1)) {
                count++;
            } else {
                result.append(input.charAt(i - 1))
                        .append(count);
                count = 1;
            }
        }
        return result.toString();
    }

    public static void main(String[] args) {
        System.out.println(encode("aaaabbdddeaaff")); //a4b2d3e1a2f2
    }
}

```
---

## Complexity

- Time: O(n)
- Space: O(n)

