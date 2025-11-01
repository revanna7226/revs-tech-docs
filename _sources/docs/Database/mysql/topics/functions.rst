MySQL Functions
===============

Introduction:
    In MySQL, **functions** are predefined operations that can be used to perform
    calculations, manipulate data, and return specific results.  
    
    They are often used in **SELECT**, **WHERE**, **ORDER BY**, and other SQL clauses.

    MySQL functions can be categorized into different types based on their purpose,
    such as: 

      - **String Functions**, 
      - **Numeric Functions**, 
      - **Date and Time Functions**, and 
      - **Aggregate Functions**.

**Types of MySQL Functions:**

1. String Functions:
    String functions are used to manipulate or extract data from string values.

    **Examples**

    .. code-block:: sql

        -- Convert text to uppercase
        SELECT UPPER('mysql') AS UpperCaseResult;

        -- Convert text to lowercase
        SELECT LOWER('MYSQL') AS LowerCaseResult;

        -- Concatenate two strings
        SELECT CONCAT('My', 'SQL') AS FullString;

        -- Get substring from position 2 of length 3
        SELECT SUBSTRING('Database', 2, 3) AS SubStringResult;

        -- Find the length of a string
        SELECT LENGTH('Hello World') AS StringLength;

        -- Replace substring within a string
        SELECT REPLACE('I love PHP', 'PHP', 'MySQL') AS ReplacedString;

        -- 

    **Output Example**

        .. list-table::
          :header-rows: 1
          :widths: 20 80

          * - Function
            - Result
          * - UPPER
            - MYSQL
          * - LOWER
            - mysql
          * - CONCAT
            - MySQL
          * - SUBSTRING
            - ata
          * - LENGTH
            - 11
          * - REPLACE
            - I love MySQL

2. Numeric Functions
    Numeric functions perform mathematical calculations on numeric data.

    **Examples**

    .. code-block:: sql

        -- Get absolute value
        SELECT ABS(-10) AS AbsoluteValue;

        -- Round number to 2 decimal places
        SELECT ROUND(123.4567, 2) AS RoundedValue;

        -- Get square root
        SELECT SQRT(25) AS SquareRoot;

        -- Get maximum and minimum of two numbers
        SELECT GREATEST(10, 20, 30) AS MaxValue, LEAST(10, 20, 30) AS MinValue;

        -- Power of a number
        SELECT POW(2, 3) AS PowerValue;

        -- Random number between 0 and 1
        SELECT RAND() AS RandomValue;

    **Output Example**

        .. list-table::
          :header-rows: 1
          :widths: 20 80

          * - Function
            - Result
          * - ABS
            - 10
          * - ROUND
            - 123.46
          * - SQRT
            - 5
          * - GREATEST
            - 30
          * - LEAST
            - 10
          * - POW
            - 8

3. Date and Time Functions
    Date functions allow you to extract or manipulate date and time data.

    **Examples**

    .. code-block:: sql

        -- Current date
        SELECT CURDATE() AS CurrentDate;

        -- Current time
        SELECT CURTIME() AS CurrentTime;

        -- Current date and time
        SELECT NOW() AS CurrentDateTime;

        -- Extract year, month, and day
        SELECT YEAR(NOW()) AS Year, MONTH(NOW()) AS Month, DAY(NOW()) AS Day;

        -- Add 10 days to current date
        SELECT DATE_ADD(CURDATE(), INTERVAL 10 DAY) AS AddedDate;

        -- Subtract 2 months from current date
        SELECT DATE_SUB(CURDATE(), INTERVAL 2 MONTH) AS SubtractedDate;

        -- Calculate difference between two dates
        SELECT DATEDIFF('2025-12-31', '2025-01-01') AS DateDifference;

    **Output Example**

        .. list-table::
          :header-rows: 1
          :widths: 30 70

          * - Function
            - Result
          * - CURDATE()
            - 2025-10-25
          * - CURTIME()
            - 11:25:40
          * - NOW()
            - 2025-10-25 11:25:40
          * - DATE_ADD()
            - 2025-11-04
          * - DATE_SUB()
            - 2025-08-25
          * - DATEDIFF()
            - 364

4. Aggregate Functions
    Aggregate functions operate on **a group of rows** and return a **single value**.

    They are commonly used with the **GROUP BY** clause.

    **Examples**

    .. code-block:: sql

        -- Total salary of all employees
        SELECT SUM(salary) AS TotalSalary FROM employees;

        -- Average salary
        SELECT AVG(salary) AS AverageSalary FROM employees;

        -- Highest salary
        SELECT MAX(salary) AS HighestSalary FROM employees;

        -- Lowest salary
        SELECT MIN(salary) AS LowestSalary FROM employees;

        -- Total number of employees
        SELECT COUNT(*) AS EmployeeCount FROM employees;

    **Output Example**

        .. list-table::
          :header-rows: 1
          :widths: 20 80

          * - Function
            - Result
          * - SUM
            - 98000.00
          * - AVG
            - 4900.00
          * - MAX
            - 9000.00
          * - MIN
            - 1200.00
          * - COUNT
            - 20

5. Control Flow Functions
    These functions perform conditional logic in queries.

    **Examples**

    .. code-block:: sql

        -- Return 'High' if salary > 5000, else 'Low'
        SELECT emp_name,
              IF(salary > 5000, 'High', 'Low') AS SalaryLevel
        FROM employees;

        -- Return first non-null value
        SELECT COALESCE(NULL, NULL, 'MySQL') AS FirstNonNull;

        -- Conditional evaluation using CASE
        SELECT emp_name,
              CASE
                  WHEN salary > 8000 THEN 'Top Earner'
                  WHEN salary BETWEEN 4000 AND 8000 THEN 'Mid Level'
                  ELSE 'Entry Level'
              END AS Category
        FROM employees;

    **Output Example**

    .. list-table::
      :header-rows: 1
      :widths: 25 25 50

      * - emp_name
        - SalaryLevel
        - Category
      * - John
        - Low
        - Entry Level
      * - Alice
        - High
        - Top Earner

6. System Functions
    System functions return information about the MySQL server or the current session.

    **Examples**

    .. code-block:: sql

        -- Current user
        SELECT USER() AS CurrentUser;

        -- MySQL version
        SELECT VERSION() AS MySQLVersion;

        -- Current database
        SELECT DATABASE() AS CurrentDatabase;

    **Output Example**

    .. list-table::
      :header-rows: 1
      :widths: 30 70

      * - Function
        - Result
      * - USER()
        - root@localhost
      * - VERSION()
        - 8.0.37
