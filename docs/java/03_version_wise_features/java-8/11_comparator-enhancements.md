---
title: Comparator Enhancements
parent: Java-8
nav_order: 12
---

# Comparator Enhancements

Java 8 made sorting much cleaner with new Comparator helper methods.

Important methods:

- `Comparator.comparing`
- `comparingInt`
- `comparingLong`
- `comparingDouble`
- `thenComparing`
- `reversed`
- `nullsFirst`
- `nullsLast`
- `naturalOrder`
- `reverseOrder`

---

## Why Were They Introduced?

Before Java 8, sorting often required verbose anonymous classes.

```
Collections.sort(users, new Comparator<User>() {
    @Override
    public int compare(User a, User b) {
        return a.getName().compareTo(b.getName());
    }
});
```

Java 8:

```
users.sort(Comparator.comparing(User::getName));
```

---

## comparing

Sort by one field.

```
users.sort(Comparator.comparing(User::getName));
```

---

## thenComparing

Sort by multiple fields.

```
users.sort(
    Comparator.comparing(User::getDepartment)
        .thenComparing(User::getName)
);
```

---

## comparingInt

Use for primitive int fields.

```
users.sort(Comparator.comparingInt(User::getAge));
```

This avoids unnecessary boxing from `int` to `Integer`.


| Getter returns | Preferred comparator           |
|----------------|--------------------------------|
| `int`          | `Comparator.comparingInt()`    |
| `long`         | `Comparator.comparingLong()`   |
| `double`       | `Comparator.comparingDouble()` |
| `Integer`      | `Comparator.comparing()`       |
| `BigDecimal`   | `Comparator.comparing()`       |
| `String`       | `Comparator.comparing()`       |
 
---

## reversed

```
users.sort(Comparator.comparing(User::getName).reversed());
```

---

## nullsFirst and nullsLast

`Comparator.nullsFirst()` and `Comparator.nullsLast()` are utility methods that specify **how `null` values should be ordered** during sorting.

Without them, sorting collections containing `null` values often results in a `NullPointerException`.

---

### `Comparator.nullsFirst()`

Places all `null` values **before** non-null values.

#### Example

```
List<String> names = Arrays.asList("Bob", null, "Alice", "John", null);

names.sort(Comparator.nullsFirst(Comparator.naturalOrder()));

System.out.println(names);
```

**Output**

```text
[null, null, Alice, Bob, John]
```

### `Comparator.nullsLast()`

Places all `null` values **after** non-null values.

#### Example

```
List<String> names = Arrays.asList("Bob", null, "Alice", "John", null);

names.sort(Comparator.nullsLast(Comparator.naturalOrder()));

System.out.println(names);
```

**Output**

```text
[Alice, Bob, John, null, null]
```


Suppose we have:

```java
class Employee {
    private String name;

    public String getName() {
        return name;
    }
}
```

Some employees may have a `null` name.

### Without `nullsLast()` / `nullsFirst()`

```
employees.sort(Comparator.comparing(Employee::getName));
```

If any employee has a `null` name, this throws:

```text
NullPointerException
```

---

### Using `nullsLast()`

```
employees.sort(
    Comparator.comparing(
        Employee::getName,
        Comparator.nullsLast(Comparator.naturalOrder())
    )
);
```

**Result**

```text
Alice
Bob
John
null
null
```
-----

## Daily Coding Example

```java
List<User> sorted = users.stream()
    .sorted(
        Comparator.comparing(User::getDepartment)
            .thenComparing(Comparator.comparingInt(User::getAge).reversed())
    )
    .collect(Collectors.toList());
```

Sort:

1. Department ascending.
2. Age descending inside department.

---


## Best Practices

- Use `comparing` for object fields.
- Use `comparingInt`, `comparingLong`, `comparingDouble` for primitives.
- Use `thenComparing` for multi-field sorting.
- Handle nulls explicitly when data can contain null.
- Prefer readable comparator chains over clever logic.

---

## Quick Summary

Java 8 Comparator helpers make sorting readable and safe. Use `comparing`, `thenComparing`, primitive comparators, and null handling helpers.


| Method                               | Meaning                                                   |
|--------------------------------------|-----------------------------------------------------------|
| `Comparator.naturalOrder()`          | Ascending order (`A → Z`, `1 → 9`)                        |
| `Comparator.reverseOrder()`          | Descending order (`Z → A`, `9 → 1`)                       |
| `Comparator.nullsFirst(c)`           | Places `null` values before non-null values               |
| `Comparator.nullsLast(c)`            | Places `null` values after non-null values                |
| `Comparator.comparing(keyExtractor)` | Compares objects using a key that implements `Comparable` |
| `Comparator.comparingInt()`          | Optimized comparator for `int` keys                       |
| `Comparator.comparingLong()`         | Optimized comparator for `long` keys                      |
| `Comparator.comparingDouble()`       | Optimized comparator for `double` keys                    |

