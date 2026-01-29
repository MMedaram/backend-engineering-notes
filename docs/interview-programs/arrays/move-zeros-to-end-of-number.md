---
title: Move Zeros to End of Number (Preserving Order)
parent: Array
nav_order: 1
---

# Move Zeros to End of Number (Preserving Order)

### Problem Statement
Given a number, move all `0` digits to the end while maintaining the relative order of non-zero digits.

### Input
> 32403050

### Output

> 32435000

### Explanation
 
- Convert number to string to process digit by digit
- Append non-zero digits to a `StringBuilder`
- Count the number of zeros
- Append zeros at the end

This approach is **simple, readable, and interview-safe**.


```java

package exercise.arrays;

import java.util.Arrays;
import java.util.stream.Collectors;

public class MoveZerosToEnd {

    public static long moveZeros(long number) {

        String numStr = String.valueOf(number);

        StringBuilder result = new StringBuilder();

        int zeroCount = 0;

        for (char ch : numStr.toCharArray()) {
            if (ch == '0') {
                zeroCount++;
            } else {
                result.append(ch);
            }
        }

        // append zeros at the end
        result.append("0".repeat(zeroCount));

        return Long.parseLong(result.toString());
    }

    public static long moveZerosJava8(long number) {

        String numStr = String.valueOf(number);

        long zeroCount = numStr.chars().filter(c -> c == '0').count();

        String nonZeroPart = numStr.chars().filter(c -> c != '0')
                .mapToObj(c -> String.valueOf((char) c))
                .collect(Collectors.joining());

        return Long.parseLong(nonZeroPart + "0".repeat((int) zeroCount));
    }

    public static int[] moveZerosInArray(int[] arr) {
        int pos = 0;

        // Move non-zero elements forward
        for (int num : arr) {
            if (num != 0) {
                arr[pos++] = num;
            }
        }
        // Fill remaining positions with zero
        while (pos < arr.length) {
            arr[pos++] = 0;
        }

        return arr;
    }

    public static void main(String[] args) {
        System.out.println(moveZeros(32403050));  // 32435000
        System.out.println(moveZerosJava8(32403050)); // 32435000

        int[] arr = {3, 2, 4, 0, 3, 0, 5, 0};
        System.out.println(Arrays.toString(moveZerosInArray(arr))); // [3, 2, 4, 3, 5, 0, 0, 0]

    }
}

```

## Complexity

- Time: O(n)
- Space: O(n)