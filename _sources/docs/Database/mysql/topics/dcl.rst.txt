DCL
===

Introduction:
    **Data Control Language (DCL)** in MySQL is used to **control access and permissions** to the database.  
    It defines which users can perform specific actions such as reading, writing, or modifying database objects.

    The two main DCL commands in MySQL are:

    - **GRANT** – To assign privileges to users.
    - **REVOKE** – To remove privileges from users.

    In some contexts, **COMMIT** and **ROLLBACK** are discussed under DCL, but technically, they belong to **TCL (Transaction Control Language)**.

**DCL Commands in MySQL**

1. GRANT Command:
    The **GRANT** command is used to assign one or more privileges to a user account.  
    
    Privileges can be given at different levels:
       - Global level (all databases)
       - Database level
       - Table level
       - Column level
       - Stored procedure level

    **Syntax:**

    .. code-block:: sql

        GRANT privilege_list
        ON database_name.table_name
        TO 'username'@'host'
        [IDENTIFIED BY 'password']
        [WITH GRANT OPTION];

    **Example 1: Grant Privilege on a Database**

    .. code-block:: sql

        GRANT ALL PRIVILEGES
        ON company_db.*
        TO 'john'@'localhost'
        IDENTIFIED BY 'john123';

    **Explanation:**
       - Grants **all privileges** on all tables in `company_db` to user **john**.
       - `'localhost'` means the user can only connect locally.
       - If the user doesn’t exist, this command will create it automatically.

    **Example 2: Grant Specific Privileges on a Table**

    .. code-block:: sql

        GRANT SELECT, INSERT, UPDATE
        ON company_db.employees
        TO 'mary'@'%';

    **Explanation:**
       - Grants only **SELECT**, **INSERT**, and **UPDATE** permissions.
       - `'%'` means the user can connect from any host.

    **Example 3: Grant Privileges with Grant Option**

    .. code-block:: sql

        GRANT SELECT, UPDATE
        ON company_db.employees
        TO 'manager'@'localhost'
        WITH GRANT OPTION;

    **Explanation:**
       - Allows user **manager** to grant these same privileges to other users.

    **Example 4: Grant Column-Level Privilege**

    .. code-block:: sql

        GRANT SELECT (ename, job), UPDATE (sal)
        ON company_db.employees
        TO 'hr_user'@'localhost';

    **Explanation:**
       - The user **hr_user** can only:
       - Read `ename` and `job` columns.
       - Update `sal` column.

2. REVOKE Command
    The **REVOKE** command removes one or more privileges from a user account.

    **Syntax:**

    .. code-block:: sql

        REVOKE privilege_list
        ON database_name.table_name
        FROM 'username'@'host';

    **Example 1: Revoke Specific Privileges**

    .. code-block:: sql

        REVOKE INSERT, UPDATE
        ON company_db.employees
        FROM 'mary'@'%';

    **Explanation:**
       - Removes **INSERT** and **UPDATE** permissions for user **mary**.
       - The user will still have any remaining privileges (e.g., SELECT).

    **Example 2: Revoke All Privileges**

    .. code-block:: sql

        REVOKE ALL PRIVILEGES, GRANT OPTION
        FROM 'john'@'localhost';

    **Explanation:**
       - Removes all privileges and the ability to grant privileges from **john**.

    **Example 3: Revoke Database-Level Privileges**

    .. code-block:: sql

        REVOKE ALL PRIVILEGES
        ON company_db.*
        FROM 'manager'@'localhost';

    **Explanation:**
       - Removes all privileges that **manager** has on `company_db`.

3. SHOW GRANTS Command
    The **SHOW GRANTS** command displays the privileges assigned to a user.

    **Example:**

    .. code-block:: sql

        SHOW GRANTS FOR 'john'@'localhost';

    **Example Output:**

    .. code-block:: sql

        GRANT USAGE ON *.* TO 'john'@'localhost';
        GRANT ALL PRIVILEGES ON `company_db`.* TO 'john'@'localhost';

4. CREATE USER and DROP USER (Related DCL Commands)
    Although not strictly part of DCL in ANSI SQL, **MySQL** often includes these commands since they control access.

    **Example: Create a New User**

    .. code-block:: sql

        CREATE USER 'emma'@'localhost' IDENTIFIED BY 'emma123';

    **Example: Drop a User**

    .. code-block:: sql

        DROP USER 'emma'@'localhost';

    Lists the users of a Database

    .. code-block:: sql

        SELECT user, host FROM mysql.user;

**Privileges in MySQL**

*Common Privileges*

+-------------------+-----------------------------------------------------+
| **Privilege**     | **Description**                                     |
+===================+=====================================================+
| SELECT            | Allows reading data from a table                    |
+-------------------+-----------------------------------------------------+
| INSERT            | Allows inserting data into a table                  |
+-------------------+-----------------------------------------------------+
| UPDATE            | Allows modifying existing data                      |
+-------------------+-----------------------------------------------------+
| DELETE            | Allows deleting data                                |
+-------------------+-----------------------------------------------------+
| CREATE            | Allows creating databases and tables                |
+-------------------+-----------------------------------------------------+
| DROP              | Allows dropping databases and tables                |
+-------------------+-----------------------------------------------------+
| ALTER             | Allows altering the structure of a table            |
+-------------------+-----------------------------------------------------+
| INDEX             | Allows creating and dropping indexes                |
+-------------------+-----------------------------------------------------+
| EXECUTE           | Allows executing stored procedures                  |
+-------------------+-----------------------------------------------------+
| GRANT OPTION      | Allows the user to grant privileges to others       |
+-------------------+-----------------------------------------------------+

Practical Example
    **Step 1:** Create a new user.

    .. code-block:: sql

        CREATE USER 'dev_user'@'localhost' IDENTIFIED BY 'devpass';

    **Step 2:** Grant privileges on a specific database.

    .. code-block:: sql

        GRANT SELECT, INSERT, UPDATE
        ON project_db.*
        TO 'dev_user'@'localhost';

    **Step 3:** Verify privileges.

    .. code-block:: sql

        SHOW GRANTS FOR 'dev_user'@'localhost';

    **Step 4:** Revoke privileges if necessary.

    .. code-block:: sql

        REVOKE UPDATE
        ON project_db.*
        FROM 'dev_user'@'localhost';

    **Step 5:** Drop user (optional).

    .. code-block:: sql

        DROP USER 'dev_user'@'localhost';

Conclusion:
   - **DCL** ensures security and proper access management in MySQL databases.  
   - **GRANT** provides privileges, while **REVOKE** removes them.  
   - It is crucial to assign the **minimum privileges** necessary for each user to ensure security.  
   - Always verify privileges using **SHOW GRANTS** and maintain strong passwords for database users.

