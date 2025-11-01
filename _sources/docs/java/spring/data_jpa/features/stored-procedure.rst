Stored Procedure
================

Introduction:
    A **Stored Procedure** is a precompiled collection of SQL statements stored in the database.  
    They can be used to encapsulate business logic at the database level for performance,  
    security, or reusability purposes.

    Spring Data JPA provides several ways to call stored procedures from a Spring Boot application.

    This document explains:
    - Different ways to call stored procedures in Spring Data JPA
    - Code examples for each approach
    - Best practices for using stored procedures effectively

Why Use Stored Procedures?
    * Encapsulation of complex SQL logic within the database.
    * Performance improvements due to precompiled SQL.
    * Reduced data transfer between application and database.
    * Centralized business rules and data integrity logic.

Ways to Call Stored Procedures in Spring Data JPA
    1. Using ``@Procedure`` annotation in Repository
    2. Using ``@NamedStoredProcedureQuery`` in Entity
    3. Using ``EntityManager`` (Programmatic Approach)

    Each approach is detailed below.

    *1. Using @Procedure Annotation*

    Spring Data JPA allows executing stored procedures directly from repository interfaces
    using the ``@Procedure`` annotation.

    **Example Stored Procedure in Database:**

    .. code-block:: sql

        CREATE PROCEDURE GET_TOTAL_ORDERS(IN customer_id BIGINT, OUT total_orders INT)
        BEGIN
            SELECT COUNT(*) INTO total_orders FROM orders WHERE customer_id = customer_id;
        END;

    **Entity Class (Optional):**

    .. code-block:: java

        @Entity
        @Table(name = "orders")
        public class Order {
            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private Long customerId;
            private Double amount;
        }

    **Repository Example:**

    .. code-block:: java

        public interface OrderRepository extends JpaRepository<Order, Long> {

            @Procedure(name = "GET_TOTAL_ORDERS")
            int getTotalOrders(@Param("customer_id") Long customerId);
        }

    **Service Layer Example:**

    .. code-block:: java

        @Service
        public class OrderService {

            @Autowired
            private OrderRepository orderRepository;

            public void printTotalOrders(Long customerId) {
                int total = orderRepository.getTotalOrders(customerId);
                System.out.println("Total Orders: " + total);
            }
        }

    **Explanation:**
       - Here, ``@Procedure`` binds the repository method to the database stored procedure.  
       - The parameters are mapped using ``@Param``.

    **2. Using @NamedStoredProcedureQuery in Entity**

    You can also define a stored procedure directly within an entity using ``@NamedStoredProcedureQuery``.

    **Example Stored Procedure in Database:**

    .. code-block:: sql

        CREATE PROCEDURE FIND_CUSTOMER_NAME(IN cust_id BIGINT, OUT cust_name VARCHAR(100))
        BEGIN
            SELECT name INTO cust_name FROM customers WHERE id = cust_id;
        END;

    **Entity Definition:**

    .. code-block:: java

        @Entity
        @Table(name = "customers")
        @NamedStoredProcedureQuery(
            name = "Customer.findNameById",
            procedureName = "FIND_CUSTOMER_NAME",
            parameters = {
                @StoredProcedureParameter(mode = ParameterMode.IN, name = "cust_id", type = Long.class),
                @StoredProcedureParameter(mode = ParameterMode.OUT, name = "cust_name", type = String.class)
            }
        )
        public class Customer {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;
        }

    **Repository Example:**

    .. code-block:: java

        public interface CustomerRepository extends JpaRepository<Customer, Long> {

            @Procedure(name = "Customer.findNameById")
            String findNameById(@Param("cust_id") Long id);
        }

    **Service Layer Example:**

    .. code-block:: java

        @Service
        public class CustomerService {

            @Autowired
            private CustomerRepository customerRepository;

            public void displayCustomerName(Long id) {
                String name = customerRepository.findNameById(id);
                System.out.println("Customer Name: " + name);
            }
        }

    **Explanation:**
       - Here, ``@NamedStoredProcedureQuery`` associates a procedure definition with the entity.  
       - Spring Data JPA maps repository methods to it using the defined name.

    **3. Using EntityManager (Programmatic Approach)**

    In cases where you need dynamic control or complex parameter handling,
    you can call stored procedures programmatically using ``EntityManager``.

    **Example Stored Procedure in Database:**

    .. code-block:: sql

        CREATE PROCEDURE GET_TOTAL_SALES(IN month_param VARCHAR(10), OUT total_sales DOUBLE)
        BEGIN
            SELECT SUM(amount) INTO total_sales FROM orders WHERE MONTH(order_date) = month_param;
        END;

    **Service Layer Example:**

    .. code-block:: java

        @Service
        public class SalesService {

            @PersistenceContext
            private EntityManager entityManager;

            public Double getTotalSales(String month) {
                StoredProcedureQuery query = entityManager
                    .createStoredProcedureQuery("GET_TOTAL_SALES");

                query.registerStoredProcedureParameter("month_param", String.class, ParameterMode.IN);
                query.registerStoredProcedureParameter("total_sales", Double.class, ParameterMode.OUT);

                query.setParameter("month_param", month);
                query.execute();

                return (Double) query.getOutputParameterValue("total_sales");
            }
        }

    **Explanation:**
       * ``createStoredProcedureQuery()`` is used to call the stored procedure.
       * ``registerStoredProcedureParameter()`` defines input/output parameters.
       * ``setParameter()`` sets the input value.
       * ``getOutputParameterValue()`` retrieves output results.

Best Practices:
    * Always **match parameter names** in Java with those in the stored procedure.
    * Handle **exceptions** and transaction boundaries properly.
    * Use **read-only transactions** when only fetching data.
    * Avoid excessive use of stored procedures for simple CRUD — use JPA queries instead.
    * Prefer ``EntityManager`` for **complex or dynamic** procedure calls.

Comparison Summary:
   .. list-table::
      :header-rows: 1
      :widths: 25 75

      * - **Approach**
        - **When to Use**
      * - ``@Procedure`` Annotation
        - Simple and quick mapping to stored procedures for straightforward use cases.
      * - ``@NamedStoredProcedureQuery``
        - When you want to define the mapping at entity level and reuse it across the repository.
      * - ``EntityManager``
        - For dynamic execution, multiple input/output parameters, or runtime configuration.

Conclusion:
    Spring Data JPA provides multiple ways to interact with stored procedures.
    For **simple mappings**, ``@Procedure`` or ``@NamedStoredProcedureQuery`` works best.  
    For **complex or dynamic** procedures, ``EntityManager`` provides full flexibility.

    By integrating stored procedures properly, you can combine **database performance**
    and **Spring’s transactional consistency** effectively.

Reference Links:
    - `Youtube Video by Java Techie <https://youtu.be/oWbKtmtPGpg?si=krdoFbrEIqOfkW-V>`_