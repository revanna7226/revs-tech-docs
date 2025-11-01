Correlated Subqueries
===========================

Introduction
    A **correlated subquery** is a subquery that **depends on the outer query** for its values.  
    Unlike a regular subquery (which can be executed independently), a correlated subquery is **evaluated once for each row** processed by the outer query.

    In other words:

    - The **inner query** references columns from the **outer query**.
    - It is often used with operators like `=`, `>`, `<`, `IN`, `EXISTS`.

Syntax
    .. code-block:: sql

        SELECT column1, column2, ...
        FROM outer_table AS outer
        WHERE column_name operator
            (SELECT column_name
            FROM inner_table AS inner
            WHERE inner.some_column = outer.some_column);

    - `outer.some_column` is referenced inside the subquery.
    - The subquery cannot run independently because it uses the outer query’s column.

Example Table
    Suppose we have two tables: `employees` and `departments`.

    .. list-table:: Employees Table
      :header-rows: 1
      :widths: 10 20 15 10

      * - emp_id
        - emp_name
        - salary
        - dept_id
      * - 101
        - John
        - 5000
        - 10
      * - 102
        - Alice
        - 6000
        - 20
      * - 103
        - Bob
        - 4500
        - 10
      * - 104
        - Carol
        - 7000
        - 20
      * - 105
        - Dave
        - 5500
        - 30

    .. list-table:: departments Table
      :header-rows: 1
      :widths: 10 20

      * - dept_id
        - dept_name
      * - 10
        - IT
      * - 20
        - HR
      * - 30
        - Sales

Example 1: Find Employees Earning More Than Department Average
    We want to find employees whose salary is greater than the **average salary of their department**.

    .. code-block:: sql

        SELECT emp_name, salary, dept_id
        FROM employees e
        WHERE salary > (
            SELECT AVG(salary)
            FROM employees
            WHERE dept_id = e.dept_id
        );

    **Explanation:**
      - The **subquery** calculates the average salary of the department corresponding to each row in the outer query.
      - `e.dept_id` refers to the **outer query**.
      - For each employee, the subquery is executed using their department ID.

Example 2: Find Employees with Maximum Salary in Their Department
    .. code-block:: sql

        SELECT emp_name, salary, dept_id
        FROM employees e
        WHERE salary = (
            SELECT MAX(salary)
            FROM employees
            WHERE dept_id = e.dept_id
        );

    **Explanation:**
      - The subquery finds the **maximum salary in the employee's department**.
      - Only employees matching that maximum salary are returned.

Example 3: Using Correlated Subquery with EXISTS
  Find departments having at least one employee earning more than 6000.

  .. code-block:: sql

      SELECT dept_name
      FROM departments d
      WHERE EXISTS (
          SELECT 1
          FROM employees e
          WHERE e.dept_id = d.dept_id
            AND e.salary > 6000
      );

  **Explanation:**
    - `EXISTS` returns TRUE if the subquery returns any row.
    - The subquery references the **outer query’s `dept_id`**.
    - Only departments with at least one employee earning > 6000 are selected.

Example 4: Using Correlated Subquery in SELECT Clause
  Calculate the **department average salary** for each employee:

  .. code-block:: sql

      SELECT emp_name, salary,
            (SELECT AVG(salary)
              FROM employees
              WHERE dept_id = e.dept_id) AS dept_avg_salary
      FROM employees e;

  **Output (sample)**

  .. list-table:: Employee Salary with Department Average
    :header-rows: 1
    :widths: 20 15 20

    * - emp_name
      - salary
      - dept_avg_salary
    * - John
      - 5000
      - 4750
    * - Bob
      - 4500
      - 4750
    * - Alice
      - 6000
      - 6500
    * - Carol
      - 7000
      - 6500
    * - Dave
      - 5500
      - 5500

  - Each employee row includes the average salary of their department, computed dynamically.

Notes
  - Correlated subqueries are **less efficient** than joins on large tables because the subquery executes **once per outer row**.
  - Use **EXPLAIN** to check performance and consider **JOINs** for optimization.
  - Common use cases:
    - Comparing a row with group-based aggregates.
    - Finding max/min per category.
    - Conditional checks with EXISTS/NOT EXISTS.

Summary
  - A **correlated subquery** references the outer query.
  - Evaluated **once per row** in the outer query.
  - Can be used in **WHERE**, **SELECT**, and **EXISTS** clauses.
  - Powerful for **row-level comparisons**, but consider performance for large datasets.