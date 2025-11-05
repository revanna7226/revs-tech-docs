# MySQL JOINS

## Introduction

A **JOIN** in MySQL is used to combine rows from **two or more tables** based on a related column between them.

Joins allow querying data across multiple tables efficiently.

## Types of Joins

MySQL supports several types of joins:

1. **INNER JOIN**
2. **LEFT JOIN (LEFT OUTER JOIN)**
3. **RIGHT JOIN (RIGHT OUTER JOIN)**
4. **FULL OUTER JOIN** _(not directly supported in MySQL)_
5. **CROSS JOIN**
6. **SELF JOIN**

### 1. INNER JOIN

**Description:** Returns rows that have matching values in both tables.

```{code-block} sql
:caption: Syntax
SELECT columns
FROM table1
INNER JOIN table2
ON table1.column = table2.column;
```

**Example:** Suppose we have two tables:

```{list-table} Employees
:header-rows: 1
:widths: 10 20 10

- - emp_id
  - emp_name
  - dept_id
- - 101
  - John
  - 10
- - 102
  - Alice
  - 20
- - 103
  - Bob
  - 10
```

```{list-table} Departments
:header-rows: 1
:widths: 10 20

- - dept_id
  - dept_name
- - 10
  - IT
- - 20
  - HR
- - 30
  - Finance
```

```{code-block} sql
SELECT e.emp_name, d.dept_name
FROM employees e
INNER JOIN departments d
ON e.dept_id = d.dept_id;
```

**Output:**

```{list-table} Employee Department Join
:header-rows: 1
:widths: 20 20

- - emp_name
  - dept_name
- - John
  - IT
- - Bob
  - HR
- - Alice
  - IT
```

### 2. LEFT JOIN (LEFT OUTER JOIN)

**Description:** Returns **all rows from the left table**, and the matched rows from the right table.
If there is no match, the result is **NULL** on the right side.

```{code-block} sql
:caption: Syntax
SELECT columns
FROM table1
LEFT JOIN table2
ON table1.column = table2.column;
```

**Example:**

```sql
SELECT e.emp_name, d.dept_name
FROM employees e
LEFT JOIN departments d
ON e.dept_id = d.dept_id;
```

:::{tip}
If an employee belongs to a department not in `departments`, `dept_name` will be `NULL`.
:::

### 3. RIGHT JOIN (RIGHT OUTER JOIN)

**Description:**
Returns **all rows from the right table**, and the matched rows from the left table.
If there is no match, the result is **NULL** on the left side.

**Syntax:**

```{code-block} sql
:caption: Syntax
SELECT columns
FROM table1
RIGHT JOIN table2
ON table1.column = table2.column;
```

**Example:**

```sql
SELECT e.emp_name, d.dept_name
FROM employees e
RIGHT JOIN departments d
ON e.dept_id = d.dept_id;
```

:::{tip}
Departments without employees will still appear with `NULL` for `emp_name`.
:::

### 4. FULL OUTER JOIN

**Description:** Returns **all rows from both tables**, with `NULL` where there is no match.

:::{tip}
**MySQL does not support FULL OUTER JOIN directly**, but it can be simulated using `UNION` of LEFT and RIGHT JOIN.
:::

**Example:**

```sql
    SELECT e.emp_name, d.dept_name
    FROM employees e
    LEFT JOIN departments d
    ON e.dept_id = d.dept_id
    UNION
    SELECT e.emp_name, d.dept_name
    FROM employees e
    RIGHT JOIN departments d
    ON e.dept_id = d.dept_id;
```

### 5. CROSS JOIN

**Description:** Returns the **Cartesian product** of two tables — every row from the first table combined with every row from the second table.

```{code-block} sql
:caption: Syntax
    SELECT columns
    FROM table1
    CROSS JOIN table2;
```

**Example:**

```sql
    SELECT e.emp_name, d.dept_name
    FROM employees e
    CROSS JOIN departments d;
```

:::{warning}
Total rows = `number of rows in employees * number of rows in departments`.
:::

### 6. SELF JOIN

**Description:**
A **self join** is when a table is joined with itself.
It is useful to compare rows within the same table.

```{code-block} sql
SELECT a.column1, b.column2
FROM table a, table b
WHERE a.column = b.column;
```

**Example:**

Suppose `employees` table has a `manager_id` column referring to `emp_id`:

```{list-table} Employees with Manager
:header-rows: 1
:widths: 10 20 10 10

- - emp_id
  - emp_name
  - dept_id
  - manager_id
- - 101
  - John
  - 10
  - 103
- - 102
  - Alice
  - 20
  - 103
- - 103
  - Bob
  - 10
  - NULL
```

```sql

    SELECT e1.emp_name AS Employee, e2.emp_name AS Manager
    FROM employees e1
    LEFT JOIN employees e2
    ON e1.manager_id = e2.emp_id;
```

**Output:**

```{list-table} Employee and Manager
:header-rows: 1
:widths: 20 20

- - Employee
  - Manager
- - John
  - Bob
- - Alice
  - Bob
- - Bob
  - NULL
```

## Conclusion

- **INNER JOIN** → Only matching rows from both tables.
- **LEFT JOIN** → All rows from left table, matched rows from right.
- **RIGHT JOIN** → All rows from right table, matched rows from left.
- **FULL OUTER JOIN** → All rows from both tables (simulated with UNION).
- **CROSS JOIN** → Cartesian product of two tables.
- **SELF JOIN** → Table joined with itself for hierarchical or comparative data.
- JOINS are essential for querying **related data** efficiently across multiple tables.
