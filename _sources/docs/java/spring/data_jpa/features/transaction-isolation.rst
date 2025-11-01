Transaction Isolation Levels
============================

Introduction:
    In **Spring Data JPA**, transactions can be customized not only by propagation
    behavior but also by **isolation levels**.

    The *isolation level* determines how transaction integrity is visible and
    protected when multiple transactions access the same data concurrently.

    Spring provides these isolation levels via the ``Isolation`` enum, which maps
    directly to JDBC isolation levels.

Why Isolation Level Matters?
    Isolation level defines how **one transaction is isolated from others** in
    terms of:
    - Reading uncommitted data
    - Repeatable reads
    - Phantom reads

    Choosing the correct isolation level is important for ensuring **data
    consistency** and **performance** in concurrent environments.

Available Isolation Levels:
    1. ``DEFAULT``  
    2. ``READ_UNCOMMITTED``  
    3. ``READ_COMMITTED``  
    4. ``REPEATABLE_READ``  
    5. ``SERIALIZABLE``  

    Each level provides different guarantees against common concurrency problems.

Concurrency Problems Overview:
   .. list-table::
      :header-rows: 1
      :widths: 25 75

      * - **Problem**
        - **Description**
      * - Dirty Read
        - A transaction reads data written by another transaction that has not yet committed.
      * - Non-repeatable Read
        - A transaction reads the same row twice and gets different data each time.
      * - Phantom Read
        - A transaction executes the same query twice and gets a different number of rows.

1. Isolation.DEFAULT
    **Definition:**
    Uses the default isolation level of the underlying database.

    **Use Case:**
    Recommended when you trust the database defaults (e.g., most RDBMSs default to
    ``READ_COMMITTED``).

    **Example:**

    .. code-block:: java

        @Service
        public class ProductService {

            @Transactional(isolation = Isolation.DEFAULT)
            public void updateProductPrice(Long id, double price) {
                Product product = productRepository.findById(id).orElseThrow();
                product.setPrice(price);
                productRepository.save(product);
            }
        }

    **Explanation:**
    Spring delegates the isolation control to the database configuration.

1. Isolation.READ_UNCOMMITTED
    **Definition:**
    Allows a transaction to read data that has been modified but not yet committed
    by another transaction.  
    This is the lowest level of isolation and can cause **dirty reads**.

    **Use Case:**
    Used in scenarios where **maximum performance** is needed and occasional dirty
    reads are acceptable.

    **Example:**

    .. code-block:: java

        @Service
        public class OrderService {

            @Transactional(isolation = Isolation.READ_UNCOMMITTED)
            public List<Order> getAllOrders() {
                System.out.println("Reading uncommitted data...");
                return orderRepository.findAll();
            }
        }

    **Explanation:**
    If another transaction updates an order but hasn’t committed yet, this method
    may still see those uncommitted changes.

2. Isolation.READ_COMMITTED
    **Definition:**
    A transaction cannot read data that has not yet been committed by other
    transactions.  
    Prevents **dirty reads** but allows **non-repeatable reads** and **phantom reads**.

    **Use Case:**
    Default for most databases like PostgreSQL, Oracle, and SQL Server.

    **Example:**

    .. code-block:: java

        @Service
        public class CustomerService {

            @Transactional(isolation = Isolation.READ_COMMITTED)
            public Customer getCustomer(Long id) {
                return customerRepository.findById(id).orElseThrow();
            }

            @Transactional(isolation = Isolation.READ_COMMITTED)
            public void updateCustomerName(Long id, String newName) {
                Customer customer = customerRepository.findById(id).orElseThrow();
                customer.setName(newName);
                customerRepository.save(customer);
            }
        }

    **Explanation:**
       - This ensures no dirty reads occur — other transactions cannot see uncommitted data.
       - However, two reads of the same row in the same transaction could yield different results if another transaction commits changes in between.

3. Isolation.REPEATABLE_READ:
    **Definition:**
    Prevents **dirty reads** and **non-repeatable reads**.  
    However, **phantom reads** (new rows matching the query condition appearing
    between executions) can still occur.

    **Use Case:**
    Ideal when transactions must see **consistent data** throughout execution.

    **Example:**

    .. code-block:: java

        @Service
        public class InventoryService {

            @Transactional(isolation = Isolation.REPEATABLE_READ)
            public void checkInventory(Long productId) {
                Product p1 = productRepository.findById(productId).orElseThrow();
                System.out.println("First read: " + p1.getStock());

                // Simulate delay
                try { Thread.sleep(5000); } catch (InterruptedException e) {}

                Product p2 = productRepository.findById(productId).orElseThrow();
                System.out.println("Second read: " + p2.getStock());
            }
        }

    **Explanation:**
       - Both reads return the same stock value, even if another transaction modifies it during the delay.  
       - This prevents non-repeatable reads but not phantom reads (new rows could still appear).

4. Isolation.SERIALIZABLE
    **Definition:**
    The **highest isolation level** — transactions are executed serially, as if one
    after another.  
    Prevents **dirty reads**, **non-repeatable reads**, and **phantom reads**, but
    at the cost of performance.

    **Use Case:**
    Used when **absolute consistency** is required (e.g., financial transactions).

    **Example:**

    .. code-block:: java

        @Service
        public class BankService {

            @Transactional(isolation = Isolation.SERIALIZABLE)
            public void transferMoney(Long fromId, Long toId, double amount) {
                Account from = accountRepository.findById(fromId).orElseThrow();
                Account to = accountRepository.findById(toId).orElseThrow();

                if (from.getBalance() < amount) {
                    throw new RuntimeException("Insufficient funds");
                }

                from.setBalance(from.getBalance() - amount);
                to.setBalance(to.getBalance() + amount);

                accountRepository.save(from);
                accountRepository.save(to);
            }
        }

    **Explanation:**
       - ``SERIALIZABLE`` ensures that transactions run one after another logically.
       - It avoids all concurrency anomalies but can severely impact throughput due to locking and blocking.

Comparison Table:
   .. list-table:: Transaction Isolation Comparison
      :header-rows: 1
      :widths: 25 15 15 15 15

      * - **Isolation Level**
        - **Dirty Reads**
        - **Non-Repeatable Reads**
        - **Phantom Reads**
        - **Performance**
      * - READ_UNCOMMITTED
        - Possible
        - Possible
        - Possible
        - High
      * - READ_COMMITTED
        - Prevented
        - Possible
        - Possible
        - Medium
      * - REPEATABLE_READ
        - Prevented
        - Prevented
        - Possible
        - Low
      * - SERIALIZABLE
        - Prevented
        - Prevented
        - Prevented
        - Lowest
      * - DEFAULT
        - Depends on database default
        - Depends on database default
        - Depends on database default
        - Depends on database default

Conclusion:
   Spring Data JPA allows fine-grained control of transaction **isolation levels**
   through the ``@Transactional`` annotation.  

   Choosing the right isolation level involves balancing **data consistency** and
   **concurrency performance**:

   - Use ``READ_COMMITTED`` for general cases (default in most systems).
   - Use ``REPEATABLE_READ`` for consistent read requirements.
   - Use ``SERIALIZABLE`` for critical financial or accounting transactions.
   - Use ``READ_UNCOMMITTED`` only for read-heavy, non-critical operations.

   By tuning isolation appropriately, developers can optimize both **data safety**
   and **application performance**.

**Reference Links:**

.. youtube:: a9z9x4bxmK4?si=QseREu8xaAViYPUd
   :width: 100%