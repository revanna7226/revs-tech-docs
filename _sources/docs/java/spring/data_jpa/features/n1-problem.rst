
Hibernate N+1 Query Problem
===========================

Overview:
   The **N+1 Query Problem** is a common performance issue in **Hibernate** and **JPA**
   that occurs when fetching related entities in a one-to-many or many-to-one relationship.

   It can cause **a large number of unnecessary database queries**, which severely impacts
   application performance.

What is the N+1 Query Problem?:
   The **N+1 problem** occurs when:

   1. **1 query** is executed to fetch the parent entities.
   2. For each of those **N parent entities**, **1 additional query** is executed to fetch the child entities.

   Hence, the total number of queries = **1 (main query) + N (child queries)**.

Example Scenario:
   Suppose we have two entities:

   - ``Department`` (parent)
   - ``Employee`` (child)

   A department can have multiple employees.

Entity Definitions:
   .. code-block:: java
      
      @Entity
      @Table(name = "departments")
      public class Department {

         @Id
         @GeneratedValue(strategy = GenerationType.IDENTITY)
         private Long id;

         private String name;

         @OneToMany(mappedBy = "department", fetch = FetchType.LAZY)
         private List<Employee> employees;

         // Getters and Setters
      }

      ---

      @Entity
      @Table(name = "employees")
      public class Employee {

         @Id
         @GeneratedValue(strategy = GenerationType.IDENTITY)
         private Long id;

         private String name;

         @ManyToOne(fetch = FetchType.LAZY)
         @JoinColumn(name = "department_id")
         private Department department;

         // Getters and Setters
      }

Repository Layer:
   .. code-block:: java

      @Repository
      public interface DepartmentRepository extends JpaRepository<Department, Long> {
      }

Service Layer Demonstration:
   .. code-block:: java

      @Service
      public class DepartmentService {

         @Autowired
         private DepartmentRepository departmentRepository;

         @Transactional(readOnly = true)
         public void displayDepartmentsAndEmployees() {
            List<Department> departments = departmentRepository.findAll();

            for (Department dept : departments) {
                  System.out.println("Department: " + dept.getName());

                  // Triggers a SELECT query for each department
                  dept.getEmployees().forEach(emp -> System.out.println("   Employee: " + emp.getName()));
            }
         }
      }

Generated SQL (N+1 Problem):
   .. code-block:: sql

      -- 1 Query to fetch all departments
      SELECT * FROM departments;

      -- N additional queries (one per department)
      SELECT * FROM employees WHERE department_id = 1;
      SELECT * FROM employees WHERE department_id = 2;
      SELECT * FROM employees WHERE department_id = 3;
      ...

   Result: **N+1 queries**

Why is this a Problem?:
   - Each department causes a separate query for its employees.
   - When there are hundreds of departments, this leads to **hundreds of queries**.
   - It increases:
  
       - Database load
       - Network latency
       - Transaction time

Fixing the N+1 Query Problem:
   There are multiple ways to solve the N+1 problem in Hibernate:

   1. **Fetch Join (Recommended)**
   2. **Entity Graphs**
   3. **Batch Fetching**

1. **Using Fetch Join (JPQL):**
 
   You can use a **JOIN FETCH** clause in JPQL to load both parent and child entities in a single query.

   .. code-block:: java

      @Repository
      public interface DepartmentRepository extends JpaRepository<Department, Long> {

         @Query("SELECT DISTINCT d FROM Department d JOIN FETCH d.employees")
         List<Department> findAllDepartmentsWithEmployees();
      }

   Usage Example:
   
   .. code-block:: java

      @Service
      public class DepartmentService {

         @Autowired
         private DepartmentRepository departmentRepository;

         @Transactional(readOnly = true)
         public void displayDepartmentsAndEmployees() {
            List<Department> departments = departmentRepository.findAllDepartmentsWithEmployees();

            for (Department dept : departments) {
                  System.out.println("Department: " + dept.getName());
                  dept.getEmployees().forEach(emp -> System.out.println("   Employee: " + emp.getName()));
            }
         }
      }

   Generated SQL:

   .. code-block:: sql

      SELECT d.*, e.*
      FROM departments d
      JOIN employees e ON d.id = e.department_id;

   **Result:** Only **one query** is executed — the N+1 problem is eliminated.

2. **Using Entity Graphs:**
   
   Entity Graphs provide a declarative way to define fetch plans.

   .. code-block:: java

      @Repository
      public interface DepartmentRepository extends JpaRepository<Department, Long> {

         @EntityGraph(attributePaths = {"employees"})
         @Query("SELECT d FROM Department d")
         List<Department> findAllWithEmployees();
      }

   Explanation:

   - The ``@EntityGraph`` annotation instructs JPA to fetch the ``employees`` association eagerly.
   - It avoids multiple SELECT queries by fetching related entities in one go.

3. **Using Hibernate Batch Fetching:**

   Batch fetching loads collections or entities in batches, rather than one by one.

   Add this to your ``application.yml``:

   .. code-block:: yaml

      spring:
         jpa:
            properties:
               hibernate:
               default_batch_fetch_size: 10

   Then annotate the relationship with ``@BatchSize``:

   .. code-block:: java

      @OneToMany(mappedBy = "department", fetch = FetchType.LAZY)
      @BatchSize(size = 10)
      private List<Employee> employees;

   Explanation:

   - Hibernate fetches employees for 10 departments in a single batch.
   - Reduces query count from N+1 to approximately **N/10 + 1**.

**Comparison of Solutions:**
   +--------------------------+-----------------------------------+--------------------------------+
   | Solution Type            | Description                       | Query Count Reduction          |
   +==========================+===================================+================================+
   | Fetch Join               | JPQL join to fetch associations   | Single query                   |
   +--------------------------+-----------------------------------+--------------------------------+
   | Entity Graph             | Declarative fetch plan            | Single query                   |
   +--------------------------+-----------------------------------+--------------------------------+
   | Batch Fetching           | Fetch associations in groups      | Partial reduction (batched)    |
   +--------------------------+-----------------------------------+--------------------------------+

Best Practices:
   - Use **Fetch Join** for simple associations.
   - Use **Entity Graphs** for dynamic fetch strategies.
   - Use **Batch Fetching** for large datasets with memory constraints.
   - Avoid ``FetchType.EAGER`` on collections — it can cause unintentional joins.
   - Always monitor queries using logs:
     
     .. code-block:: yaml

        spring:
          jpa:
            show-sql: true
            properties:
              hibernate:
                format_sql: true

Conclusion:
   The **Hibernate N+1 Query Problem** is a common performance pitfall that can severely degrade
   application performance in data-rich systems.

   **Key Takeaways:**

   - The problem arises when each parent entity triggers separate queries for its children.
   - **Fetch Join** and **Entity Graphs** are the most effective solutions.
   - **Batch Fetching** helps in scenarios with large datasets.
   - Always analyze generated SQL queries during development to detect and prevent the N+1 issue early.

   By using the right fetching strategy, you can significantly optimize your application's performance
   and reduce unnecessary database load.
