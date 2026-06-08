---
title: LengthOfLongestSubstring
parent: Strings
nav_order: 10
---

## LengthOfLongestSubstring

```java
package exercise.strings;

import java.util.HashMap;
import java.util.Map;

public class LengthOfLongestSubstring {

    public static int lengthOfLongestSubstring(String s) {

        // Stores the last index where each character was seen.
        // Example:
        // a -> 0
        // b -> 1
        // c -> 2
        Map<Character, Integer> lastSeen = new HashMap<>();

        // Left boundary of our current window
        int left = 0;

        // Stores the maximum length found so far
        int result = 0;

        // Right boundary of the window
        for (int right = 0; right < s.length(); right++) {

            char ch = s.charAt(right);

            /*
             * If:
             * 1. Character already exists in map
             * 2. Its last occurrence is inside current window
             *
             * Then we found a duplicate.
             *
             * Move left pointer just after previous occurrence.
             */
            if (lastSeen.containsKey(ch) && lastSeen.get(ch) >= left) {
                left = lastSeen.get(ch) + 1;
            }

            // Update latest position of current character
            lastSeen.put(ch, right);

            // Current window length = right - left + 1
            result = Math.max(result, right - left + 1);
        }

        return result;
    }

    public static String longestSubstring(String s) {

        Map<Character, Integer> lastSeen = new HashMap<>();

        int left = 0;

        int maxLength = 0;
        int startIndex = 0; // start of longest substring found

        for (int right = 0; right < s.length(); right++) {

            char ch = s.charAt(right);

            if (lastSeen.containsKey(ch) && lastSeen.get(ch) >= left) {
                left = lastSeen.get(ch) + 1;
            }

            lastSeen.put(ch, right);

            int currentLength = right - left + 1;

            if (currentLength > maxLength) {
                maxLength = currentLength;
                startIndex = left;
            }
        }

        return s.substring(startIndex, startIndex + maxLength);
    }

    public static void main(String[] args) {
        System.out.println(lengthOfLongestSubstring("abcde acd abcdefg defaaahi"));
    }
}
```
