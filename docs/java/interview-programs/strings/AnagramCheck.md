---
title: AnagramCheck
parent: Strings
nav_order: 2
---

# Check if two strings are anagrams 

If two strings are anagrams:

- Same length
- Same characters after sorting

Example Input :
- "eat", "tea" - anagrams
- "hello", "world" - not anagrams

```java

import java.util.Arrays;

public class AnagramCheck {

    public static boolean isAnagram(String s1, String s2) {

        if (s1 == null || s2 == null) return false;

        s1 = s1.replaceAll("\\s+", "").toLowerCase();
        s2 = s2.replaceAll("\\s+", "").toLowerCase();

        if (s1.length() != s2.length()) return false;

        char[] a1 = s1.toCharArray();
        char[] a2 = s2.toCharArray();

        Arrays.sort(a1);
        Arrays.sort(a2);

        return Arrays.equals(a1, a2);
    }

    public static void main(String[] args) {
        System.out.println(isAnagram("eat", "tea"));   // true
        System.out.println(isAnagram("hello", "world")); // false
    }
}
```


# Given an array of strings, group the anagrams together

### Input: strs = ["eat","tea","tan","ate","nat","bat"] 
### Output: [["bat"],["nat","tan"],["ate","eat","tea"]]

```java
package exercise.arrays;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class GroupAnagrams {

    public static List<List<String>> groupAnagrams(String[] strings) {

        Map<String, List<String>> map = new HashMap<>();

        for (String word : strings) {
            char[] chars = word.toCharArray();
            Arrays.sort(chars);
            String sortedKey = new String(chars);

            map.computeIfAbsent(sortedKey, k -> new ArrayList<>())
                    .add(word);
        }

        return new ArrayList<>(map.values()); // [[eat, tea, ate], [bat], [tan, nat]]
    }

    public static void main(String[] args) {
        String[] strs = {"eat","tea","tan","ate","nat","bat"};
        System.out.println(groupAnagrams(strs));
    }
}
```


