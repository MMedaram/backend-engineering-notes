---
title: JOINS
parent: SQL
nav_order: 3
---

# JOINS in SQL

## 1. Definition

A **JOIN** is used to combine rows from **two or more tables** based on a related column between them.

In relational databases, data is usually normalized across multiple tables.  
JOINS allow us to **retrieve related data from those tables in a single query**.

Example use cases in backend systems:

- Fetch employees with their department names
- Retrieve orders along with customer details
- Get transactions with account information
- Load user profile with address data

---

## 2. Example Tables

### Employee

| id | name | dept_id | salary |
|---|---|---|---|
|1|Alice|10|50000|
|2|Bob|20|60000|
|3|Charlie|10|55000|
|4|David|NULL|45000|

### Department

| id | dept_name |
|---|---|
|10|Engineering|
|20|HR|
|30|Finance|

---

## 3. Types of JOINS

| Join Type | Description |
|---|---|
| INNER JOIN | Returns rows with matching values in both tables |
| LEFT JOIN | Returns all rows from left table and matching rows from right table |
| RIGHT JOIN | Returns all rows from right table and matching rows from left table |
| FULL JOIN | Returns all rows from both tables |
| CROSS JOIN | Cartesian product of both tables |
| SELF JOIN | Table joined with itself |

---

## 3.1. INNER JOIN


Returns only rows where the join condition matches in **both tables**.

### Example

```sql
SELECT e.name, d.dept_name FROM employee e INNER JOIN department d ON e.dept_id = d.id;
```

### Result

| name    | dept_name   |
| ------- | ----------- |
| Alice   | Engineering |
| Bob     | HR          |
| Charlie | Engineering |

Employee **David** is not included because dept_id is NULL.


## 3.2. LEFT JOIN (LEFT OUTER JOIN)

Returns:

- All rows from the left table
- Matching rows from the right table

If no match exists, right table columns become NULL.

### Example

```sql
SELECT e.name, d.dept_name FROM employee e
LEFT JOIN department d ON e.dept_id = d.id;
```

### Result 

| name    | dept_name   |
| ------- | ----------- |
| Alice   | Engineering |
| Bob     | HR          |
| Charlie | Engineering |
| David   | NULL        |

David appears because LEFT JOIN keeps all rows from employee.

## 3.3. RIGHT JOIN (RIGHT OUTER JOIN)

Returns:

- All rows from the right table
- Matching rows from the left table

### Example

```sql
SELECT e.name, d.dept_name FROM employee e
RIGHT JOIN department d ON e.dept_id = d.id;
```

### Result

| name    | dept_name   |
| ------- | ----------- |
| Alice   | Engineering |
| Bob     | HR          |
| Charlie | Engineering |
| NULL    | Finance     |

Finance appears because no employee belongs to that department.

## 3.4. FULL JOIN (FULL OUTER JOIN)

- Returns all rows from both tables.

If no match exists, columns from the other table become NULL.

### Example

```sql
SELECT e.name, d.dept_name FROM employee e
FULL JOIN department d ON e.dept_id = d.id;
```

### Result
| name    | dept_name   |
| ------- | ----------- |
| Alice   | Engineering |
| Bob     | HR          |
| Charlie | Engineering |
| David   | NULL        |
| NULL    | Finance     |


## 3.5. CROSS JOIN
   
Returns Cartesian product of two tables.

Every row of table A is combined with every row of table B.

### Example

```sql
SELECT e.name, d.dept_name FROM employee e
CROSS JOIN department d;
```

If:

- Employee rows = 4
- Department rows = 3

**Result rows** = 4 × 3 = 12

---

## 3.6.  SELF JOIN

A table joined with itself.

Used for hierarchical relationships such as:

- Employees and managers
- Category trees
- Parent-child relationships

### Example Table

**Employee**    

| id | name    | manager_id |
| -- | ------- | ---------- |
| 1  | Alice   | NULL       |
| 2  | Bob     | 1          |
| 3  | Charlie | 1          |


### Query

```sql
SELECT e.name AS employee, m.name AS manager FROM employee e
LEFT JOIN employee m ON e.manager_id = m.id;
```

### Result

| employee | manager |
| -------- | ------- |
| Alice    | NULL    |
| Bob      | Alice   |
| Charlie  | Alice   |


---

## 4. JOIN Filtering 

Filtering in **WHERE** vs **ON** can produce different results.

### Example (Correct LEFT JOIN)

```sql
SELECT e.name, d.dept_name FROM employee e
LEFT JOIN department d ON e.dept_id = d.id;
```

### Mistake

```
SELECT e.name, d.dept_name FROM employee e
LEFT JOIN department d ON e.dept_id = d.id
WHERE d.id IS NOT NULL;
```

This effectively becomes an **INNER JOIN** because the WHERE clause removes NULL rows.

---

## 5. Performance Best Practices

### Index Join Columns

Example:

- employee.dept_id
- department.id

Indexes help the database quickly locate matching rows.

### Avoid Joining Large Tables Without Filters

#### Bad:

```sql
SELECT * FROM orders JOIN transactions ON orders.id = transactions.order_id;
```

#### Better:

```sql
SELECT * FROM orders JOIN transactions ON orders.id = transactions.order_id
WHERE orders.created_date > '2025-01-01';
```

--

# Summary

- JOINS combine rows from multiple tables using related columns.
- Most common join used in production systems is INNER JOIN.
- LEFT JOIN is used when all rows from the main table must be preserved.
- Filtering conditions should be placed carefully in ON vs WHERE.
- Indexing join columns greatly improves performance.

