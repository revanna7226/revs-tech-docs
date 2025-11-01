SQL Constraints
===============

Introduction:
    **SQL Constraints** are rules enforced on data in tables to ensure accuracy, consistency, and reliability.  
    Constraints limit the type of data that can go into a table, maintaining data integrity in the database.

    Constraints can be applied to:
    
    - A **column** (Column-level constraint)
    - The **whole table** (Table-level constraint)

Types of SQL Constraints:

1. **NOT NULL Constraint**

Ensures that a column cannot have a NULL value.

**Example:**

.. code-block:: sql

    CREATE TABLE Employees (
        emp_id INT NOT NULL,
        first_name VARCHAR(50) NOT NULL,
        last_name VARCHAR(50),
        salary DECIMAL(10, 2)
    );

    INSERT INTO Employees (emp_id, first_name, last_name, salary)
    VALUES (1, 'John', 'Doe', 50000.00);

    -- The following statement will fail because first_name cannot be NULL
    INSERT INTO Employees (emp_id, first_name, salary)
    VALUES (2, NULL, 40000.00);

2. **UNIQUE Constraint**

Ensures that all values in a column (or group of columns) are unique.

**Example:**

.. code-block:: sql

    CREATE TABLE Departments (
        dept_id INT PRIMARY KEY,
        dept_name VARCHAR(100) UNIQUE
    );

    INSERT INTO Departments (dept_id, dept_name)
    VALUES (1, 'HR');

    -- This will fail because 'HR' already exists
    INSERT INTO Departments (dept_id, dept_name)
    VALUES (2, 'HR');

3. **PRIMARY KEY Constraint**

Uniquely identifies each record in a table.  
It is a combination of **NOT NULL** and **UNIQUE** constraints.

**Example:**

.. code-block:: sql

    CREATE TABLE Students (
        student_id INT PRIMARY KEY,
        name VARCHAR(100),
        age INT
    );

    INSERT INTO Students (student_id, name, age)
    VALUES (1, 'Alice', 22);

    -- This will fail (duplicate primary key)
    INSERT INTO Students (student_id, name, age)
    VALUES (1, 'Bob', 24);

4. **FOREIGN KEY Constraint**

Enforces a relationship between two tables by referencing the **PRIMARY KEY** of another table.

**Example:**

.. code-block:: sql

    CREATE TABLE Courses (
        course_id INT PRIMARY KEY,
        course_name VARCHAR(100)
    );

    CREATE TABLE Enrollments (
        enroll_id INT PRIMARY KEY,
        student_id INT,
        course_id INT,
        FOREIGN KEY (course_id) REFERENCES Courses(course_id)
    );

    -- Insert valid record
    INSERT INTO Courses VALUES (101, 'Database Systems');
    INSERT INTO Enrollments VALUES (1, 1, 101);

    -- This will fail because course_id 999 does not exist in Courses table
    INSERT INTO Enrollments VALUES (2, 2, 999);

5. **CHECK Constraint**

Ensures that all values in a column satisfy a specific condition.

**Example:**

.. code-block:: sql

    CREATE TABLE Employees (
        emp_id INT PRIMARY KEY,
        name VARCHAR(50),
        age INT CHECK (age >= 18),
        salary DECIMAL(10,2) CHECK (salary > 0)
    );

    INSERT INTO Employees VALUES (1, 'John', 25, 35000.00);

    -- This will fail because age < 18
    INSERT INTO Employees VALUES (2, 'Alex', 15, 25000.00);

6. **DEFAULT Constraint**

Provides a default value for a column when no value is specified.

**Example:**

.. code-block:: sql

    CREATE TABLE Orders (
        order_id INT PRIMARY KEY,
        order_date DATE DEFAULT CURRENT_DATE,
        status VARCHAR(20) DEFAULT 'PENDING'
    );

    INSERT INTO Orders (order_id)
    VALUES (1001);

    -- The row will automatically have order_date as today's date and status as 'PENDING'

7. **AUTO INCREMENT (IDENTITY)**

Automatically generates a unique number when a new record is inserted.  
This is often used with the **PRIMARY KEY** constraint.

**Example:**

.. code-block:: sql

    CREATE TABLE Products (
        product_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
        product_name VARCHAR(100),
        price DECIMAL(10,2)
    );

    INSERT INTO Products (product_name, price)
    VALUES ('Laptop', 85000.00),
           ('Mouse', 1200.00);

    -- product_id will be generated automatically

8. **COMPOSITE KEY Constraint**

A primary key made up of two or more columns.  
Used when a single column cannot uniquely identify a record.

**Example:**

.. code-block:: sql

    CREATE TABLE StudentCourses (
        student_id INT,
        course_id INT,
        PRIMARY KEY (student_id, course_id)
    );

    INSERT INTO StudentCourses VALUES (1, 101);
    -- The following will fail because (1, 101) already exists
    INSERT INTO StudentCourses VALUES (1, 101);

**Summary of Constraints:**
    +----------------+--------------------------------------------+
    | **Constraint** | **Description**                            |
    +================+============================================+
    | NOT NULL       | Ensures column cannot have NULL values     |
    +----------------+--------------------------------------------+
    | UNIQUE         | Ensures all values are unique              |
    +----------------+--------------------------------------------+
    | PRIMARY KEY    | Unique + Not Null                          |
    +----------------+--------------------------------------------+
    | FOREIGN KEY    | Maintains referential integrity            |
    +----------------+--------------------------------------------+
    | CHECK          | Restricts values based on a condition      |
    +----------------+--------------------------------------------+
    | DEFAULT        | Assigns a default value                    |
    +----------------+--------------------------------------------+
    | AUTO INCREMENT | Automatically generates unique IDs         |
    +----------------+--------------------------------------------+
    | COMPOSITE KEY  | Combines multiple columns as a primary key |
    +----------------+--------------------------------------------+

Conclusion:
    SQL Constraints play a crucial role in ensuring data integrity, reliability, and accuracy.  
    They help enforce rules at the database level, preventing invalid data entry and maintaining consistent relationships between tables.
