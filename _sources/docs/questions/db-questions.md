# Database Questions

1.  Difference between Delete, Truncate and Drop.

    | Feature / Aspect                      | DELETE                                          | TRUNCATE                                    | DROP                                  |
    | ------------------------------------- | ----------------------------------------------- | ------------------------------------------- | ------------------------------------- |
    | **Purpose**                           | Removes specific rows from a table              | Removes all rows from a table               | Deletes the entire table structure    |
    | **Data Removal**                      | Row by row                                      | All rows at once (no condition allowed)     | Entire table data and structure       |
    | **WHERE Clause Support**              | Yes                                             | No                                          | Not applicable                        |
    | **Rollback (Transaction Safe)**       | Yes, can be rolled back (if within transaction) | Yes (in some DBs), but depends on DB engine | No, cannot be rolled back             |
    | **Speed**                             | Slower (logs each deleted row)                  | Faster (minimal logging)                    | Fastest (removes definition entirely) |
    | **Affects Table Structure**           | No                                              | No                                          | Yes (removes structure)               |
    | **Triggers Execution**                | Yes                                             | No (in most DBs)                            | No                                    |
    | **Resets Auto Increment Counter**     | No                                              | Yes                                         | Yes (since table is removed)          |
    | **Used With Foreign Key Constraints** | Can delete rows with proper constraints         | Usually restricted if referenced            | Fails if referenced by another table  |
    | **Memory Space Freed**                | Frees only deleted rows’ space                  | Frees all table data space                  | Frees entire table’s allocated space  |
    | **Example**                           | `DELETE FROM emp WHERE id=101;`                 | `TRUNCATE TABLE emp;`                       | `DROP TABLE emp;`                     |

2.  What is DDL, DML and TCL? Difference between DDL and DML.
3.  What are the different types of errors you came across developing your application?
4.  We have 2 tables: EMPLOYEE(EMP_ID, EMP_NAME, EMP_SAL, DEPT_ID) and DEPT(DEPT_ID, DEPT_NAME)
    a) We want to increase salary 10% for all the employees who belong to HR dept. Write a query for the same.  
     b) Write a query that gives count of employees for each dept.  
     c) Write a query that gives the name of the dept that has the maximum employee count.
5.  Write a simple stored procedure.
6.  How to create empty tables with the same structure as another table?
7.  Suppose you have one employee table. We need to find out all employees whose first name is exactly 4 characters long.
8.  What are the differences between OLTP and OLAP?
9.  What is a Self-Join? Give an example of Self-Join.
10. What are Constraints in SQL? Give examples.
11. Write a query to remove duplicates from a table.
12. What is temp table and what are the advantages of using temp tables?
13. Give an example of SQL query where we need to use "HAVING" clause? What is the difference between GROUP BY and ORDER BY?
14. Do you know in which table user login information is saved?
15. What are ACID properties?
16. What is a view? Advantages of using views?
17. How to retrieve n-th highest salary?
18. What are triggers and why do we need triggers? Mention some disadvantages of triggers.
19. What is a primary key?
20. What is the difference between primary key and an Index?
21. Can a primary key contain more than one column?
22. How do you make a simple select on all columns of a table?
23. You have 2 tables that are connected by a unique ID. How do you write a query to take all information on both tables based on a given ID?
24. What is UNIQUE used for?
25. What is Inner Join, Left Join, Right Join? How are they different?
26. Tell something about internal tables that the database is using for its own purpose and the uses of internal tables.
27. What's the volume of the large tables that you worked on in your project? Did you get any performance hit while joining with large tables? If yes, what steps did you take to improve performance?
28. What is a cursor? Why is a cursor costly? What can be used to replace a cursor? Mention some situations where we can’t ignore cursor.
29. What is statistics? How are statistics related to Index?
30. How are you doing error handling?
31. What's the functionality of commits, rollback, and savepoints and how do they work internally?
32. If you have a select with 2 columns in WHERE, let’s say id and parent_id, what index should we put to optimize the query?
33. You have a table with a number of rows, and you want to delete half of them, you don't care which are deleted. How can you do that?
34. Explain what an execution plan is, and when do we need to check the explain plan?
35. What are the steps you want to take to improve query performance?
36. What is the size of your database? How do you know? You need to send the daily database size information to your manager, how would you do that?
37. What is transactional log? What is TCL?
38. How does the database object permission mechanism work? You want to REVOKE update/write permission from a group of users, how would you achieve that?
39. What are the different types of database locks you use?
40. What are the performance measures you want to take to keep your database healthy?
41. Did you work on data archival mechanism? How did you design it and how does the design work? Explain.
42. Did you work on any batch framework? How did you design it? Explain.
43. How to get a latest salary date of an employee from
    database when we have two tables emp, salary.

    - emp -> empid, name, dept
    - sal -> salid, empid, salary, date

    ```sql
     select e.empid, e.name, max(s.date) as latest_salary_date
     from emp e
     join sal s on e.empid = s.empid
     group by e.empid, e.name;
    ```

    If you want latest salary date including amount

    ```sql
     select e.empid, e.name, s.salary, s.date as latest_salary_date
     from emp e
     join sal s on e.empid = s.empid
     where s.date = (select max(date) from sal where empid = e.empid);
    ```
