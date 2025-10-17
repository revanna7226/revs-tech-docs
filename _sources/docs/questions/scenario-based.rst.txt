Scenario based Questions
===============================

#. You have been given an Excel file and it has a huge number of rows and columns. Now the current process reads the file row by row. This operation is time consuming, so if we want to improve the performance, what can we do?
#. What would be your approach to analyse a production issue where huge data is being loaded via ETL/batch?
#. Some legacy monolithic code has been migrated to Spring Boot. Previously the monolithic code ran in a batch process and there was a DB class with multiple instance variables used throughout the class to calculate and share between methods. Now after migrating to Spring Boot, multiple requests need to be handled by this service simultaneously. What needs to change?
#. Can we mark a class as prototype bean if any singleton scope bean is autowired in that class?
#. When you add a new jar into your project, your app does not start saying "expected 1 bean but found X". How will you investigate the issue and what will be your approach to solve this problem?
#. A microservice fails to complete a task. How would you handle this failure in Java, and how would you ensure that the task is retried?
#. Your Kafka topic is receiving duplicate messages. How can you prevent or handle duplicate message consumption by consumers?
#. Which Query is more efficient and why?
    .. code-block:: sql

        SELECT e.name, e.salary, COUNT(e.name)
        FROM employees e GROUP BY e.name, e.salary
        HAVING e.salary > 50000.00;

        SELECT e.name, e.salary, COUNT(e.name)
        FROM employees e WHERE e.salary > 50000.00
        GROUP BY e.name, e.salary;

#. What might be causing the delay in startup time for a microservice with a large database?
#. When you launch instances in a cluster placement group, what network performance parameters can you expect?
#. How do you ensure the security of CI/CD pipelines and the software they deploy?
