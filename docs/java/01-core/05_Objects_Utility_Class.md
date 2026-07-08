---
title: Objects Class
parent: Core Concepts
nav_order: 5
---

# Objects Class in Java

`java.util.Objects` is a small utility class for common object operations.

In simple words, it helps us write safer and cleaner code when we deal with:

- null checks
- equality checks
- hash code creation
- safe string conversion
- stream null filtering
- default values
- index validation

This class is used a lot in daily backend coding because many production bugs come from simple mistakes like:

- calling `.equals()` on a null object
- forgetting to validate constructor arguments
- writing the wrong `hashCode()`
- logging null values badly
- allowing invalid list/string index values

---

## Common Imports Used in Examples

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;
import java.util.Objects;
import java.util.function.Supplier;
import java.util.stream.Collectors;
```

---

## Important: Object vs Objects

| Name                | Meaning                                  |
|---------------------|------------------------------------------|
| `java.lang.Object`  | Parent class of all Java classes         |
| `java.util.Objects` | Utility class with static helper methods |

Example:

```java
Object value = "Java";

boolean result = Objects.equals(value, "Java");
```

`Object` is the base class.

`Objects` is a helper class.

---

## Version Timeline

| Java Version | What changed                                                   |
|--------------|----------------------------------------------------------------|
| Java 7       | `Objects` class introduced                                     |
| Java 8       | Added `isNull`, `nonNull`, and supplier-based `requireNonNull` |
| Java 9       | Added default-value helpers and index-checking helpers         |

There are no preview features here. These are normal production APIs.

---

## Quick Method Map

| Method                                           | Daily Use                                |
|--------------------------------------------------|------------------------------------------|
| `Objects.equals(a, b)`                           | Null-safe equality check                 |
| `Objects.deepEquals(a, b)`                       | Deep comparison for arrays/nested arrays |
| `Objects.hashCode(obj)`                          | Null-safe hash code for one value        |
| `Objects.hash(a, b, c)`                          | Hash code for multiple fields            |
| `Objects.toString(obj)`                          | Safe string conversion                   |
| `Objects.toString(obj, defaultText)`             | Safe string conversion with fallback     |
| `Objects.requireNonNull(obj)`                    | Fail fast if required value is null      |
| `Objects.requireNonNull(obj, message)`           | Fail fast with clear error message       |
| `Objects.requireNonNull(obj, messageSupplier)`   | Lazy error message creation              |
| `Objects.isNull(obj)`                            | Stream-friendly null check               |
| `Objects.nonNull(obj)`                           | Stream-friendly not-null check           |
| `Objects.compare(a, b, comparator)`              | Compare using a comparator               |
| `Objects.requireNonNullElse(obj, defaultObj)`    | Java 9 fallback value                    |
| `Objects.requireNonNullElseGet(obj, supplier)`   | Java 9 lazy fallback value               |
| `Objects.checkIndex(index, length)`              | Java 9 index validation                  |
| `Objects.checkFromToIndex(from, to, length)`     | Java 9 range validation                  |
| `Objects.checkFromIndexSize(from, size, length)` | Java 9 range + size validation           |

---

# 1. Objects.equals()

## Why do we need it?

Normal `.equals()` can throw `NullPointerException` if the left side is null.

Bad:

```
String input = null;

if (input.equals("ACTIVE")) {
    System.out.println("User is active");
}
```

Output:

```text
Exception in thread "main" java.lang.NullPointerException
```

Because `input` is null, Java cannot call `.equals()` on it.

---

## Better way

```
String input = null;

if (Objects.equals(input, "ACTIVE")) {
    System.out.println("User is active");
}
```

Output:

```text
No exception
```

`Objects.equals()` safely handles null values.

---

## Daily backend use case

```java
public boolean isSameCustomer(Customer oldCustomer, Customer newCustomer) {
    return Objects.equals(oldCustomer.getEmail(), newCustomer.getEmail())
            && Objects.equals(oldCustomer.getMobileNumber(), newCustomer.getMobileNumber());
}
```

This is cleaner than writing many null checks manually.

---

## Edge cases

| Case                         | Result  |
|------------------------------|---------|
| `Objects.equals(null, null)` | `true`  |
| `Objects.equals(null, "A")`  | `false` |
| `Objects.equals("A", null)`  | `false` |
| `Objects.equals("A", "A")`   | `true`  |
| `Objects.equals("A", "B")`   | `false` |

Important:

```
Objects.equals(a, b)
```

internally behaves like:

```
a == b || (a != null && a.equals(b))
```

So if `a` is not null, Java still uses `a.equals(b)`.

---

# 2. Objects.deepEquals()

## Why do we need it?

Normal `equals()` does not compare array values properly.

Example:

```
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

