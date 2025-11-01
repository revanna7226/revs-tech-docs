Spring Data JPA Caching in Detail
=================================

Overview:
    Caching is a mechanism used to temporarily store frequently accessed data in memory
    so that future requests for the same data can be served faster without hitting the database.

    **Spring Data JPA** provides support for caching through integration with
    **JPA providers** such as **Hibernate**, which offers both first-level and second-level caching.

    Caching improves performance and scalability by reducing database access,
    network latency, and query execution time.

Types of Caching in JPA:
    There are two primary levels of caching in JPA:

    1. **First-Level Cache (Session Cache)**:

       - Enabled by default in Hibernate.
       - Operates at the **EntityManager** or **Session** level.
       - Caches entities within the same transaction or session.
       - Cleared when the session ends.

    2. **Second-Level Cache (Shared Cache)**:

       - Optional and configurable.
       - Operates across sessions.
       - Stores entities, collections, and query results.
       - Requires a caching provider like **Ehcache**, **Caffeine**, or **Hazelcast**.

First-Level Cache/Session Cache:
    The **first-level cache** is automatically enabled in Hibernate and cannot be turned off.

    Example:

    .. code-block:: java

        @Service
        public class EmployeeService {

            @Autowired
            private EmployeeRepository employeeRepository;

            @Transactional
            public void demonstrateFirstLevelCache() {
                // The first call hits the database
                Employee emp1 = employeeRepository.findById(1L).orElseThrow();

                // The second call fetches from the first-level cache (no DB query)
                Employee emp2 = employeeRepository.findById(1L).orElseThrow();

                System.out.println(emp1 == emp2); // true
            }
        }

    Explanation:
       - Both calls use the same **EntityManager** context.
       - The second call retrieves the entity from the **first-level cache** instead of querying the database.
       - The cache is cleared once the transaction completes.

Second-Level Cache/Shared Cache:
    To enable **second-level caching**, we must configure:

    1. Hibernate cache properties
    2. Cache provider (Ehcache, Caffeine, etc.)
    3. Entity-level cache annotations

    **Step 1: Add Dependencies**:
    For **Ehcache**, add the following dependency in ``pom.xml``:

    .. code-block:: xml

        <dependency>
            <groupId>org.hibernate.orm</groupId>
            <artifactId>hibernate-jcache</artifactId>
        </dependency>
        <dependency>
            <groupId>org.ehcache</groupId>
            <artifactId>ehcache</artifactId>
        </dependency>

    **Step 2: Configure Hibernate Caching**:
    Add the following properties to your ``application.yml``:

    .. code-block:: yaml

        spring:
            jpa:
                properties:
                    hibernate:
                        cache:
                            use_second_level_cache: true
                            use_query_cache: true
                            region:
                                factory_class: org.hibernate.cache.jcache.JCacheRegionFactory
                hibernate:
                    ddl-auto: update

    **Step 3: Create Ehcache Configuration:**
    Create a file named ``ehcache.xml`` in ``src/main/resources``:

    .. code-block:: xml

        <config
                xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
                xmlns='http://www.ehcache.org/v3'
                xsi:schemaLocation="http://www.ehcache.org/v3 http://www.ehcache.org/schema/ehcache-core.xsd">

            <cache alias="employee">
                <heap unit="entries">1000</heap>
                <expiry>
                    <ttl unit="seconds">300</ttl>
                </expiry>
            </cache>

        </config>

    - This block defines one cache instance with the alias (name) employee        
    - ``<heap unit="entries">1000</heap>`` This defines the in-memory storage size of the cache known as the heap store.
    - ``unit="entries"`` specifies the unit type.
    - The value 1000 means this cache can hold up to 1000 entries in the JVM heap before older entries are evicted. 
    - Heap storage is typically the fastest cache tier stored directly in Java memory.
    - ``<expiry>`` and ``<ttl unit="seconds">300</ttl>``, This defines the expiration policy for entries within the cache.
    - ``ttl`` stands for Time-To-Live.
    - ``unit="seconds"`` and value ``300`` mean that each entry lives for 300 seconds (5 minutes) after creation or update.
    - Once the time expires, the entry is automatically removed or refreshed according to the configured strategy. 

    **Step 4: Enable Caching in the Entity:**
    Annotate your entity class with ``@Cacheable`` and Hibernate’s ``@org.hibernate.annotations.Cache``:

    .. code-block:: java

        @Entity
        @Table(name = "employees")
        @Cacheable
        @Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;
            private String department;

            // Getters and Setters
        }
    
    Cache Concurrency Strategies:
    Hibernate supports multiple caching strategies:

    - ``READ_ONLY`` – For immutable entities (best performance).
    - ``NONSTRICT_READ_WRITE`` – Cache is not updated immediately after transaction.
    - ``READ_WRITE`` – Ensures strong consistency.
    - ``TRANSACTIONAL`` – Uses JTA transactions.

    **Step 5: Repository Layer:**

    .. code-block:: java

        @Repository
        public interface EmployeeRepository extends JpaRepository<Employee, Long> {
        }

    **Step 6: Service Layer Demonstration:**

    .. code-block:: java

        @Service
        public class EmployeeService {

            @Autowired
            private EmployeeRepository employeeRepository;

            @Transactional
            public Employee getEmployeeById(Long id) {
                // First call fetches from DB and caches it
                Employee emp1 = employeeRepository.findById(id).orElseThrow();

                // Second call retrieves from second-level cache
                Employee emp2 = employeeRepository.findById(id).orElseThrow();

                System.out.println("Are both objects same? " + (emp1 == emp2));
                return emp2;
            }
        }

    **Step 7: Enable Query Caching (Optional):**
    You can enable query caching using Hibernate query hints.

    .. code-block:: java

        @Repository
        public interface EmployeeRepository extends JpaRepository<Employee, Long> {

            @Query("SELECT e FROM Employee e WHERE e.department = :dept")
            @org.springframework.data.jpa.repository.QueryHints(
                @javax.persistence.QueryHint(name = org.hibernate.jpa.HibernateHints.HINT_CACHEABLE, value = "true")
            )
            List<Employee> findByDepartment(String dept);
        }

    **Step 8: Testing Caching Behavior:**

    .. code-block:: java

        @SpringBootTest
        public class EmployeeServiceTest {

            @Autowired
            private EmployeeService employeeService;

            @Test
            public void testCaching() {
                employeeService.getEmployeeById(1L); // Hits DB
                employeeService.getEmployeeById(1L); // Uses second-level cache
            }
        }

    You should see that the second call does **not** trigger a SQL query, proving the cache is working.

Benefits of JPA Caching:
    - Reduces database load and improves performance.
    - Faster data access for frequently read entities.
    - Transparent to developers – minimal code changes.
    - Helps scalability under high concurrency.

Best Practices:
    - Use **first-level cache** for short-lived transactions.
    - Use **second-level cache** for frequently accessed static data.
    - Set appropriate TTL (Time To Live) to prevent stale data.
    - Avoid caching write-heavy entities.
    - Monitor cache statistics for performance tuning.

Conclusion:
    Spring Data JPA Caching leverages Hibernate’s first and second-level caching mechanisms
    to optimize database performance and response time.  
    By integrating providers like **Ehcache**, developers can easily cache entities, queries,
    and collections, improving scalability and reducing redundant database calls.

**Key Takeaways:**
    - First-level cache is automatic and session-scoped.  
    - Second-level cache requires explicit configuration.  
    - Query caching enhances read performance for repeated queries.  
    - Proper cache strategy and TTL settings are crucial for data consistency.
