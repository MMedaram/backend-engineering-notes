---
title: filter() vs map()
parent: Streams
nav_order: 5
---

# filter() vs map()

In Java Streams, `filter()` and `map()` are intermediate operations,
but they serve completely different purposes.

Understanding their difference is essential for writing clean,
functional-style code.

---

## Basic Difference

| filter() | map() |
|-----------|--------|
| Selects elements | Transforms elements |
| Removes unwanted items | Converts items to another form |
| Returns same type | May return different type |
| Uses Predicate | Uses Function |

---

##  What is filter()?

`filter()` is used to **retain elements that match a condition**.

It does NOT modify elements.
It only decides whether an element should stay in the stream.


### Example – Filtering Even Numbers

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

List<Integer> evens = numbers.stream()
                             .filter(n -> n % 2 == 0)
                             .collect(Collectors.toList());
```

#### Output:

> [2, 4]

✔ Same type (Integer)     
✔ Some elements removed       

---

## What is map()?

map() is used to transform each element into another form.

It does NOT remove elements.     
It converts them.     

#### Example – Squaring Numbers

```
List<Integer> squares = numbers.stream()
.map(n -> n * n)
.collect(Collectors.toList());
```

##### Output:

> [1, 4, 9, 16, 25]


✔ All elements preserved    
✔ Values transformed      

---

> `filter()` is used to select elements based on a condition, while `map()` is used to transform elements from one form to another. `filter()` keeps or removes elements, whereas `map()` converts each element into a new value.



