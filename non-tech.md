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

_Reference guide covering: NULL handling, ORDER BY DESC, and SQL clause order._
