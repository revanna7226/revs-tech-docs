TCL
===

Introduction:
    **TCL (Transaction Control Language)** commands in MySQL are used to manage transactions in a database.  
    
    A **transaction** is a sequence of one or more SQL statements that are executed as a single unit of work.  
    If any statement fails, the entire transaction can be rolled back to maintain data integrity.

    TCL ensures the **ACID** properties of a transaction:

    - **Atomicity:** All operations in a transaction are completed successfully or none at all.
    - **Consistency:** The database remains consistent before and after the transaction.
    - **Isolation:** Transactions are executed independently without interference.
    - **Durability:** Once committed, the changes are permanent.

Common TCL Commands:
    MySQL provides the following **TCL commands**:

    1. **START TRANSACTION / BEGIN**
    2. **COMMIT**
    3. **ROLLBACK**
    4. **SAVEPOINT**
    5. **RELEASE SAVEPOINT**
    6. **SET AUTOCOMMIT**

    Each is explained below with practical examples.

1. START TRANSACTION / BEGIN:
    **Purpose:**
    Begins a new transaction.  
    Once started, all SQL statements are part of this transaction until it is either committed or rolled back.

    **Syntax:**

    .. code-block:: sql

        START TRANSACTION;
        -- or
        BEGIN;

    **Example:**

    .. code-block:: sql

        START TRANSACTION;

        INSERT INTO accounts (account_id, name, balance)
        VALUES (1, 'Alice', 1000);

        INSERT INTO accounts (account_id, name, balance)
        VALUES (2, 'Bob', 500);

        -- Changes are not yet permanent
        COMMIT;  -- Make changes permanent

2. COMMIT
    **Purpose:**
    Saves all the changes made during the current transaction permanently to the database.

    **Syntax:**

    .. code-block:: sql

        COMMIT;

    **Example:**

    .. code-block:: sql

        START TRANSACTION;

        UPDATE accounts
        SET balance = balance - 200
        WHERE account_id = 1;

        UPDATE accounts
        SET balance = balance + 200
        WHERE account_id = 2;

        COMMIT;

    **Explanation:**
       - If both updates succeed, the changes are committed.
       - If there’s an error before `COMMIT`, the transaction can be rolled back to its previous state.

3. ROLLBACK
    **Purpose:**
    Undo all the changes made in the current transaction since the last `COMMIT` or since the transaction started.

    **Syntax:**

    .. code-block:: sql

        ROLLBACK;

    **Example:**

    .. code-block:: sql

        START TRANSACTION;

        UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
        UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;

        -- Suppose the second query fails or user cancels operation
        ROLLBACK;

    **Explanation:**
       - All updates made in the transaction are undone.
       - The balances remain unchanged as before the transaction started.

4. SAVEPOINT
    **Purpose:**
    Sets a point within a transaction to which you can later roll back without affecting prior operations.

    **Syntax:**

    .. code-block:: sql

        SAVEPOINT savepoint_name;

    **Example:**

    .. code-block:: sql

        START TRANSACTION;

        UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
        SAVEPOINT after_first_update;

        UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
        SAVEPOINT after_second_update;

        -- Rollback only to the first savepoint
        ROLLBACK TO after_first_update;

        COMMIT;

    **Explanation:**
       - The second update will be undone.
       - The first update (before `after_first_update`) will remain.

5. RELEASE SAVEPOINT
    **Purpose:**
    Deletes a previously created savepoint.  
    After release, the savepoint cannot be used for rollback.

    **Syntax:**

    .. code-block:: sql

        RELEASE SAVEPOINT savepoint_name;

    **Example:**

    .. code-block:: sql

        START TRANSACTION;

        UPDATE accounts SET balance = balance - 50 WHERE account_id = 1;
        SAVEPOINT deduct_fees;

        UPDATE accounts SET balance = balance + 50 WHERE account_id = 2;
        RELEASE SAVEPOINT deduct_fees;

        COMMIT;

    **Explanation:**
       - The savepoint `deduct_fees` is released.
       - The transaction proceeds normally and commits.

6. SET AUTOCOMMIT
    **Purpose:**
    Enables or disables the automatic commit mode in MySQL.

    **Syntax:**

    .. code-block:: sql

        SET AUTOCOMMIT = 0; 
        -- or 
        SET AUTOCOMMIT = 1;


    **Explanation:**
       - `SET AUTOCOMMIT = 1` → Every SQL statement is automatically committed (default).
       - `SET AUTOCOMMIT = 0` → You must explicitly commit or roll back transactions.

    **Example:**

    .. code-block:: sql

        SET AUTOCOMMIT = 0;

        INSERT INTO accounts VALUES (3, 'Charlie', 300);
        INSERT INTO accounts VALUES (4, 'David', 400);

        -- Changes are not saved yet
        COMMIT;  -- Now changes are permanent

        SET AUTOCOMMIT = 1;  -- Return to default mode

Transaction Example (Combined)
    **Example Scenario: Money Transfer Between Two Accounts**

    .. code-block:: sql

        START TRANSACTION;

        UPDATE accounts
        SET balance = balance - 200
        WHERE account_id = 1;

        UPDATE accounts
        SET balance = balance + 200
        WHERE account_id = 2;

        -- Check for any error
        IF (SELECT balance FROM accounts WHERE account_id = 1) < 0 THEN
            ROLLBACK;
        ELSE
            COMMIT;
        END IF;

    **Explanation:**
       - If the debit account goes negative, `ROLLBACK` restores previous balances.
       - Otherwise, `COMMIT` finalizes the transaction.

Important Notes:
   - TCL commands apply only to **transactional storage engines** like **InnoDB**.
   - Non-transactional engines like **MyISAM** do **not support** ROLLBACK or COMMIT.
   - Always use transactions for critical operations such as:

       - Banking transfers
       - Inventory updates
       - Reservation systems


**Summary of MySQL TCL Commands:**

+---------------------+-------------------------------------------+
| **Command**         | **Description**                           |
+=====================+===========================================+
| START TRANSACTION   | Begins a transaction                      |
+---------------------+-------------------------------------------+
| COMMIT              | Saves all changes permanently             |
+---------------------+-------------------------------------------+
| ROLLBACK            | Reverts changes since last commit         |
+---------------------+-------------------------------------------+
| SAVEPOINT           | Creates a rollback point within a txn     |
+---------------------+-------------------------------------------+
| RELEASE SAVEPOINT   | Removes a defined savepoint               |
+---------------------+-------------------------------------------+
| SET AUTOCOMMIT      | Enables/Disables auto-commit mode         |
+---------------------+-------------------------------------------+

Conclusion:
    MySQL **TCL commands** provide precise control over how and when changes are made to the database.  
    They help maintain **data integrity** and ensure that incomplete or failed operations do not corrupt data.  
    Using transactions effectively is essential for building **reliable and consistent applications**.
