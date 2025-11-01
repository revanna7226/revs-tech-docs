Transaction Propagation
=======================

Introduction:
    Spring Data JPA provides robust transaction management through the
    ``@Transactional`` annotation. One of its most powerful features is **transaction
    propagation**, which defines how transactions relate to each other when one
    transactional method calls another.

    In other words, propagation determines **what happens to an existing transaction
    when a new transactional method is invoked**.

    Spring provides several propagation behaviors via the ``Propagation`` enum.

Available Transaction Propagation Types:
    1. ``REQUIRED`` (Default)
    2. ``REQUIRES_NEW``
    3. ``SUPPORTS``
    4. ``MANDATORY``
    5. ``NOT_SUPPORTED``
    6. ``NEVER``
    7. ``NESTED``

    Below we explain each type in detail with examples.

1. Propagation.REQUIRED
    **Definition:**
    If a transaction exists, the current method joins it.
    If there's no existing transaction, a new one is created.

    **Use case:**
    This is the **default** and most commonly used propagation behavior.

    **Example:**

    .. code-block:: java

        @Service
        public class OrderService {

            @Autowired
            private PaymentService paymentService;

            @Transactional(propagation = Propagation.REQUIRED)
            public void placeOrder() {
                System.out.println("Placing order...");
                paymentService.processPayment();
            }
        }

        @Service
        public class PaymentService {

            @Transactional(propagation = Propagation.REQUIRED)
            public void processPayment() {
                System.out.println("Processing payment...");
            }
        }

    **Explanation:**
    
    - If ``placeOrder()`` starts a transaction, ``processPayment()`` joins the same one.
    - If ``placeOrder()`` rolls back, both operations roll back together.

2. Propagation.REQUIRES_NEW
    **Definition:**
    Always starts a new transaction.
    If an existing transaction is present, it is suspended until the new one completes.

    **Use case:**
    Used when you need to **run an independent transaction** within another.

    **Example:**

    .. code-block:: java

        @Service
        public class OrderService {

            @Autowired
            private PaymentService paymentService;

            @Transactional(propagation = Propagation.REQUIRED)
            public void placeOrder() {
                System.out.println("Order started");
                paymentService.processPayment();
                throw new RuntimeException("Error in order");
            }
        }

        @Service
        public class PaymentService {

            @Transactional(propagation = Propagation.REQUIRES_NEW)
            public void processPayment() {
                System.out.println("Payment processed successfully");
            }
        }

    **Explanation:**

       - Here, ``processPayment()`` runs in a **new transaction** independent of ``placeOrder()``.
       - Even if ``placeOrder()`` fails and rolls back, ``processPayment()`` will still be committed.

3. Propagation.SUPPORTS
    **Definition:**
    If a transaction exists, the method joins it.
    If there’s no transaction, it executes non-transactionally.

    **Use case:**
    Used for methods that can safely execute with or without a transaction.

    **Example:**

    .. code-block:: java

        @Service
        public class ProductService {

            @Transactional(propagation = Propagation.SUPPORTS)
            public List<Product> listProducts() {
                return productRepository.findAll();
            }
        }

    **Explanation:**
       - If ``listProducts()`` is called from a transactional context, it will join it. Otherwise, it runs without a transaction.

4. Propagation.MANDATORY
    **Definition:**
    The method must run within an existing transaction.
    If no transaction exists, an exception is thrown.

    **Use case:**
    Used when a method **must always** be executed inside a transaction.

    **Example:**

    .. code-block:: java

        @Service
        public class AuditService {

            @Transactional(propagation = Propagation.MANDATORY)
            public void audit(String message) {
                System.out.println("Auditing: " + message);
            }
        }

        @Service
        public class UserService {

            @Autowired
            private AuditService auditService;

            @Transactional
            public void createUser() {
                System.out.println("Creating user...");
                auditService.audit("User created");
            }
        }

    **Explanation:**
        - ``auditService.audit()`` requires an active transaction.
        - If you call it outside a transaction, Spring throws ``TransactionRequiredException``.

5. Propagation.NOT_SUPPORTED
    **Definition:**
    Runs the method **non-transactionally**.
    If there’s an active transaction, it is **suspended**.

    **Use case:**
    Used for read-only or reporting operations that **should not be part of any transaction**.

    **Example:**

    .. code-block:: java

        @Service
        public class ReportService {

            @Transactional(propagation = Propagation.NOT_SUPPORTED)
            public void generateReport() {
                System.out.println("Generating report outside transaction");
            }
        }

    **Explanation:**
        If ``generateReport()`` is called within a transaction, Spring will suspend it and run this method outside the transactional context.

6. Propagation.NEVER
    **Definition:**
    Indicates that the method must **not run within a transaction**.
    If a transaction exists, an exception is thrown.

    **Use case:**
    Used for operations that must explicitly **avoid transactions**.

    **Example:**

    .. code-block:: java

        @Service
        public class NotificationService {

            @Transactional(propagation = Propagation.NEVER)
            public void sendNotification() {
                System.out.println("Sending notification without transaction");
            }
        }

    **Explanation:**
        If ``sendNotification()`` is called inside a transactional context, Spring throws ``IllegalTransactionStateException``.

7. Propagation.NESTED
    **Definition:**
    Runs the method within a **nested transaction** if a current transaction exists.
    Otherwise, it behaves like ``REQUIRED`` (starts a new transaction).

    Nested transactions use **savepoints**, so only part of the transaction can be rolled back.

    **Use case:**
    Used when you want a rollback of only a **portion** of the main transaction.

    **Example:**

    .. code-block:: java

        @Service
        public class OrderService {

            @Autowired
            private InventoryService inventoryService;

            @Transactional
            public void placeOrder() {
                System.out.println("Placing order...");
                try {
                    inventoryService.updateInventory();
                } catch (Exception e) {
                    System.out.println("Error in inventory update, continuing order...");
                }
            }
        }

        @Service
        public class InventoryService {

            @Transactional(propagation = Propagation.NESTED)
            public void updateInventory() {
                System.out.println("Updating inventory...");
                throw new RuntimeException("Inventory update failed");
            }
        }

    **Explanation:**
        - ``updateInventory()`` creates a nested transaction with a savepoint.
        - When it fails, only the nested transaction rolls back, while ``placeOrder()`` continues normally.

**Summary Table:**
   .. list-table::
      :header-rows: 1
      :widths: 20 50

      * - **Propagation Type**
        - **Behavior**
      * - REQUIRED
        - Join existing or create new transaction
      * - REQUIRES_NEW
        - Always start new transaction
      * - SUPPORTS
        - Join if exists, else non-transactional
      * - MANDATORY
        - Must join existing, else error
      * - NOT_SUPPORTED
        - Suspend existing, run non-transactional
      * - NEVER
        - Throw error if transaction exists
      * - NESTED
        - Start nested transaction with savepoint



Conclusion:
    Spring Data JPA’s ``@Transactional`` propagation options allow fine-grained
    control over how your service methods participate in transactions. By selecting
    the right propagation type, you can balance **data consistency**, **performance**,
    and **business logic independence** effectively.

**Reference Links:**

.. youtube:: NKuo8hyIwgQ?si=rpgm8Fe0Qd3-JZ8-
   :width: 100%

