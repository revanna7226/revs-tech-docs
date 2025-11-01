Data Types
==========

Introduction:
    MySQL provides a wide range of **data types** to store various kinds of data efficiently and accurately.  
    
    Choosing the correct data type is crucial for **database performance**, **storage optimization**, and **data integrity**.

    MySQL data types can be broadly categorized into the following groups:

    1. Numeric Data Types
    2. Date and Time Data Types
    3. String (Character) Data Types
    4. JSON Data Type
    5. Spatial Data Types

1. Numeric Data Types:
    Numeric data types store numbers that can be **integers**, **decimals**, or **floating-point** values.

    Common numeric data types:

    .. list-table:: MySQL Numeric Data Types
      :header-rows: 1
      :widths: 20 20 60

      * - **Data Type**
        - **Storage (Bytes)**
        - **Description**
      * - TINYINT
        - 1 byte
        - Range: -128 to 127 or 0 to 255 (UNSIGNED)
      * - SMALLINT
        - 2 bytes
        - Range: -32,768 to 32,767
      * - MEDIUMINT
        - 3 bytes
        - Range: -8,388,608 to 8,388,607
      * - INT / INTEGER
        - 4 bytes
        - Range: -2,147,483,648 to 2,147,483,647
      * - BIGINT
        - 8 bytes
        - Range: very large integers
      * - DECIMAL(M,D)
        - Variable
        - Exact numeric with M digits and D decimal places
      * - FLOAT
        - 4 bytes
        - Approximate single-precision floating-point number
      * - DOUBLE
        - 8 bytes
        - Approximate double-precision floating-point number
      * - BIT(M)
        - 1–8 bytes
        - Bit-field values

    Example Usage

    .. code-block:: sql

        CREATE TABLE products (
            product_id INT PRIMARY KEY,
            price DECIMAL(10,2),
            stock INT,
            discount_percent FLOAT
        );

        INSERT INTO products VALUES
        (1, 499.99, 20, 10.5),
        (2, 999.50, 15, 5.25);

    Explanation
       - **INT**: Used for integer numbers like quantity or count.
       - **DECIMAL(10,2)**: Stores exact monetary values like prices.
       - **FLOAT / DOUBLE**: Used when precision is less important (e.g., scientific calculations).

2. Date and Time Data Types
    Date and time types are used to store temporal information such as dates, times, or timestamps.

    .. list-table:: **MySQL Date and Time Data Types**
      :header-rows: 1
      :widths: 20 25 55

      * - **Data Type**
        - **Format**
        - **Description**
      * - DATE
        - 'YYYY-MM-DD'
        - Stores date only.
      * - DATETIME
        - 'YYYY-MM-DD HH:MM:SS'
        - Stores date and time (no timezone).
      * - TIMESTAMP
        - 'YYYY-MM-DD HH:MM:SS'
        - Stores date/time (auto time zone adjustment).
      * - TIME
        - 'HH:MM:SS'
        - Stores time only.
      * - YEAR
        - 'YYYY'
        - Stores year (range: 1901–2155).
   
    Example Usage

    .. code-block:: sql

        CREATE TABLE employees (
            emp_id INT PRIMARY KEY,
            emp_name VARCHAR(100),
            hire_date DATE,
            login_time DATETIME,
            last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
        );

        INSERT INTO employees (emp_id, emp_name, hire_date, login_time)
        VALUES (1, 'John Doe', '2021-05-15', '2021-05-15 09:30:00');

    Explanation
       - **DATE**: Stores only the calendar date.
       - **DATETIME**: Stores both date and time without timezone adjustments.
       - **TIMESTAMP**: Automatically updates with the current time on row modification.
       - **YEAR**: Ideal for storing years such as car manufacturing years.

