Pagination and Sorting
========================

In real-world applications, working with a large dataset can lead to performance issues if all records are fetched at once. 
Spring Data JPA provides **Pagination** and **Sorting** mechanisms to retrieve manageable subsets of data in a structured order.

This feature is primarily provided through the **PagingAndSortingRepository** and **JpaRepository** interfaces.

#. Key Concepts:
    1. **Pagination**
    Pagination helps in splitting a large dataset into smaller chunks (pages).
    It is achieved using the ``Pageable`` interface.

    - ``Pageable`` defines the pagination information such as page number, page size, and sorting order.
    - ``Page`` is a sublist of a list of objects, providing information about the total number of pages and elements.

    2. **Sorting**
    Sorting allows you to order the data based on one or more fields.
    The ``Sort`` class provides multiple ways to define sorting behavior.

#. Repository Interfaces:
    Spring Data JPA provides the following interfaces for pagination and sorting:

    - ``PagingAndSortingRepository<T, ID>``  
    Extends ``CrudRepository`` and provides methods for pagination and sorting.

    - ``JpaRepository<T, ID>``  
    Extends ``PagingAndSortingRepository`` and provides additional JPA-related functionality.

#. Method Signatures:
    Commonly used repository methods for pagination and sorting:

    .. code-block:: java

        Page<T> findAll(Pageable pageable);
        List<T> findAll(Sort sort);

#. Pagination Example:
    Let's consider an example using an entity class ``Employee``.

    **Entity Class:**

    .. code-block:: java

        @Entity
        public class Employee {
            
            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;
            
            private String name;
            private String department;
            private double salary;

            // Getters and Setters
        }

    **Repository Interface:**

    .. code-block:: java

        public interface EmployeeRepository extends JpaRepository<Employee, Long> {
        }

    **Service Layer Example:**

    .. code-block:: java

        @Service
        public class EmployeeService {

            @Autowired
            private EmployeeRepository employeeRepository;

            public Page<Employee> getEmployees(int page, int size) {
                Pageable pageable = PageRequest.of(page, size);
                Page<Employee> empPage = employeeRepository.findAll(pageable);
                
                List<Employee> employees = empPage.getContent();  // Get the list of Employees on the current page
                int totalPages = empPage.getTotalPages();  // Get total number of pages
                long totalElements = empPage.getTotalElements();  // Get total number of elements

                System.out.println("Total Pages: " + totalPages);
                System.out.println("Total Elements: " + totalElements);
                employees.forEach(System.out::println);  // Print the employees on the current page
                return empPage;
            }
        }

    **Controller Example:**

    .. code-block:: java

        @RestController
        @RequestMapping("/employees")
        public class EmployeeController {

            @Autowired
            private EmployeeService employeeService;

            @GetMapping
            public Page<Employee> getPaginatedEmployees(
                    @RequestParam(defaultValue = "0") int page,
                    @RequestParam(defaultValue = "5") int size) {

                return employeeService.getEmployees(page, size);
            }
        }

    **Explanation:**

    - ``PageRequest.of(page, size)`` creates a ``Pageable`` object.
    - Page number is **zero-based**, so ``page = 0`` means the first page.
    - The ``Page<Employee>`` returned contains:
        - Current page content
        - Total pages
        - Total elements
        - Page size and number

#. Sorting Example:
    You can easily add sorting to pagination using ``PageRequest.of(page, size, Sort.by(...))``.

    **Service Layer Example with Sorting:**

    .. code-block:: java

        @Service
        public class EmployeeService {

            @Autowired
            private EmployeeRepository employeeRepository;

            public Page<Employee> getSortedEmployees(int page, int size, String sortBy) {
                Pageable pageable = PageRequest.of(page, size, Sort.by(sortBy));
                return employeeRepository.findAll(pageable);
            }
        }

    **Controller Example:**

    .. code-block:: java

        @RestController
        @RequestMapping("/employees")
        public class EmployeeController {

            @Autowired
            private EmployeeService employeeService;

            @GetMapping("/sorted")
            public Page<Employee> getSortedEmployees(
                    @RequestParam(defaultValue = "0") int page,
                    @RequestParam(defaultValue = "5") int size,
                    @RequestParam(defaultValue = "name") String sortBy) {

                return employeeService.getSortedEmployees(page, size, sortBy);
            }
        }

    **Explanation:**
      - ``Sort.by(sortBy)`` defines the field name used for sorting.
      - You can define ascending or descending order as follows:
          - ``Sort.by(Sort.Direction.ASC, "name")``
          - ``Sort.by(Sort.Direction.DESC, "salary")``

#. Sorting by Multiple Fields:
    You can define multiple sorting fields using ``Sort.by(...)`` as shown below:

    .. code-block:: java

        Sort sort = Sort.by("department").ascending()
                        .and(Sort.by("salary").descending());
        Pageable pageable = PageRequest.of(0, 10, sort);
        Page<Employee> employees = employeeRepository.findAll(pageable);

        // only sorting without pagination
        List<Employee> employees = employeeRepository.findAll(Sort.by(sortBy).ascending());
        
        // Sorting by Multiple Fields
        List<Employee> employees = employeeRepository.findAll(Sort.by("name").ascending().and(Sort.by("department").descending()));

#. Custom Queries with Pagination:
    You can also use pagination in custom query methods or JPQL queries.

    **Example:**

    .. code-block:: java

        @Query("SELECT e FROM Employee e WHERE e.department = :dept")
        Page<Employee> findByDepartment(@Param("dept") String dept, Pageable pageable);

    **Usage:**

    .. code-block:: java

        Pageable pageable = PageRequest.of(0, 5, Sort.by("salary").descending());
        Page<Employee> employees = employeeRepository.findByDepartment("IT", pageable);

    **Explanation:**
      - The query fetches only the employees from a specific department.
      - Pagination and sorting are automatically applied by Spring Data JPA.

#. Summary:
    - **Pagination** helps manage large datasets efficiently.
    - **Sorting** provides ordered retrieval of data.
    - ``Pageable`` and ``Sort`` classes are central to these operations.
    - ``JpaRepository`` offers built-in methods for both pagination and sorting.
    - You can combine both features easily in repository queries.

#. Advantages:
    - Reduces memory load by fetching limited records.
    - Improves response times in REST APIs.
    - Easy integration with Spring MVC for REST endpoints.
