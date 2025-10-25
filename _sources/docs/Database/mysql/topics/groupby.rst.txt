MySQL GROUP BY Clause
=====================

Introduction:
    The **GROUP BY** clause in MySQL is used to **group rows that have the same values** in specified columns into **summary rows**, such as for calculating aggregates like **COUNT(), SUM(), AVG(), MAX(), MIN()**.

    It is commonly used in **combination with aggregate functions** to summarize data.

Syntax:
    .. code-block:: sql

        SELECT column1, column2, ..., aggregate_function(column)
        FROM table_name
        WHERE conditions
        GROUP BY column1, column2, ...;

    - `aggregate_function()` can be one of: `COUNT()`, `SUM()`, `AVG()`, `MAX()`, `MIN()`.
    - Columns listed in `GROUP BY` determine **how the rows are grouped**.

Examples:

1. Group by Single Column:
    Calculate the **total salary per department**:

    .. code-block:: sql

        SELECT department_id, SUM(salary) AS total_salary
        FROM employees
        GROUP BY department_id;

    **Explanation:**  
       - All employees are grouped by `department_id`.  
       - `SUM(salary)` calculates the total salary in each department.

2. Group by Multiple Columns
    Calculate **average salary per department per job_title**:

    .. code-block:: sql

        SELECT department_id, job_title, AVG(salary) AS avg_salary
        FROM employees
        GROUP BY department_id, job_title;

    **Explanation:**  
       - Rows are grouped by `department_id` **and** `job_title`.  
       - `AVG(salary)` calculates the average salary within each group.

3. Using GROUP BY with COUNT()
    Count the number of employees in each department:

    .. code-block:: sql

        SELECT department_id, COUNT(*) AS emp_count
        FROM employees
        GROUP BY department_id;

4. Using GROUP BY with HAVING Clause
    The **HAVING** clause filters groups after aggregation (unlike `WHERE`, which filters rows before aggregation).

    Example: Departments with total salary greater than 13000:

    .. code-block:: sql

        SELECT department_id, SUM(salary) AS total_salary
        FROM employees
        GROUP BY department_id
        HAVING SUM(salary) > 13000;

5. Order Grouped Results
    You can combine **GROUP BY** with **ORDER BY**:

    .. code-block:: sql

        SELECT department_id, SUM(salary) AS total_salary
        FROM employees
        GROUP BY department_id
        ORDER BY total_salary DESC;

    - Groups are ordered by the calculated `total_salary` in descending order.

Notes
   - All selected columns that are **not aggregated** must be included in the `GROUP BY` clause.  
   - MySQL allows selecting non-aggregated columns **without grouping** if `ONLY_FULL_GROUP_BY` is disabled, but it may produce unpredictable results.  
   - `GROUP BY` is often combined with `HAVING`, `ORDER BY`, and aggregate functions to generate meaningful reports.

Conclusion
   - `GROUP BY` is used to **aggregate data into groups** based on column values.  
   - Commonly used with aggregate functions: `COUNT()`, `SUM()`, `AVG()`, `MAX()`, `MIN()`.  
   - Supports **single or multiple columns**, `HAVING` clause, and `ORDER BY` for sorted summaries.  
   - Essential for generating **reports and statistical summaries** in MySQL.
