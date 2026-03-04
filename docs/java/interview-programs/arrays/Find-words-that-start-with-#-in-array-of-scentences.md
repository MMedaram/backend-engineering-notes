---
title: Find '#words' in array[sentences] 
parent: Array
nav_order: 2
---

# Find words that start with '#' in an array of sentences

## Input: 

```java
String[] sentences = {
"Here is a #sample sentence #java is #powefull",
"This is another #example of a sentence.",
"#Java streams are powerful.",
"Find words that start with #."
};
```

## output

> [#sample, #java, #powefull, #example, #Java, #]


```java

package exercise.arrays;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class FindWordsStartWithHash {

    public static void main(String[] args) {
        String[] sentences = {
                "Here is a #sample sentence #java is #powefull",
                "This is another #example of a sentence.",
                "#Java streams are powerful.",
                "Find words that start with #."
        };

        List<String> hashtags = Arrays.stream(sentences)
                .flatMap(sentence -> Arrays.stream(sentence.split("\\s+"))) // Split by whitespace
                .filter(word -> word.startsWith("#"))                      // Filter for #
                .map(word -> word.replaceAll("[^#a-zA-Z0-9]", ""))         // Clean punctuation (optional)
                .distinct()                                                // Remove duplicates
                .collect(Collectors.toList());

        System.out.println(hashtags);
        // Output: [#sample, #java, #powefull, #example, #Java, #]
    }
}

```