---
title: N+1 Query Problem
parent: Spring Data JPA
nav_order: 8
---

# N+1 Query Problem in JPA

The **N+1 query problem** occurs when an application executes:
- **1 query** to fetch a list of entities
- **N additional queries** to fetch related entities for each record

This leads to serious **performance issues** in real-world applications.

---

## What Does N+1 Mean

**👉 Imagine you have two entities:** 

- Department → OneToMany → Employee.
- You want to fetch the Department with all Employees.

```java 

public interface DepartmentRepository extends JpaRepository<Department, Long> {

    List<Department> findAll();
}
```

#### Generated SQL:

```sql 
SELECT * FROM department;
SELECT * FROM employee WHERE department_id = 1;

```

Total Queries Executed = 1 (for Department) + N (for each Employee List)

 ✅ This is called the N+1 Problem.

- 🚨 1 query to fetch all departments.
- 🚨 5 additional queries to fetch employees for each department.

---

### Why N+1 Happens

N+1 happens because of:

- FetchType.LAZY
- Accessing related entities inside a loop
- Default repository methods (findAll)
- Persistence context fetching data on demand

#### Important:

N+1 is not a bug — it is how JPA works by default.

---

### Why N+1 Is Dangerous

- Too many database calls
- High DB load
- Slow API responses
- Poor scalability

#### Example:

- 10 rows → fine
- 10,000 rows → disaster

---

### How to Detect N+1

**Enable SQL logs**

```properties
spring.jpa.show-sql=true
hibernate.format_sql=true
```

**Red flag**

- Same query executed repeatedly
- Only ID value changes

---

## Correct Ways to Solve N+1

---


### 1️⃣ Join Fetch

```java
@Query("SELECT o FROM Order o JOIN FETCH o.customer ") 
List<Order> findAllWithCustomer();
```

✔ One query

✔ Orders + customers loaded together

### 2️⃣ EntityGraph (CLEAN & SAFE)

```java 
@EntityGraph(attributePaths = "customer")
List<Order> findAll();
```

✔ Declarative

✔ No JPQL change

✔ Preferred in many teams

EntityGraph tells tp JPA :

- 💡 "Hey JPA, when fetching orders → automatically fetch customers."
- 💡 Do NOT do lazy loading → Combine the query.

### 3️⃣ DTO Projection (BEST FOR APIs)

```java 
@Query("""
SELECT new com.bank.dto.OrderDTO(o.id, c.name)
FROM Order o JOIN o.customer c
""")
List<OrderDTO> findOrderSummaries();
```

✔ No entities

✔ No lazy loading

✔ Best performance

### 4️⃣ Batch Fetching (PARTIAL FIX)

```properties
hibernate.default_batch_fetch_size=20
```

✔ Reduces queries

❌ Does not eliminate N+1

---

### Summary

- N+1 = 1 query + N queries
- Happens due to lazy loading
- Join Fetch is most common fix
- EntityGraph is clean solution
- DTO projection is best for APIs
- EAGER fetch is not a solution

>Final Takeaway
>
>N+1 problem is not a framework issue —
>it is a data fetching design issue.

