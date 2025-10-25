MySQL Subqueries
================

Introduction:
    A **subquery** (also called an **inner query** or **nested query**) is a SQL query **embedded within another query**.  
    Subqueries are used to perform operations whose results are needed by the **outer query**.  

    Subqueries help simplify complex queries and avoid multiple queries in application code.

Types of Subqueries:
    1. **Single-row subquery**  
    Returns **one row** and one column. Often used with comparison operators: =, >, <, >=, <=, <>.

    2. **Multiple-row subquery**  
    Returns **multiple rows** but one column. Often used with operators: IN, ANY, ALL.

    3. **Multiple-column subquery**  
    Returns **one or more columns**. Often used with comparison operators or tuple comparison.

    4. **Correlated subquery**  
    The inner query depends on values from the outer query. Executed **once for each row** of the outer query.

    5. **Nested subquery**  
    Subqueries can be **nested inside another subquery**.

Syntax:
    Basic Subquery:

    .. code-block:: sql

        SELECT column1, column2
        FROM table1
        WHERE column3 operator
            (SELECT column4
            FROM table2
            WHERE condition);

**Examples**

1. Single-row Subquery
    Find employees whose salary is **equal to the highest salary**:

    .. code-block:: sql

        SELECT emp_name, salary
        FROM employees
        WHERE salary = (
            SELECT MAX(salary)
            FROM employees
        );

    **Explanation:**
       - Inner query returns the **maximum salary**.
       - Outer query returns employees with that salary.

2. Multiple-row Subquery
    Find employees in departments **10 or 20** using a subquery:

    .. code-block:: sql

        SELECT emp_name, department_id
        FROM employees
        WHERE department_id IN (
            SELECT dept_id
            FROM departments
            WHERE dept_id IN (10, 20)
        );

    **Explanation:**
       - Inner query returns multiple department IDs.
       - Outer query selects employees matching those IDs.

3. Multiple-column Subquery
    Find employees whose **salary and department match** the maximum salary in their department:

    .. code-block:: sql

        SELECT emp_name, salary, department_id
        FROM employees e1
        WHERE (salary, department_id) = (
            SELECT MAX(salary), department_id
            FROM employees e2
            WHERE e1.department_id = e2.department_id
        );

    **Explanation:**
       - Inner query returns a tuple `(max_salary, department_id)`.
       - Outer query selects employees matching that tuple.

4. Correlated Subquery
    Find employees whose salary is **greater than the average salary in their department**:

    .. code-block:: sql

        SELECT emp_name, salary, department_id
        FROM employees e1
        WHERE salary > (
            SELECT AVG(salary)
            FROM employees e2
            WHERE e1.department_id = e2.department_id
        );

    **Explanation:**
       - Inner query references **outer query** column `e1.department_id`.
       - Evaluated **once per outer row**.

5. Subquery in FROM Clause
    Subqueries can be used as **derived tables** in the FROM clause:

    .. code-block:: sql

        SELECT department_id, MAX(salary) AS max_salary
        FROM (
            SELECT department_id, salary
            FROM employees
        ) AS dept_salaries
        GROUP BY department_id;

    **Explanation:**
       - Inner query returns a temporary table.
       - Outer query performs aggregation on it.

6. Subquery in SELECT Clause
    Subqueries can also be in the SELECT clause to calculate values per row:

    .. code-block:: sql

        SELECT emp_name,
            salary,
            (SELECT AVG(salary)
                FROM employees
                WHERE department_id = e.department_id) AS dept_avg_salary
        FROM employees e;

    **Explanation:**
       - Calculates department average salary for each employee.
       - Useful for **per-row calculations**.

Important Notes
   - Subqueries **must be enclosed in parentheses** `()`.
   - Avoid using correlated subqueries on **very large tables** — performance may be slow.
   - You can combine subqueries with **JOINs** for better performance.
   - Subqueries can return **single or multiple rows/columns**.
   - Operators like `IN`, `NOT IN`, `EXISTS`, `ALL`, `ANY`, `SOME` are commonly used with subqueries.

Example Summary
    .. code-block:: sql

        -- 1. Single-row
        SELECT emp_name FROM employees
        WHERE salary = (SELECT MAX(salary) FROM employees);

        -- 2. Multiple-row
        SELECT emp_name FROM employees
        WHERE department_id IN (SELECT dept_id FROM departments WHERE location='NY');

        -- 3. Correlated
        SELECT emp_name FROM employees e1
        WHERE salary > (SELECT AVG(salary) FROM employees e2 WHERE e1.department_id=e2.department_id);

        -- 4. Subquery in SELECT
        SELECT emp_name,
            (SELECT AVG(salary) FROM employees e2 WHERE e1.department_id=e2.department_id) AS dept_avg
        FROM employees e1;

Conclusion
   - Subqueries are a **powerful tool** in MySQL for complex queries.
   - They allow queries to **depend on the result of other queries**.
   - Can be used in **WHERE, FROM, SELECT** clauses.
   - Understanding **correlated vs non-correlated subqueries** is essential for performance optimization.
