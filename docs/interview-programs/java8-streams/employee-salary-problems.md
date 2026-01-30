---
title: Find Nth Highest Salary Employee in Each Department
parent: Java8+ Real world Examples(Employee)
nav_order: 2
---

# Find N'th Highest Salary Employee in Each Department

---

1. Group employees by department 
   - **Collectors.groupingBy**
2. For each department:
    - Sort employees by salary in descending order
   
      - **sorted(Comparator.comparing(Employee::getSalary).reversed())**
      
    - Skip the highest salary employee **skip**
    - Pick the next one (2nd highest) **findFirst**
3. Store result as:
    - `Map<Department, Optional<Employee>>` OR
    - `Map<Department, Employee>` (if data is guaranteed)


### 2nd Highest Salary Employee in Each Department

```java
  public static Map<String, Optional<Employee>> getSecondHighestSalaryEmployeeByDepartment(List<Employee> employees) {

        return employees.stream().collect(Collectors.groupingBy(Employee::getDepartment,
                Collectors.collectingAndThen(Collectors.toList(),list -> list.stream().
                        sorted(Comparator.comparing(Employee::getSalary).reversed())
                        .skip(1) 
                        .findFirst())
        ));
    }
```



### First Highest Salary Employee in Each Department

```java
    public static Map<String, Optional<Employee>> getFirstHighestSalaryEmployeeByDepartment(List<Employee> employees) {

        return employees.stream().collect(Collectors.groupingBy(Employee::getDepartment,
                Collectors.collectingAndThen(Collectors.toList(),list ->
                        list.stream().max(Comparator.comparing(Employee::getSalary)))
        ));
    }
```

### Least Salary Employee in Each Department

```java

    public static Map<String, Optional<Employee>> getLeastSalaryEmployeeByDepartment(List<Employee> employees) {

        return employees.stream().collect(Collectors.groupingBy(Employee::getDepartment,
                Collectors.collectingAndThen(Collectors.toList(),list ->
                        list.stream().min(Comparator.comparing(Employee::getSalary)))
        ));
    }

```

