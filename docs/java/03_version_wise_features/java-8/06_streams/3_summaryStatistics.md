---
title: summaryStatistics
parent: Stream API
nav_order: 3
---

# Java Streams - summaryStatistics

`summaryStatistics()` gives count, sum, min, max, and average in one pass.

It is available on primitive streams:

- `IntStream`
- `LongStream`
- `DoubleStream`

---

## Why Do We Need mapToInt?

```
list.stream()
    .mapToInt(Integer::intValue)
    .summaryStatistics();
```

Even if the list contains integers, `list.stream()` returns:

```
Stream<Integer>
```

`summaryStatistics()` is not available on `Stream<Integer>`.

So we convert it to:

```
IntStream
```

using:

```
mapToInt(Integer::intValue)
```

---

## What Does summaryStatistics Provide?

It computes:

- Count
- Sum
- Min
- Max
- Average

and returns:

```
IntSummaryStatistics
```

---

## Example 1: Student Scores

```java
public static void main(String[] args) {
    List<Integer> scores = Arrays.asList(78, 92, 85, 67, 95, 88);

    IntSummaryStatistics stats = scores.stream()
        .mapToInt(Integer::intValue)
        .summaryStatistics();

    System.out.println("Count   : " + stats.getCount());
    System.out.println("Sum     : " + stats.getSum());
    System.out.println("Min     : " + stats.getMin());
    System.out.println("Max     : " + stats.getMax());
    System.out.println("Average : " + stats.getAverage());
}
```

Output:

```text
Count   : 6
Sum     : 505
Min     : 67
Max     : 95
Average : 84.16666666666667
```

---

## Example 2: Employee Salaries

```java
class Employee {
    private String name;
    private String department;
    private int salary;

    Employee(String name, String department, int salary) {
        this.name = name;
        this.department = department;
        this.salary = salary;
    }

    String getDepartment() {
        return department;
    }

    int getSalary() {
        return salary;
    }
}
```

```
List<Employee> employees = Arrays.asList(
    new Employee("John", "IT", 50000),
    new Employee("Jane", "IT", 70000),
    new Employee("Mike", "HR", 60000)
);

IntSummaryStatistics salaryStats = employees.stream()
    .mapToInt(Employee::getSalary)
    .summaryStatistics();

System.out.println(salaryStats);
```

---

## Example 3: Grouping With Statistics

```java
Map<String, IntSummaryStatistics> statsByDept =
    employees.stream()
        .collect(Collectors.groupingBy(
            Employee::getDepartment,
            Collectors.summarizingInt(Employee::getSalary)
        ));
```

This gives department-wise:

- Employee count.
- Total salary.
- Lowest salary.
- Highest salary.
- Average salary.

---

## Empty Stream Edge Case

```java
IntSummaryStatistics stats = IntStream.empty().summaryStatistics();
```

Result:

```text
count = 0
sum = 0
min = Integer.MAX_VALUE
max = Integer.MIN_VALUE
average = 0.0
```

So check count before trusting min and max.

```
if (stats.getCount() > 0) {
    System.out.println(stats.getMin());
}
```

---

## When To Use It

Use `summaryStatistics()` when you need two or more of:

- Count.
- Sum.
- Min.
- Max.
- Average.

If you only need one metric, use the direct method.

```java
int max = scores.stream()
    .mapToInt(Integer::intValue)
    .max()
    .orElse(0);
```

---

## Quick Summary

`summaryStatistics()` computes multiple numeric metrics in a single traversal. Use it for reports, dashboards, and analytics-style code.
