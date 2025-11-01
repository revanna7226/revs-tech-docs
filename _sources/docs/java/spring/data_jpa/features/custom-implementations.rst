Spring Data JPA Custom Implementations
======================================

Introduction:
    Spring Data JPA automatically implements most repository methods based on
    method naming conventions or JPQL queries. However, there are scenarios where
    you need to write **custom logic** that cannot be expressed via derived queries
    or annotations.

    In such cases, you can provide **custom repository implementations**.

    Custom repository implementations allow you to:
    - Write complex business queries
    - Use the ``EntityManager`` directly
    - Combine native SQL and JPQL
    - Mix repository patterns with service logic cleanly

Approaches to Custom Implementations:
    Spring Data JPA provides two main ways to create custom logic:

    1. **Custom method inside the same repository interface using @Query**
    2. **Separate custom repository interface and its implementation**

    The first approach works for small custom queries.
    For complex logic, the second approach is recommended.

1. Using @Query (Simple Customization):
    You can write custom JPQL or native SQL queries using ``@Query``.

    **Example:**

    .. code-block:: java

        @Repository
        public interface EmployeeRepository extends JpaRepository<Employee, Long> {

            // JPQL query
            @Query("SELECT e FROM Employee e WHERE e.salary > :salary")
            List<Employee> findEmployeesWithSalaryGreaterThan(@Param("salary") double salary);

            // Native SQL query
            @Query(value = "SELECT * FROM employees WHERE department = :dept", nativeQuery = true)
            List<Employee> findByDepartment(@Param("dept") String department);
        }

    **Explanation:**
       - ``@Query`` allows defining queries directly on the repository method.
       - Use ``nativeQuery = true`` for raw SQL.
       - Works well for simple custom requirements.

2. Custom Repository Implementation (Advanced):
    For more complex cases, you can define a **custom repository interface**
    and provide your own **implementation**.

    **Steps to Create Custom Repository Implementation:**

    **Step 1: Create an Entity:**

    .. code-block:: java

        @Entity
        @Table(name = "employees")
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;
            private String department;
            private double salary;

            // getters and setters
        }

    **Step 2: Create a Base Repository**

    .. code-block:: java

        public interface EmployeeRepository extends JpaRepository<Employee, Long>, EmployeeRepositoryCustom {
        }

    **Explanation:**
       - The ``EmployeeRepository`` extends both ``JpaRepository`` and our **custom interface**.
       - The custom interface (``EmployeeRepositoryCustom``) will contain method declarations for custom logic.

    **Step 3: Create a Custom Repository Interface**

    .. code-block:: java

        public interface EmployeeRepositoryCustom {
            List<Employee> findEmployeesWithSalaryRange(double minSalary, double maxSalary);
            void updateEmployeeDepartment(Long id, String newDepartment);
        }

    **Step 4: Provide the Custom Implementation**

    The implementation class must follow the naming convention:
    **<RepositoryInterfaceName>Impl**

    That is, if the interface is ``EmployeeRepositoryCustom``,
    the implementation class must be ``EmployeeRepositoryImpl``.

    .. code-block:: java

        @Repository
        public class EmployeeRepositoryImpl implements EmployeeRepositoryCustom {

            @PersistenceContext
            private EntityManager entityManager;

            @Override
            public List<Employee> findEmployeesWithSalaryRange(double minSalary, double maxSalary) {
                String jpql = "SELECT e FROM Employee e WHERE e.salary BETWEEN :min AND :max";
                return entityManager.createQuery(jpql, Employee.class)
                                    .setParameter("min", minSalary)
                                    .setParameter("max", maxSalary)
                                    .getResultList();
            }

            @Override
            public void updateEmployeeDepartment(Long id, String newDepartment) {
                String jpql = "UPDATE Employee e SET e.department = :dept WHERE e.id = :id";
                entityManager.createQuery(jpql)
                            .setParameter("dept", newDepartment)
                            .setParameter("id", id)
                            .executeUpdate();
            }
        }

    **Explanation:**
       - ``EntityManager`` provides direct access to JPA operations.
       - You can execute JPQL, native queries, and updates.
       - The custom class is automatically detected if the name follows the ``Impl`` suffix rule.

    **Step 5: Use Custom Methods in Service**

    .. code-block:: java

        @Service
        public class EmployeeService {

            @Autowired
            private EmployeeRepository employeeRepository;

            public void executeCustomQueries() {
                // Fetch employees within salary range
                List<Employee> employees = employeeRepository.findEmployeesWithSalaryRange(40000, 80000);
                employees.forEach(e -> System.out.println(e.getName()));

                // Update department
                employeeRepository.updateEmployeeDepartment(1L, "HR");
            }
        }

    **Explanation:**
       - The service layer calls the custom repository methods as if they were normal JPA repository methods.

Advantages of Custom Implementations:
   .. list-table::
      :header-rows: 1
      :widths: 25 75

      * - **Advantage**
        - **Description**
      * - Reusable Logic
        - Custom methods can encapsulate complex logic and be reused across services.
      * - Performance Optimization
        - You can fine-tune JPQL or SQL queries for better performance.
      * - Flexibility
        - Allows mixing native SQL, JPQL, and Criteria API as needed.
      * - Clean Separation
        - Keeps custom business logic separate from standard repository methods.

Summary:
   .. list-table::
      :header-rows: 1
      :widths: 25 75

      * - **Approach**
        - **When to Use**
      * - ``@Query``
        - When writing small custom queries that fit within a single method.
      * - Custom Repository Implementation
        - When complex business logic, dynamic queries, or batch operations are required.

Conclusion:
   Spring Data JPA Custom Implementations provide a flexible and powerful way
   to extend repository capabilities beyond derived queries. By combining
   ``EntityManager`` with repository interfaces, you can perform sophisticated
   operations while maintaining clean separation of concerns.