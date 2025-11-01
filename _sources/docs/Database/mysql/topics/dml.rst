DML
===

Introduction:
    **DML (Data Manipulation Language)** commands in MySQL are used to **manipulate the data** stored in database tables.  
    They allow you to **insert**, **update**, **delete**, and **query** data.

    DML operations are usually executed within a **transaction**, meaning you can use
    `COMMIT` and `ROLLBACK` to save or undo changes.

**Common MySQL DML Commands:**

1. **INSERT**

The **INSERT** statement is used to add new records (rows) into a table.

**Syntax:**

.. code-block:: sql

    INSERT INTO table_name (column1, column2, ...)
    VALUES (value1, value2, ...);

**Example:**

.. code-block:: sql

    CREATE TABLE employees (
        emp_id INT PRIMARY KEY,
        name VARCHAR(50),
        job_title VARCHAR(50),
        salary DECIMAL(10,2),
        department VARCHAR(50)
    );

    INSERT INTO employees (emp_id, name, job_title, salary, department)
    VALUES (101, 'Alice', 'Developer', 60000.00, 'IT');

    INSERT INTO employees VALUES (102, 'Bob', 'Manager', 80000.00, 'HR');

**Key Points:**

- You can insert multiple rows at once:
- You can omit columns with default values.
  
.. code-block:: sql

    INSERT INTO employees (emp_id, name, job_title, salary, department)
    VALUES 
        (103, 'Charlie', 'Analyst', 50000.00, 'Finance'),
        (104, 'David', 'Developer', 62000.00, 'IT');

----------------------------------

2. **UPDATE**

The **UPDATE** statement modifies existing data in a table.

**Syntax:**

.. code-block:: sql

    UPDATE table_name
    SET column1 = value1, column2 = value2, ...
    WHERE condition;

**Example:**

.. code-block:: sql

    -- Increase salary of all IT department employees by 10%
    UPDATE employees
    SET salary = salary * 1.10
    WHERE department = 'IT';

    -- Change job title for one employee
    UPDATE employees
    SET job_title = 'Senior Developer'
    WHERE emp_id = 101;

**Key Points:**

- Always use a **WHERE** clause, otherwise all records will be updated.
- You can use arithmetic operations in updates (e.g., salary * 1.10).

----------------------------------

3. **DELETE**

The **DELETE** statement removes one or more records from a table.

**Syntax:**

.. code-block:: sql

    DELETE FROM table_name
    WHERE condition;

**Example:**

.. code-block:: sql

    -- Delete one employee by ID
    DELETE FROM employees
    WHERE emp_id = 104;

    -- Delete all employees in HR department
    DELETE FROM employees
    WHERE department = 'HR';

**Key Points:**

- If you omit the **WHERE** clause, **all rows** will be deleted.
- Deleted data can be recovered using `ROLLBACK` if within a transaction.

--------------------------------

4. **SELECT**

The **SELECT** statement retrieves data from one or more tables.

**Syntax:**

.. code-block:: sql

    SELECT column1, column2, ...
    FROM table_name
    WHERE condition
    ORDER BY column_name;

**Example:**

.. code-block:: sql

    -- Retrieve all employees
    SELECT * FROM employees;

    -- Select specific columns
    SELECT name, job_title, salary FROM employees;

    -- Filter by condition
    SELECT name, salary FROM employees WHERE department = 'IT';

    -- Sort results
    SELECT name, salary FROM employees ORDER BY salary DESC;

**Additional Examples:**

.. code-block:: sql

    -- Using aggregate functions
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department;

    -- Using WHERE with multiple conditions
    SELECT * FROM employees
    WHERE department = 'IT' AND salary > 60000;

**Key Points:**

- `SELECT` does not change data — it’s used for querying.
- Combine with clauses like `WHERE`, `ORDER BY`, `GROUP BY`, and `HAVING`.

-------------------------------

1. **Transaction Control**

DML operations can be grouped into transactions using:

.. code-block:: sql

    START TRANSACTION;
    INSERT INTO employees VALUES (105, 'Emma', 'HR Executive', 40000, 'HR');
    UPDATE employees SET salary = salary + 2000 WHERE emp_id = 105;
    COMMIT;

If something goes wrong, you can undo changes:

.. code-block:: sql

    ROLLBACK;

**Key Points:**

- Use `COMMIT` to make DML changes permanent.
- Use `ROLLBACK` to undo uncommitted changes.
- Use `SAVEPOINT` to create partial rollback checkpoints.

----------------------------------------

**Summary:**

+-------------------+---------------------------------------------+
| **Command**       | **Purpose**                                 |
+===================+=============================================+
| INSERT            | Add new rows into a table                   |
+-------------------+---------------------------------------------+
| UPDATE            | Modify existing rows                        |
+-------------------+---------------------------------------------+
| DELETE            | Remove rows from a table                    |
+-------------------+---------------------------------------------+
| SELECT            | Retrieve data from tables                   |
+-------------------+---------------------------------------------+

--------------------------------------

Conclusion:
    DML commands form the core of everyday MySQL operations.  
    They allow database users to **add, modify, query, and delete** data efficiently while maintaining transactional control using `COMMIT` and `ROLLBACK`.

    Proper use of DML ensures data consistency, accuracy, and reliability in MySQL databases.
