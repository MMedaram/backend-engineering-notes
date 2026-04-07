---
title: SmallestAndLargestWord
parent: Strings
nav_order: 7
---

# Smallest And Largest Word in given string

Given a string, return:

- Smallest word (by length)
- Largest word (by length)



## Example

```
Input:  "Java is powerful and widely used"
Output:
Smallest word: is
Largest word: powerful
```


```java

package exercise.strings;

import java.util.Comparator;
import java.util.stream.Stream;

public class SmallestAndLargestWord {
    public static void main(String[] args) {

        String input = "Java is powerful and widely used";

        findWords(input);
    }


    public static void findWords(String input){

        if(input == null || input.trim().isEmpty()){
            System.out.println("Invalid Input");
            return;
        }

        String min = Stream.of(input.split(" ")).min(Comparator.comparingInt(String::length)).orElse("");
        String max = Stream.of(input.split(" ")).max(Comparator.comparingInt(String::length)).orElse("");


        System.out.println("Smallest word : "+min);
        System.out.println("Largest word : "+max);
    }
}

```

