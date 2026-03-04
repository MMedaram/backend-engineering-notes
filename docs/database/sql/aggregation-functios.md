---
title: Aggregation Functions
parent: SQL
nav_order: 1
---

# Aggregation Functions in SQL

## 1. Definition
Aggregation functions perform a **calculation on multiple rows of data and return a single summarized value**.

They are commonly used for:
- Data analysis
- Reporting
- Statistical calculations
- Group-based summaries

Aggregation functions operate on a **set of rows**, not a single row.

---

## 2. Common Aggregation Functions

| Function | Description |
|---|---|
| COUNT() | Returns number of rows |
| SUM() | Returns sum of values |
| AVG() | Returns average value |
| MIN() | Returns minimum value |
| MAX() | Returns maximum value |

---

## 3. Basic Examples

Assume table:

```sql
Employee
---------
id
name
salary
dept_id
```

### COUNT

```sql
SELECT COUNT(*) FROM employee;
```

Returns total number of rows.

### SUM

```sql
SELECT SUM(salary) FROM employee;
```

Returns total salary.

### AVG

```sql
SELECT AVG(salary) FROM employee;
```

Returns average salary.

### MIN

```sql
SELECT MIN(salary) FROM employee;
```

Returns lowest salary.

### MAX

```sql
SELECT MAX(salary) FROM employee;
```

Returns highest salary.

---

## 4. Aggregation with WHERE

- Filtering happens before aggregation.

Example:

```sal
SELECT AVG(salary) FROM employee WHERE dept_id = 10;
```

Execution flow:

- Apply WHERE
- Filter rows
- Apply aggregation on remaining rows
- Return result

---

## 5. Aggregation with GROUP BY

Aggregation can be applied per group of rows.

Example:

```sql 
SELECT dept_id, AVG(salary) FROM employee GROUP BY dept_id;
```

Result:

| dept_id | avg_salary |
| ------- | ---------- |
| 10      | 55000      |
| 20      | 62000      |


Each department becomes a group.

---

## 6. Internal Working (Execution Concept)

Typical execution steps:

- Table Scan / Index Scan
- Apply WHERE filter
- Create groups (if GROUP BY exists)
- Maintain aggregation state per group
- Produce final result

---

## 7. Important Behaviour

COUNT Variations

| Query                  | Behaviour                     |
| ---------------------- | ----------------------------- |
| COUNT(*)               | Counts all rows               |
| COUNT(column)          | Counts non-null values        |
| COUNT(DISTINCT column) | Counts unique non-null values |

  

Example:

```sql
SELECT COUNT(DISTINCT dept_id) FROM employee;
```

---

## 8. Aggregation + NULL Handling

Most aggregation functions ignore NULL values.

Example:

```sql
salary
------
50000
60000
NULL
```

Results:

```
COUNT(salary) = 2
AVG(salary) = (50000 + 60000) / 2
```

NULL values are not included.

---

## 9. Multiple Aggregations

SQL allows multiple aggregation calculations in one query.

Example:

```sql
SELECT
COUNT(*) AS total_employees,
AVG(salary) AS avg_salary,
MAX(salary) AS highest_salary
FROM employee;
```


## 10. Aggregation Without GROUP BY

If no GROUP BY exists, the entire table becomes a single group.

Example:

```sql 
SELECT MAX(salary) FROM employee;
```

#### Output:

Single row.



## 11. Common Mistakes

Selecting non-grouped columns

Invalid query:

```sql
SELECT dept_id, name, AVG(salary) FROM employee GROUP BY dept_id;
```

Reason:
- name is not aggregated or grouped.

#### Correct approach:

```sql
SELECT dept_id, AVG(salary) FROM employee GROUP BY dept_id;
```

## 12. Summary

- Aggregation functions summarize multiple rows into one value.
- Most common functions: COUNT, SUM, AVG, MIN, MAX.
- WHERE filters rows before aggregation.
- GROUP BY creates groups for aggregation.
- Aggregation ignores NULL values.
