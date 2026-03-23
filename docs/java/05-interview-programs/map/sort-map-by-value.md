---
title: Sort a map by Values
parent: Map
nav_order: 2
---

# Sort a map by Values

unlike keys, a Map cannot be directly sorted by value because the map structure is indexed by keys.

So the standard approach is:

- Convert the map to entry set
- Sort entries by value
- Collect back to a LinkedHashMap (to maintain order)

```
 Map<String, Integer> map = new HashMap<>();
        map.put("Banana", 3);
        map.put("Apple", 1);
        map.put("Orange", 2);
        map.put("Grapes", 5);
```

## 1. Sort Map by Value (Ascending) — Java 8

```java

    public static Map<String, Integer> sortingByValueUsingStreams(Map<String, Integer> map) {

        return map.entrySet().stream().sorted(Map.Entry.comparingByValue())
                .collect(Collectors.toMap(
                        Map.Entry::getKey,
                        Map.Entry::getValue,
                        (oldValue,newValue) -> oldValue,
                        LinkedHashMap::new
                ));
    }
```

> {Apple=1, Orange=2, Banana=3, Grapes=5}

---

## 2. Sort Map by Value (Descending)

```java

    public static Map<String, Integer> sortingByValueInDescendingOrderUsingStreams(Map<String, Integer> map) {

        return map.entrySet().stream().sorted(Map.Entry.comparingByValue(Comparator.reverseOrder()))
                .collect(Collectors.toMap(
                        Map.Entry::getKey,
                        Map.Entry::getValue,
                        (oldValue,newValue) -> oldValue,
                        LinkedHashMap::new
                ));
    }

```

> {Grapes=5, Banana=3, Orange=2, Apple=1}

---

## 3. Pre-Java 8 Approach (Collections.sort)

```
List<Map.Entry<String, Integer>> list =
        new ArrayList<>(map.entrySet());

Collections.sort(list, (a, b) -> a.getValue().compareTo(b.getValue()));

Map<String, Integer> sortedMap = new LinkedHashMap<>();

for (Map.Entry<String, Integer> entry : list) {
    sortedMap.put(entry.getKey(), entry.getValue());
}
```

- If values are duplicate, sorting by value will still keep both entries;
- because sorting happens on entries, not values alone.