3. String (Character) Data Types
    String types are used to store text, characters, and binary data.

    .. list-table:: **MySQL String (Character) Data Types**
      :header-rows: 1
      :widths: 20 25 55

      * - **Data Type**
        - **Max Length**
        - **Description**
      * - CHAR(M)
        - 0–255 characters
        - Fixed-length string.
      * - VARCHAR(M)
        - 0–65,535 characters
        - Variable-length string.
      * - TEXT
        - 65,535 characters
        - Large text blocks.
      * - MEDIUMTEXT
        - 16,777,215 characters
        - Medium-size text.
      * - LONGTEXT
        - 4,294,967,295 characters
        - Very large text storage.
      * - BLOB
        - 65,535 bytes
        - Binary data (e.g., images, files).
      * - ENUM
        - Up to 65,535 values
        - One value from a predefined list.
      * - SET
        - Up to 64 values
        - Multiple values from a predefined list.
   
    Example Usage

    .. code-block:: sql

        CREATE TABLE users (
            user_id INT PRIMARY KEY,
            username VARCHAR(50),
            gender ENUM('Male', 'Female', 'Other'),
            interests SET('Sports', 'Music', 'Reading', 'Travel'),
            bio TEXT
        );

        INSERT INTO users
        VALUES (1, 'Alice', 'Female', 'Music,Travel', 'Loves exploring new countries.');

    Explanation
       - **CHAR**: Best for fixed-length fields like country codes (‘USA’, ‘IND’).
       - **VARCHAR**: Ideal for variable-length text like names and email addresses.
       - **TEXT/BLOB**: Used for storing large text or binary data.
       - **ENUM/SET**: Useful for columns with predefined possible values.

4. JSON Data Type
    MySQL 5.7+ supports the **JSON** data type for storing structured JSON documents.

    Example Usage

    .. code-block:: sql

        CREATE TABLE orders (
            order_id INT PRIMARY KEY,
            order_data JSON
        );

        INSERT INTO orders (order_id, order_data)
        VALUES (101, '{"customer":"John","items":["Pen","Book"],"total":250.50}');

    You can query JSON fields using MySQL’s **JSON functions**:

    .. code-block:: sql

        SELECT
            order_data->'$.customer' AS Customer,
            JSON_EXTRACT(order_data, '$.total') AS Total
        FROM orders;

5. Spatial Data Types
    Spatial types are used for **geographical and geometric** data.

    .. list-table:: **MySQL Spatial Data Types**
      :header-rows: 1
      :widths: 25 75

      * - **Data Type**
        - **Description**
      * - GEOMETRY
        - Represents any geometric object.
      * - POINT
        - A single location defined by X and Y coordinates.
      * - LINESTRING
        - A series of points forming a line.
      * - POLYGON
        - A closed area defined by multiple points.
                
    Example Usage

    .. code-block:: sql

        CREATE TABLE locations (
            loc_id INT PRIMARY KEY,
            name VARCHAR(100),
            coordinates POINT
        );

        INSERT INTO locations VALUES (1, 'City Park', ST_GeomFromText('POINT(12.34 56.78)'));

Choosing the Right Data Type
   1. Use the **smallest possible** numeric type (e.g., `TINYINT` instead of `INT` for small ranges).
   2. Prefer **DECIMAL** for financial data (accurate) over **FLOAT/DOUBLE** (approximate).
   3. Use **CHAR** for fixed-length codes; **VARCHAR** for variable text.
   4. Use **DATE**, **DATETIME**, or **TIMESTAMP** for temporal data depending on timezone and auto-update needs.
   5. Use **JSON** for flexible, semi-structured data.
   6. Avoid **TEXT/BLOB** unless necessary—they are slower to process.

Example Summary
  .. code-block:: sql

      CREATE TABLE employee_details (
          emp_id INT PRIMARY KEY,
          emp_name VARCHAR(100),
          gender ENUM('Male','Female','Other'),
          salary DECIMAL(10,2),
          joining_date DATE,
          last_login TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
          bio TEXT
      );

      INSERT INTO employee_details
      VALUES (1, 'Alice', 'Female', 55000.75, '2023-02-10', DEFAULT, 'Software Engineer');

Conclusion
   - MySQL provides a **diverse set of data types** for storing different data formats.
   - Choosing the right data type improves:
     
     * **Storage efficiency**
     * **Query performance**
     * **Data consistency**
     
   - Proper use of data types ensures your database is **optimized**, **scalable**, and **accurate**.