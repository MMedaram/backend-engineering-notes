---
title: Frequency of Words
parent: Strings
nav_order: 3
---


# frequency of each word in a String using streams

```java
package exercise.strings;

import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

public class FrequencyOfEachWord {

    public static void main(String[] args) {
        String input = "Hello Mohan, how are you mohan, did you completed kafka training mohan ?";
        System.out.println(frequencyOfEachWordMap(input));
    }

    private static Map<String, Long> frequencyOfEachWordUsingStreams(String input) {
        return Arrays.stream(input.split("\\s+"))
                .map(word -> word.toLowerCase().replaceAll("[^a-z0-9\\s]", "")) // Clean and normalize
                .filter(word -> !word.isEmpty())                             // Remove empty strings after cleaning
                .collect(Collectors.groupingBy(
                        Function.identity(),                                     // Use the word as the key
                        Collectors.counting()                                    // Count occurrences
                ));
        // {how=1, are=1, kafka=1, mohan=3, training=1, completed=1, hello=1, did=1, you=2}

    }

    private static Map<String, Integer> frequencyOfEachWordMap(String input) {

        input = input.toLowerCase().replaceAll("[^a-z0-9\\s]", ""); //Clean and normalize

        Map<String,Integer> frequencyCount = new HashMap<>();

        for(String word : input.split("\\s+")){

            frequencyCount.put(word, frequencyCount.getOrDefault(word,0)+1);
        }

        for(String s : frequencyCount.keySet()){
            if( frequencyCount.get(s) > 1){
                System.out.println(s + ":"+frequencyCount.get(s));
            }
        }

        return frequencyCount; //{how=1, are=1, mohan=3, kafka=1, training=1, hello=1, completed=1, you=2, did=1}

    }



}


```