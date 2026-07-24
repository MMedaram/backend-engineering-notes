---
title: Records
parent: Java-16
nav_order: 1
---

# Java 16 - Records

# What is a Record?

A **Record** is a special type of class introduced to reduce boilerplate code for classes whose main purpose is to **store data**.

Instead of writing constructors, getters, equals(), hashCode(), and toString(), Java generates them automatically.

Think of a Record as a **data container**.

---

# Real-world Example

Suppose you receive employee information from an API.

Without Records:

```java
public class Employee {
    private final int id;
    private final String name;
    private final String department;

    public Employee(int id, String name, String department) {
        this.id = id;
        this.name = name;
        this.department = department;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getDepartment() {
        return department;
    }

    // equals()
    // hashCode()
    // toString()
}
```

Lots of code.

With Record:

```java
public record Employee(int id, String name, String department) {
}
```

That's it.

Java automatically creates everything needed.

---

# Why Records?

Records solve one common problem.

Most business applications have many DTOs like

- Employee
- Customer
- Order
- Product
- Invoice
- Response Objects
- Request Objects

Almost all of them only hold data.

Instead of writing hundreds of lines of repetitive code, Records reduce it to one line.

---

# What Java Generates Automatically

Given

```java
public record Employee(int id, String name) {
}
```

Java automatically generates

- Private final fields
- Constructor
- Getter methods
- equals()
- hashCode()
- toString()

---

# Generated Fields

Java internally creates

```java
private final int id;
private final String name;
```

Notice

- private
- final

You cannot change them later.

---

# Generated Constructor

Java creates

```java
public Employee(int id, String name) {
    this.id = id;
    this.name = name;
}
```

---

# Generated Getter Methods

Normal class

```
employee.getName();
```

Record

```
employee.name();
employee.id();
```

Notice

There is NO get prefix.

Component name itself becomes the method.

---

# Generated toString()

```
Employee employee = new Employee(101, "John");

System.out.println(employee);
```

Output

```
Employee[id=101, name=John]
```

Very useful while debugging.

---

# Generated equals()

```
Employee e1 = new Employee(1, "John");
Employee e2 = new Employee(1, "John");

System.out.println(e1.equals(e2));
```

Output

```
true
```

Java compares values.

Not memory addresses.

---

# Generated hashCode()

Automatically generated.

Useful when storing records in

- HashMap
- HashSet

No need to write manually.

---

# Record Components

```java
public record Employee(
        int id,
        String name,
        double salary
) {
}
```

These are called **Record Components**.

Not fields.

Java converts them into private final fields internally.

---

# Immutability

Records are immutable.

Meaning

After object creation

Values cannot change.

Example

```java
Employee e = new Employee(1, "John");
```

Cannot do

```
e.name = "David";
```

Compile Error

---

# Why Immutable?

Immutable objects

- Thread-safe
- Easy to cache
- Predictable
- Less bugs
- Better for APIs

---

# Can Record Have Methods?

Yes.

```java
public record Employee(int id, String name) {

    public String display() {
        return id + " - " + name;
    }
}
```

Perfectly valid.

---

# Can Record Have Static Variables?

Yes.

```java
public record Employee(int id, String name) {

    static String company = "Oracle";
}
```

Allowed.

---

# Can Record Have Static Methods?

Yes.

```java
public record Employee(int id, String name) {

    static void printCompany() {
        System.out.println("Oracle");
    }
}
```

---

# Can Record Have Instance Variables?

No.

```java
public record Employee(int id, String name) {

    int age;
}
```

Compile Error

Reason

Record state is completely defined by its components.

---

# Can Record Have Final Instance Variables?

No.

```java
final int age;
```

Still Compile Error.

Only record components become instance fields.

---

# Can Record Have Constructors?

Yes.

Two types

- Compact Constructor
- Canonical Constructor

---

# Compact Constructor

```java
public record Employee(int id, String name) {

    public Employee {

        if(id <= 0)
            throw new IllegalArgumentException("Invalid id");
    }
}
```

Notice

No parameter list.

Java automatically uses record components.

---

# Canonical Constructor

```java
public record Employee(int id, String name) {

    public Employee(int id, String name) {

        this.id = id;
        this.name = name;
    }
}
```

Usually not needed.

Prefer Compact Constructor.

---

# Validation

Very common.

```java
public record Employee(int id, String name) {

    public Employee {

        if(id <= 0)
            throw new IllegalArgumentException("Id must be positive");

        if(name == null)
            throw new NullPointerException("Name cannot be null");

        if(name.isBlank())
            throw new IllegalArgumentException("Name cannot be blank");
    }
}
```

