Transactions with @Transactional
================================

Overview:
    Transactions are a core concept in enterprise applications to ensure data
    integrity and consistency. In Spring Boot, transactions are managed using
    the ``@Transactional`` annotation provided by the Spring Framework.

    A **transaction** is a unit of work that must either complete entirely or
    fail completely. This ensures the **ACID** properties of database operations:

    * **Atomicity** – All operations within a transaction succeed or none do.
    * **Consistency** – The database remains in a consistent state before and after the transaction.
    * **Isolation** – Each transaction is independent of others.
    * **Durability** – Once committed, the transaction’s changes are permanent.

    Spring Boot simplifies transaction management through declarative annotations,
    primarily ``@Transactional``.

Understanding @Transactional:
    The ``@Transactional`` annotation tells Spring to create a proxy around the
    annotated class or method to handle transaction boundaries automatically.

    When a method annotated with ``@Transactional`` is invoked:

    1. A transaction is started.
    2. The method executes within this transaction.
    3. If the method completes normally, the transaction commits.
    4. If an unchecked exception occurs, the transaction rolls back.

Example of @Transactional Usage:
    Consider a simple banking scenario with two entities: ``Account`` and ``Transaction``.

    **1. Entity Classes:**

    .. code-block:: java

        @Entity
        @Table(name = "accounts")
        public class Account {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String accountNumber;
            private double balance;
            
            // getters and setters
        }

        @Entity
        @Table(name = "transactions")
        public class Transaction {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private double amount;

            @ManyToOne
            @JoinColumn(name = "account_id")
            private Account account;

            // getters and setters
        }


    **2. Repository Interfaces:**

    .. code-block:: java

        public interface AccountRepository extends JpaRepository<Account, Long> {}

        public interface TransactionRepository extends JpaRepository<Transaction, Long> {}


    **3. Service Layer with @Transactional:**

    .. code-block:: java

        @Service
        public class BankingService {

            private final AccountRepository accountRepository;
            private final TransactionRepository transactionRepository;

            @Autowired
            public BankingService(AccountRepository accountRepository,
                                TransactionRepository transactionRepository) {
                this.accountRepository = accountRepository;
                this.transactionRepository = transactionRepository;
            }

            @Transactional
            public void transferFunds(Long fromAccountId, Long toAccountId, double amount) {
                Account fromAccount = accountRepository.findById(fromAccountId)
                        .orElseThrow(() -> new RuntimeException("Sender not found"));
                Account toAccount = accountRepository.findById(toAccountId)
                        .orElseThrow(() -> new RuntimeException("Receiver not found"));

                if (fromAccount.getBalance() < amount) {
                    throw new RuntimeException("Insufficient funds");
                }

                fromAccount.setBalance(fromAccount.getBalance() - amount);
                toAccount.setBalance(toAccount.getBalance() + amount);

                accountRepository.save(fromAccount);
                accountRepository.save(toAccount);

                Transaction debitTransaction = new Transaction();
                debitTransaction.setAccount(fromAccount);
                debitTransaction.setAmount(-amount);

                Transaction creditTransaction = new Transaction();
                creditTransaction.setAccount(toAccount);
                creditTransaction.setAmount(amount);

                transactionRepository.save(debitTransaction);
                transactionRepository.save(creditTransaction);
            }
        }

    Explanation:
       * The method ``transferFunds`` performs multiple database updates.
       * The ``@Transactional`` annotation ensures all operations happen in one transaction.
       * If any exception occurs, all changes (both account updates and transaction records) are rolled back automatically.

Transactional Propagation and Isolation:
    ``@Transactional`` offers advanced attributes to control transaction behavior.

    **Common Attributes:**

    1. **propagation** — Defines how transactions interact.
    2. **isolation** — Defines the level of visibility between concurrent transactions.
    3. **readOnly** — Optimizes read-only operations.
    4. **rollbackFor / noRollbackFor** — Customize rollback behavior.

    Example:

    .. code-block:: java

        @Transactional(
            propagation = Propagation.REQUIRED,
            isolation = Isolation.READ_COMMITTED,
            readOnly = false,
            rollbackFor = {RuntimeException.class}
        )
        public void processTransaction() {
            // business logic
        }

    **Propagation Types:**

    * ``REQUIRED`` (default) – Join existing transaction or create a new one.
    * ``REQUIRES_NEW`` – Suspend existing transaction and start a new one.
    * ``MANDATORY`` – Must run within an existing transaction.
    * ``SUPPORTS`` – Join if one exists, else run non-transactionally.
    * ``NEVER`` – Run non-transactionally; fail if a transaction exists.
    * ``NESTED`` – Start a nested transaction within the main one.

    **Isolation Levels:**

    * ``READ_UNCOMMITTED`` – Can read uncommitted changes (dirty reads).
    * ``READ_COMMITTED`` – Prevents dirty reads (default for most databases).
    * ``REPEATABLE_READ`` – Prevents non-repeatable reads.
    * ``SERIALIZABLE`` – Highest isolation, prevents phantom reads.

Rollback Rules:
    By default, Spring rolls back on **unchecked exceptions (RuntimeException, Error)**,
    but **not** on checked exceptions.

    To roll back on checked exceptions, specify it explicitly:

    .. code-block:: java

        @Transactional(rollbackFor = Exception.class)
        public void performOperation() throws Exception {
            // business logic
            throw new Exception("Force rollback");
        }

Read-Only Transactions:
    For query-only operations, you can mark the transaction as read-only:

    .. code-block:: java

        @Transactional(readOnly = true)
        public List<Account> findAllAccounts() {
            return accountRepository.findAll();
        }

    This improves performance by disabling dirty checking and avoiding unnecessary flushes.

Testing Transactional Behavior:
    Spring test framework automatically rolls back transactions after each test.

    .. code-block:: java

        @SpringBootTest
        @Transactional
        public class BankingServiceTest {

            @Autowired
            private BankingService bankingService;

            @Test
            public void testFundTransfer() {
                bankingService.transferFunds(1L, 2L, 100.0);
                // all DB changes rolled back automatically after test
            }
        }

Summary:
    * ``@Transactional`` ensures atomicity, consistency, and rollback safety.
    * Without it, each repository method executes in its own transaction.
    * Always annotate service-layer methods that perform multiple database operations.
    * Use ``readOnly=true`` for fetch-only methods.
    * Customize behavior using propagation, isolation, and rollback rules.

Best Practices:
    * Apply ``@Transactional`` at the **service layer**, not in controllers.
    * Avoid using ``@Transactional`` directly on repository methods (Spring Data manages them).
    * Keep transactions short — avoid remote calls or lengthy computations inside them.
    * Explicitly define rollback rules for checked exceptions if necessary.
    * Use ``@Transactional(readOnly = true)`` for queries to improve performance.

Conclusion:
    The ``@Transactional`` annotation in Spring Boot provides a clean, declarative
    way to manage transactions. When used correctly, it guarantees data integrity,
    prevents partial updates, and simplifies error handling across multiple database
    operations.

Reference Links:
    - `Youtube Video by Java Techie <https://youtu.be/q6SidSElNbI?si=zQvJDvxAMoK7uu-H>`_