Normalization
=============

Introduction
  **Normalization** is a database design technique that organizes tables and their relationships to **reduce redundancy**, **avoid anomalies**, and **improve data integrity**.  
  It involves dividing large tables into smaller, related tables and linking them using relationships.

  The main goals of normalization are:

  - Eliminate duplicate data (redundancy)
  - Ensure data dependencies make sense
  - Make database design flexible and consistent

Normal Forms
  Normalization is achieved through several **Normal Forms (NF)**. Each normal form has specific requirements.

1. First Normal Form (1NF)

  **Definition:**  
  A table is in **1NF** if:

  - All columns contain **atomic (indivisible) values**
  - Each row is **unique**
  - No repeating groups or arrays

  **Example:**  

  Consider a table `students` storing multiple courses in a single column:

  .. list-table:: students Table
    :header-rows: 1
    :widths: 10 20 30

    * - student_id
      - student_name
      - courses
    * - 1
      - Alice
      - Math, Physics
    * - 2
      - Bob
      - Chemistry


  **Problems:**
    - `courses` column is not atomic.
    - Hard to query for a single course.

  **Solution (1NF):**  

  Split courses into separate rows:

  .. list-table:: Student-Course Table
    :header-rows: 1
    :widths: 10 20 20

    * - student_id
      - student_name
      - course
    * - 1
      - Alice
      - Math
    * - 1
      - Alice
      - Physics
    * - 2
      - Bob
      - Chemistry

  **SQL Example:**

  .. code-block:: sql

      CREATE TABLE students_1nf (
          student_id INT,
          student_name VARCHAR(50),
          course VARCHAR(50),
          PRIMARY KEY (student_id, course)
      );

2. Second Normal Form (2NF)
  
  **Definition:**  
  A table is in **2NF** if:

  - It is in **1NF**
  - Every non-key column is **fully functionally dependent** on the primary key
  - No partial dependency exists

  **Example:**  

  Table `enrollments`:

  .. list-table:: enrollments Table
    :header-rows: 1
    :widths: 10 10 20 20

    * - student_id
      - course_id
      - student_name
      - course_name
    * - 1
      - 101
      - Alice
      - Math
    * - 1
      - 102
      - Alice
      - Physics
    * - 2
      - 103
      - Bob
      - Chemistry

  **Problems:**
    - `student_name` depends only on `student_id` (partial dependency)
    - `course_name` depends only on `course_id`

  **Solution (2NF):**  

  Split into two tables:

  .. code-block:: sql

      CREATE TABLE students (
          student_id INT PRIMARY KEY,
          student_name VARCHAR(50)
      );

      CREATE TABLE courses (
          course_id INT PRIMARY KEY,
          course_name VARCHAR(50)
      );

      CREATE TABLE enrollments (
          student_id INT,
          course_id INT,
          PRIMARY KEY (student_id, course_id),
          FOREIGN KEY (student_id) REFERENCES students(student_id),
          FOREIGN KEY (course_id) REFERENCES courses(course_id)
      );

3. Third Normal Form (3NF)

  **Definition:**  
  A table is in **3NF** if:

  - It is in **2NF**
  - No **transitive dependency** exists
  - Non-key columns depend **only on the primary key**

  **Example:**  

  Table `employees`:

  .. list-table:: Employee-Department Table
    :header-rows: 1
    :widths: 10 20 10 20

    * - emp_id
      - emp_name
      - dept_id
      - dept_name
    * - 101
      - John
      - 10
      - HR
    * - 102
      - Alice
      - 20
      - IT

  **Problem:**

  - `dept_name` depends on `dept_id`, not directly on `emp_id` → transitive dependency

  **Solution (3NF):**  

  Split department info into a separate table:

  .. code-block:: sql

      CREATE TABLE employees (
          emp_id INT PRIMARY KEY,
          emp_name VARCHAR(50),
          dept_id INT,
          FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
      );

      CREATE TABLE departments (
          dept_id INT PRIMARY KEY,
          dept_name VARCHAR(50)
      );

Higher Normal Forms:
   - **BCNF (Boyce-Codd Normal Form)** → Handles certain edge cases of 3NF  
   - **4NF (Fourth Normal Form)** → Handles multi-valued dependencies  
   - **5NF (Fifth Normal Form)** → Handles join dependencies  

   These are usually required in **complex databases** to eliminate subtle redundancies.

Benefits of Normalization
   - Reduces **data redundancy**
   - Prevents **update, insert, and delete anomalies**
   - Ensures **data integrity**
   - Simplifies **maintenance and future modifications**

Drawbacks of Normalization
   - More **tables** and **joins** can reduce query performance
   - Sometimes **denormalization** is used for **reporting** or **read-heavy applications**

Summary
   - **Normalization** is organizing database tables to reduce redundancy and maintain integrity.
   - **1NF** → Atomic values, unique rows  
   - **2NF** → Remove partial dependencies  
   - **3NF** → Remove transitive dependencies  
   - Higher NFs → BCNF, 4NF, 5NF  
   - SQL examples show how to split tables and define **primary & foreign keys**.

