DDL
===

Introduction:
    **DDL (Data Definition Language)** in MySQL is used to define and manage all database objects such as databases, tables, views, indexes, and constraints.

    DDL statements **define the structure** of the database rather than manipulating the data inside it.  
    Each DDL command causes an **implicit COMMIT** — meaning once executed, it cannot be rolled back.

Common DDL Commands:
    The major MySQL DDL commands are:

    - **CREATE** – Create database objects.
    - **ALTER** – Modify the structure of existing objects.
    - **DROP** – Delete objects.
    - **TRUNCATE** – Remove all data from a table (structure remains).
    - **RENAME** – Change the name of a database object.

    Let's explore each with examples.

1. CREATE:
    Used to create new databases or tables.

    **Example 1: Create a Database**

    .. code-block:: sql

        CREATE DATABASE company_db;

    **Example 2: Create a Table**

    .. code-block:: sql

        USE company_db;

        CREATE TABLE employees (
            emp_id      INT AUTO_INCREMENT PRIMARY KEY,
            first_name  VARCHAR(50) NOT NULL,
            last_name   VARCHAR(50),
            job_title   VARCHAR(50),
            salary      DECIMAL(10,2) CHECK (salary > 0),
            hire_date   DATE DEFAULT (CURRENT_DATE)
        );

    **Explanation:**
       - `AUTO_INCREMENT` generates unique IDs automatically.
       - `PRIMARY KEY` uniquely identifies each record.
       - `CHECK` ensures data validity.
       - `DEFAULT` sets a default value.

    **Example 3: Create a Table with Foreign Key**

    .. code-block:: sql

        CREATE TABLE departments (
            dept_id   INT PRIMARY KEY,
            dept_name VARCHAR(50)
        );

        CREATE TABLE employees (
            emp_id     INT PRIMARY KEY,
            emp_name   VARCHAR(50),
            dept_id    INT,
            FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
        );

    This establishes a **one-to-many** relationship between `departments` and `employees`.

2. ALTER
    Used to modify the structure of an existing table — such as adding, deleting, or renaming columns and constraints.

    **Example 1: Add a New Column**

    .. code-block:: sql

        ALTER TABLE employees ADD COLUMN email VARCHAR(100);

    **Example 2: Modify an Existing Column**

    .. code-block:: sql

        ALTER TABLE employees MODIFY COLUMN salary DECIMAL(12,2);

    **Example 3: Rename a Column**

    .. code-block:: sql

        ALTER TABLE employees CHANGE COLUMN job_title designation VARCHAR(50);

    **Example 4: Add a Foreign Key**

    .. code-block:: sql

        ALTER TABLE employees
        ADD CONSTRAINT fk_dept FOREIGN KEY (dept_id) REFERENCES departments(dept_id);

    **Example 5: Drop a Column**

    .. code-block:: sql

        ALTER TABLE employees DROP COLUMN email;

3. DROP
    Used to permanently delete a database or table.  
    **Warning:** This operation cannot be rolled back.

    **Example 1: Drop a Table**

    .. code-block:: sql

        DROP TABLE employees;

    **Example 2: Drop a Database**

    .. code-block:: sql

        DROP DATABASE company_db;

    **Example 3: Drop a Constraint**

    .. code-block:: sql

        ALTER TABLE employees DROP FOREIGN KEY fk_dept;

4. TRUNCATE:
    Used to remove **all data** from a table while keeping the table structure intact.

    **Example:**

    .. code-block:: sql

        TRUNCATE TABLE employees;

    **Explanation:**
       - It’s faster than `DELETE FROM employees;`
       - Auto-increment counters are reset.
       - Cannot be rolled back.

5. RENAME
    Used to change the name of a table or database.

    **Example 1: Rename a Table**

    .. code-block:: sql

        RENAME TABLE employees TO emp_master;

    **Example 2: Rename a Database** *(MySQL 8+ manual operation)*

    To rename a database, export it, create a new one, and import data back.

    **Example Steps:**

    .. code-block:: bash

        mysqldump old_db > old_db.sql
        mysqladmin create new_db
        mysql new_db < old_db.sql
        mysqladmin drop old_db

6. CREATE INDEX
    Used to improve query performance by creating indexes on columns.

    **Example 1: Create a Single-Column Index**

    .. code-block:: sql

        CREATE INDEX idx_emp_name ON employees(emp_name);

    **Example 2: Create a Composite Index**

    .. code-block:: sql

        CREATE INDEX idx_name_salary ON employees(emp_name, salary);

    **Example 3: Drop an Index**

    .. code-block:: sql

        DROP INDEX idx_emp_name ON employees;

7. CREATE VIEW
    A **view** is a virtual table based on a query result.

    **Example:**

    .. code-block:: sql

        CREATE VIEW high_salary_employees AS
        SELECT emp_name, salary
        FROM employees
        WHERE salary > 50000;

    **Example to Query the View:**

    .. code-block:: sql

        SELECT * FROM high_salary_employees;

    **Example to Drop a View:**

    .. code-block:: sql

        DROP VIEW high_salary_employees;

**Summary of DDL Commands**

+-------------+-------------------------------------------+----------------------------+
| **Command** | **Description**                           | **Rollback Possible?**     |
+=============+===========================================+============================+
| CREATE      | Creates database objects                  | No                         |
+-------------+-------------------------------------------+----------------------------+
| ALTER       | Modifies structure of database objects    | No                         |
+-------------+-------------------------------------------+----------------------------+
| DROP        | Deletes objects permanently               | No                         |
+-------------+-------------------------------------------+----------------------------+
| TRUNCATE    | Deletes all data, retains structure       | No                         |
+-------------+-------------------------------------------+----------------------------+
| RENAME      | Changes the name of an object             | No                         |
+-------------+-------------------------------------------+----------------------------+
| CREATE INDEX| Improves performance using indexes        | No                         |
+-------------+-------------------------------------------+----------------------------+
| CREATE VIEW | Creates a virtual table (view)            | No                         |
+-------------+-------------------------------------------+----------------------------+

Conclusion:
    MySQL DDL provides the foundational commands for defining and managing database structures.  
    These commands help in creating efficient and well-structured databases that ensure data integrity and optimized performance.
