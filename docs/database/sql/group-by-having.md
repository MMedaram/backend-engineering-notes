---
title: GROUP BY & HAVING
parent: SQL
nav_order: 2
---

# GROUP BY & HAVING

## 1. Definition

`GROUP BY` is used to **group rows that have the same values in specified columns** into summary rows.

It is typically used with **aggregation functions** to compute results **per group instead of the whole table**.

Example use cases:
- Count employees per department
- Calculate average salary per department
- Total sales per day
- Total transactions per user

---

## 2. Basic Syntax

```sql
SELECT column_name, AGG_FUNCTION(column) FROM table_name GROUP BY column_name;
```

---

## 3. Example Table

```sql
Employee
---------
id
name
salary
dept_id
```

Sample data:

| id | name | salary | dept_id |
| -- | ---- | ------ | ------- |
| 1  | A    | 50000  | 10      |
| 2  | B    | 60000  | 10      |
| 3  | C    | 55000  | 20      |
| 4  | D    | 70000  | 20      |


---

## 4.GROUP BY Example

**Find average salary per department.**

```sql
SELECT dept_id, AVG(salary) FROM employee GROUP BY dept_id;
```

Result:

| dept_id | avg_salary |
| ------- | ---------- |
| 10      | 55000      |
| 20      | 62500      |


---

## 5. GROUP BY Execution Flow

SQL execution order (simplified):

```sql
FROM
WHERE
GROUP BY
HAVING
SELECT
ORDER BY
```

Steps:

- Read rows from table
- Apply WHERE filter
- Group rows by GROUP BY column
- Apply aggregation functions
- Apply HAVING filter
- Return results

---

## 6. Multiple Column GROUP BY

Grouping can be done using multiple columns.

Example:

```sql
SELECT dept_id, manager_id, COUNT(*) FROM employee GROUP BY dept_id, manager_id;
```

This creates groups based on the combination of both columns.

---

# 7. HAVING Clause

HAVING is used to filter grouped results.

- WHERE filters rows before grouping
- HAVING filters groups after aggregation

Example:

```sql
SELECT dept_id, COUNT(*) FROM employee GROUP BY dept_id
HAVING COUNT(*) > 5;
```

Only departments with more than 5 employees will appear.

## 8. WHERE vs HAVING

| Feature          | WHERE           | HAVING          |
| ---------------- | --------------- | --------------- |
| Applied on       | Rows            | Groups          |
| Execution stage  | Before GROUP BY | After GROUP BY  |
| Uses aggregation | Not allowed     | Allowed         |
| Performance      | Faster          | Slightly slower |


Example:

**Using WHERE**

```sql
SELECT dept_id, COUNT(*) FROM employee WHERE salary > 50000 GROUP BY dept_id;
```

First filters rows → then groups.

**Using HAVING**

```sql
SELECT dept_id, COUNT(*) FROM employee GROUP BY dept_id HAVING COUNT(*) > 5;
```

Groups first → then filters groups.


## 9. Selecting Columns with GROUP BY

Rule:

Every column in SELECT must be either:

- **in GROUP BY**
- **or used inside an aggregation function**

Invalid query:

```sql
SELECT dept_id, name, AVG(salary) FROM employee GROUP BY dept_id;
```
Reason:
- name is neither aggregated nor grouped.

Correct version:

```sql
SELECT dept_id, AVG(salary) FROM employee GROUP BY dept_id;
```

---

# 10. GROUP BY with ORDER BY

Results can be sorted.

Example:

```sql
SELECT dept_id, COUNT(*) FROM employee GROUP BY dept_id ORDER BY COUNT(*) DESC;
```
Departments with the highest employee count appear first.

---

## 11 Summary

- GROUP BY groups rows based on column values.
- Aggregation functions operate per group.
- WHERE filters rows before grouping.
- HAVING filters grouped results.
- Columns in SELECT must either be grouped or aggregated.
