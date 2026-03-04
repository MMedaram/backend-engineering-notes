---
title: Frequency of Words
parent: Strings
nav_order: 3
---


# frequency of each word in a String using streams

```java
package exercise.strings;
import java.util.Arrays;
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

public class FrequencyOfEachWord {

        public static void main(String[] args) {
            String input = "Hello Mohan, how are you doing ?";

            Map<String, Long> frequencyMap = Arrays.stream(input.split("\\s+"))
                    .map(word -> word.replaceAll("[^a-zA-Z]", "").toLowerCase()) // Clean and normalize
                    .filter(word -> !word.isEmpty())                             // Remove empty strings after cleaning
                    .collect(Collectors.groupingBy(
                            Function.identity(),                                     // Use the word as the key
                            Collectors.counting()                                    // Count occurrences
                    ));

            System.out.println(frequencyMap);
            // Output: {how=1, doing=1, are=1, mohan=1, hello=1, you=1}
        }
    }


```