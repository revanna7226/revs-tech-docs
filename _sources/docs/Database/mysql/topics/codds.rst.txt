Codd's Rules for Relational Databases
======================================

Introduction
  **Codd's 12 rules** were defined by Dr. Edgar F. Codd in 1985 to define what constitutes a **relational database system (RDBMS)**.  
  These rules provide a framework for evaluating **database management systems (DBMS)** and ensuring **data integrity, consistency, and relational principles**.

**Codd's Rules**

Rule 0: Foundation Rule
  > "For any system that is advertised as a relational database management system, it must manage its data using its relational capabilities."

  - The system must support **all data as tables (relations)**.
  - Example:

  .. code-block:: sql

      CREATE TABLE employees (
          emp_id INT PRIMARY KEY,
          emp_name VARCHAR(50),
          department VARCHAR(50)
      );

  All operations (SELECT, INSERT, UPDATE, DELETE) operate on **relations (tables)**.

Rule 1: The Information Rule
  > "All information in a relational database is represented explicitly at the logical level and in exactly one way – by values in tables."

  - All data is stored in **rows and columns**.
  - Example:

  .. code-block:: sql

      -- Each row represents an employee
      INSERT INTO employees (emp_id, emp_name, department)
      VALUES (101, 'John', 'HR');

  - No hidden pointers or proprietary storage methods.

Rule 2: The Guaranteed Access Rule
  > "Each data item (atomic value) is guaranteed to be accessible by using a combination of table name, primary key, and column name."

  - Every value can be uniquely accessed.
  - Example:

  .. code-block:: sql

      SELECT emp_name
      FROM employees
      WHERE emp_id = 101;

  - Here, `emp_id` ensures **unique row access**, and `emp_name` is the column.

Rule 3: Systematic Treatment of Null Values
  > "Null values must be supported for missing or inapplicable information in a systematic way, independent of data type."

  - Nulls represent **unknown or missing data**.
  - Example:

  .. code-block:: sql

      INSERT INTO employees (emp_id, emp_name, department)
      VALUES (102, 'Alice', NULL);

  - Queries can handle nulls:

  .. code-block:: sql

      SELECT * FROM employees WHERE department IS NULL;

Rule 4: Dynamic Online Catalog Based on the Relational Model
  > "The database must provide a relational catalog (data dictionary) accessible through the same relational language."

  - Example: Accessing schema info using SQL:

  .. code-block:: sql

      SHOW TABLES;
      DESCRIBE employees;

  - The catalog itself is represented **as tables**.

Rule 5: Comprehensive Data Sublanguage Rule
  > "The database must support at least one relational language that allows data definition, manipulation, and transaction control."

  - MySQL’s SQL satisfies this:
    
  .. code-block:: sql

      -- Data Definition Language (DDL)
      CREATE TABLE departments (
          dept_id INT PRIMARY KEY,
          dept_name VARCHAR(50)
      );

      -- Data Manipulation Language (DML)
      INSERT INTO departments VALUES (10, 'HR');

      -- Query
      SELECT * FROM departments;

Rule 6: View Updating Rule
  > "All views that are theoretically updatable must be updatable by the system."

  - Example: Creating a view

  .. code-block:: sql

      CREATE VIEW hr_employees AS
      SELECT emp_id, emp_name
      FROM employees
      WHERE department = 'HR';

      -- Updatable view
      UPDATE hr_employees
      SET emp_name = 'John Doe'
      WHERE emp_id = 101;

  - MySQL supports updatable views under certain conditions.

Rule 7: High-Level Insert, Update, Delete
  > "The system must support set-at-a-time insert, update, and delete operations, not just one row at a time."

  - Example: Update multiple rows at once

  .. code-block:: sql

      UPDATE employees
      SET department = 'IT'
      WHERE department = 'HR';

Rule 8: Physical Data Independence
  > "Application programs and terminal activities remain functionally unaffected when any changes are made in either storage representations or access methods."

  - Example: Changing table storage engine:

  .. code-block:: sql

      ALTER TABLE employees ENGINE = InnoDB;

  - Queries remain **unchanged**, demonstrating **data independence**.

Rule 9: Logical Data Independence
  > "Changes to logical structure (like adding a column) should not require changes to application programs."

  - Example: Adding a column:

  .. code-block:: sql

      ALTER TABLE employees ADD COLUMN email VARCHAR(100);

  - Applications querying existing columns continue to work.

Rule 10: Integrity Independence
  > "Integrity constraints must be stored in the catalog and managed by the DBMS, not in the application."

  - Example:

  .. code-block:: sql

      CREATE TABLE employees (
          emp_id INT PRIMARY KEY,
          emp_name VARCHAR(50) NOT NULL,
          salary DECIMAL(10,2) CHECK (salary > 0)
      );

  - DBMS enforces **constraints**, not the application code.

Rule 11: Distribution Independence
  > "The DBMS should ensure users do not have to change queries when data is distributed across multiple locations."

  - Example: MySQL Cluster or replication allows queries without worrying about **physical location**:

  .. code-block:: sql

      SELECT * FROM employees WHERE department = 'HR';

  - Same query works even if data is **distributed**.

Rule 12: Non-Subversion Rule
  > "No lower-level language should be able to bypass the integrity rules enforced by the relational system."

  - Example:

      - MySQL’s storage engine or internal API cannot bypass **primary key, unique, or check constraints**.

Conclusion
  Codd's 12 rules provide a **theoretical foundation for relational databases**.  

  - Ensure **data integrity** and **consistency**.
  - Promote **data independence** (physical & logical).
  - Guide **RDBMS vendors** in designing relational systems.
  - SQL and modern RDBMS like **MySQL, PostgreSQL, Oracle, SQL Server** implement most of these rules.

