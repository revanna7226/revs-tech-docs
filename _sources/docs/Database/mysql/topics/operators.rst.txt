Operators
=========

Introduction:
    In MySQL, **operators** are special symbols or keywords used in SQL statements to perform operations on **data**.  
    
    Operators are essential for filtering, calculating, and manipulating data in queries.

    MySQL operators can be classified into several categories:

    1. Arithmetic Operators
    2. Comparison Operators
    3. Logical Operators
    4. Bitwise Operators
    5. Assignment Operators
    6. Other Special Operators (LIKE, IN, BETWEEN, IS NULL)

**1. Arithmetic Operators**

Used to perform mathematical calculations.

+----------+---------------------+----------------+
| Operator | Description         | Example        |
+==========+=====================+================+
| ``+``    | Addition            | SELECT 5 + 3;  |
+----------+---------------------+----------------+
| ``-``    | Subtraction         | SELECT 5 - 3;  |
+----------+---------------------+----------------+
| ``*``    | Multiplication      | SELECT 5 * 3;  |
+----------+---------------------+----------------+
| ``/``    | Division            | SELECT 10 / 2; |
+----------+---------------------+----------------+
| ``%``    | Modulus (remainder) | SELECT 10 % 3; |
+----------+---------------------+----------------+

**Example:**

.. code-block:: sql

    SELECT salary, salary * 12 AS annual_salary
    FROM employees;

**2. Comparison Operators**

Used to compare values and return **TRUE or FALSE**.

+----------+-----------------------------+-------------------------------------------------------+
| Operator | Description                 | Example                                               |
+==========+=============================+=======================================================+
| =        | Equal to                    | SELECT * FROM emp WHERE id = 101;                     |
+----------+-----------------------------+-------------------------------------------------------+
| <> or != | Not equal to                | SELECT * FROM emp WHERE id != 101;                    |
+----------+-----------------------------+-------------------------------------------------------+
| >        | Greater than                | SELECT * FROM emp WHERE salary > 5000;                |
+----------+-----------------------------+-------------------------------------------------------+
| <        | Less than                   | SELECT * FROM emp WHERE salary < 5000;                |
+----------+-----------------------------+-------------------------------------------------------+
| >=       | Greater than or equal to    | SELECT * FROM emp WHERE salary >= 5000;               |
+----------+-----------------------------+-------------------------------------------------------+
| <=       | Less than or equal to       | SELECT * FROM emp WHERE salary <= 5000;               |
+----------+-----------------------------+-------------------------------------------------------+
| BETWEEN  | Within a range              | SELECT * FROM emp WHERE salary BETWEEN 3000 AND 7000; |
+----------+-----------------------------+-------------------------------------------------------+
| LIKE     | Pattern matching            | SELECT * FROM emp WHERE name LIKE 'J%';               |
+----------+-----------------------------+-------------------------------------------------------+
| IN       | Matches any value in a list | SELECT * FROM emp WHERE dept IN (10, 20);             |
+----------+-----------------------------+-------------------------------------------------------+
| IS NULL  | Checks for NULL             | SELECT * FROM emp WHERE commission IS NULL;           |
+----------+-----------------------------+-------------------------------------------------------+

**3. Logical Operators**

Used to combine multiple conditions in **WHERE** clauses.

+----------+------------------------------------------+------------------------------------------------------+
| Operator | Description                              | Example                                              |
+==========+==========================================+======================================================+
| AND      | Returns TRUE if both conditions are true | SELECT * FROM emp WHERE dept = 10 AND salary > 2000; |
+----------+------------------------------------------+------------------------------------------------------+
| OR       | Returns TRUE if either condition is true | SELECT * FROM emp WHERE dept = 10 OR dept = 20;      |
+----------+------------------------------------------+------------------------------------------------------+
| NOT      | Negates a condition                      | SELECT * FROM emp WHERE NOT dept = 10;               |
+----------+------------------------------------------+------------------------------------------------------+


**4. Bitwise Operators**

Operate on **binary representations** of numbers.

+----------+-------------+----------------+-----+
| Operator | Description | Example        |     |
+==========+=============+================+=====+
| &        | Bitwise AND | SELECT 5 & 3;  |     |
+----------+-------------+----------------+-----+
| ``|``    | Bitwise OR  | SELECT 5       | 3;  |
+----------+-------------+----------------+-----+
| ^        | Bitwise XOR | SELECT 5 ^ 3;  |     |
+----------+-------------+----------------+-----+
| ~        | Bitwise NOT | SELECT ~5;     |     |
+----------+-------------+----------------+-----+
| <<       | Left shift  | SELECT 5 << 1; |     |
+----------+-------------+----------------+-----+
| >>       | Right shift | SELECT 5 >> 1; |     |
+----------+-------------+----------------+-----+

**5. Assignment Operators**

Used to **assign values** to variables in MySQL.

+----------+----------------------------+----------------+
| Operator | Description                | Example        |
+==========+============================+================+
| :=       | Assign a value to variable | SET @x := 100; |
+----------+----------------------------+----------------+

**6. Other Special Operators:**

    - **LIKE** → Pattern matching with `%` (any characters) or `_` (single character).  
    - **IN** → Checks if a value exists in a set.  
    - **BETWEEN … AND …** → Checks if a value is within a range.  
    - **IS NULL / IS NOT NULL** → Checks for NULL values.  

**Example:**

.. code-block:: sql

    SELECT * FROM employees
    WHERE department IN (10, 20)
      AND salary BETWEEN 3000 AND 6000
      AND name LIKE 'J%';

Conclusion:
    - Operators are essential to **filter, calculate, and manipulate data** in MySQL.  
    - Choosing the correct operator ensures **accurate query results**.  
    - MySQL supports **arithmetic, comparison, logical, bitwise, assignment, and special operators**.  
    - Always test operators carefully, especially with **NULL** values and **logical conditions**.