System.out.println(a.equals(b));
```

Output:

```text
false
```

Reason:

Arrays do not override `equals()` like normal value objects. They compare object references.

---

## Better way

```
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

System.out.println(Objects.deepEquals(a, b));
```

Output:

```text
true
```

---

## Nested array example

```
String[][] first = {
        {"Java", "Spring"},
        {"SQL", "Kafka"}
};

String[][] second = {
        {"Java", "Spring"},
        {"SQL", "Kafka"}
};

System.out.println(Objects.deepEquals(first, second));
```

Output:

```text
true
```

---

## Edge cases

| Case                              | Result                 |
|-----------------------------------|------------------------|
| `Objects.deepEquals(null, null)`  | `true`                 |
| `Objects.deepEquals(null, array)` | `false`                |
| Same array reference              | `true`                 |
| Different arrays with same values | `true`                 |
| Normal objects                    | Uses normal `equals()` |

Best practice:

Use `deepEquals()` mainly for arrays.

Do not use it as a replacement for proper business comparison between domain objects.

---

# 3. Objects.hashCode()

## Why do we need it?

Calling `.hashCode()` on null throws `NullPointerException`.

Bad:

```
String email = null;

System.out.println(email.hashCode());
```

Output:

```text
java.lang.NullPointerException
```

---

## Better way

```
String email = null;

System.out.println(Objects.hashCode(email));
```

Output:

```text
0
```

If the object is null, `Objects.hashCode()` returns `0`.

---

## Daily use case

Use this when implementing `hashCode()` for a class with one important field.

```java
public class Customer {
    private String email;

    @Override
    public int hashCode() {
        return Objects.hashCode(email);
    }
}
```

---

## Edge cases

| Case                      | Result                     |
|---------------------------|----------------------------|
| `Objects.hashCode(null)`  | `0`                        |
| `Objects.hashCode("abc")` | Same as `"abc".hashCode()` |

Best practice:

Use `Objects.hashCode(value)` when you need a hash code for one value.

Use `Objects.hash(a, b, c)` when you need a hash code for multiple values.

---

# 4. Objects.hash()

## Why do we need it?

When a class has multiple fields, `hashCode()` should include the same fields used by `equals()`.

Example:

```java
public class Customer {
    private String email;
    private String countryCode;

    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (!(obj instanceof Customer other)) {
            return false;
        }
        return Objects.equals(email, other.email)
                && Objects.equals(countryCode, other.countryCode);
    }

    @Override
    public int hashCode() {
        return Objects.hash(email, countryCode);
    }
}
```

This is useful when the object is stored in:

- `HashSet`
- `HashMap`
- `LinkedHashSet`
- `ConcurrentHashMap` keys

---

## Edge cases

### 1. Single value surprise

```
String value = "abc";

System.out.println(Objects.hashCode(value));
System.out.println(Objects.hash(value));
```

These two results are not the same.

Reason:

`Objects.hash(value)` treats the input as an array of values.

For one value, prefer:

```
Objects.hashCode(value)
```

### 2. Null value

```
System.out.println(Objects.hash((Object) null));
```

Output:

```text
31
```

Reason:

This means "hash an array containing one null value".

### 3. Empty values

```
System.out.println(Objects.hash());
```

Output:

```text
1
```

---

## Best practice

If `equals()` uses 3 fields, `hashCode()` should use the same 3 fields.

Bad:

```
// equals checks email and countryCode,
// but hashCode uses only email
return Objects.hash(email);
```

Good:

```
return Objects.hash(email, countryCode);
```

---

# 5. Objects.toString()

## Why do we need it?

Calling `.toString()` on null throws `NullPointerException`.

Bad:

```
Object value = null;

System.out.println(value.toString());
```

Output:

```text
java.lang.NullPointerException
```

---

## Safe conversion

```
Object value = null;

System.out.println(Objects.toString(value));
```

Output:

```text
null
```

Important:

This output is the text `"null"`, not a Java null reference.

---

## Safe conversion with default text

```
Object value = null;

