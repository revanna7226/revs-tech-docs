Spring Data JPA Repositories
=============================

Introduction:
    In **Spring Data JPA**, repositories are central interfaces that help simplify data access layers by reducing boilerplate code.  
    They allow developers to perform CRUD operations, pagination, and custom queries without explicitly writing SQL or JPQL.

    Spring Data JPA automatically implements repository interfaces based on the method names and query annotations provided.

Key Repository Interfaces:
    Spring Data JPA provides several repository interfaces, each offering different levels of functionality:

    1. **CrudRepository<T, ID>**
        - Provides basic CRUD operations.
        - Example methods: ``save()``, ``findById()``, ``findAll()``, ``deleteById()``.

    2. **PagingAndSortingRepository<T, ID>**
        - Extends ``CrudRepository``.
        - Adds pagination and sorting capabilities using ``Pageable`` and ``Sort`` objects.
        - Example methods: ``findAll(Pageable)``, ``findAll(Sort)``.

    1. **JpaRepository<T, ID>**
        - Extends ``PagingAndSortingRepository``.
        - Adds JPA-specific methods like ``flush()``, ``saveAndFlush()``, ``deleteInBatch()``.
        - Commonly used in Spring Boot projects for full JPA functionality.

Repository Hierarchy:
        .. code-block:: text

            Repository
            ├── CrudRepository
            │       └── PagingAndSortingRepository
            │               └── JpaRepository

Basic Example:
    Let's create a simple example for managing ``Employee`` entities.

    **Step 1: Define the Entity**

    .. code-block:: java

        import jakarta.persistence.Entity;
        import jakarta.persistence.GeneratedValue;
        import jakarta.persistence.GenerationType;
        import jakarta.persistence.Id;

        @Entity
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;
            private String name;
            private String department;

            // Constructors
            public Employee() {}
            public Employee(String name, String department) {
                this.name = name;
                this.department = department;
            }

            // Getters and Setters
            public Long getId() { return id; }
            public void setId(Long id) { this.id = id; }

            public String getName() { return name; }
            public void setName(String name) { this.name = name; }

            public String getDepartment() { return department; }
            public void setDepartment(String department) { this.department = department; }
        }

    **Step 2: Define the Repository Interface**

    .. code-block:: java

        import org.springframework.data.jpa.repository.JpaRepository;
        import org.springframework.stereotype.Repository;

        @Repository
        public interface EmployeeRepository extends JpaRepository<Employee, Long> {

            // Derived Query Methods
            List<Employee> findByDepartment(String department);

            List<Employee> findByNameContaining(String keyword);

            // Custom JPQL Query
            @Query("SELECT e FROM Employee e WHERE e.department = :department AND e.name LIKE %:keyword%")
            List<Employee> searchEmployees(@Param("department") String department, @Param("keyword") String keyword);
        }

    **Step 3: Use Repository in Service Layer**

    .. code-block:: java

        import org.springframework.stereotype.Service;
        import org.springframework.beans.factory.annotation.Autowired;

        @Service
        public class EmployeeService {

            @Autowired
            private EmployeeRepository employeeRepository;

            public List<Employee> getAllEmployees() {
                return employeeRepository.findAll();
            }

            public Employee saveEmployee(Employee employee) {
                return employeeRepository.save(employee);
            }

            public List<Employee> getEmployeesByDepartment(String department) {
                return employeeRepository.findByDepartment(department);
            }

            public void deleteEmployee(Long id) {
                employeeRepository.deleteById(id);
            }
        }

    **Step 4: Use in a Controller**

    .. code-block:: java

        import org.springframework.web.bind.annotation.*;
        import java.util.List;

        @RestController
        @RequestMapping("/employees")
        public class EmployeeController {

            private final EmployeeService employeeService;

            public EmployeeController(EmployeeService employeeService) {
                this.employeeService = employeeService;
            }

            @GetMapping
            public List<Employee> getAllEmployees() {
                return employeeService.getAllEmployees();
            }

            @PostMapping
            public Employee createEmployee(@RequestBody Employee employee) {
                return employeeService.saveEmployee(employee);
            }

            @GetMapping("/department/{department}")
            public List<Employee> getByDepartment(@PathVariable String department) {
                return employeeService.getEmployeesByDepartment(department);
            }

            @DeleteMapping("/{id}")
            public void deleteEmployee(@PathVariable Long id) {
                employeeService.deleteEmployee(id);
            }
        }

Query Derivation Mechanism:
    Spring Data JPA derives queries automatically based on method names.  
    It interprets method names to generate SQL or JPQL queries.

    Refer `Official documentation <https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html>`_ for more query methods

    **Examples:**
    - ``findByName(String name)`` → ``SELECT * FROM employee WHERE name = ?``
    - ``findByDepartmentAndName(String department, String name)`` → ``SELECT * FROM employee WHERE department = ? AND name = ?``

Custom Queries:
    You can define custom queries using the ``@Query`` annotation.

    .. code-block:: java

        @Query("SELECT e FROM Employee e WHERE e.name LIKE %:name%")
        List<Employee> searchByName(@Param("name") String name);

Pagination and Sorting:
    Spring Data JPA provides built-in pagination and sorting support.

    Refer `here <pagination-sort.html>`_ for more details

**Summary:**

    **Spring Data JPA Repositories** provide:
        - Simplified data access layer.
        - CRUD, pagination, and sorting support.
        - Query derivation from method names.
        - Custom JPQL/Native queries.
        - Batch operations and integration with JPA.

    By extending the appropriate repository interface, developers can build powerful data layers with minimal boilerplate code.
