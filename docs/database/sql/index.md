---
title: SQL
parent: Database
nav_order: 1
---

# SQL (Structured Query Language)

SQL (Structured Query Language) is a **standard language used to interact with relational databases**.  
It is used to **store, retrieve, manipulate, and manage data** stored in tables.

SQL allows applications to perform operations such as:

- Querying data
- Inserting new records
- Updating existing data
- Deleting records
- Defining database structure
- Controlling access to data

SQL is used by most relational database systems such as:

- PostgreSQL
- MySQL
- Oracle
- SQL Server
- MariaDB

---

# Types of SQL Commands

SQL commands are broadly categorized into the following groups.

| Category | Purpose | Examples |
|---|---|---|
| DDL (Data Definition Language) | Defines database structure | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| DML (Data Manipulation Language) | Manipulates table data | `INSERT`, `UPDATE`, `DELETE` |
| DQL (Data Query Language) | Retrieves data | `SELECT` |
| TCL (Transaction Control Language) | Manages transactions | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |
| DCL (Data Control Language) | Controls access | `GRANT`, `REVOKE` |

---

# SQL Logical Query Execution Order

The logical execution order defines how the database processes different parts of a query.

| Execution Order | Clause           | Description                                        |
| --------------- | ---------------- | -------------------------------------------------- |
| 1               | FROM             | Identify tables involved in the query              |
| 2               | JOIN             | Combine rows from multiple tables                  |
| 3               | ON               | Apply join condition                               |
| 4               | WHERE            | Filter rows before grouping                        |
| 5               | GROUP BY         | Create groups of rows                              |
| 6               | HAVING           | Filter grouped results                             |
| 7               | SELECT           | Choose columns to return                           |
| 8               | DISTINCT         | Remove duplicate rows                              |
| 9               | WINDOW FUNCTIONS | Apply window calculations (ROW_NUMBER, RANK, etc.) |
| 10              | ORDER BY         | Sort the result set                                |
| 11              | LIMIT / OFFSET   | Restrict number of rows returned                   |


---

## SQL syntax order (how we write queries):

```sql
SELECT
FROM
JOIN
WHERE
GROUP BY
HAVING
ORDER BY
LIMIT
```

## SQL execution order (how database processes queries):

```sql
FROM → JOIN → ON → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → WINDOW → ORDER BY → LIMIT
```

---

# Senior Developer Checklist - SQL (Top 40 things you must know)

1. Core Querying

- SELECT basics
- WHERE filtering
- ORDER BY
- DISTINCT
- LIMIT / OFFSET

2. Data Aggregation

- Aggregation functions
- GROUP BY
- HAVING
- Aggregation performance considerations

3. JOINS (very important)

- INNER JOIN
- LEFT JOIN
- RIGHT JOIN
- FULL JOIN
- SELF JOIN
- JOIN filtering (ON vs WHERE)
- Join execution strategies

4. Subqueries

- Scalar subqueries
- Correlated subqueries
- EXISTS vs IN
- Derived tables

5. Window Functions

- ROW_NUMBER
- RANK
- DENSE_RANK
- PARTITION BY
- Running totals
- LAG / LEAD

6. Query Design Patterns

- Typical interview and production problems:
- Second highest salary
- Top N per group
- Latest record per user
- Duplicate detection
- Deduplication
- Hierarchical queries

7. Performance & Optimization

- Index types
- Composite indexes
- Query execution plans
- Table scan vs index scan
- Join strategies

8. Transactions & Concurrency

- ACID properties
- Isolation levels
- Locks
- Deadlocks
- Transaction control

9. Schema & Data Integrity

- Primary keys
- Foreign keys
- Unique constraints
- Check constraints
- Normalization basics

---