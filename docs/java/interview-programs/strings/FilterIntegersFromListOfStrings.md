---
title: Filter Integers From List of Strings
parent: Strings
nav_order: 5
---


# Filter Integers From List of Strings

Filter only the integer values from a List<String> and return them as a new list.

```java

 List<String> list = Arrays.asList("10", "abc", "25", "xyz", "100", "-123");

 public static List<Integer> getIntegersFromList(List<String> list){

     return list.stream()
             .filter(s -> s.matches("-?\\d+"))
             .map(Integer::valueOf)
             .collect(Collectors.toList());
 }

 // Result:  [10, 25, 100, -123]

```

**Explanation**     

```text
1. stream() → converts list into stream
2. filter() → keeps only numeric strings
3. matches("-?\\d+") → regex for integer numbers
4. map(Integer::valueOf) → converts String → Integer
5. collect() → converts stream back to list
```
**Regex meaning:**

| Pattern | Meaning                |
| ------- | ---------------------- |
| `-?`    | optional negative sign |
| `\d+`   | one or more digits     |

---

## Using Try-Catch 

Regex sometimes fails with edge cases.       
A safer approach is try parsing.        

```java

public static List<Integer> getIntegersFromListUsingParseInt(List<String> list) {

    return list.stream().map(s -> {
        try {
            return Integer.parseInt(s);
        } catch (NumberFormatException e) {
            return null;
        }
    }).filter(Objects::nonNull).toList();
}
```

Why this approach is better

- Handles parsing exactly like Java does
- Avoids regex mistakes
- Preferred in backend systems


