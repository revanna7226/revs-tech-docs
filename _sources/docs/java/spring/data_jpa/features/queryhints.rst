Spring Data JPA — Query Hints
=============================

**Query Hints** in Spring Data JPA are used to provide additional instructions (or "hints")
to the underlying JPA provider (such as Hibernate) about how a query should be executed.  
These hints can influence query performance, caching, locking, and other execution behaviors.

Spring Data JPA supports query hints via the ``@QueryHints`` annotation or directly through
``@QueryHint`` on repository methods.

They are commonly used for:
    - Enabling/disabling query caching
    - Making queries read-only
    - Controlling fetch size or timeout
    - Applying database-specific optimizations
    - Overriding default persistence provider settings

You can use both JPA standard hints and vendor-specific hints (e.g., Hibernate-specific).

Inspect the statistics of your query to see if query hints can help improve performance.

.. code-block:: properties

    # application.properties
    spring.jpa.properties.hibernate.generate_statistics=true
    logging.level.org.hibernate.stat=debug

Basic Usage:
    Spring Data JPA allows you to define query hints at the repository method level using
    the ``@QueryHints`` annotation (a container for one or more ``@QueryHint`` annotations).

    Each ``@QueryHint`` has two attributes:
      - ``name`` — the name/key of the hint
      - ``value`` — the corresponding hint value

    #. Example (Read-Only Query):

        .. code-block:: java

            import org.springframework.data.jpa.repository.JpaRepository;
            import org.springframework.data.jpa.repository.QueryHints;

            import jakarta.persistence.QueryHint;
            import java.util.List;

            @Entity
            public class Employee {
                @Id
                @GeneratedValue(strategy = GenerationType.IDENTITY)
                private Long id;

                private String name;
                private String department;

                // Getters and Setters
            }

            public interface EmployeeRepository extends JpaRepository<Employee, Long> {

                @QueryHints(@QueryHint(name = org.hibernate.jpa.HibernateHints.HINT_READ_ONLY, value = "true"))
                List<Employee> findByDepartment(String department);
            }

        Explanation:
            - The query result is marked as **read-only**, meaning Hibernate will not track changes to the entities.
            - This can improve performance for large queries when updates are not required.
            - ``HINT_READ_ONLY`` is specific to Hibernate (vendor-specific).


    #. Example (Enable Second-Level Cache):

        If you have configured a second-level cache (e.g., EHCache or Caffeine),
        you can instruct Hibernate to use it for a particular query:

        .. code-block:: java

            import org.springframework.data.jpa.repository.QueryHints;
            import jakarta.persistence.QueryHint;

            public interface ProductRepository extends JpaRepository<Product, Long> {

                @QueryHints({
                    @QueryHint(name = org.hibernate.jpa.HibernateHints.HINT_CACHEABLE, value = "true"),
                    @QueryHint(name = org.hibernate.jpa.HibernateHints.HINT_CACHE_REGION, value = "productQueries")
                })
                List<Product> findByCategory(String category);
            }

        Explanation:
        - ``HINT_CACHEABLE`` enables query caching.
        - ``HINT_CACHE_REGION`` specifies the cache region to store query results.
        - Query caching should be used carefully as it increases memory usage.

    #. Example (Timeout and Fetch Size):

        You can also define query execution hints such as fetch size or timeout.

        .. code-block:: java

            public interface CustomerRepository extends JpaRepository<Customer, Long> {

                @QueryHints({
                    @QueryHint(name = org.hibernate.jpa.HibernateHints.HINT_FETCH_SIZE, value = "50"),
                    @QueryHint(name = org.hibernate.jpa.HibernateHints.HINT_TIMEOUT, value = "5000")
                })
                List<Customer> findAll();
            }

        Explanation:
            - ``HINT_FETCH_SIZE`` tells the database driver how many rows to fetch in each batch.
            - ``HINT_TIMEOUT`` defines the query timeout (in milliseconds).
            - These can help optimize performance in large data queries.


        JPA Standard Hints vs Vendor-Specific Hints:

        There are **two main types** of query hints
            1. **JPA Standard Hints** — defined in ``javax.persistence.QueryHints`` (portable across JPA providers)
            2. **Vendor-Specific Hints** — provided by the implementation (e.g., Hibernate)

        Common Standard Hints:

        .. list-table::
            :header-rows: 1
            :widths: 40 60

            * - Hint Name
              - Description
            * - ``javax.persistence.query.timeout``
              - Query timeout in milliseconds
            * - ``javax.persistence.fetchgraph``
              - Apply a named entity graph
            * - ``javax.persistence.loadgraph``
              - Load an entity graph partially
            * - ``javax.persistence.cache.retrieveMode``
              - Defines cache retrieval behavior
            * - ``javax.persistence.cache.storeMode``
              - Defines cache storage behavior

        Common Hibernate-Specific Hints:

        .. list-table::
            :header-rows: 1
            :widths: 40 60

            * - Hint Name
              - Description
            * - ``org.hibernate.readOnly``
              - Marks query results as read-only
            * - ``org.hibernate.cacheable``
              - Enables query caching
            * - ``org.hibernate.cacheRegion``
              - Sets a specific cache region
            * - ``org.hibernate.fetchSize``
              - Fetch batch size for JDBC
            * - ``org.hibernate.timeout``
              - Query timeout (in seconds)
            * - ``org.hibernate.comment``
              - Adds a comment to the SQL (for debugging)
        

    #. Example (Entity Graphs with Query Hints):

        Entity graphs can be combined with query hints to customize fetching strategies.

        .. code-block:: java

            @EntityGraph(attributePaths = {"department", "projects"})
            @QueryHints(@QueryHint(name = org.hibernate.jpa.HibernateHints.HINT_READ_ONLY, value = "true"))
            List<Employee> findAll();

        Explanation:
            - ``@EntityGraph`` tells JPA which relationships to eagerly fetch.
            - ``@QueryHint`` ensures read-only results.
            - This combination provides both performance and fine-grained control over fetching.


    #. Using @Query with Query Hints:

        You can apply query hints to custom JPQL or native queries as well.

        .. code-block:: java

            @Query("SELECT e FROM Employee e WHERE e.department = :dept")
            @QueryHints(@QueryHint(name = org.hibernate.jpa.HibernateHints.HINT_READ_ONLY, value = "true"))
            List<Employee> getEmployeesByDept(@Param("dept") String department);

        Explanation:
            - Works the same way for custom queries.
            - Hints influence the query behavior during execution, not the query structure.

    #. Dynamic Query Hints (Programmatic):

        If you create queries programmatically using ``EntityManager``, you can set hints dynamically:

        .. code-block:: java

            @PersistenceContext
            private EntityManager em;

            public List<Employee> findReadOnlyEmployees() {
                TypedQuery<Employee> query = em.createQuery("SELECT e FROM Employee e", Employee.class);
                query.setHint(org.hibernate.jpa.HibernateHints.HINT_READ_ONLY, true);
                return query.getResultList();
            }

        Explanation:
            - ``setHint()`` can be called on any JPA query before execution.
            - Useful for dynamic or conditional hints.

Best Practices:
    - Use query hints sparingly — only when tuning is required.
    - Avoid vendor-specific hints if portability across JPA providers is needed.
    - Prefer read-only hints for queries that don’t modify entities.
    - Use second-level caching only for frequently executed and stable queries.
    - Always measure performance impact before and after applying hints.

References:
    - `Youtube Video <https://youtu.be/aredZWI4Tz0?si=qcFz_9sSRNtGykWA>`_
    - `Spring Data JPA Reference Documentation <https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-hints>`_
    - `Hibernate Query Hints Documentation <https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#hql-query-hints>`_
    - `Jakarta Persistence Specification <https://jakarta.ee/specifications/persistence/>`_
