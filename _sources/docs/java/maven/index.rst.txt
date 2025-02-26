Apache Maven
============

Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project's build, reporting and documentation from a central piece of information.

Maven Commands
--------------

.. code-block:: bash
    
    # Build the project
    mvn clean install

    # Build the project without running tests
    mvn clean install -DskipTests

    # Run the spring-boot project
    mvn spring-boot:run