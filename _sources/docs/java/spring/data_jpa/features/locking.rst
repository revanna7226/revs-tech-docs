Optimistic and Pessimistic Locking
==================================

Overview:
    When multiple transactions try to access and modify the same data concurrently,
    it can lead to **data inconsistencies** or **race conditions**.  
    To handle this, JPA provides two main types of **locking mechanisms**:

    1. **Optimistic Locking**
    2. **Pessimistic Locking**

    These mechanisms ensure **data integrity** in concurrent transaction environments.

**What is Locking?**
    **Locking** prevents multiple transactions from modifying the same entity simultaneously.

    - **Optimistic Locking**: Assumes that conflicts are rare and detects conflicts at commit time.
    - **Pessimistic Locking**: Assumes conflicts are likely and prevents them by locking the data at read time.

Optimistic Locking:
    **Optimistic Locking** works by maintaining a **version number** (or timestamp) for each entity record.  
    Every time the entity is updated, the version is incremented.  
    If two users attempt to update the same record simultaneously,  
    the second update fails with an exception (usually `OptimisticLockException`).

    It’s a **non-blocking** mechanism — no database-level locks are held during reads.

    **How Optimistic Locking Works:**

    1. Entity is loaded with its version number.
    2. Transaction modifies the entity.
    3. On commit, Hibernate compares the current version with the one in the database.
    4. If versions differ, Hibernate throws `OptimisticLockException`.

    **Step 1: Enable Version Field in Entity:**

    .. code-block:: java

        @Entity
        @Table(name = "accounts")
        public class Account {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String owner;
            private Double balance;

            @Version
            private Integer version;

            // Getters and Setters
        }

    **Step 2: Repository Layer:**

    .. code-block:: java

        @Repository
        public interface AccountRepository extends JpaRepository<Account, Long> {
        }

    **Step 3: Service Layer Demonstration**

    .. code-block:: java

        @Service
        public class AccountService {

            @Autowired
            private AccountRepository accountRepository;

            @Transactional
            public void updateAccountBalance(Long accountId, double amount) {
                Account account = accountRepository.findById(accountId)
                        .orElseThrow(() -> new RuntimeException("Account not found"));

                account.setBalance(account.getBalance() + amount);

                // Simulate delay to test concurrent update
                try {
                    Thread.sleep(5000);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }

                accountRepository.save(account);
            }
        }

    **Step 4: Testing Optimistic Locking:**
    If two transactions load the same record concurrently:

    1. Both fetch version = 1.
    2. Transaction A commits, incrementing version to 2.
    3. Transaction B tries to commit but still holds version = 1 → causes an exception.

    Example Test Scenario:

    .. code-block:: java

        @SpringBootTest
        public class OptimisticLockingTest {

            @Autowired
            private AccountService accountService;

            @Test
            public void testOptimisticLocking() throws InterruptedException {
                Thread t1 = new Thread(() -> accountService.updateAccountBalance(1L, 100));
                Thread t2 = new Thread(() -> accountService.updateAccountBalance(1L, 200));

                t1.start();
                t2.start();

                t1.join();
                t2.join();
            }
        }

    Result: The second thread throws

    .. code-block:: text

        jakarta.persistence.OptimisticLockException: Row was updated or deleted by another transaction

Pessimistic Locking:
    **Pessimistic Locking** locks the database record when it is read,  
    ensuring that no other transaction can read or modify it until the current transaction completes.

    It is a **blocking** mechanism — other transactions wait for the lock to be released.

    Use **Pessimistic Locking** when:
    
    - Data conflicts are frequent.
    - You must ensure strong consistency.

    **How Pessimistic Locking Works:**

    1. Transaction A reads a record with a **database lock**.
    2. Transaction B tries to access the same record and is **blocked** until A commits or rolls back.

    **Step 1: Use Lock Annotations in Repository:**
    You can specify pessimistic locks using ``@Lock`` annotation and ``LockModeType`` in your repository.

    .. code-block:: java

        @Repository
        public interface AccountRepository extends JpaRepository<Account, Long> {

            @Lock(LockModeType.PESSIMISTIC_WRITE)
            @Query("SELECT a FROM Account a WHERE a.id = :id")
            Account findAccountForUpdate(@Param("id") Long id);
        }

    **Step 2: Service Layer Example:**

    .. code-block:: java

        @Service
        public class PessimisticLockingService {

            @Autowired
            private AccountRepository accountRepository;

            @Transactional
            public void transfer(Long fromId, Long toId, double amount) {
                Account fromAccount = accountRepository.findAccountForUpdate(fromId);
                Account toAccount = accountRepository.findAccountForUpdate(toId);

                fromAccount.setBalance(fromAccount.getBalance() - amount);
                toAccount.setBalance(toAccount.getBalance() + amount);

                accountRepository.save(fromAccount);
                accountRepository.save(toAccount);
            }
        }

Lock Modes in JPA:JPA provides several lock modes for use with ``@Lock``
    - ``LockModeType.READ`` – Allows shared locks for read-only access.
    - ``LockModeType.WRITE`` – Used for pessimistic writes.
    - ``LockModeType.OPTIMISTIC`` – Uses optimistic version checking.
    - ``LockModeType.OPTIMISTIC_FORCE_INCREMENT`` – Forces version increment.
    - ``LockModeType.PESSIMISTIC_READ`` – Acquires shared lock (others can read but not write).
    - ``LockModeType.PESSIMISTIC_WRITE`` – Acquires exclusive lock.
    - ``LockModeType.PESSIMISTIC_FORCE_INCREMENT`` – Forces version increment with pessimistic lock.

Comparison: Optimistic vs Pessimistic Locking:
    +-------------------------+-----------------------------+-----------------------------+
    | Feature                 | Optimistic Locking          | Pessimistic Locking         |
    +=========================+=============================+=============================+
    | Lock timing             | On commit                   | On read (query execution)   |
    +-------------------------+-----------------------------+-----------------------------+
    | Database lock           | No (logical via version)    | Yes (physical lock)         |
    +-------------------------+-----------------------------+-----------------------------+
    | Performance             | Better under low contention | Slower under high contention|
    +-------------------------+-----------------------------+-----------------------------+
    | Risk of deadlock        | None                        | Possible                    |
    +-------------------------+-----------------------------+-----------------------------+
    | Failure behavior        | Throws exception on commit  | Waits or times out          |
    +-------------------------+-----------------------------+-----------------------------+
    | Use case                | Rare conflicts              | Frequent conflicts          |
    +-------------------------+-----------------------------+-----------------------------+

Best Practices:
    - Use **Optimistic Locking** by default for scalability.
    - Use **Pessimistic Locking** when strict consistency is required.
    - Always handle `OptimisticLockException` or `PessimisticLockException`.
    - Avoid long transactions while holding locks.
    - Keep transactions as short as possible.

Conclusion:
    Spring Data JPA provides robust support for both **Optimistic** and **Pessimistic Locking** mechanisms
    to manage concurrent data access.

    - **Optimistic Locking** is lightweight, detects conflicts at commit time, and is ideal for high-concurrency systems.
    - **Pessimistic Locking** prevents conflicts proactively by locking records in the database,
    ensuring stronger consistency at the cost of scalability.

    By understanding both strategies, developers can choose the most suitable
    locking approach based on their **application's concurrency and performance requirements**.

Reference Links:
    - Java Techie `GitHub Repo <https://github.com/Java-Techie-jt/database-locking>`_.