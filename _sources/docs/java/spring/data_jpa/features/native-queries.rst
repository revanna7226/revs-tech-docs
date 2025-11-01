Native Queries
==============================

Introduction:
    Spring Data JPA provides a powerful abstraction layer for database interactions.
    Typically, developers use JPQL (Java Persistence Query Language) or derived query
    methods. However, in some cases, you might need to execute **database-specific SQL**
    statements directly. For these situations, Spring Data JPA allows the use of
    **native SQL queries**.

    A **native query** is a raw SQL statement executed directly against the database,
    bypassing JPQL translation. It is especially useful when:

    - You need to use **database-specific functions** not supported by JPQL.
    - You want to **improve performance** with optimized SQL.
    - You must **call stored procedures** or **complex joins**.

Defining Native Queries:
    You can define native queries in two main ways:

    1. Using the ``@Query`` annotation with ``nativeQuery = true``.
    2. Using the ``@NamedNativeQuery`` annotation at the entity level.

1. Using @Query with nativeQuery = true
    Spring Data JPA allows native SQL directly inside repository methods.

    **Example:**

    .. code-block:: java

        @Entity
        @Table(name = "employees")
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;
            private String department;
            private Double salary;

            // Getters and setters
        }

        public interface EmployeeRepository extends JpaRepository<Employee, Long> {

            // Native SQL query
            @Query(value = "SELECT * FROM employees WHERE department = :dept", nativeQuery = true)
            List<Employee> findByDepartment(@Param("dept") String department);

            @Query(value = "SELECT * FROM employees WHERE salary > :minSalary", nativeQuery = true)
            List<Employee> findByMinSalary(@Param("minSalary") double minSalary);
        }

    **Explanation:**
       - The ``@Query`` annotation defines the SQL string.
       - ``nativeQuery = true`` tells Spring Data JPA to treat the query as raw SQL.
       - ``:dept`` and ``:minSalary`` are named parameters mapped to method arguments.

2. Using @NamedNativeQuery:
    You can also define reusable native queries at the **entity level** using the
    ``@NamedNativeQuery`` annotation.

    **Example:**

    .. code-block:: java

        @Entity
        @Table(name = "employees")
        @NamedNativeQuery(
            name = "Employee.findHighSalaryEmployees",
            query = "SELECT * FROM employees WHERE salary > :salary",
            resultClass = Employee.class
        )
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;
            private String department;
            private Double salary;

            // Getters and setters
        }

        public interface EmployeeRepository extends JpaRepository<Employee, Long> {

            @Query(nativeQuery = true, name = "Employee.findHighSalaryEmployees")
            List<Employee> findHighSalaryEmployees(@Param("salary") double salary);
        }

    **Explanation:**
       - ``@NamedNativeQuery`` defines a static SQL query bound to an entity.
       - ``resultClass`` maps the result set to the entity.
       - The repository method refers to it using ``name = "EntityName.queryName"``.

Mapping Native Query Results:
    By default, if your SQL returns the same columns as your entity fields,
    Spring Data JPA automatically maps results to that entity.

    However, if you want to map results to a **DTO** or **custom object**, you can use
    ``@SqlResultSetMapping``.

    **Example (Mapping to a DTO):**

    .. code-block:: java

        public class EmployeeDTO {

            private String name;
            private String department;

            public EmployeeDTO(String name, String department) {
                this.name = name;
                this.department = department;
            }

            // Getters
        }

        @Entity
        @SqlResultSetMapping(
            name = "EmployeeDTOMapping",
            classes = @ConstructorResult(
                targetClass = EmployeeDTO.class,
                columns = {
                    @ColumnResult(name = "name", type = String.class),
                    @ColumnResult(name = "department", type = String.class)
                }
            )
        )
        @NamedNativeQuery(
            name = "Employee.findEmployeeDTOs",
            query = "SELECT name, department FROM employees",
            resultSetMapping = "EmployeeDTOMapping"
        )
        public class Employee {
            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;
            private String name;
            private String department;
            private Double salary;
        }

        public interface EmployeeRepository extends JpaRepository<Employee, Long> {

            @Query(nativeQuery = true, name = "Employee.findEmployeeDTOs")
            List<EmployeeDTO> findEmployeeDTOs();
        }

    **Explanation:**
       - ``@SqlResultSetMapping`` defines how columns map to a DTO constructor.
       - ``@ConstructorResult`` specifies column-to-constructor mapping.

Executing Update and Delete Native Queries:
    For modifying queries (INSERT, UPDATE, DELETE), use ``@Modifying`` along with
    ``@Transactional``.

    **Example:**

    .. code-block:: java

        public interface EmployeeRepository extends JpaRepository<Employee, Long> {

            @Modifying
            @Transactional
            @Query(value = "UPDATE employees SET salary = salary + :bonus WHERE department = :dept", nativeQuery = true)
            int increaseSalaryByDepartment(@Param("dept") String department, @Param("bonus") double bonus);
        }

    **Explanation:**
       - ``@Modifying`` tells Spring Data JPA that this is an update query.
       - ``@Transactional`` ensures the operation runs within a transaction.
       - The method returns an ``int`` indicating the number of affected rows.

Calling Stored Procedures with Native Queries:
    You can call stored procedures using the same approach.

    **Example:**

    .. code-block:: java

        @Procedure(name = "increase_salary_proc")
        @Query(value = "CALL increase_salary(:dept, :bonus)", nativeQuery = true)
        void callIncreaseSalaryProcedure(@Param("dept") String department, @Param("bonus") double bonus);

Advantages of Native Queries:
   - Full access to **database-specific SQL features**.
   - Can use **complex joins**, **subqueries**, and **vendor-specific functions**.
   - Helpful when **performance tuning** is required.

Disadvantages of Native Queries
   - Lose **database portability** (since SQL may vary between vendors).
   - No automatic JPQL translation or syntax checking.
   - Result mapping must be handled carefully to match entity fields.

Conclusion:
    Spring Data JPA native queries are a powerful tool when you need precise control
    over SQL execution. While JPQL and repository-derived methods cover most use
    cases, native queries provide the flexibility for database-optimized and
    complex operations. Use them wisely, especially when database portability is not
    a major concern.