System.out.println(Objects.toString(value, "N/A"));
```

Output:

```text
N/A
```

---

## Daily backend use case

```java
String displayName = Objects.toString(user.getDisplayName(), "Guest");
```

Useful for:

- logs
- audit messages
- CSV export
- simple display fallback

---

## Edge cases

| Code                              | Result   |
|-----------------------------------|----------|
| `Objects.toString(null)`          | `"null"` |
| `Objects.toString(null, "N/A")`   | `"N/A"`  |
| `Objects.toString("Java", "N/A")` | `"Java"` |
| `Objects.toString(null, null)`    | `null`   |

Best practice:

Use a real default value like `"N/A"`, `"UNKNOWN"`, or `""` if the caller expects a non-null string.

---

# 6. Objects.requireNonNull()

## Why do we need it?

Some values are mandatory. If they are missing, the code should fail immediately with a clear error.

Example:

```java
public class OrderService {
    private final PaymentClient paymentClient;

    public OrderService(PaymentClient paymentClient) {
        this.paymentClient = Objects.requireNonNull(paymentClient, "paymentClient must not be null");
    }
}
```

If someone creates `OrderService` incorrectly:

```
new OrderService(null);
```

Output:

```text
java.lang.NullPointerException: paymentClient must not be null
```

This is better than getting a confusing `NullPointerException` later in some other method.

---

## Daily backend use cases

### Constructor validation

```java
public UserService(UserRepository userRepository) {
    this.userRepository = Objects.requireNonNull(userRepository, "userRepository must not be null");
}
```

### Method input validation

```java
public void sendEmail(String email) {
    Objects.requireNonNull(email, "email must not be null");

    // send email
}
```

### Setter validation

```java
public void setStatus(String status) {
    this.status = Objects.requireNonNull(status, "status must not be null");
}
```

---

## Supplier-based message: Java 8

Sometimes creating the error message is expensive.

```
Objects.requireNonNull(user, () -> "User not found for id: " + request.getUserId());
```

The supplier runs only when `user` is null.

For simple text, use normal string message:

```
Objects.requireNonNull(user, "user must not be null");
```

---

## Edge cases

### 1. No custom message

```
Objects.requireNonNull(null);
```

Output:

```text
java.lang.NullPointerException
```

### 2. Custom message

```
Objects.requireNonNull(null, "name must not be null");
```

Output:

```text
java.lang.NullPointerException: name must not be null
```

### 3. Message itself is null

```
Objects.requireNonNull(null, (String) null);
```

Output:

```text
java.lang.NullPointerException
```

The exception is still thrown, but the message is null.

### 4. Supplier is null and object is null

```
Objects.requireNonNull(null, (Supplier<String>) null);
```

Output:

```text
java.lang.NullPointerException
```

Reason:

Java tries to call the supplier to build the message, but the supplier itself is null.

### 5. Supplier is null but object is not null

```java
String name = Objects.requireNonNull("Mohan", (Supplier<String>) null);
```

Output:

```text
No exception
```

Reason:

The supplier is not needed because the value is already present.

---

## Best practice

Use `requireNonNull()` at the boundary of your code:

- constructor inputs
- public method inputs
- service dependencies
- required request fields after validation/mapping

Do not use it everywhere blindly. If null is a valid business case, handle it properly.

---

# 7. Objects.isNull() and Objects.nonNull()

These methods were added in Java 8.

They are mainly useful as method references in streams.

---

## Objects.nonNull()

```
List<String> names = Arrays.asList("Java", null, "Spring", null, "Kafka");

List<String> validNames = names.stream()
        .filter(Objects::nonNull)
        .collect(Collectors.toList());

System.out.println(validNames);
```

Output:

```text
[Java, Spring, Kafka]
```

---

## Objects.isNull()

```
List<String> names = Arrays.asList("Java", null, "Spring", null);

long missingCount = names.stream()
        .filter(Objects::isNull)
        .count();

System.out.println(missingCount);
```

Output:

```text
2
```

---

## Daily backend use case

```java
List<Order> validOrders = orders.stream()
        .filter(Objects::nonNull)
        .filter(order -> Objects.nonNull(order.getOrderId()))
        .collect(Collectors.toList());
```

---

## Edge cases

| Code                      | Result  |
|---------------------------|---------|
| `Objects.isNull(null)`    | `true`  |
| `Objects.isNull("Java")`  | `false` |
| `Objects.nonNull(null)`   | `false` |
| `Objects.nonNull("Java")` | `true`  |

Best practice:

Use these mainly with streams.

For normal `if` conditions, simple Java is often easier to read:

```
if (user != null) {
    // readable and common
}
```

---

# 8. Objects.compare()

## Why do we need it?

`Objects.compare()` compares two values using a given `Comparator`.

Example:

```
Comparator<Integer> comparator = Integer::compareTo;

