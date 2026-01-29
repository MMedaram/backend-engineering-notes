---
title: Employee Sorting Problems (Java 8+)
parent: Java8+ Real world Examples(Employee)
nav_order: 1
---

# Employee Sorting Problems (Java 8+)

---

## Default Sorting Order

> **Numbers** → sorted in ascending order     
> 
> 1, 2, 3, 4, 5


> **Characters / Strings** → sorted in alphabetical order (A → Z)     
>    
> a, b, c, d

```java
 List<Employee> employees = Employee.getEmployeeList();
```
### 1. Sort Employees by Salary (Ascending) and Name (Descending)

```java
    public static List<Employee> sortBySalaryAscAndNameDescStream(List<Employee> employees){

        return employees.stream().sorted(Comparator.comparing(Employee::getSalary)
                                .thenComparing(Employee::getName, Comparator.reverseOrder()))
                .collect(Collectors.toList());

    }

```

### 2. Sort Employees by Salary Descending order

```java
public static List<Employee> sortBySalaryDesc(List<Employee> employees){
        return employees.stream().sorted(Comparator.comparingDouble(Employee::getSalary).reversed()).toList();
    }
```