Recommended.

---

# Which Exceptions Should You Throw?

Invalid number

```
IllegalArgumentException
```

Null object

```
NullPointerException
```

Invalid state

```
IllegalStateException
```

Usually validation uses

- IllegalArgumentException
- NullPointerException

---

# Default Values

Not supported.

Cannot do

```
public record Employee(
    int id = 10,
    String name = "John"
)
```

Compile Error.

---

# How to Handle Defaults?

Create another constructor.

```java
public record Employee(int id, String name) {

    public Employee() {
        this(0, "Unknown");
    }
}
```

---

# Can Record Extend Another Class?

No.

```
record Employee(...) extends Person
```

Compile Error.

Reason

Every record already extends

```
java.lang.Record
```

Java supports only single inheritance.

---

# Can Record Implement Interface?

Yes.

```java
interface Printable{
    void print();
}

public record Employee(int id,String name)
        implements Printable{

    @Override
    public void print(){

    }
}
```

Very common.

---

# Can Record Be Abstract?

No.

Compile Error.

---

# Can Record Be Final?

All Records are already final.

No class can extend them.

---

# Can We Extend a Record?

No.

```
class Manager extends Employee
```

Compile Error.

---

# Can Record Have Mutable Objects?

Yes.

```java
record Employee(List<String> skills){
}
```

But

```
employee.skills().add("Java");
```

Works.

Because

Record is immutable.

List is mutable.

Only reference is final.

Object is not.

---

# Recommended Way

Use immutable collections.

```
List.copyOf(skills)
```

or

```
List.of(...)
```

Inside constructor.

Example

```java
public record Employee(List<String> skills) {

    public Employee {
        skills = List.copyOf(skills);
    }
}
```

Now external code cannot modify it.

---

# Nested Records

Allowed.

```java
record Employee(
        int id,
        Address address
){

    record Address(
            String city,
            String state
    ){}
}
```

---

# Serialization

Records support serialization.

If needed

```
implements Serializable
```

---

# Reflection

Records can be inspected using Reflection.

```
Employee.class.isRecord();
```

Returns

```
true
```

---

# Record Patterns

Introduced in later Java versions.

Very useful with Pattern Matching.

Example

```
if(obj instanceof Employee(int id, String name)){
}
```

Learn later with Java 21.

---

# Common Compile Errors

## Adding instance variable

```java
int age;
```

Compile Error.

---

## Modifying field

```
this.id = 100;
```

Outside constructor

Compile Error.

---

## Extending Record

Compile Error.

---

## Extending another class

Compile Error.

---

## Missing validation

Technically allowed.

But may create invalid objects.

Always validate important fields.

---

# Daily Coding Best Practices

Use Records for

- DTOs
- API Request Objects
- API Response Objects
- Kafka Messages
- Event Objects
- Configuration Objects
- Search Filters
- Read-only Models
- Value Objects

Avoid Records for

- JPA/Hibernate @Entity classes
- Classes with changing state
- Business logic-heavy classes
- Objects that require setters
- Mutable domain models

---

# Spring Boot Usage

Very common.

Request DTO

```java
public record EmployeeRequest(
        String name,
        double salary
){}
```

Response DTO

```java
public record EmployeeResponse(
        int id,
        String name
){}
```

Controller

```
@PostMapping
public EmployeeResponse save(
        @RequestBody EmployeeRequest request
){
    ...
}
```

Simple, clean, and less code.

---

# Advantages

- Less boilerplate code
- Immutable by default
- Auto-generated constructor
- Auto-generated getters
- Auto-generated equals()
- Auto-generated hashCode()
- Auto-generated toString()
- Easy to read
- Easy to maintain
- Thread-safe by design
- Excellent for APIs and DTOs

---

# Limitations

- Cannot extend another class
- Cannot be extended
- Cannot add instance fields
- No setters
- No default values for components
- Mutable component objects remain mutable unless copied
- Not suitable for JPA entities
- Not suitable when object state changes frequently


# Quick Revision

✅ Best for data-only classes

✅ Immutable by default

✅ Auto-generates constructor, getters, equals(), hashCode(), and toString()

✅ Getter style: `employee.name()` instead of `employee.getName()`

✅ Supports constructors, methods, static members, and interfaces

✅ Does not support setters or additional instance fields

✅ Prefer Compact Constructors for validation

✅ Throw `IllegalArgumentException` for invalid values and `NullPointerException` for required null checks

✅ Use `List.copyOf()` or other immutable collections when storing mutable objects

✅ Ideal for DTOs, API models, and event messages

❌ Avoid using Records for JPA entities or objects whose state changes over time
