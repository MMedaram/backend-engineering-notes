---
title: Custom Exceptions
parent: Exception-Handling
nav_order: 1
---

# Custom Exceptions

In Java, **custom exceptions** are user-defined exception classes that **represent application-specific error conditions.**

They are created by **extending existing exception classes.**

Java exceptions are mainly of two types:

| Type                | Parent Class                             | Compile Time Check          | Example                                            |
| ------------------- | ---------------------------------------- | --------------------------- | -------------------------------------------------- |
| Checked Exception   | `Exception` (but not `RuntimeException`) | Checked at compile time     | `IOException`, `SQLException`                      |
| Unchecked Exception | `RuntimeException`                       | Not checked at compile time | `NullPointerException`, `IllegalArgumentException` |


---

## 1. Custom Checked Exception

A **checked exception** must be **handled using try-catch or declared with throws.**

### Step 1: Create Custom Exception Class

Extend Exception.   

```java
public class InvalidAgeException extends Exception {

    public InvalidAgeException(String message) {
        super(message);
    }
}
```


### Step 2: Use the Exception

```java
public class Test {

    static void validateAge(int age) throws InvalidAgeException {
        if (age < 18) {
            throw new InvalidAgeException("Age must be 18 or above");
        }
        System.out.println("Valid age");
    }

    public static void main(String[] args) {
        try {
            validateAge(15);
        } catch (InvalidAgeException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

**Flow**

- Method detects invalid condition
- throw new InvalidAgeException()
- Caller must handle or declare it


## 2. Custom Unchecked Exception

Unchecked exceptions do not require `try-catch` or `throws` declaration.

### Step 1: Create Exception Class

Extend RuntimeException.

```java
public class InvalidAmountException extends RuntimeException {

    public InvalidAmountException(String message) {
        super(message);
    }
}
```

### Step 2: Use the Exception

```java
public class Test {

    static void withdraw(double amount) {
        if (amount <= 0) {
            throw new InvalidAmountException("Amount must be greater than zero");
        }
        System.out.println("Withdrawal successful");
    }

    public static void main(String[] args) {
        withdraw(-100);
    }
}
```
Here no try-catch required.

---

## 3. Common Constructors in Custom Exceptions

It is good practice to provide multiple constructors.

```java
public class BusinessException extends Exception {

    public BusinessException() {
        super();
    }

    public BusinessException(String message) {
        super(message);
    }

    public BusinessException(String message, Throwable cause) {
        super(message, cause);
    }

    public BusinessException(Throwable cause) {
        super(cause);
    }
}
```

This helps when wrapping other exceptions.

Example:

```
catch(SQLException e){
throw new BusinessException("Database error occurred", e);
}
```


---

## 4. Real-world Spring Boot Example

Example: Insufficient Balance

```java
public class InsufficientBalanceException extends RuntimeException {

    public InsufficientBalanceException(String message) {
        super(message);
    }
}
```

### Service layer:

```
if(account.getBalance() < amount){
throw new InsufficientBalanceException("Not enough balance");
}
```

### ControllerAdvice:

```java

@ExceptionHandler(InsufficientBalanceException.class)
public ResponseEntity<String> handleException(InsufficientBalanceException ex){
    
    return ResponseEntity.badRequest().body(ex.getMessage());

}

```

---

## 5. When to Use Which

**Use Checked Exception when**

- Caller can recover from the error
- Example
  - File not found
  - Network failure
  - Database connection issue

**Use Unchecked Exception when**

- Error is programming mistake or business rule violation
- Example
  - Invalid input
  - Insufficient balance
  - Illegal state
  

