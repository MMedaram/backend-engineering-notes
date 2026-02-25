---
title: Employee Grouping Problems (Java 8+)
parent: Java8+ Real world Examples(Employee)
nav_order: 3
---

# Employee Grouping Problems (Java 8+)

## Group Employees by Gender

```java
public static Map<String, List<Employee>> groupEmployeesByGender(List<Employee> employees) {

        return employees.stream().collect(Collectors.groupingBy(Employee::getGender));
    }
```


## Group Employees by Department

```java
public static Map<String, List<Employee>> groupEmployeesByDepartment(List<Employee> employees) {

    return employees.stream().collect(Collectors.groupingBy(Employee::getDepartment));

}
```

## Group Employees by Department AND Gender

- First group by department
- Inside each department, group by gender

```java
 public static Map<String, Map<String, List<Employee>>>  groupEmployeesByDepartmentAndGender(List<Employee> employees) {

        return employees.stream().collect(Collectors.groupingBy(Employee::getDepartment,Collectors.groupingBy(Employee::getGender)));
    }
```


```java
public static void main(String[] args) {

    Map<String, Map<String, List<Employee>>> result = groupEmployeesByDepartmentAndGender(employees);
    result.forEach((department, genderMap) -> {
        System.out.println("Department: " + department);
        genderMap.forEach((gender, empList) -> {
            System.out.println("  Gender: " + gender);
            empList.forEach(emp ->
                    System.out.println("    " + emp)
            );
        });
        System.out.println(); // blank line between departments
    });

}

```

#### Group Employees by Department AND Gender Result

```pgsql

Department: Finance
  Gender: Male
    Employee{id=103, name=Ravi, salary=70000.0}
    Employee{id=107, name=Manoj, salary=70000.0}

Department: HR
  Gender: Male
    Employee{id=109, name=Vikram, salary=80000.0}
  Gender: Female
    Employee{id=102, name=Sita, salary=55000.0}
    Employee{id=105, name=Kiran, salary=65000.0}
    Employee{id=110, name=Anjali, salary=88000.0}

Department: IT
  Gender: Female
    Employee{id=106, name=Lakshmi, salary=74000.0}
    Employee{id=108, name=Neha, salary=75000.0}
  Gender: Male
    Employee{id=101, name=Mohan, salary=50000.0}
    Employee{id=104, name=Ajay, salary=65000.0}

```