System.out.println(Objects.compare(10, 20, comparator));
```

Output:

```text
-1
```

---

## Daily backend use case

```java
Comparator<Customer> byName = Comparator.comparing(Customer::getName);

int result = Objects.compare(customer1, customer2, byName);
```

---

## Null-safe sorting example

```
Comparator<String> nullLastStringComparator =
        Comparator.nullsLast(String::compareTo);

System.out.println(Objects.compare(null, "Java", nullLastStringComparator));
```

Output:

```text
1
```

Reason:

`nullsLast` treats null as greater than normal values, so null comes last.

---

## Edge cases

| Case                                        | Result                 |
|---------------------------------------------|------------------------|
| Same object reference                       | Returns `0`            |
| Both values are null                        | Returns `0`            |
| One value is null                           | Depends on comparator  |
| Comparator is null and values are different | `NullPointerException` |

Example:

```
Objects.compare("A", "B", null);
```

Output:

```text
java.lang.NullPointerException
```

Best practice:

Usually you will use `Comparator` directly for sorting. Use `Objects.compare()` when you are writing comparison helper methods.

---

# 9. Java 9: requireNonNullElse()

`requireNonNullElse()` returns the first value if it is not null. Otherwise, it returns the default value.

```
String requestedName = null;

String name = Objects.requireNonNullElse(requestedName, "Guest");

System.out.println(name);
```

Output:

```text
Guest
```

---

## Difference from Objects.toString(obj, default)

`Objects.toString(obj, defaultText)` returns a string.

`Objects.requireNonNullElse(obj, defaultObj)` returns the same type as the object.

Example:

```java
Integer pageSize = null;

Integer finalPageSize = Objects.requireNonNullElse(pageSize, 20);
```

---

## Edge cases

### 1. First value is present

```
Objects.requireNonNullElse("Java", "Default");
```

Output:

```text
Java
```

### 2. First value is null, default is present

```
Objects.requireNonNullElse(null, "Default");
```

Output:

```text
Default
```

### 3. Both values are null

```
Objects.requireNonNullElse(null, null);
```

Output:

```text
java.lang.NullPointerException
```

Important:

The default value must not be null.

---

# 10. Java 9: requireNonNullElseGet()

`requireNonNullElseGet()` is useful when the default value should be created only if needed.

```java
String displayName = Objects.requireNonNullElseGet(
        user.getDisplayName(),
        () -> loadDefaultDisplayName()
);
```

If `user.getDisplayName()` is not null, `loadDefaultDisplayName()` is not called.

---

## Daily backend use case

```java
Profile profile = Objects.requireNonNullElseGet(
        cachedProfile,
        () -> profileRepository.findDefaultProfile()
);
```

Use this when fallback creation is:

- costly
- database-based
- API-based
- not needed most of the time

---

## Edge cases

### 1. Object is present

```java
String value = Objects.requireNonNullElseGet("Java", () -> "Default");
```

Output:

```text
Java
```

Supplier is not called.

### 2. Object is null

```java
String value = Objects.requireNonNullElseGet(null, () -> "Default");
```

Output:

```text
Default
```

### 3. Object is null and supplier is null

```
Objects.requireNonNullElseGet(null, null);
```

Output:

```text
java.lang.NullPointerException
```

### 4. Supplier returns null

```
Objects.requireNonNullElseGet(null, () -> null);
```

Output:

```text
java.lang.NullPointerException
```

Important:

This method does not allow the final result to be null.

---

# 11. Java 9: Index Check Methods

Java 9 added helper methods for index validation.

These are useful when you are writing your own:

- list-like class
- array utility
- pagination helper
- substring/slice method
- buffer reader

---

## checkIndex(index, length)

Checks that index is between `0` and `length - 1`.

```
int index = Objects.checkIndex(2, 5);

