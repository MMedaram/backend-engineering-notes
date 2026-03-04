---
title: Sort a map by Key
parent: Map
nav_order: 1
---

# sort a map by key

In Java, a Map does not guarantee ordering by default (e.g., HashMap). To sort a map by key, there are a few standard approaches depending on the situation.

```
 Map<String, Integer> map = new HashMap<>();
        map.put("Banana", 3);
        map.put("Apple", 1);
        map.put("Orange", 2);
        map.put("Grapes", 5);
```

## 1. Using TreeMap (Simplest)

TreeMap automatically sorts entries **by key in natural order.**

```java
    public static Map<String, Integer> sortingByUsingTreeMap(Map<String, Integer> map){

        return new TreeMap<>(map);
    }
```

### Result

> {Apple=1, Banana=3, Grapes=5, Orange=2}

- TreeMap internally uses a Red-Black Tree
- Keys are sorted using natural ordering (Comparable).

---

## 2. Using Java 8 Streams

Useful when you want sorted result but still return a Map.

```java
    public static Map<String, Integer> sortingByUsingStreams(Map<String, Integer> map){

       return map.entrySet().stream()
                        .sorted(Map.Entry.comparingByKey())
                        .collect(Collectors.toMap(
                                Map.Entry::getKey,
                                Map.Entry::getValue,
                                (oldVal, newVal) -> oldVal,
                                LinkedHashMap::new
                        ));

    }

```

### Why LinkedHashMap?

- Because it maintains insertion order, which preserves the sorted order.

---

## 3. Reverse Order Sorting

If you want descending order by key.

```java
 public static Map<String, Integer> sortingByUsingTreeMapInReverseOrder(Map<String, Integer> map){

        Map<String, Integer> sortedMap =  new TreeMap<>(Comparator.reverseOrder());

        sortedMap.putAll(map);

        return sortedMap;
    }
```


```
.sorted(Map.Entry.comparingByKey(Comparator.reverseOrder()))
```


#### Result

> {Orange=2, Grapes=5, Banana=3, Apple=1}

---

## 4. Using Custom Comparator

If sorting needs custom logic.

```
Map<String, Integer> sortedMap =
        new TreeMap<>((a, b) -> b.compareTo(a));

sortedMap.putAll(map);
```


---

| Approach                     | When to Use                      |
| ---------------------------- | -------------------------------- |
| `TreeMap`                    | When you always want sorted keys |
| Java 8 Streams               | When sorting only once           |
| `Collections.reverseOrder()` | When descending order required   |
| Custom Comparator            | When custom sorting logic needed |


