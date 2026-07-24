---
title: Sealed Classes
parent: Java-17
nav_order: 1
---

# Java 17 - Sealed Classes 

---------

# What are Sealed Classes?

A **Sealed Class** allows you to **control which classes are allowed to extend it**.

Normally, any class can extend another class (unless it is `final`).

With Sealed Classes, **you decide exactly who can inherit your class**.

Think of it as a **whitelist of child classes**.

---

# Real-world Banking Example

Suppose your banking system supports only these account types:

- Savings Account
- Current Account
- Loan Account

Business Rule:

> No other account types should ever exist.

Without Sealed Classes:

```java
class BankAccount {

}
```

Anyone can create

```java
class SalaryAccount extends BankAccount{

}

class CryptoAccount extends BankAccount{

}

class UnknownAccount extends BankAccount{

}
```

This may violate business rules.

---

# Using Sealed Class

```java
public sealed class BankAccount
        permits SavingsAccount,
                CurrentAccount,
                LoanAccount {

}
```

Now only these classes can extend BankAccount.

Nobody else can.

---

# Child Classes

```java
public final class SavingsAccount
        extends BankAccount {

}

public final class CurrentAccount
        extends BankAccount {

}

public final class LoanAccount
        extends BankAccount {

}
```

Perfect.

---

# Why were Sealed Classes introduced?

Sometimes inheritance should be restricted.

Examples

Banking

Only

- Savings
- Current
- Loan

Payment

Only

- UPI
- Card
- NetBanking

Insurance

Only

- Health
- Life
- Vehicle

Vehicle

Only

- Car
- Bike
- Bus

You know all possible child classes.

Java should prevent others.

---

# Syntax

```java
public sealed class Parent
        permits Child1,
                Child2 {

}
```

---

# Meaning of permits

The `permits` keyword defines

Who is allowed to inherit.

Example

```
permits SavingsAccount,
        CurrentAccount
```

Only these two.

---

# What if permits is missing?

If child classes are in different files

Compile Error.

If child classes are in same source file

Java may infer them.

Best Practice

Always write

```
permits
```

Explicitly.

Makes code readable.

---

# What happens if another class extends it?

```java
class SalaryAccount
        extends BankAccount{

}
```

Compile Error

Something similar to

```
Class is not allowed to extend sealed class
```

---

# Child Classes MUST choose one keyword

Every permitted child must declare one of

- final
- sealed
- non-sealed

Nothing else.

---

# Option 1 : final

Most common.

```java
final class SavingsAccount
        extends BankAccount{

}
```

Meaning

Inheritance stops here.

Nobody can extend SavingsAccount.

Recommended for most business models.

---

# Option 2 : sealed

```java
sealed class LoanAccount
        extends BankAccount
        permits HomeLoan,
                VehicleLoan{

}
```

LoanAccount again controls

Who can extend it.

---

Example

```java
final class HomeLoan
        extends LoanAccount{

}

final class VehicleLoan
        extends LoanAccount{

}
```

---

# Option 3 : non-sealed

```java
non-sealed class CurrentAccount
        extends BankAccount{

}
```

Meaning

Restrictions are removed.

Now

```java
class SalaryAccount
        extends CurrentAccount{

}

class CorporateAccount
        extends CurrentAccount{

}
```

Allowed.

---

# Banking Hierarchy Example

```java
sealed class BankAccount
        permits SavingsAccount,
                CurrentAccount,
                LoanAccount {

}
```

Savings

```java
final class SavingsAccount
        extends BankAccount{

}
```

Current

```java
non-sealed class CurrentAccount
        extends BankAccount{

}
```

Loan

```java
sealed class LoanAccount
        extends BankAccount
        permits HomeLoan,
                CarLoan{

}
```

Child

```java
final class HomeLoan
        extends LoanAccount{

}
```

---

# Why final is recommended?

Suppose

```java
final class SavingsAccount{

}
```

Nobody can accidentally create

```
GoldSavingsAccount

DiamondSavingsAccount

PremiumSavingsAccount
```

Business remains controlled.

---

# Why non-sealed?

Suppose bank allows

Many Current Account types

- Salary
- Corporate
- Premium
- Student

Then

```
non-sealed CurrentAccount
```

Makes sense.

---

# Why sealed?

Suppose Loan Account

Supports only

- Home
- Car
- Education

Future developers should not create

GoldLoan

CryptoLoan

UnknownLoan

unless business changes.

---

# Can Sealed Class be Abstract?

Yes.

Very common.

```java
public abstract sealed class BankAccount
        permits SavingsAccount,
                CurrentAccount{

}
```

Usually preferred.

You don't create generic BankAccount.

You create specific account types.

---

# Can Sealed Interface exist?

Yes.

Very common.

Example

```java
public sealed interface Payment
        permits CardPayment,
                UpiPayment,
                NetBankingPayment{

}
```

---

Child

```java
final class CardPayment
        implements Payment{

}
```

---

# Constructor Rules

Same as normal class.

Allowed.

