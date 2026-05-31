# SQL Quick Reference Guide

A handy reference for common SQL concepts and queries.

---

## 1. Fetching NULL Values

You **cannot** use `= NULL` to find null values. Always use `IS NULL`.

```sql
-- ✅ Correct
SELECT * FROM table_name
WHERE column_name IS NULL;

-- ❌ Wrong - returns no rows
SELECT * FROM table_name
WHERE column_name = NULL;
```

### Why `= NULL` doesn't work

NULL means **"unknown"** — comparing anything to an unknown value returns **UNKNOWN**, not TRUE or FALSE.

| Expression         | Result   |
| ------------------ | -------- |
| `NULL = NULL`      | UNKNOWN  |
| `NULL != NULL`     | UNKNOWN  |
| `NULL = 1`         | UNKNOWN  |
| `NULL IS NULL`     | TRUE ✅  |
| `NULL IS NOT NULL` | FALSE ✅ |

### Common NULL examples

```sql
-- Find rows where a column is null
SELECT * FROM employees WHERE manager_id IS NULL;

-- Find rows where a column is NOT null
SELECT * FROM employees WHERE manager_id IS NOT NULL;

-- Combine with other conditions
SELECT * FROM orders
WHERE shipped_date IS NULL
  AND status = 'pending';

-- Replace null with a default value
SELECT name, COALESCE(phone, 'N/A') AS phone
FROM customers
WHERE phone IS NULL;
```

---

## 2. Sorting with ORDER BY DESC

Use `ORDER BY` with `DESC` to sort results in descending order.

```sql
SELECT * FROM table_name
ORDER BY column_name DESC;
```

### Examples

```sql
-- Sort numbers (highest first)
SELECT * FROM products ORDER BY price DESC;

-- Sort text (Z to A)
SELECT * FROM employees ORDER BY name DESC;

-- Sort dates (newest first)
SELECT * FROM orders ORDER BY order_date DESC;

-- Sort by multiple columns
SELECT * FROM employees
ORDER BY department ASC, salary DESC;
```

### ASC vs DESC

| Keyword | Meaning               | Numbers | Text  | Dates        |
| ------- | --------------------- | ------- | ----- | ------------ |
| `ASC`   | Ascending _(default)_ | 1 → 9   | A → Z | Oldest first |
| `DESC`  | Descending            | 9 → 1   | Z → A | Newest first |

> **Note:** `ASC` is the default, so `ORDER BY col` and `ORDER BY col ASC` are equivalent.

---

## 3. Correct SQL Clause Order

`ORDER BY` must always come **after** `WHERE`.

```sql
-- ✅ Correct
SELECT * FROM employees
WHERE department = 'Sales'
ORDER BY salary DESC;

-- ❌ Wrong - will throw an error
SELECT * FROM employees
ORDER BY salary DESC
WHERE department = 'Sales';
```

### Full SQL Clause Order

Always write clauses in this sequence:

```sql
SELECT        -- 1. columns to fetch
FROM          -- 2. table
WHERE         -- 3. filter rows
GROUP BY      -- 4. group rows
HAVING        -- 5. filter groups
ORDER BY      -- 6. sort results
LIMIT         -- 7. limit rows
```

> **Memory tip:** **S**ome **F**riends **W**ent **G**rocery **H**opping **O**n **L**adders
> → SELECT, FROM, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT

## 4. Filtering Unique Values with DISTINCT

Use `DISTINCT` to remove duplicate values from results.

```sql
SELECT DISTINCT column_name FROM table_name;
```

### Examples

```sql
-- Single column
SELECT DISTINCT department FROM employees;

-- Multiple columns (combination must be unique)
SELECT DISTINCT department, job_title FROM employees;

-- With WHERE clause
SELECT DISTINCT department FROM employees
WHERE status = 'active'
ORDER BY department ASC;
```

### Other Ways to Get Unique Values

```sql
-- GROUP BY with aggregation
SELECT department, COUNT(*) as total
FROM employees
GROUP BY department;

-- HAVING - only truly unique values (appear exactly once)
SELECT email FROM users
GROUP BY email
HAVING COUNT(*) = 1;

-- ROW_NUMBER - keep one row per duplicate group
SELECT * FROM (
    SELECT *,
    ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) as rn
    FROM users
) t
WHERE rn = 1;
```

### DISTINCT vs HAVING COUNT = 1

|                     | `DISTINCT`               | `HAVING COUNT(*) = 1`    |
| ------------------- | ------------------------ | ------------------------ |
| Shows duplicates    | ❌ Removed               | ❌ Excluded              |
| Shows unique values | ✅ Yes                   | ✅ Yes                   |
| Use case            | Remove dupes from result | Only truly unique values |

---

## Putting It All Together

A query combining all the concepts above:

```sql
SELECT
  e.name,
  COALESCE(e.phone, 'N/A') AS phone,
  e.salary,
  e.bonus
FROM employees e
WHERE e.manager_id IS NOT NULL        -- NULL check
  AND e.salary = e.bonus              -- column comparison
ORDER BY e.salary DESC;               -- sort descending (after WHERE)
```

---

_Reference guide covering: NULL handling, ORDER BY DESC, SQL clause order, and DISTINCT._
