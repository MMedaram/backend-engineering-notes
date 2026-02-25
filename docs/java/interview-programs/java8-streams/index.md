---
title: Java8+ Real world Examples(Employee)  
parent: Programs – Java
nav_order: 3
---

# Java8+ Real world Examples (Employee)

## Employee 

This model is designed specifically for:
- Java 8+ Streams practice
- Interview questions on grouping, sorting, filtering
- Real-world backend-style problems

We intentionally keep it **simple but realistic**.

```java

package common;

import java.time.LocalDate;
import java.util.Arrays;
import java.util.List;

public class Employee {
    private int id;
    private String name;
    private int age;
    private String department;
    private double salary;
    private String gender;
    private LocalDate joiningDate;
    private boolean active;
    private String designation;
    private String phoneNumber;
    private List<String> skills;
    public Employee(){

    }
    // Constructor
    public Employee(int id, String name, int age, String department, double salary,
                    String gender, LocalDate joiningDate, boolean active,
                    String designation, String phoneNumber,List<String> skills) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.department = department;
        this.salary = salary;
        this.gender = gender;
        this.joiningDate = joiningDate;
        this.active = active;
        this.designation = designation;
        this.phoneNumber = phoneNumber;
        this.skills = skills;
    }

    // Getters (only getters for immutability demo)
    public int getId() { return id; }
    public String getName() { return name; }
    public int getAge() { return age; }
    public String getDepartment() { return department; }
    public double getSalary() { return salary; }
    public String getGender() { return gender; }
    public LocalDate getJoiningDate() { return joiningDate; }
    public boolean isActive() { return active; }
    public String getDesignation() { return designation; }
    public String getPhoneNumber() { return phoneNumber; }
    public List<String> getSkills(){ return skills;}

    // Static method to generate Employee list
    public static List<Employee> getEmployeeList() {
        return Arrays.asList(
                new Employee(101, "Mohan", 28, "IT", 50000, "Male", LocalDate.of(2020, 1, 15), true, "Developer", "9876543210",Arrays.asList("Java", "Spring", "SQL")),
                new Employee(102, "Sita", 32, "HR", 55000, "Female", LocalDate.of(2018, 3, 10), true, "HR Manager", "8765432109",Arrays.asList("Recruitment", "Communication")),
                new Employee(103, "Ravi", 40, "Finance", 60000, "Male", LocalDate.of(2015, 7, 25), true, "Finance Head", "7654321098",Arrays.asList("Excel", "Accounting", "Compliance")),
                new Employee(104, "Ajay", 26, "IT", 65000, "Male", LocalDate.of(2021, 6, 12), true, "Developer", "6543210987",Arrays.asList("Java", "Angular")),
                new Employee(105, "Kiran", 38, "Admin", 65000, "Female", LocalDate.of(2017, 11, 30), false, "Admin Officer", "5432109876", List.of("Operations")),
                new Employee(106, "Lakshmi", 29, "IT", 70000, "Female", LocalDate.of(2019, 9, 5), true, "Senior Developer", "4321098765",Arrays.asList("Java", "Spring Boot", "Kafka")),
                new Employee(107, "Manoj", 35, "Finance", 70000, "Male", LocalDate.of(2016, 4, 18), true, "Accountant", "3210987654",Arrays.asList("Accounting", "Taxation")),
                new Employee(108, "Neha", 27, "IT", 75000, "Female", LocalDate.of(2022, 2, 20), true, "Tester", "2109876543",Arrays.asList("React", "NodeJS")),
                new Employee(109, "Vikram", 45, "Management", 80000, "Male", LocalDate.of(2010, 8, 14), true, "Director", "1098765432",Arrays.asList("Leadership", "Strategy")),
                new Employee(110, "Anjali", 31, "HR", 88000, "Female", LocalDate.of(2019, 5, 8), true, "HR Executive", "9988776655",Arrays.asList("Training", "People Management"))
        );
    }

    @Override
    public String toString() {
        return "Employee{ id=" + id + ", name=" + name + ", gender=" + gender + ", department=" + department + ", salary=" + salary+"}\n";
    }
}

```