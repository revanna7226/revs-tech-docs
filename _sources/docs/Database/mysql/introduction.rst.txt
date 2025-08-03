MySQL 
=====

.. list-table:: MYSQL Credentials
   :widths: 50 50
   :header-rows: 1

   * - MySQL Username
     - MYSQL Password
   * - root
     - admin


MySQL Command Line Client
========================= 

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