```java
sealed class BankAccount
        permits SavingsAccount{

    BankAccount(){

    }
}
```

---

# Can Sealed Class have methods?

Yes.

```java
sealed class BankAccount
        permits SavingsAccount{

    void print(){

    }
}
```

---

# Can Sealed Class have fields?

Yes.

Exactly like normal class.

```java
private String accountNumber;
```

Allowed.

---

# Can Sealed Class be final?

No.

Makes no sense.

```java
final sealed class Account{

}
```

Compile Error.

Either

Allow limited inheritance

OR

Allow no inheritance.

Not both.

---

# Can Sealed Class extend another class?

Yes.

```java
sealed class Account extends Object1{}
```

Or

```java
sealed class CurrentAccount
        extends BankAccount{

}
```

Allowed.

---

# Can Child be outside package?

Java requires permitted subclasses to be accessible according to the language rules. In practice, keep sealed hierarchies together in the same module or package structure where the compiler can verify them.

Best Practice

Keep parent and children together.

---

# Common Compile Errors

## Missing final/sealed/non-sealed

```java
class SavingsAccount
        extends BankAccount{

}
```

Compile Error.

Must choose

- final
- sealed
- non-sealed

---

## Not in permits

```java
class SalaryAccount
        extends BankAccount{

}
```

Compile Error.

Not permitted.

---

## Child missing extends

```java
final class SavingsAccount{

}
```

Not a compile error.

But not part of hierarchy.

---

## Wrong permits

```
permits Savings,Current
```

But

```java
class SavingsAccount{

}
```

Compile Error.

Names must match.

---

# Why not use final class?

Suppose

```java
final class BankAccount{

}
```

No inheritance at all.

Impossible to create

Savings

Current

Loan

So final is too restrictive.

---

# Difference

Normal Class

Anyone can inherit.

---

Final Class

Nobody can inherit.

---

Sealed Class

Only selected classes can inherit.

Perfect balance.

---

# Comparison

| Type   | Inheritance      |
|--------|------------------|
| Normal | Anyone           |
| Final  | Nobody           |
| Sealed | Selected Classes |

---

# Pattern Matching Advantage

Later Java versions

```
switch(account){

    case SavingsAccount s ->

    case LoanAccount l ->

    case CurrentAccount c ->

}
```

Compiler knows

These are ALL possible types.

No unknown subclasses.

Safer.

Faster.

Cleaner.

---

# Spring Boot Usage

Very useful.

Payment Types

```java
sealed interface Payment
        permits CardPayment,
                UpiPayment,
                WalletPayment{

}
```

API

```
processPayment(Payment payment)
```

Compiler knows

Only three implementations exist.

---

# Event Processing

```java
sealed interface Event
        permits
        UserCreated,
        UserDeleted,
        UserUpdated{

}
```

Cleaner event handling.

---

# Microservices

Commands

```java
sealed interface Command
        permits
        CreateUser,
        UpdateUser,
        DeleteUser{

}
```

Very common.

---

# Daily Coding Best Practices

Use Sealed Classes when

- Number of child classes is fixed
- Business rules control inheritance
- Domain model is stable
- Compiler should prevent new subclasses
- Working with Pattern Matching
- Modeling finite business states

Avoid Sealed Classes when

- Plugins are expected
- External teams need to extend your classes
- Frameworks generate subclasses
- Business rules change frequently
- You don't know all future child classes

---

# Banking Examples

Good Use Cases

Account Types

- Savings
- Current
- Loan

Transaction Types

- Deposit
- Withdrawal
- Transfer

Card Types

- Credit
- Debit
- Prepaid

Customer Types

- Individual
- Corporate

Loan Types

- Home
- Vehicle
- Education

KYC Status

- Pending
- Verified
- Rejected

---

# Advantages

- Better domain modeling
- Compiler-enforced inheritance rules
- Prevents accidental subclasses
- Easier maintenance
- Better readability
- Safer Pattern Matching
- Cleaner architecture
- Stronger business rule enforcement

---

# Limitations

- More restrictive than normal inheritance
- Not suitable when subclasses change frequently
- Frameworks that rely on runtime subclass generation may require special consideration
- Requires every permitted child to declare `final`, `sealed`, or `non-sealed`

---

# Quick Revision

✅ Restricts inheritance to specific classes

✅ Uses the `permits` keyword to define allowed child classes

✅ Every permitted child must be `final`, `sealed`, or `non-sealed`

✅ `final` → no further inheritance

✅ `sealed` → continue controlling inheritance

✅ `non-sealed` → remove restrictions for that branch

✅ Works with both classes and interfaces

✅ Excellent for banking, payments, events, commands, and fixed business models

✅ Makes Pattern Matching safer because the compiler knows all possible implementations

❌ Avoid when inheritance should remain open or external extensions are expected

---

## Version Timeline

| Version | Status         | Change                                      |
|---------|----------------|---------------------------------------------|
| Java 15 | Preview        | Initial feature.                            |
| Java 16 | Second preview | Refined preview.                            |
| Java 17 | Standard       | Final production feature; no preview flags. |
