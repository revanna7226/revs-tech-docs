Spring Data JPA Specifications
==============================

Introduction:
    Spring Data JPA **Specifications** provide a powerful and flexible way to build
    dynamic queries at runtime using the **Criteria API**.  
    They are part of the ``org.springframework.data.jpa.domain.Specification`` interface
    and are used when the query parameters are **optional or dynamic**.

    This helps developers avoid writing multiple repository query methods for different
    combinations of parameters.

Key Concept:
    A ``Specification`` defines a **predicate** (a condition) that can be combined
    with other predicates using logical operators such as **AND**, **OR**, and **NOT**.

    The interface:
    
    .. code-block:: java

        public interface Specification<T> {
            Predicate toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder cb);
        }

    Where:

    - **Root<T>** : Represents the entity being queried.
    - **CriteriaQuery<?>** : Represents the overall query.
    - **CriteriaBuilder** : Used to construct conditions (Predicates).

Example Entity:
    .. code-block:: java

        @Entity
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;
            private String department;
            private Double salary;

            // getters and setters
        }

Repository Interface:
    To use Specifications, your repository must extend ``JpaSpecificationExecutor``.

    .. code-block:: java

        public interface EmployeeRepository extends
                JpaRepository<Employee, Long>,
                JpaSpecificationExecutor<Employee> {
        }

Creating Specifications:
    You can define Specifications as separate methods or static inner classes.

Example 1: Simple Specification:
    .. code-block:: java

        public class EmployeeSpecification {

            public static Specification<Employee> hasDepartment(String department) {
                return (root, query, cb) ->
                        cb.equal(root.get("department"), department);
            }

            public static Specification<Employee> hasMinimumSalary(Double salary) {
                return (root, query, cb) ->
                        cb.greaterThanOrEqualTo(root.get("salary"), salary);
            }

            public static Specification<Employee> nameContains(String keyword) {
                return (root, query, cb) ->
                        cb.like(cb.lower(root.get("name")), "%" + keyword.toLowerCase() + "%");
            }
        }

Combining Specifications:
    Specifications can be combined using ``and()``, ``or()``, and ``not()`` methods.

    .. code-block:: java

        Specification<Employee> spec = Specification
                .where(EmployeeSpecification.hasDepartment("IT"))
                .and(EmployeeSpecification.hasMinimumSalary(50000.0))
                .and(EmployeeSpecification.nameContains("john"));

        List<Employee> employees = employeeRepository.findAll(spec);

    **Explanation:**

    - ``Specification.where(...)`` initializes the first condition.
    - ``.and(...)`` and ``.or(...)`` chain multiple conditions dynamically.
    - The resulting predicate translates into SQL queries automatically.

Example: Building Dynamic Search
    Suppose we have an API endpoint that takes optional search parameters:
    ``name``, ``department``, and ``minSalary``.

    We can build a dynamic Specification like this:

    .. code-block:: java

        @Service
        public class EmployeeService {

            @Autowired
            private EmployeeRepository employeeRepository;

            public List<Employee> searchEmployees(String name, String department, Double minSalary) {

                Specification<Employee> spec = Specification.where(null);

                if (name != null && !name.isEmpty()) {
                    spec = spec.and(EmployeeSpecification.nameContains(name));
                }
                if (department != null && !department.isEmpty()) {
                    spec = spec.and(EmployeeSpecification.hasDepartment(department));
                }
                if (minSalary != null) {
                    spec = spec.and(EmployeeSpecification.hasMinimumSalary(minSalary));
                }

                return employeeRepository.findAll(spec);
            }
        }

    **Explanation:**

    - Each parameter is checked dynamically.
    - Only non-null values are added as filtering conditions.
    - No need to create multiple repository query methods.

Example Controller:
    .. code-block:: java

        @RestController
        @RequestMapping("/employees")
        public class EmployeeController {

            @Autowired
            private EmployeeService employeeService;

            @GetMapping("/search")
            public List<Employee> search(
                    @RequestParam(required = false) String name,
                    @RequestParam(required = false) String department,
                    @RequestParam(required = false) Double minSalary) {
                return employeeService.searchEmployees(name, department, minSalary);
            }
        }

Specification with Joins:
    You can also use joins to filter based on related entities.

    Example: Suppose ``Employee`` has a relation to ``Department`` entity.

    .. code-block:: java

        @Entity
        public class Department {
            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;
            private String name;
        }

        @Entity
        public class Employee {
            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;
            private String name;
            private Double salary;

            @ManyToOne
            private Department department;
        }

    Specification with Join:

    .. code-block:: java

        public static Specification<Employee> hasDepartmentName(String deptName) {
            return (root, query, cb) -> {
                Join<Employee, Department> department = root.join("department");
                return cb.equal(department.get("name"), deptName);
            };
        }

Sorting and Pagination with Specifications:
    You can also use Specifications with ``Pageable`` and ``Sort``:

    .. code-block:: java

        Pageable pageable = PageRequest.of(0, 5, Sort.by("salary").descending());

        Specification<Employee> spec = Specification
                .where(EmployeeSpecification.hasMinimumSalary(40000.0));

        Page<Employee> page = employeeRepository.findAll(spec, pageable);

Advantages of Using Specifications:
   .. list-table::
      :header-rows: 1
      :widths: 25 75

      * - **Advantage**
        - **Description**
      * - Dynamic Query Building
        - Build flexible queries at runtime based on user input.
      * - Reusability
        - Common conditions can be reused across services.
      * - Type-Safe
        - Built using JPA Criteria API, ensuring compile-time safety.
      * - Composability
        - Combine multiple conditions easily using logical operators.
      * - Cleaner Repositories
        - Avoids writing multiple query methods for every parameter combination.

Conclusion:
   Spring Data JPA Specifications offer a **clean, reusable, and dynamic** way to
   construct database queries without writing explicit SQL or JPQL.

   They are ideal for implementing **search filters**, **advanced query conditions**, or
   **multi-criteria search APIs** in enterprise applications.

   By leveraging ``Specification.where()``, ``and()``, and ``or()``, developers can
   build complex queries with minimal code while maintaining readability and reusability.