System.out.println(index);
```

Output:

```text
2
```

Valid range:

```text
0, 1, 2, 3, 4
```

---

## Invalid checkIndex example

```
Objects.checkIndex(5, 5);
```

Output:

```text
java.lang.IndexOutOfBoundsException
```

Reason:

Index `5` is invalid when length is `5`.

Last valid index is `4`.

---

## checkFromToIndex(from, to, length)

Checks a range where:

- `from` is inclusive
- `to` is exclusive

```
Objects.checkFromToIndex(2, 5, 10);
```

Valid range:

```text
2, 3, 4
```

---

## Invalid range example

```
Objects.checkFromToIndex(5, 2, 10);
```

Output:

```text
java.lang.IndexOutOfBoundsException
```

Reason:

`from` cannot be greater than `to`.

---

## checkFromIndexSize(from, size, length)

Checks a range using starting index and size.

```
Objects.checkFromIndexSize(2, 3, 10);
```

This means:

```text
Start at 2 and take 3 elements: 2, 3, 4
```

---

## Invalid size example

```
Objects.checkFromIndexSize(8, 5, 10);
```

Output:

```text
java.lang.IndexOutOfBoundsException
```

Reason:

Starting at `8` and taking `5` elements crosses the length `10`.

---

## Index edge cases

| Method                                   | Invalid Cases                                                |
|------------------------------------------|--------------------------------------------------------------|
| `checkIndex(index, length)`              | `index < 0`, `index >= length`, `length < 0`                 |
| `checkFromToIndex(from, to, length)`     | `from < 0`, `from > to`, `to > length`, `length < 0`         |
| `checkFromIndexSize(from, size, length)` | `from < 0`, `size < 0`, `from + size > length`, `length < 0` |

All invalid cases throw:

```text
IndexOutOfBoundsException
```

---

# Daily Coding Examples

## 1. Validate required dependency

```java
public NotificationService(EmailClient emailClient) {
    this.emailClient = Objects.requireNonNull(emailClient, "emailClient must not be null");
}
```

## 2. Compare nullable fields

```java
boolean sameEmail = Objects.equals(oldUser.getEmail(), newUser.getEmail());
```

## 3. Remove null values from stream

```java
List<String> validEmails = emails.stream()
        .filter(Objects::nonNull)
        .collect(Collectors.toList());
```

## 4. Give default page size

```java
Integer pageSize = Objects.requireNonNullElse(request.getPageSize(), 20);
```

## 5. Safe log value

```
log.info("Request id: {}", Objects.toString(requestId, "missing"));
```

## 6. Build equals and hashCode

```java
@Override
public boolean equals(Object obj) {
    if (this == obj) {
        return true;
    }
    if (!(obj instanceof Product other)) {
        return false;
    }
    return Objects.equals(code, other.code)
            && Objects.equals(country, other.country);
}

@Override
public int hashCode() {
    return Objects.hash(code, country);
}
```

---

# When Not to Use Objects

## 1. Do not use it for blank string checks

`Objects.nonNull(name)` only checks not null.

It does not check empty or blank strings.

```
String name = "";

System.out.println(Objects.nonNull(name));
```

Output:

```text
true
```

For strings, also check blank/empty based on your Java version and project utility methods.

---

## 2. Do not use requireNonNull for optional business fields

Bad:

```
this.middleName = Objects.requireNonNull(middleName);
```

If middle name is optional, null may be allowed.

Better:

```
this.middleName = middleName;
```

or use a default:

```
this.middleName = Objects.toString(middleName, "");
```

---

## 3. Do not use Objects. hash blindly in high-performance code

`Objects.hash()` creates an array internally because it accepts varargs.

For normal business applications, this is fine.

For very hot code paths, manual hash code calculation can be faster.

Example:

```
int result = 17;
result = 31 * result + Objects.hashCode(email);
result = 31 * result + Objects.hashCode(countryCode);
return result;
```

Most backend code can safely use `Objects.hash()` for readability.

---

# Best Practices

1. Use `Objects.equals()` for nullable field comparison.
2. Use `Objects.requireNonNull()` in constructors for mandatory dependencies.
3. Use clear error messages with `requireNonNull`.
4. Use `Objects::nonNull` in streams to remove null values.
5. Use `Objects.hash()` with the same fields used in `equals()`.
6. Use `Objects.hashCode()` for one field, not `Objects.hash(oneField)`.
7. Use Java 9 default methods only if your project runs on Java 9 or later.
8. Use index-check methods when building low-level utility code.
9. Do not confuse null check with empty/blank string validation.
10. Do not hide business problems by replacing every null with a default value.

---

# One-Liner

`java.util.Objects` is a utility class introduced in Java 7 to make common object operations safer and cleaner, especially null-safe equality, hash code creation, string conversion, required null checks, stream null filtering, default values, and index validation.

---

# Summary

1. `Objects` is a utility class, not the same as `Object`.
2. It helps reduce null-related bugs.
3. It is useful in daily backend code.
4. Java 8 added stream-friendly null helpers.
5. Java 9 added default-value and index-check helpers.
6. Use it for clean code, but do not overuse it where simple checks are clearer.

