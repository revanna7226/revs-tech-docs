Introduction to MySQL
=========================

.. list-table:: MYSQL Credentials
   :widths: 50 50
   :header-rows: 1

   * - MySQL Username
     - MYSQL Password
   * - root
     - admin

MySQL Command Line Client:
   1. Login to MYSQL using the command line client:
      
      .. code-block:: bash

         mysql -u root -p

      Enter the password when prompted.

   2. To create a new database:
      
      .. code-block:: bash
         
         CREATE DATABASE my_database;

   3. To List all databases:

      .. code-block:: bash

         SHOW DATABASES;

   4. To use a specific database:
      
      .. code-block:: bash

         USE my_database;


   5. To list all tables in the current database:
      
      .. code-block:: bash

         SHOW TABLES;

   6. To show table definition.

      .. code-block:: bash

         DESCRIBE table_name;

What Is a Database Schema in MySQL?
   In MySQL, a database schema is a logical container that holds all your database objects — such as tables, views, indexes, stored procedures, triggers, and relations.

   - Essentially, in MySQL **A schema is the same as a database**. 
   - When you create a new database in MySQL, you are actually creating a schema.
   - In other RDBMS (like Oracle or PostgreSQL), a database may contain multiple schemas, but in MySQL, a schema = database.

Column Alias in MySQL
   A column alias is a temporary name given to a column or an expression in a SQL query.
   It is mainly used to make query output more readable or to rename calculated columns.

   Example:
      .. code-block:: sql

         SELECT column_name AS alias_name
         FROM table_name;

         -- AS Keyword is optional
         
         SELECT column_name alias_name
         FROM table_name;   

   Alias names containing spaces or special characters should be enclosed in backticks (`), double or single quotes:               

DISTINCT Keyword in MySQL
   The DISTINCT keyword is used in SELECT queries to return only unique values, removing duplicates from the result set.

   .. code-block:: sql

      SELECT DISTINCT column1, column2, ...
      FROM table_name;

   - Returns unique combinations of the specified columns.
   - Can be applied to one or more columns.         

MySQL ORDER BY Clause:
   The **ORDER BY** clause in MySQL is used to **sort the result set** of a query based on one or more columns.  
   
   You can sort the data in **ascending (ASC)** or **descending (DESC)** order.  
   
   By default, **ORDER BY sorts in ascending order**.

   .. code-block:: sql

      SELECT column1, column2, ...
      FROM table_name
      ORDER BY column1 [ASC|DESC], column2 [ASC|DESC], ...;

   - `ORDER BY` must appear **after WHERE, GROUP BY, and HAVING** clauses.
   - Sorting can also be done by **column index** (position) instead of name:
   - `ORDER BY` is used to **sort query results** in ascending or descending order.
   - Can sort by **one or multiple columns**, **aliases**, or **column positions**.
   - Works with `LIMIT` for **top N queries**.
   - Essential for reporting, ranking, and presenting data in a readable order.      