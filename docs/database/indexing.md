---
title: Indexes in RDBMS
parent: Database
nav_order: 1
---

# Indexes in RDBMS

An **index** in an RDBMS is a **separate data structure** that improves query
performance by allowing the database to **locate rows quickly** without scanning
the entire table.

> **Core idea:**  
> Indexes speed up READ operations by trading extra storage and write overhead.

---

## Why Indexes Are Needed

Without indexes:
- Database performs **full table scan**
- Every row is checked against the `WHERE` condition
- Performance degrades as table size grows

With indexes:
- Database uses an optimized lookup structure
- Only required rows are accessed
- Queries run much faster

---

## How Indexes Work (High Level)

When an index is created:
- Database creates a **separate storage structure**
- Indexed column values are stored in **sorted order**
- Each value points to the actual row location (ROWID)

Most RDBMS use **B-Tree** indexes by default.

---

## What Indexes Store

✔ Indexed column value(s)  
✔ Pointer (ROWID) to table row

❌ Full row data  
❌ Non-indexed columns

> **Important:**  
> Index does NOT rearrange table rows.  
> Table data and index data are stored separately.

---

## What Happens Without an Index

```sql
SELECT * FROM customer WHERE email = 'abc@gmail.com';
```

### Without index:

- DB scans every row
- Applies condition row by row
- Time complexity ≈ O(N)   

This is called a Full Table Scan.

### What Happens With an Index

```sql
CREATE INDEX idx_customer_email ON customer(email);
```

### With index:

- DB searches index B-Tree (log N)
- Finds matching ROWID
- Fetches only required row(s)
- Time complexity ≈ O(log N)

---

### Types of Indexes (Common)

####  1️⃣ B-Tree Index (Default)

- Balanced tree structure
- Supports:
    -  `=`
    -  `<`,` >`
    -  `BETWEEN`
    -  `ORDER BY`
- Most commonly used


#### 2️⃣ Hash Index

- Uses hash function
- Very fast for = queries
- ❌ Not suitable for range queries
- Limited support in many RDBMS

#### 3️⃣ Bitmap Index

- Uses bitmaps
- Good for low-cardinality columns
- Common in data warehouses
- ❌ Not ideal for OLTP systems

---

### Where Indexes Help Most

Indexes are useful on columns used in:

- WHERE
- JOIN
- ORDER BY
- GROUP BY

Typical examples:

- Primary keys
- Foreign keys
- Email, account number, reference IDs

---

### Indexes and Write Operations (Important Trade-off)

Indexes slow down:

- INSERT
- UPDATE
- DELETE

Because:  

- Table data must be updated
- Index structure must also be updated and rebalanced

> **Golden rule:**  
>Indexes speed up reads but slow down writes.

---

### When You SHOULD Create Indexes

✔ Large tables   
✔ Frequently queried columns   
✔ Columns used in joins   
✔ High-selectivity columns (many unique values)   

### When You SHOULD NOT Create Indexes

❌ Very small tables   
❌ Columns rarely used in queries   
❌ Columns with very few distinct values    
❌ Write-heavy tables with frequent updates    

---

### Index Selectivity (Important Concept)

- Selectivity = uniqueness of column values
- High selectivity → good index candidate
- Low selectivity → poor index candidate

### Example:

- email → high selectivity ✔
- gender → low selectivity ❌

---

### Indexes in JOINs (Very Important)

```sql
SELECT *
FROM orders o
JOIN customer c ON o.customer_id = c.id;
```

Indexes on:

- customer.id
- orders.customer_id

Help:

- Avoid nested full scans
- Improve join performance drastically 
---

## Banking / Enterprise Perspective

In banking systems:

- Account number → indexed
- Customer ID → indexed
- Transaction reference → indexed

But:

- Balance columns → careful indexing
- Write-heavy tables → minimal indexes

Wrong indexing can:

- Reduce throughput
- Increase lock contention
- Cause performance issues

---

## Interview-Ready Summary

- Index is a separate data structure
- Uses extra storage
- Improves read performance
- Slows down write operations
- Most commonly implemented using B-Trees


> Indexes are a performance optimization tool.   
> Use them thoughtfully to balance read speed, write cost, and storage