# Phase 52 – Advanced Database Engineering, Database Performance Optimization and Distributed Data Management

**Level:** Advanced  
**Category:** Database Engineering / Database Architecture / Performance Optimization / Distributed Systems  
**Primary Database:** MySQL 8.4 / InnoDB  
**Additional Technologies:** Redis, MySQL Replication, Database Sharding, Connection Pooling

---

# 1. Learning Objectives

In this phase, you will learn how databases work internally, how to optimize database performance, and how to design database systems that can handle large amounts of data and concurrent users.

By the end of this phase, you should understand:

1. Advanced database architecture.
2. Database storage engines and internal structures.
3. How SQL queries are executed.
4. Query execution plans using `EXPLAIN`.
5. B-Tree and Hash indexes.
6. Primary, secondary, composite and covering indexes.
7. Index optimization strategies.
8. Database locking mechanisms.
9. Transaction isolation levels.
10. Deadlocks and concurrency management.
11. Advanced transaction management.
12. Database replication.
13. Read replicas and replication lag.
14. Database partitioning.
15. Database sharding.
16. Connection pooling.
17. Redis caching strategies.
18. Database backup and recovery.
19. Point-in-time recovery.
20. High availability and disaster recovery.
21. Distributed transactions.
22. Database consistency models.
23. CAP Theorem.
24. Database performance monitoring.

---

# PART 1: ADVANCED DATABASE ARCHITECTURE

## 2. What Is Database Architecture?

Database architecture describes how a database system stores, processes, manages and retrieves data.

A database is not simply a collection of tables.

Internally, it contains multiple components responsible for:

- Query processing.
- Query optimization.
- Memory management.
- Disk storage.
- Index management.
- Transaction management.
- Concurrency control.
- Logging and recovery.

### Simplified Database Architecture

```text
             Application
                  |
                  v
             Database Driver
                  |
                  v
              SQL Request
                  |
                  v
          +-------------------+
          |   Query Processor |
          +-------------------+
                  |
                  v
          +-------------------+
          | Query Optimizer   |
          +-------------------+
                  |
                  v
          +-------------------+
          | Execution Engine  |
          +-------------------+
                  |
                  v
          +-------------------+
          |  Storage Engine   |
          +-------------------+
                  |
          ---------------------
          |         |         |
          v         v         v
        Buffer    Indexes   Disk Files
        Pool
```

Let's understand the important components.

### 2.1 Query Processor

The query processor receives SQL statements and prepares them for execution.

For example:

```sql
SELECT *
FROM users
WHERE id = 10;
```

The database needs to understand:

- Which table is being accessed?
- Which columns are requested?
- Which conditions must be evaluated?
- Which indexes might help?
- How should the result be returned?

### 2.2 Query Optimizer

The query optimizer evaluates possible execution strategies and selects a plan based on its cost estimates.

For example, consider:

```sql
SELECT *
FROM users
WHERE email = 'john@example.com';
```

The database might:

1. Scan the entire table.
2. Use an index on `email`.
3. Use another available access path.

The optimizer estimates which strategy is more efficient.

### 2.3 Execution Engine

The execution engine performs the selected operations.

It interacts with the storage engine to retrieve or modify records.

### 2.4 Storage Engine

A storage engine manages how data is stored and accessed.

MySQL supports multiple storage engines.

The most important one for typical transactional applications is InnoDB.

---

## 3. Understanding MySQL Storage Engines

A storage engine determines how MySQL handles table storage and operations.

### Common MySQL Storage Engines

| Storage Engine | Characteristics |
|---|---|
| InnoDB | Transactions, ACID, row-level locking and foreign keys |
| MyISAM | Older engine with table-level locking |
| MEMORY | Stores table data in memory |
| ARCHIVE | Designed for compressed archival data |
| NDB | Distributed, clustered database engine |

### InnoDB

InnoDB is the default storage engine in MySQL 8.4.

Important features:

- ACID transactions.
- Row-level locking.
- Foreign key support.
- Crash recovery.
- Multi-Version Concurrency Control (MVCC).
- Clustered indexes.
- Redo and undo logging.

For most transactional applications, InnoDB is the appropriate default. [MySQL](https://dev.mysql.com/doc/refman/8.4/en/innodb-introduction.html?ff=nopfpls\&utm_source=chatgpt.com)


### Example

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(150)
) ENGINE = InnoDB;
```

Check the storage engine:

```sql
SHOW TABLE STATUS
WHERE Name = 'users';
```

Or:

```sql
SELECT
    TABLE_NAME,
    ENGINE
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = DATABASE();
```

---

# PART 2: DATABASE INTERNALS

## 4. How Does a Database Store Data?

A database stores data in files on persistent storage.

However, databases do not usually read and write individual rows directly from disk every time.

They organize data into pages.

### What Is a Database Page?

A page is a fixed-size unit of storage used by the database engine.

InnoDB commonly uses a default page size of 16 KB, although the page size can be configured.

Simplified representation:

```text
Database File
      |
      v
+-------------+
| Page 1      |
|-------------|
| Record 1    |
| Record 2    |
| Record 3    |
+-------------+

+-------------+
| Page 2      |
|-------------|
| Record 4    |
| Record 5    |
| Record 6    |
+-------------+

+-------------+
| Page 3      |
|-------------|
| Record 7    |
| Record 8    |
+-------------+
```

A database can retrieve multiple records from a page after reading that page into memory.

### Why Are Pages Important?

Reading a complete page is generally more efficient than performing separate disk operations for individual records.

Pages are important for:

- Storage organization.
- Index structures.
- Buffer pool management.
- Disk I/O optimization.
- Data retrieval.

---

## 5. What Is the Buffer Pool?

The InnoDB Buffer Pool is an area of memory used to cache database pages.

Instead of repeatedly reading data from disk, MySQL can retrieve frequently accessed pages from memory.

### Example

Suppose an application repeatedly executes:

```sql
SELECT *
FROM users
WHERE id = 100;
```

First request:

```text
MySQL
  |
  v
Check Buffer Pool
  |
  v
Page Not Found
  |
  v
Read Page from Disk
  |
  v
Store Page in Buffer Pool
  |
  v
Return Result
```

Second request:

```text
MySQL
  |
  v
Check Buffer Pool
  |
  v
Page Found
  |
  v
Return Result
```

The second request may avoid disk I/O.

### Important Buffer Pool Concepts

| Concept | Meaning |
|---|---|
| Buffer Pool | Memory cache for InnoDB pages |
| Page | Fixed-size storage unit |
| Cache Hit | Requested page is already available in memory |
| Cache Miss | Requested page must be loaded |
| Dirty Page | Modified page not yet flushed to its data file |

### Check Buffer Pool Configuration

```sql
SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
```

### Important

A large buffer pool can improve performance, but allocating too much memory can cause operating-system memory pressure.

Configure it according to:

- Available RAM.
- Database workload.
- Other applications running on the server.
- Operating system requirements.

---

## 6. Redo Log and Undo Log

InnoDB uses different logs for transaction durability and recovery.

Two important mechanisms are:

1. Redo Log.
2. Undo Log.

### 6.1 Redo Log

The redo log records information needed to redo changes after a crash.

Its primary purpose is to support durability and crash recovery.

Example:

```text
Transaction
    |
    v
Modify Database Page
    |
    v
Record Redo Information
    |
    v
Commit Transaction
    |
    v
Recoverable Changes
```

The exact write and flush behavior depends on configuration.

### 6.2 Undo Log

The undo log stores information needed to reverse changes or provide earlier versions of records.

It supports:

- Transaction rollback.
- MVCC.
- Consistent reads.

Example:

Suppose the current balance is ₹10,000.

A transaction changes it to ₹8,000.

```text
Original Value: 10,000
New Value:       8,000
```

The undo information helps reconstruct the previous version when required.

### Redo Log vs Undo Log

| Redo Log | Undo Log |
|---|---|
| Supports crash recovery | Supports rollback and MVCC |
| Reapplies changes | Helps reconstruct previous versions |
| Focuses on durability | Focuses on previous states |
| Used during recovery | Used during rollback and consistent reads |

### 6.3 Binary Log (Binlog)

MySQL also maintains a binary log.

The binary log records database changes for purposes such as:

- Replication.
- Point-in-time recovery.
- Change-data capture integrations.

Do not confuse the binary log with the InnoDB redo log.

| Log | Main Purpose |
|---|---|
| Redo Log | InnoDB crash recovery |
| Undo Log | Rollback and MVCC |
| Binary Log | Replication and point-in-time recovery |

---

# PART 3: DATABASE QUERY EXECUTION

## 7. How Does MySQL Execute a SQL Query?

Consider:

```sql
SELECT *
FROM users
WHERE email = 'john@example.com';
```

A simplified query-processing flow:

```text
SQL Query
    |
    v
Parsing
    |
    v
Validation
    |
    v
Query Optimization
    |
    v
Execution Plan
    |
    v
Storage Engine
    |
    v
Retrieve Records
    |
    v
Return Result
```

Let's understand each stage.

### Step 1: Parsing

The database checks SQL syntax and creates an internal representation of the query.

### Step 2: Validation

The database checks whether:

- The table exists.
- The referenced columns exist.
- The user has permission.
- The query is semantically valid.

### Step 3: Optimization

The optimizer evaluates possible execution strategies.

For example:

```sql
SELECT *
FROM users
WHERE email = 'john@example.com';
```

Possible strategies:

- Full table scan.
- Index lookup.
- Another suitable access path.

### Step 4: Execution

The selected plan is executed.

### Step 5: Result Generation

The database returns the matching records.

---

## 8. What Is a Full Table Scan?

A full table scan means the database examines all rows in a table to evaluate a query.

Example:

```sql
SELECT *
FROM users
WHERE city = 'Ahmedabad';
```

Suppose:

```text
Total Rows = 1,000,000
```

If there is no suitable index, MySQL may need to examine a large portion of the table.

Conceptually:

```text
User Table
    |
    v
Row 1
    |
    v
Row 2
    |
    v
Row 3
    |
    v
...
    |
    v
Row 1,000,000
```

This can be expensive.

However, a full table scan is not always bad.

If a query needs most rows, scanning the table may be more efficient than performing many separate index lookups.

**Important:** The goal is not to eliminate every full table scan. The goal is to choose an efficient execution plan for the workload.

---

# PART 4: EXPLAIN AND QUERY OPTIMIZATION

## 9. What Is EXPLAIN?

`EXPLAIN` displays the execution plan MySQL expects to use for a query.

Example:

```sql
EXPLAIN
SELECT *
FROM users
WHERE email = 'john@example.com';
```

It helps you understand:

- Which table is accessed.
- Which index is selected.
- How the table is accessed.
- How many rows may be examined.
- Whether additional sorting or temporary operations are required.

### Common EXPLAIN Columns

| Column | Meaning |
|---|---|
| id | Query block identifier |
| select_type | Type of SELECT operation |
| table | Table being accessed |
| partitions | Partitions considered |
| type | Access method |
| possible_keys | Candidate indexes |
| key | Selected index |
| key_len | Length of selected index key |
| ref | Values or columns compared with the index |
| rows | Estimated rows examined |
| filtered | Estimated percentage of rows passing the condition |
| Extra | Additional execution information |

### Important Access Types

| Type | General Meaning |
|---|---|
| system | Very small system table |
| const | At most one matching row |
| eq_ref | Unique lookup for each preceding row |
| ref | Non-unique index lookup |
| range | Index range scan |
| index | Full index scan |
| ALL | Full table scan |

These are common MySQL access types, not a universal ranking independent of the query.

### Example

Suppose the table contains:

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(150),
    city VARCHAR(100)
);
```

Without an index on email:

```sql
EXPLAIN
SELECT *
FROM users
WHERE email = 'john@example.com';
```

You may see:

```text
type: ALL
key: NULL
```

Now create an index:

```sql
CREATE INDEX idx_users_email
ON users(email);
```

Run EXPLAIN again.

You may see an index-based access method such as:

```text
type: ref
key: idx_users_email
```

The actual result depends on table size, statistics, data distribution and query shape.

---

## 10. EXPLAIN ANALYZE

`EXPLAIN ANALYZE` executes the query and provides actual execution information.

Example:

```sql
EXPLAIN ANALYZE
SELECT *
FROM users
WHERE email = 'john@example.com';
```

It can show:

- Actual execution time.
- Actual rows returned.
- Number of loops.
- Estimated versus actual row counts.

### EXPLAIN vs EXPLAIN ANALYZE

| EXPLAIN | EXPLAIN ANALYZE |
|---|---|
| Shows execution plan estimates | Executes the statement and reports actual execution |
| Does not normally execute the SELECT query | Executes the SELECT query |
| Useful for understanding optimizer choices | Useful for measuring actual plan behavior |
| Lower execution risk | Can consume production resources |

**Important:** Use EXPLAIN ANALYZE carefully on production systems because it executes the statement. For SELECT queries, this means the query actually runs; it can still be expensive.

---

## 11. Common Query Optimization Techniques

### 11.1 Avoid SELECT *

Poor:

```sql
SELECT *
FROM users;
```

Better:

```sql
SELECT id, name, email
FROM users;
```

Why?

- Less data transfer.
- Less memory usage.
- Potentially better index coverage.
- Reduced unnecessary column retrieval.

### 11.2 Use Appropriate WHERE Conditions

Poor:

```sql
SELECT *
FROM orders;
```

Better:

```sql
SELECT id, total_amount, status
FROM orders
WHERE status = 'completed';
```

### 11.3 Avoid Functions on Indexed Columns When They Prevent Index Use

Potentially inefficient:

```sql
SELECT *
FROM orders
WHERE YEAR(created_at) = 2026;
```

Better:

```sql
SELECT *
FROM orders
WHERE created_at >= '2026-01-01'
AND created_at < '2027-01-01';
```

The second query is more suitable for using a normal index on `created_at`.

### 11.4 Avoid Leading Wildcards When You Need a Normal B-Tree Lookup

Potentially inefficient:

```sql
SELECT *
FROM users
WHERE name LIKE '%john%';
```

A normal B-Tree index generally cannot efficiently seek to an arbitrary substring when the pattern begins with `%`.

For prefix search:

```sql
SELECT *
FROM users
WHERE name LIKE 'john%';
```

A suitable B-Tree index may help.

For advanced text search, consider full-text indexes or dedicated search engines.

### 11.5 Avoid Unnecessary Subqueries

Do not assume joins are always faster than subqueries or vice versa.

Compare execution plans and actual performance.

### 11.6 Use LIMIT

```sql
SELECT id, title
FROM expenses
ORDER BY created_at DESC
LIMIT 20;
```

This reduces the number of returned records.

### 11.7 Optimize JOIN Conditions

Ensure frequently joined columns have appropriate indexes.

Example:

```sql
SELECT
    users.name,
    orders.total_amount
FROM users
INNER JOIN orders
    ON users.id = orders.user_id;
```

A primary key on `users.id` and a suitable index on `orders.user_id` can help this query.

---

# PART 5: DATABASE INDEXING

## 12. What Is a Database Index?

An index is a data structure that helps the database locate records without scanning the entire table.

Think of a book.

Without an index, you may need to read every page to find a topic.

With an index, you can locate the relevant page much faster.

A database index serves a similar purpose.

### Without an Index

```text
Users Table
    |
    v
Search Row 1
    |
    v
Search Row 2
    |
    v
Search Row 3
    |
    v
Search Row 4
```

### With an Index

```text
       Index
         |
         v
   Find Search Key
         |
         v
   Locate Record
         |
         v
     Return Data
```

### Advantages

- Faster lookups.
- Faster filtering.
- Faster joins.
- Faster sorting in suitable cases.
- Faster uniqueness checks.

### Disadvantages

- Additional storage.
- Slower inserts.
- Slower updates to indexed columns.
- Slower deletes in some workloads.
- Additional index maintenance.

**Important:** An index is not automatically beneficial for every column.

---

## 13. What Is a B-Tree?

A B-Tree is a balanced search tree used to organize keys efficiently.

Database indexes commonly use B-Tree-family structures.

InnoDB uses B+Tree-style indexes.

A simplified representation:

```text
                 [50]
                /    \
               /      \
          [20, 30]   [70, 90]
           / | \      / | \
          /  |  \    /  |  \
         ... ... ... ... ...
```

The tree is balanced, so searching does not require scanning every indexed key.

### Why Are B+Trees Useful?

They support:

- Equality lookups.
- Range queries.
- Ordered traversal.
- Sorting.
- Prefix searches on indexed values.

Example:

```sql
SELECT *
FROM users
WHERE id BETWEEN 100 AND 200;
```

A suitable B-Tree index can help locate the beginning of the range and traverse matching keys.

### B-Tree vs Linear Search

| Feature | Linear Search | B+Tree Index |
|---|---|---|
| Search | Examines records sequentially | Navigates a tree |
| Large datasets | Often expensive | Usually more efficient |
| Range queries | Requires scanning | Supports ordered traversal |
| Extra storage | No separate index required | Requires index storage |

---

## 14. What Is a Hash Index?

A hash index uses a hash function to map keys to locations or buckets.

Conceptually:

```text
Key: john@example.com
          |
          v
      Hash Function
          |
          v
       Bucket 25
          |
          v
      Matching Entry
```

Hash-based indexes are particularly useful for equality lookups.

Example:

```sql
WHERE email = 'john@example.com'
```

However, ordinary hash indexes do not naturally support ordered range operations.

### B-Tree vs Hash Index

| Feature | B-Tree | Hash |
|---|---|---|
| Equality lookup | Yes | Yes |
| Range query | Yes | Generally no |
| Sorting | Can help | No |
| Ordered traversal | Yes | No |
| Prefix search | Can help | No |
| Common MySQL InnoDB index | Yes | No general user-created hash index |

**Important MySQL detail:** InnoDB supports B-Tree indexes for ordinary user-created indexes. It also has an internal Adaptive Hash Index feature, which is managed by InnoDB. You do not normally create a general-purpose user-defined hash index on an InnoDB table.

---

## 15. Types of Database Indexes

### 15.1 Primary Key Index

A primary key uniquely identifies a record.

Example:

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100)
);
```

The primary key is indexed automatically.

In InnoDB, the primary key is the clustered index.

### 15.2 Secondary Index

A secondary index is an additional index created on another column.

Example:

```sql
CREATE INDEX idx_users_email
ON users(email);
```

Query:

```sql
SELECT *
FROM users
WHERE email = 'john@example.com';
```

### 15.3 Unique Index

A unique index prevents duplicate indexed values.

```sql
CREATE UNIQUE INDEX idx_users_email_unique
ON users(email);
```

Example:

```text
john@example.com  -> Allowed
john@example.com  -> Rejected
```

MySQL unique indexes permit multiple NULL values in a nullable unique column because NULL values are not considered equal for uniqueness purposes.

### 15.4 Composite Index

A composite index contains multiple columns.

Example:

```sql
CREATE INDEX idx_orders_user_status
ON orders(user_id, status);
```

This index is useful for queries involving its leading columns.

### 15.5 Full-Text Index

Full-text indexes support text-search operations.

Example:

```sql
CREATE FULLTEXT INDEX idx_articles_content
ON articles(content);
```

Query:

```sql
SELECT *
FROM articles
WHERE MATCH(content)
AGAINST('database optimization');
```

Full-text search has different semantics from a normal B-Tree lookup.

### 15.6 Spatial Index

Spatial indexes support geographic and geometric data queries.

They are useful for:

- Location-based applications.
- Geographic search.
- Spatial relationships.

---

## 16. Clustered vs Secondary Indexes

This is an important InnoDB concept.

### Clustered Index

In InnoDB, table rows are organized according to the clustered index.

The primary key normally serves as the clustered index.

Simplified:

```text
Primary Key Index
       |
       v
+----------------+
| Primary Key 10  | ---> Row Data
| Primary Key 20  | ---> Row Data
| Primary Key 30  | ---> Row Data
+----------------+
```

### Secondary Index

A secondary index stores the indexed columns along with the primary key value.

Conceptually:

```text
Secondary Index on Email

john@example.com  ---> Primary Key 10
mary@example.com  ---> Primary Key 20
sam@example.com   ---> Primary Key 30
```

The database can use the primary key to locate the full clustered row when required.

### Comparison

| Clustered Index | Secondary Index |
|---|---|
| Organizes table rows | Separate index structure |
| InnoDB has one clustered index per table | A table can have multiple secondary indexes |
| Usually primary key | Created on other columns |
| Stores row data in its leaf pages | Stores indexed values and primary key |

### Important Optimization

Keep primary keys reasonably compact when practical.

A wide primary key is carried into secondary index entries, increasing secondary index storage.

---

## 17. Composite Indexes and the Leftmost Prefix Rule

Consider:

```sql
CREATE INDEX idx_orders_user_status_date
ON orders(user_id, status, created_at);
```

This index has three columns:

```text
(user_id, status, created_at)
```

MySQL can generally use its leftmost prefixes.

Possible useful conditions:

```sql
WHERE user_id = 10;
```

```sql
WHERE user_id = 10
AND status = 'completed';
```

```sql
WHERE user_id = 10
AND status = 'completed'
AND created_at >= '2026-01-01';
```

But this query does not use the full leftmost prefix:

```sql
WHERE status = 'completed';
```

The index begins with `user_id`, so it is not generally an efficient direct lookup for status alone.

### Index Column Order

Suppose you frequently execute:

```sql
SELECT *
FROM orders
WHERE user_id = 10
AND status = 'completed';
```

A suitable index might be:

```sql
CREATE INDEX idx_orders_user_status
ON orders(user_id, status);
```

Column order should be chosen according to:

- Actual query patterns.
- Filtering conditions.
- Join conditions.
- Sorting requirements.
- Column selectivity.
- Workload characteristics.

Do not choose index order using a simplistic rule such as always putting the most selective column first.

---

## 18. Covering Index

A covering index contains all the columns needed by a particular query.

Example:

```sql
CREATE INDEX idx_users_email_name
ON users(email, name);
```

Query:

```sql
SELECT email, name
FROM users
WHERE email = 'john@example.com';
```

The index may contain everything needed to return the result.

The database may avoid an additional lookup into the clustered table.

This can improve performance.

### Why Covering Indexes Matter

They can reduce:

- Random page reads.
- Additional table lookups.
- I/O operations.

However, very wide covering indexes increase storage and write overhead.

---

## 19. Index Selectivity and Cardinality

### Selectivity

Selectivity describes how effectively a condition narrows down matching records.

Example:

A table contains 100,000 users.

| Column | Example Values | Selectivity |
|---|---|---|
| Gender | 2–3 common values | Usually low |
| Country | Several common values | Often moderate or low |
| Email | Mostly unique values | High |
| User ID | Unique values | Very high |

A highly selective index often helps equality lookups.

### Cardinality

Cardinality refers to the number of distinct values in a column or index.

For example:

```text
Gender:
Male, Female, Other
```

The cardinality is low.

```text
Email:
Mostly unique email addresses
```

The cardinality is high.

**Important:** High cardinality does not automatically mean an index is useful. The actual query workload matters.

---

## 20. When Should You Create an Index?

Create indexes when columns are frequently used in:

- WHERE conditions.
- JOIN conditions.
- ORDER BY clauses.
- GROUP BY operations.
- Uniqueness constraints.

But avoid unnecessary indexes.

Example:

```sql
CREATE INDEX idx_expenses_user
ON expenses(user_id);

CREATE INDEX idx_expenses_created
ON expenses(created_at);
```

Before adding another index, examine the actual query plan.

### Check Existing Indexes

```sql
SHOW INDEX FROM expenses;
```

### Remove an Unnecessary Index

```sql
DROP INDEX idx_expenses_created
ON expenses;
```

### Advanced Feature: Invisible Indexes

MySQL supports invisible indexes.

They allow you to test the effect of making an index unavailable to the optimizer without immediately dropping it.

Example:

```sql
ALTER TABLE expenses
ALTER INDEX idx_expenses_created INVISIBLE;
```

Restore visibility:

```sql
ALTER TABLE expenses
ALTER INDEX idx_expenses_created VISIBLE;
```

This can help evaluate whether an index is still needed.

---

# PART 6: DATABASE LOCKING

## 21. What Is Database Locking?

Locking controls concurrent access to database records.

Suppose two users attempt to update the same bank account simultaneously.

Current balance:

```text
₹10,000
```

Transaction A withdraws ₹2,000.

Transaction B withdraws ₹3,000.

Without proper concurrency control, both transactions might read the same old balance and overwrite each other's updates.

Database locking helps prevent such problems.

### Simplified Locking Example

```text
Transaction A
      |
      v
Lock Account Row
      |
      v
Update Balance
      |
      v
Commit
      |
      v
Release Lock
```

Transaction B may need to wait until the conflicting lock is released.

---

## 22. Types of Database Locks

### 22.1 Shared Lock

A shared lock permits compatible read operations but prevents conflicting modifications.

Example:

```sql
SELECT *
FROM accounts
WHERE id = 10
FOR SHARE;
```

### 22.2 Exclusive Lock

An exclusive lock is used when a transaction modifies a record.

Example:

```sql
SELECT *
FROM accounts
WHERE id = 10
FOR UPDATE;
```

Other transactions cannot perform conflicting operations on the locked record until the lock is released.

### 22.3 Record Lock

A record lock applies to an index record.

### 22.4 Gap Lock

A gap lock prevents inserts into a particular index range.

### 22.5 Next-Key Lock

A next-key lock combines a record lock with a gap lock.

These mechanisms are used by InnoDB to manage concurrency and prevent certain phantom-row scenarios.

### Lock Types Summary

| Lock | Purpose |
|---|---|
| Shared Lock | Compatible reading |
| Exclusive Lock | Protect modification |
| Record Lock | Lock an index record |
| Gap Lock | Prevent insertion into an index gap |
| Next-Key Lock | Record lock plus preceding gap |

MySQL's locking behavior depends on the storage engine, transaction isolation level, indexes and query conditions. [MySQL](https://dev.mysql.com/doc/refman/8.4/en/innodb-locking.html?utm_source=chatgpt.com)

---

## 23. Pessimistic vs Optimistic Locking

### Pessimistic Locking

Assumes conflicts are possible and locks records before modifying them.

Example:

```sql
START TRANSACTION;

SELECT balance
FROM accounts
WHERE id = 10
FOR UPDATE;

UPDATE accounts
SET balance = balance - 1000
WHERE id = 10;

COMMIT;
```

The transaction obtains a lock before changing the account.

### Optimistic Locking

Assumes conflicts are relatively uncommon.

It uses a version number or similar condition to detect whether another transaction modified the record.

Example table:

```sql
CREATE TABLE products (
    id BIGINT PRIMARY KEY,
    name VARCHAR(100),
    stock INT,
    version INT NOT NULL DEFAULT 1
);
```

Update:

```sql
UPDATE products
SET
    stock = stock - 1,
    version = version + 1
WHERE id = 10
AND version = 5;
```

Then check affected rows.

- One affected row: update succeeded.
- Zero affected rows: the version no longer matches or the record does not exist.

### Comparison

| Pessimistic Locking | Optimistic Locking |
|---|---|
| Locks before modifying | Detects conflicts during update |
| Useful for high-contention operations | Useful when conflicts are relatively rare |
| Can cause lock waiting | Can require retries |
| Example: `FOR UPDATE` | Example: version column |

---

# PART 7: TRANSACTION ISOLATION LEVELS

## 24. What Is Transaction Isolation?

Isolation determines how concurrently executing transactions interact.

Without appropriate isolation, concurrent transactions can experience inconsistent results.

The SQL standard defines four major isolation levels.

1. Read Uncommitted.
2. Read Committed.
3. Repeatable Read.
4. Serializable.

MySQL InnoDB supports all four, and its default isolation level is Repeatable Read. [MySQL](https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-isolation-levels.html?utm_source=chatgpt.com)

## 25. Dirty Read

A dirty read occurs when one transaction reads changes made by another transaction that has not yet committed.

Example:

```text
Transaction A:
UPDATE accounts
SET balance = 5000
WHERE id = 10;

Transaction B:
SELECT balance
FROM accounts
WHERE id = 10;
```

If Transaction A later rolls back, Transaction B may have read a value that never became committed data.

Read Uncommitted permits dirty reads.

## 26. Non-Repeatable Read

A non-repeatable read occurs when a transaction reads the same row twice and obtains different committed values.

Example:

```text
Transaction A:
SELECT balance FROM accounts WHERE id = 10;

Transaction B:
UPDATE accounts
SET balance = 8000
WHERE id = 10;

COMMIT;

Transaction A:
SELECT balance FROM accounts WHERE id = 10;
```

Depending on isolation level, Transaction A may see different values in its two reads.

## 27. Phantom Read

A phantom read occurs when a repeated range query returns a different set of rows because another transaction inserted or deleted matching records.

Example:

Transaction A:

```sql
SELECT *
FROM orders
WHERE amount > 5000;
```

Transaction B inserts a new matching order and commits.

Transaction A repeats the query.

The result may contain an additional row, depending on the isolation level and locking strategy.

## 28. Isolation Levels Comparison

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|---|---|---|---|
| Read Uncommitted | Possible | Possible | Possible |
| Read Committed | Prevented | Possible | Possible |
| Repeatable Read | Prevented | Prevented for consistent reads | SQL-standard phantom behavior requires nuance |
| Serializable | Prevented | Prevented | Prevented |

**Important MySQL detail:** InnoDB's Repeatable Read uses MVCC snapshots for ordinary consistent reads and next-key locking for certain locking operations. Its behavior is more nuanced than the simplified SQL-standard table.

### Set Isolation Level

For a session:

```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

For the next transaction:

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

### When to Use Each Level

| Level | Common Use |
|---|---|
| Read Uncommitted | Rarely appropriate for critical application data |
| Read Committed | Workloads where fresh committed reads are preferred |
| Repeatable Read | Common transactional applications |
| Serializable | Operations requiring stronger concurrency guarantees |

Do not change isolation levels just to improve performance without understanding the resulting consistency behavior.

---

# PART 8: DATABASE DEADLOCKS

## 29. What Is a Deadlock?

A deadlock occurs when two or more transactions wait for each other in a cycle.

Example:

Transaction A:

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 100
WHERE id = 1;
```

Transaction B:

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 200
WHERE id = 2;
```

Now:

Transaction A tries to update account 2.

Transaction B tries to update account 1.

```text
Transaction A
     |
     | Holds Lock on Account 1
     |
     v
Waits for Account 2
     ^
     |
     | Holds Lock on Account 2
     |
Transaction B
     |
     v
Waits for Account 1
```

Neither transaction can proceed.

This is a deadlock.

## 30. How Does MySQL Handle Deadlocks?

InnoDB detects deadlocks and normally rolls back one transaction to break the cycle.

The application should handle deadlock errors and retry the entire transaction when appropriate.

A deadlock is not necessarily a sign of a broken database. It can occur in correctly functioning concurrent systems.

## 31. How to Prevent Deadlocks

### 1. Access Records in a Consistent Order

Always update account 1 before account 2.

Do not let different code paths acquire locks in opposite orders.

### 2. Keep Transactions Short

Avoid unnecessary operations while holding database locks.

### 3. Use Appropriate Indexes

Poorly indexed queries may scan and lock more records than expected.

### 4. Avoid Unnecessary User Interaction Inside Transactions

Do not open a transaction and then wait for a user to confirm something.

### 5. Retry Deadlocked Transactions

Use bounded retries with backoff.

Example:

```typescript
async function runWithRetry(operation, maxRetries = 3) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await operation();
    } catch (error: any) {
      const isDeadlock = error.code === "ER_LOCK_DEADLOCK";

      if (!isDeadlock || attempt === maxRetries) {
        throw error;
      }

      const delay = 100 * attempt;

      await new Promise(resolve =>
        setTimeout(resolve, delay)
      );
    }
  }
}
```

The `operation` must create and execute a fresh transaction on every retry. Do not retry only the last failed SQL statement when the earlier statements belong to a transaction that has been rolled back.

---

# PART 9: ADVANCED TRANSACTION MANAGEMENT

## 32. What Is a Database Transaction?

A transaction is a logical unit of database work.

Example: Transferring money between two accounts.

```text
Account A: ₹10,000
Account B: ₹5,000
```

Transfer ₹2,000 from A to B.

Expected result:

```text
Account A: ₹8,000
Account B: ₹7,000
```

Both operations must succeed together.

### Transaction Example

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 2000
WHERE id = 1;

UPDATE accounts
SET balance = balance + 2000
WHERE id = 2;

COMMIT;
```

If something fails:

```sql
ROLLBACK;
```

## 33. ACID Properties

ACID stands for:

- Atomicity.
- Consistency.
- Isolation.
- Durability.

| Property | Meaning |
|---|---|
| Atomicity | All operations succeed or the transaction is rolled back |
| Consistency | Transactions preserve database constraints and invariants |
| Isolation | Concurrent transactions are controlled |
| Durability | Committed changes survive supported failure scenarios |

### Important

ACID does not automatically guarantee correct business logic.

For example, a transaction may successfully transfer money to the wrong account if the application supplies the wrong account ID.

Database integrity and application correctness are both required.

---

## 34. Transaction Management in Node.js

Using `mysql2`:

```bash
npm install mysql2
```

Example:

```typescript
import mysql from "mysql2/promise";

const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  waitForConnections: true,
  connectionLimit: 10
});

async function transferMoney(
  fromAccountId: number,
  toAccountId: number,
  amount: number
) {
  const connection = await pool.getConnection();

  try {
    await connection.beginTransaction();

    const [accounts] = await connection.execute(
      `SELECT id, balance
       FROM accounts
       WHERE id IN (?, ?)
       ORDER BY id
       FOR UPDATE`,
      [fromAccountId, toAccountId]
    );

    // Production implementation must verify that both
    // expected accounts were returned.

    await connection.execute(
      `UPDATE accounts
       SET balance = balance - ?
       WHERE id = ?
       AND balance >= ?`,
      [amount, fromAccountId, amount]
    );

    await connection.execute(
      `UPDATE accounts
       SET balance = balance + ?
       WHERE id = ?`,
      [amount, toAccountId]
    );

    await connection.commit();

    return {
      success: true
    };

  } catch (error) {
    await connection.rollback();
    throw error;

  } finally {
    connection.release();
  }
}
```

This example illustrates transaction handling.

A complete implementation must also validate:

- The amount is positive.
- Both accounts exist.
- The accounts are eligible for transfers.
- The source account has sufficient funds.
- Both accounts belong to the correct customer or tenant.
- Each expected update affects the correct number of rows.

Use consistent account locking order and handle deadlock retries.

---

# PART 10: DATABASE REPLICATION

## 35. What Is Database Replication?

Replication copies database changes from one database server to another.

It is commonly used for:

- High availability.
- Read scaling.
- Disaster recovery.
- Geographic distribution.
- Reporting workloads.

### Basic Architecture

```text
             Application
                  |
                  v
            Primary Database
                  |
                  | Replication
                  |
          -------------------
          |                 |
          v                 v
      Replica 1         Replica 2
```

The primary handles writes.

Replicas receive changes and may handle reads.

### MySQL Replication

MySQL commonly uses binary logs to communicate changes to replicas.

Simplified flow:

```text
Primary Database
       |
       v
   Binary Log
       |
       v
Replica Receives Changes
       |
       v
Relay Log
       |
       v
Replica Applies Changes
```

### Primary vs Replica

| Primary | Replica |
|---|---|
| Commonly handles writes | Commonly handles read traffic |
| Source of replicated changes | Receives changes |
| Maintains binary logs for replication | Applies replicated changes |
| May be used for critical transactions | May experience replication delay |

MySQL documents replication as a binary-log-based mechanism. [MySQL](https://dev.mysql.com/doc/refman/8.4/en/innodb-and-mysql-replication.html?utm_source=chatgpt.com)

---

## 36. Synchronous vs Asynchronous Replication

### Asynchronous Replication

The primary commits changes without waiting for every replica to apply them.

```text
Primary
   |
   | Commit
   |
   v
Client Receives Success
   |
   v
Replica Applies Change Later
```

Advantages:

- Lower write latency.
- Less dependency on replica availability.

Disadvantages:

- Replication lag.
- Potential data loss if the primary fails before changes reach a replica.

### Synchronous Replication

The primary waits for an appropriate acknowledgement from another system before considering a write complete.

Advantages:

- Stronger durability guarantees under the chosen protocol.

Disadvantages:

- Higher latency.
- Greater dependency on replica availability.
- More complex failure handling.

**Important:** MySQL's common asynchronous replication should not be described as fully synchronous replication. MySQL also offers semi-synchronous replication, which waits for a replica acknowledgement at a particular stage, but that does not necessarily mean the replica has applied the transaction.

---

## 37. What Is Replication Lag?

Replication lag is the delay between a change being committed on the primary and becoming visible on a replica.

Example:

```text
Primary:
Balance = ₹8,000

Replica:
Balance = ₹10,000
```

The replica has not yet applied the latest transaction.

This can cause a read-after-write problem.

### Example

1. User updates their profile.
2. The primary commits the update.
3. The frontend immediately requests the profile from a replica.
4. The replica has not received the update.
5. The frontend displays old information.

### Solutions

- Route critical read-after-write requests to the primary.
- Use a consistency-aware read strategy.
- Monitor replication lag.
- Avoid sending every read to replicas without considering consistency.

---

# PART 11: DATABASE PARTITIONING

## 38. What Is Database Partitioning?

Partitioning divides a large table into smaller logical pieces called partitions.

The application can still query the table as one logical table.

Example:

Suppose an orders table contains 100 million records.

Instead of storing all records in one partition, the table can be divided by date.

```text
Orders Table
     |
     |-- Partition 2024
     |
     |-- Partition 2025
     |
     |-- Partition 2026
```

### Why Use Partitioning?

- Manage very large tables.
- Improve some queries through partition pruning.
- Simplify retention and archival operations.
- Manage historical data more efficiently.

Partitioning is not automatically a performance improvement. It is useful when the workload and partitioning strategy align.

---

## 39. Types of Partitioning

### 39.1 Range Partitioning

Rows are assigned according to ranges.

Example:

```sql
CREATE TABLE orders (
    id BIGINT NOT NULL,
    order_date DATE NOT NULL,
    total_amount DECIMAL(10, 2)
)
PARTITION BY RANGE (YEAR(order_date)) (
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p2025 VALUES LESS THAN (2026),
    PARTITION p2026 VALUES LESS THAN (2027),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);
```

This divides orders by year.

### 39.2 List Partitioning

Rows are assigned according to a predefined list of values.

Example:

```sql
CREATE TABLE customers (
    id BIGINT NOT NULL,
    region_id INT NOT NULL
)
PARTITION BY LIST (region_id) (
    PARTITION p_north VALUES IN (1, 2),
    PARTITION p_south VALUES IN (3, 4),
    PARTITION p_west VALUES IN (5, 6)
);
```

### 39.3 Hash Partitioning

Rows are distributed using a hash function.

Example:

```sql
CREATE TABLE logs (
    id BIGINT NOT NULL,
    user_id BIGINT NOT NULL,
    message TEXT
)
PARTITION BY HASH(user_id)
PARTITIONS 4;
```

### 39.4 Key Partitioning

MySQL applies its internal key-based partitioning algorithm to the specified columns.

It is useful when partitioning based on key values rather than a manually defined range.

---

## 40. Partition Pruning

Partition pruning means MySQL avoids scanning partitions that cannot contain matching records.

Suppose a table is partitioned by year.

Query:

```sql
SELECT *
FROM orders
WHERE order_date >= '2026-01-01'
AND order_date < '2027-01-01';
```

If the optimizer can determine that only the 2026 partition contains matching rows, it may avoid scanning other partitions.

This is partition pruning.

### Partitioning vs Indexing

| Partitioning | Indexing |
|---|---|
| Divides table data into partitions | Creates search structures |
| Helps manage large tables | Helps locate records |
| Can reduce scanned partitions | Can reduce examined records |
| Does not replace indexes | Does not replace partition design |

**MySQL partitioning constraint:** Partitioned InnoDB tables have restrictions on unique keys. Every unique key, including the primary key, must include all columns used in the partitioning expression. Design the schema carefully before implementing partitioning.

---

# PART 12: DATABASE SHARDING

## 41. What Is Database Sharding?

Sharding distributes data across multiple independent database servers.

Unlike partitioning, which divides a table within a database system, sharding distributes data across separate database instances or nodes.

Example:

```text
                Application
                     |
                     v
                Shard Router
                     |
          -------------------------
          |           |           |
          v           v           v
       Shard 1     Shard 2     Shard 3
       Users       Users       Users
       1-1000      1001-2000   2001-3000
```

### Why Use Sharding?

Suppose one database server cannot efficiently handle the workload.

Sharding distributes data and traffic across multiple servers.

Benefits:

- Horizontal scaling.
- Increased storage capacity.
- Distributed workload.
- Potentially higher throughput.

Challenges:

- Complex queries across shards.
- Distributed transactions.
- Rebalancing.
- Operational complexity.
- Data consistency.
- Backup and recovery across nodes.

---

## 42. Sharding Strategies

### 42.1 Range-Based Sharding

Data is divided according to ranges.

Example:

```text
User ID 1–1000       -> Shard 1
User ID 1001–2000    -> Shard 2
User ID 2001–3000    -> Shard 3
```

Advantage:

- Simple routing.

Disadvantage:

- Uneven distribution may occur.

### 42.2 Hash-Based Sharding

A hash function determines which shard stores a record.

Example:

```javascript
const shardNumber = userId % 4;
```

For user ID 101:

```text
101 % 4 = 1
```

The record goes to Shard 1 if shards are numbered from 0.

Advantage:

- Can distribute data more evenly.

Disadvantage:

- Adding or removing shards may require extensive data movement with simple modulo hashing.

### 42.3 Consistent Hashing

Consistent hashing maps keys and nodes onto a logical hash ring.

When nodes are added or removed, the system can limit the amount of data that must be remapped.

It is useful in distributed caching and sharded systems.

### 42.4 Geographic Sharding

Data is distributed according to geographic location.

Example:

```text
India Users    -> India Database
Europe Users   -> Europe Database
US Users       -> US Database
```

This can help with latency and data-residency requirements.

### Partitioning vs Sharding

| Partitioning | Sharding |
|---|---|
| Divides data into partitions | Divides data across database nodes |
| Often managed by one database instance | Uses multiple database instances |
| Simplifies large-table management | Supports horizontal scaling |
| Usually transparent to the application | Often requires routing logic |
| Lower operational complexity | Higher operational complexity |

**Important:** Do not introduce sharding before you have measured the limits of a well-designed single database and evaluated simpler scaling options.

---

# PART 13: CONNECTION POOLING

## 43. What Is a Database Connection?

A database connection is a communication channel between an application and a database server.

Example:

```text
Node.js Application
        |
        v
   MySQL Connection
        |
        v
    MySQL Server
```

Creating a new database connection for every request can be expensive.

It involves:

- Network setup.
- Authentication.
- Session initialization.
- Resource allocation.

Connection pooling helps reuse connections.

## 44. What Is Connection Pooling?

A connection pool maintains a set of reusable database connections.

```text
            Node.js App
                 |
                 v
           Connection Pool
                 |
        ---------------------
        |         |         |
        v         v         v
    Connection Connection Connection
        1         2         3
        |         |         |
        ---------------------
                 |
                 v
             MySQL Server
```

When a request needs a connection:

1. The pool provides an available connection.
2. The application performs database operations.
3. The application releases the connection.
4. The pool makes it available for reuse.

### Why Use Connection Pooling?

- Reduces connection creation overhead.
- Controls concurrent database connections.
- Improves resource utilization.
- Improves application scalability.

## 45. MySQL Connection Pool Example

Using `mysql2`:

```typescript
import mysql from "mysql2/promise";

const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,

  waitForConnections: true,

  connectionLimit: 10,

  maxIdle: 10,

  idleTimeout: 60000,

  queueLimit: 0
});
```

Use the pool:

```typescript
const [rows] = await pool.execute(
  "SELECT * FROM users WHERE id = ?",
  [10]
);
```

For transactions:

```typescript
const connection = await pool.getConnection();

try {
  await connection.beginTransaction();

  // Execute transactional queries.

  await connection.commit();

} catch (error) {
  await connection.rollback();
  throw error;

} finally {
  connection.release();
}
```

### Connection Pool Best Practices

1. Configure a reasonable connection limit.
2. Always release acquired connections.
3. Use connection pools for application workloads.
4. Monitor active and waiting connections.
5. Handle connection failures.
6. Avoid holding connections during unrelated operations.
7. Account for all application instances when calculating total database connections.

### Example Calculation

Suppose:

```text
Application Instances = 5
Pool Size Per Instance = 10
```

Maximum application connections:

```text
5 × 10 = 50
```

If you deploy more instances, the total connection demand increases.

---

# PART 14: DATABASE CACHING AND REDIS

## 46. What Is Database Caching?

Caching stores frequently accessed data in a faster storage layer.

Instead of querying the database repeatedly, the application retrieves cached data.

Example:

```text
Client
  |
  v
Node.js Backend
  |
  v
Check Redis Cache
  |
  |-- Cache Hit --> Return Cached Data
  |
  |-- Cache Miss
           |
           v
       MySQL Query
           |
           v
       Store in Redis
           |
           v
       Return Result
```

### Why Use Caching?

- Reduce database load.
- Reduce response latency.
- Improve throughput.
- Reduce repeated computation.
- Support high-read workloads.

---

## 47. What Is Redis?

Redis is an in-memory data structure server commonly used for:

- Caching.
- Session storage.
- Rate limiting.
- Distributed coordination.
- Queues and streams.
- Pub/Sub.
- Real-time counters.

Redis supports data structures such as:

- Strings.
- Hashes.
- Lists.
- Sets.
- Sorted sets.
- Streams.

It also supports replication and persistence options. [Redis](https://redis.io/about/?utm_source=chatgpt.com)

### Redis vs MySQL

| Redis | MySQL |
|---|---|
| Primarily memory-oriented | Persistent relational database |
| Key-value and data structures | Relational tables |
| Very fast cache operations | Strong relational query capabilities |
| Often used for temporary or derived data | Commonly stores primary business records |
| Supports persistence options | Designed for durable relational storage |

**Important:** Redis is not merely a cache. It can be used as a database, but whether it should be the primary data store depends on the application's durability, consistency and query requirements.

---

## 48. Redis Basic Commands

### String

```redis
SET username "john"
```

Retrieve:

```redis
GET username
```

### Set Expiration

```redis
SET user:10:profile "{...}" EX 300
```

This sets a five-minute expiration.

### Hash

```redis
HSET user:10 name "John" email "john@example.com"
```

Retrieve:

```redis
HGETALL user:10
```

### Delete

```redis
DEL username
```

### Check Expiration

```redis
TTL user:10:profile
```

---

## 49. Cache-Aside Pattern

Cache-aside is one of the most common caching strategies.

The application checks the cache first.

If data is missing, it queries the database and stores the result in the cache.

### Flow

```text
Request Data
     |
     v
Check Redis
     |
     |-- Found --> Return Data
     |
     |-- Not Found
             |
             v
        Query MySQL
             |
             v
        Store in Redis
             |
             v
        Return Data
```

### Node.js Example

Install:

```bash
npm install redis
```

Example:

```typescript
import { createClient } from "redis";

const redis = createClient({
  url: process.env.REDIS_URL
});

await redis.connect();

async function getUser(userId: number) {
  const cacheKey = `user:${userId}`;

  const cachedUser = await redis.get(cacheKey);

  if (cachedUser) {
    return JSON.parse(cachedUser);
  }

  const [rows] = await pool.execute(
    "SELECT id, name, email FROM users WHERE id = ?",
    [userId]
  );

  const users = rows as any[];

  if (users.length === 0) {
    return null;
  }

  const user = users[0];

  await redis.set(
    cacheKey,
    JSON.stringify(user),
    {
      EX: 300
    }
  );

  return user;
}
```

This uses a five-minute cache expiration.

In production, also handle Redis connection errors and avoid allowing a cache outage to take down the entire application when a safe database fallback is possible.

---

## 50. Cache Invalidation

Cache invalidation means removing or updating cached data when the original database data changes.

Suppose:

```text
MySQL:
User Name = John

Redis:
User Name = John
```

The user updates their name to James.

MySQL:

```text
User Name = James
```

Redis still contains:

```text
User Name = John
```

The cache is now stale.

### Solution

After a successful database update:

```typescript
await pool.execute(
  "UPDATE users SET name = ? WHERE id = ?",
  ["James", userId]
);

await redis.del(`user:${userId}`);
```

The next request retrieves fresh data from MySQL and repopulates the cache.

### Common Cache Invalidation Strategies

| Strategy | Description |
|---|---|
| TTL | Data expires automatically |
| Explicit Invalidation | Delete or update cache after database changes |
| Write-Through | Write database and cache as part of a coordinated operation |
| Write-Behind | Cache accepts writes and updates persistent storage asynchronously |
| Refresh-Ahead | Refresh cached data before expiration |

Write-behind strategies require careful durability and failure handling.

---

## 51. Redis Eviction Policies

Redis may need to remove cached keys when memory reaches its configured limit.

Common eviction policies include:

| Policy | Meaning |
|---|---|
| noeviction | Reject writes when memory limit is reached |
| allkeys-lru | Evict least recently used keys |
| volatile-lru | Evict least recently used keys with expiration |
| allkeys-lfu | Evict least frequently used keys |
| volatile-lfu | Evict least frequently used keys with expiration |
| allkeys-random | Evict random keys |
| volatile-random | Evict random expiring keys |
| volatile-ttl | Evict keys with the shortest remaining TTL |

Redis documents these eviction policies in its official documentation. [Redis](https://redis.io/docs/latest/reference/eviction/?utm_source=chatgpt.com)

### LRU vs LFU

**LRU – Least Recently Used**

Removes keys that have not been accessed recently.

**LFU – Least Frequently Used**

Removes keys that are accessed less frequently.

The appropriate policy depends on the access pattern.

---

# PART 15: DATABASE BACKUP AND RECOVERY

## 52. Why Is Database Backup Important?

A database can be lost or corrupted because of:

- Hardware failure.
- Accidental deletion.
- Software bugs.
- Ransomware.
- Incorrect migrations.
- Human error.
- Infrastructure failure.

A backup allows you to restore data after a failure.

### Backup Types

| Backup Type | Description |
|---|---|
| Full Backup | Copies the complete database |
| Incremental Backup | Copies changes since a previous backup |
| Differential Backup | Copies changes since the last full backup |
| Logical Backup | Exports database objects and data as SQL or another logical format |
| Physical Backup | Copies database files or storage-level representations |

### Logical Backup Example

Using `mysqldump`:

```bash
mysqldump -u root -p expense_tracker > backup.sql
```

Restore:

```bash
mysql -u root -p expense_tracker < backup.sql
```

### Backup Specific Tables

```bash
mysqldump -u root -p expense_tracker users expenses > backup.sql
```

### Backup All Databases

```bash
mysqldump -u root -p --all-databases > all_databases.sql
```

For a live InnoDB database, use an appropriate consistent backup strategy and understand the implications of schema changes and concurrent operations.

---

## 53. Point-in-Time Recovery (PITR)

Point-in-time recovery allows you to restore a database to a specific point in time.

Suppose:

```text
10:00 AM - Database Backup
11:00 AM - User Updates Records
12:00 PM - Important Data Deleted
```

You want to recover the database to 11:55 AM.

A full backup alone cannot necessarily restore that exact state.

You can combine:

1. A full backup.
2. Binary logs containing subsequent changes.

### PITR Flow

```text
Full Backup
     |
     v
Restore Backup
     |
     v
Replay Binary Logs
     |
     v
Stop Before Unwanted Operation
     |
     v
Recovered Database
```

PITR is especially useful when accidental changes or deletions occur.

### Important

Backup frequency and binary log retention must be planned together.

A backup is not sufficient if the logs needed to replay changes have already been deleted.

---

## 54. Backup Best Practices

1. Schedule automated backups.
2. Keep multiple backup copies.
3. Store backups separately from the production database.
4. Encrypt sensitive backups.
5. Restrict backup access.
6. Monitor backup failures.
7. Define retention policies.
8. Test restore procedures regularly.
9. Maintain binary logs where PITR is required.
10. Document recovery procedures.

**Important:** A backup that has never been restored and tested should not be considered a proven recovery strategy.

---

# PART 16: HIGH AVAILABILITY AND DISASTER RECOVERY

## 55. What Is High Availability?

High Availability (HA) means designing systems to remain operational despite certain failures.

Suppose your primary database server fails.

Without HA:

```text
Application
    |
    v
Database Server
    |
    X
 Server Failure
```

The application cannot access the database.

With an appropriate HA architecture:

```text
             Application
                  |
                  v
             Database Router
                  |
          -------------------
          |                 |
          v                 v
      Primary DB        Standby DB
          |
          X
       Failure
                            |
                            v
                      Promotion
                            |
                            v
                      New Primary
```

The exact failover mechanism depends on the database platform.

### HA Techniques

- Replication.
- Automatic failover.
- Health checks.
- Load balancing.
- Redundant infrastructure.
- Monitoring.
- Backup and recovery.

## 56. What Is Disaster Recovery?

Disaster Recovery (DR) is the process of restoring systems after a major failure.

Examples:

- Entire data center failure.
- Cloud region outage.
- Data corruption.
- Security incident.
- Accidental deletion of critical data.

### High Availability vs Disaster Recovery

| High Availability | Disaster Recovery |
|---|---|
| Focuses on maintaining service | Focuses on restoring service |
| Handles expected component failures | Handles larger incidents |
| Often uses standby systems | Uses backups and recovery infrastructure |
| Usually aims for short interruption | Recovery may take longer |

---

## 57. RTO and RPO

Two important disaster recovery metrics are:

### RTO – Recovery Time Objective

RTO defines how long a system can remain unavailable after a failure.

Example:

```text
RTO = 30 minutes
```

The target is to restore the service within 30 minutes.

### RPO – Recovery Point Objective

RPO defines the maximum acceptable amount of data loss measured in time.

Example:

```text
RPO = 5 minutes
```

The recovery strategy aims to lose no more than approximately five minutes of recent data.

### RTO vs RPO

| RTO | RPO |
|---|---|
| Maximum target downtime | Maximum target data-loss window |
| Time to restore service | How far back recovery may need to go |
| Measures availability recovery | Measures data recovery |

### Example

An e-commerce application defines:

```text
RTO = 15 minutes
RPO = 1 minute
```

This means:

- The service should be restored within 15 minutes.
- The recovery strategy should limit data loss to approximately one minute of transactions.

Achieving these objectives requires suitable architecture, monitoring, backups and regular recovery testing.

---

# PART 17: DISTRIBUTED TRANSACTIONS

## 58. What Is a Distributed Transaction?

A distributed transaction involves operations across multiple independent systems or databases.

Example:

An e-commerce order involves:

1. Order Database.
2. Payment Service.
3. Inventory Service.

```text
           Order Service
                 |
          -----------------
          |               |
          v               v
     Payment Service  Inventory Service
          |               |
          v               v
     Payment DB       Inventory DB
```

Suppose payment succeeds but inventory reservation fails.

How should the system handle this?

A single local database transaction cannot automatically make independent services atomic together.

This is where distributed transaction patterns become important.

---

## 59. Two-Phase Commit (2PC)

Two-Phase Commit is a distributed transaction protocol.

It has two main phases.

### Phase 1: Prepare

The coordinator asks all participating systems whether they can commit.

```text
Coordinator
     |
     |-- Prepare --> Database A
     |
     |-- Prepare --> Database B
```

Each participant responds with a prepared or failed status.

### Phase 2: Commit or Abort

If all participants successfully prepare, the coordinator instructs them to commit.

Otherwise, it instructs them to abort.

```text
          Coordinator
                |
        -----------------
        |               |
        v               v
    Database A      Database B
        |               |
        v               v
      Commit          Commit
```

### Advantages

- Coordinates atomic outcomes across participants under the protocol's assumptions.
- Provides a structured distributed commit process.

### Disadvantages

- Coordination overhead.
- Participants may remain blocked while waiting for a decision.
- Coordinator failure creates recovery complexity.
- Not suitable for every microservice architecture.

---

## 60. Saga Pattern

Saga is a distributed transaction pattern that breaks a large business operation into smaller local transactions.

Each local transaction has a corresponding compensating action where needed.

Example: E-commerce order.

```text
Create Order
     |
     v
Reserve Inventory
     |
     v
Process Payment
     |
     v
Confirm Order
```

Suppose payment fails.

The system may execute compensation:

```text
Payment Failed
      |
      v
Release Inventory
      |
      v
Cancel Order
```

### Saga Example

| Step | Action | Compensation |
|---|---|---|
| 1 | Create Order | Cancel Order |
| 2 | Reserve Inventory | Release Inventory |
| 3 | Charge Payment | Refund Payment |
| 4 | Confirm Order | Reopen or compensate according to business rules |

### Saga Types

**Choreography**

Services communicate through events without a central coordinator.

**Orchestration**

A central orchestrator controls the sequence of operations.

### 2PC vs Saga

| 2PC | Saga |
|---|---|
| Distributed commit protocol | Distributed business transaction pattern |
| Coordinates commit/abort | Uses local transactions and compensations |
| Can block participants | Usually avoids global database locks |
| Strong atomic commit semantics under assumptions | Eventual business consistency |
| Suitable for compatible transactional participants | Common in microservice workflows |

---

# PART 18: DATABASE CONSISTENCY MODELS

## 61. What Is Database Consistency?

Consistency describes the guarantees a database system provides about the visibility and ordering of data changes.

Different systems provide different consistency models.

### Strong Consistency

After a successful write, subsequent reads observe that write according to the system's consistency guarantees.

### Eventual Consistency

Replicas may temporarily contain different values, but they are expected to converge if updates stop and replication succeeds.

### Read-Your-Writes Consistency

A client should be able to observe its own successful writes in subsequent reads.

### Monotonic Reads

Once a client has observed a particular value, later reads should not move backward to an older state.

### Example: Eventual Consistency

```text
Primary:
Balance = ₹8,000

Replica 1:
Balance = ₹8,000

Replica 2:
Balance = ₹10,000
```

After replication catches up:

```text
Primary:
Balance = ₹8,000

Replica 1:
Balance = ₹8,000

Replica 2:
Balance = ₹8,000
```

The replicas have converged.

### Consistency Models Summary

| Model | Meaning |
|---|---|
| Strong Consistency | Reads reflect writes according to strong ordering guarantees |
| Eventual Consistency | Replicas eventually converge |
| Read-Your-Writes | Client can observe its own writes |
| Monotonic Reads | Client does not observe older states after newer ones |
| Causal Consistency | Causally related operations are observed in a consistent order |

---

# PART 19: CAP THEOREM

## 62. What Is CAP Theorem?

CAP Theorem is a concept in distributed systems.

It describes a trade-off involving three properties:

- Consistency.
- Availability.
- Partition Tolerance.

### Consistency (C)

Every successful read observes the latest committed write according to the system's consistency model.

### Availability (A)

Every request to a non-failing node receives a response, although that response may not contain the latest data.

### Partition Tolerance (P)

The system continues operating despite communication failures between parts of the distributed system.

### CAP Diagram

```text
                 Consistency
                      /\
                     /  \
                    /    \
                   /      \
                  /        \
                 /__________\
          Availability    Partition
                           Tolerance
```

### What Does CAP Actually Mean?

When a network partition occurs, a distributed system cannot guarantee both full consistency and full availability for every request.

It must choose how to behave during the partition.

### CP System

Prioritizes consistency during a network partition.

Some requests may be rejected or delayed.

### AP System

Prioritizes availability during a network partition.

Some requests may receive stale or conflicting data that must later be reconciled.

### CAP Comparison

| CP | AP |
|---|---|
| Prioritizes consistency during partition | Prioritizes availability during partition |
| May reject requests | May return potentially stale data |
| Useful when consistency is critical | Useful when availability is critical |
| Requires handling reduced availability during partition | Requires reconciliation or conflict handling |

**Important:** CAP does not mean a database simply chooses any two properties at all times. The trade-off becomes relevant when a network partition occurs.

---

# PART 20: DATABASE PERFORMANCE MONITORING

## 63. Why Is Database Monitoring Important?

Even a correctly designed database can become slow because of:

- Increased traffic.
- Missing indexes.
- Slow queries.
- Lock contention.
- Connection exhaustion.
- Memory pressure.
- Disk I/O bottlenecks.
- Replication lag.

Monitoring helps identify these problems before they cause major outages.

## 64. Important Database Metrics

| Metric | Meaning |
|---|---|
| Query Latency | Time taken by database queries |
| QPS | Queries per second |
| TPS | Transactions per second |
| CPU Usage | CPU consumed by database |
| Memory Usage | Database memory consumption |
| Disk I/O | Storage read/write activity |
| Connections | Active database connections |
| Slow Queries | Queries exceeding a time threshold |
| Lock Wait Time | Time spent waiting for locks |
| Deadlock Count | Number of detected deadlocks |
| Replication Lag | Delay between primary and replica |
| Cache Hit Ratio | Percentage of accesses served from cache |

### Useful MySQL Commands

Check active processes:

```sql
SHOW FULL PROCESSLIST;
```

Check variables:

```sql
SHOW VARIABLES;
```

Check global status:

```sql
SHOW GLOBAL STATUS;
```

Check InnoDB status:

```sql
SHOW ENGINE INNODB STATUS;
```

Check indexes:

```sql
SHOW INDEX FROM users;
```

Check table size:

```sql
SELECT
    TABLE_NAME,
    ROUND(
        (DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024,
        2
    ) AS size_mb
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = DATABASE();
```

### Slow Query Log

MySQL can record queries that exceed a configured execution-time threshold.

Example configuration:

```ini
slow_query_log = ON
long_query_time = 1
```

This example logs queries exceeding one second, subject to the configured server settings.

Do not blindly set thresholds without considering workload requirements.

---

# PART 21: COMPLETE DATABASE ARCHITECTURE EXAMPLE

## 65. Designing a Scalable Expense Tracker Database

Let's apply these concepts to a real-world application.

Suppose you are building an Expense Tracker.

It has:

- Users.
- Categories.
- Expenses.
- Reports.
- Authentication.
- Redis caching.

### Database Schema

#### Users

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE = InnoDB;
```

#### Categories

```sql
CREATE TABLE categories (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    user_id BIGINT NOT NULL,

    CONSTRAINT fk_categories_user
    FOREIGN KEY (user_id)
    REFERENCES users(id)
) ENGINE = InnoDB;
```

#### Expenses

```sql
CREATE TABLE expenses (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    category_id BIGINT NOT NULL,
    title VARCHAR(150) NOT NULL,
    amount DECIMAL(12, 2) NOT NULL,
    expense_date DATE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_expenses_user
    FOREIGN KEY (user_id)
    REFERENCES users(id),

    CONSTRAINT fk_expenses_category
    FOREIGN KEY (category_id)
    REFERENCES categories(id),

    CHECK (amount > 0)
) ENGINE = InnoDB;
```

### Create Indexes

```sql
CREATE INDEX idx_expenses_user
ON expenses(user_id);
```

```sql
CREATE INDEX idx_expenses_category
ON expenses(category_id);
```

```sql
CREATE INDEX idx_expenses_user_date
ON expenses(user_id, expense_date);
```

### Common Query

Retrieve expenses for a particular user during a date range.

```sql
SELECT
    id,
    title,
    amount,
    expense_date
FROM expenses
WHERE user_id = 10
AND expense_date >= '2026-01-01'
AND expense_date < '2026-02-01'
ORDER BY expense_date DESC;
```

The composite index:

```sql
(user_id, expense_date)
```

is a candidate for this query.

Verify its actual usage with:

```sql
EXPLAIN ANALYZE
SELECT
    id,
    title,
    amount,
    expense_date
FROM expenses
WHERE user_id = 10
AND expense_date >= '2026-01-01'
AND expense_date < '2026-02-01'
ORDER BY expense_date DESC;
```

### Add Redis Caching

Cache frequently accessed reports.

```text
GET /api/reports/monthly
          |
          v
      Check Redis
          |
          |-- Hit --> Return Cached Report
          |
          |-- Miss
                  |
                  v
              Query MySQL
                  |
                  v
              Store in Redis
                  |
                  v
              Return Report
```

### Add Read Replicas

```text
                Expense Tracker API
                         |
                 -----------------
                 |               |
                 v               v
             Write DB         Read DB
             Primary          Replica
                 |               ^
                 |               |
                 +---------------+
                   Replication
```

Use the primary for writes and consistency-sensitive reads.

Use the replica for suitable read workloads where some replication delay is acceptable.

### Add Backup and Recovery

```text
Production MySQL
       |
       v
Scheduled Full Backup
       |
       v
Secure Backup Storage
       |
       v
Binary Log Retention
       |
       v
Point-in-Time Recovery
```

### Add Monitoring

Monitor:

- Slow queries.
- Query execution plans.
- Connection pool utilization.
- Buffer pool behavior.
- Deadlocks.
- Replication lag.
- Redis memory.
- Cache hit rates.
- Backup completion.

This architecture is a starting point. Introduce replicas, partitioning and additional infrastructure only when workload measurements justify them.

---

# PART 22: COMMON DATABASE ENGINEERING MISTAKES

## 66. Mistakes to Avoid

| Mistake | Better Approach |
|---|---|
| Creating indexes on every column | Index according to query workload |
| Ignoring EXPLAIN | Inspect execution plans |
| Using SELECT * everywhere | Retrieve required columns |
| Ignoring connection pooling | Reuse controlled connections |
| Keeping transactions open too long | Keep transactions short |
| Ignoring deadlocks | Use consistent lock ordering and retries |
| Using huge OFFSET values | Consider cursor pagination |
| Assuming replicas are always current | Account for replication lag |
| Adding sharding too early | Optimize and scale simpler architectures first |
| Caching everything | Cache appropriate, frequently accessed data |
| Ignoring cache invalidation | Design invalidation and consistency strategies |
| Treating Redis as a permanent backup | Configure appropriate persistence and backup |
| Never testing backups | Perform regular restore tests |
| Assuming replication is backup | Maintain independent backups |
| Ignoring RTO and RPO | Define recovery objectives |
| Using distributed transactions unnecessarily | Prefer simpler workflows where possible |
| Ignoring database monitoring | Monitor queries, locks and resources |

---

# PART 23: INTERVIEW QUESTIONS AND ANSWERS

## 67. Basic Questions

### Q1. What is a database storage engine?

A storage engine manages how a database stores, retrieves and modifies table data.

### Q2. What is InnoDB?

InnoDB is MySQL's default transactional storage engine. It supports ACID transactions, row-level locking, foreign keys and crash recovery.

### Q3. What is a database page?

A database page is a fixed-size unit used to organize and transfer database data between memory and storage.

### Q4. What is the InnoDB Buffer Pool?

It is a memory area that caches InnoDB data and index pages to reduce disk I/O.

### Q5. What is a redo log?

It records information used to redo changes during crash recovery.

### Q6. What is an undo log?

It stores information used for rollback and reconstructing earlier row versions.

### Q7. What is a binary log?

It records database changes and supports replication and point-in-time recovery.

### Q8. What is a database index?

An index is a data structure that helps locate records efficiently.

### Q9. What is a full table scan?

A full table scan examines all rows in a table to evaluate a query.

### Q10. What is EXPLAIN?

EXPLAIN displays the execution plan that MySQL expects to use for a query.

## 68. Intermediate Questions

### Q11. What is a B-Tree index?

It is a balanced tree-based index structure that supports efficient lookups and ordered operations.

### Q12. What is a hash index?

A hash index uses hashing to locate keys, primarily supporting equality lookups.

### Q13. What is a composite index?

A composite index contains multiple columns.

### Q14. What is the leftmost prefix rule?

A composite index can generally be used through its leading columns and their prefixes.

### Q15. What is a covering index?

A covering index contains all columns needed by a query, potentially avoiding an additional table lookup.

### Q16. What is index selectivity?

It describes how effectively an index condition narrows down matching records.

### Q17. What is a clustered index?

In InnoDB, the clustered index organizes table rows according to the indexed key, normally the primary key.

### Q18. What is a secondary index?

A secondary index is an additional index that stores indexed values and the primary key needed to locate the clustered row.

### Q19. What is connection pooling?

Connection pooling maintains reusable database connections to reduce connection creation overhead.

### Q20. What is replication lag?

It is the delay between a change being committed on the primary database and being applied on a replica.

## 69. Advanced Questions

### Q21. What is MVCC?

Multi-Version Concurrency Control maintains multiple versions of records so that transactions can read consistent snapshots while other transactions modify data.

### Q22. What is a dirty read?

A dirty read occurs when a transaction reads uncommitted changes made by another transaction.

### Q23. What is a non-repeatable read?

It occurs when the same row is read twice within a transaction and returns different committed values.

### Q24. What is a phantom read?

It occurs when a repeated range query returns a different set of matching rows because another transaction changed the set.

### Q25. What is a deadlock?

A deadlock occurs when transactions wait on each other in a circular dependency.

### Q26. How does InnoDB handle deadlocks?

It detects the deadlock and normally rolls back one of the transactions.

### Q27. What is optimistic locking?

Optimistic locking detects conflicting modifications using version numbers or similar conditions rather than holding locks throughout the operation.

### Q28. What is database partitioning?

Partitioning divides a table into smaller logical pieces based on a partitioning strategy.

### Q29. What is database sharding?

Sharding distributes data across multiple independent database servers.

### Q30. What is partition pruning?

It is the optimizer's ability to eliminate partitions that cannot contain matching rows.

### Q31. What is the difference between partitioning and sharding?

Partitioning divides table data into partitions, while sharding distributes data across independent database nodes.

### Q32. What is Redis?

Redis is an in-memory data structure server commonly used for caching, session storage, queues and other low-latency operations.

### Q33. What is cache invalidation?

It is the process of removing or updating cached data when the underlying source data changes.

### Q34. What is the cache-aside pattern?

The application checks the cache first and queries the database when the data is not present.

### Q35. What is point-in-time recovery?

PITR restores a database to a particular point in time using backups and transaction logs.

### Q36. What is high availability?

High availability is the design of systems to remain operational despite certain failures.

### Q37. What are RTO and RPO?

RTO defines the target recovery time. RPO defines the maximum acceptable data-loss window.

### Q38. What is a distributed transaction?

A distributed transaction coordinates operations across multiple independent systems or databases.

### Q39. What is the Saga pattern?

Saga coordinates a sequence of local transactions using compensating actions when failures occur.

### Q40. What is CAP Theorem?

CAP describes the trade-off between consistency and availability during network partitions in distributed systems.

---

# PART 24: PRACTICAL ASSIGNMENTS

## 70. Assignment 1 – Database Internals

Study and document:

- InnoDB architecture.
- Buffer Pool.
- Redo Log.
- Undo Log.
- Binary Log.
- Database Pages.
- Clustered Index.

Create a diagram showing how a SELECT and UPDATE operation interact with these components.

## 71. Assignment 2 – Query Optimization

Create a table containing at least 100,000 expense records.

Write queries for:

- Searching by user ID.
- Filtering by category.
- Filtering by date.
- Sorting by amount.
- Grouping by month.

Use:

```sql
EXPLAIN
```

and:

```sql
EXPLAIN ANALYZE
```

Compare execution plans before and after creating indexes.

## 72. Assignment 3 – Advanced Indexing

Create:

- Primary key index.
- Unique index.
- Single-column index.
- Composite index.
- Covering index.
- Full-text index.

Understand which queries benefit from each index.

Also test the leftmost prefix rule.

## 73. Assignment 4 – Transaction Management

Create a banking database.

Implement:

- Deposit.
- Withdrawal.
- Transfer between accounts.
- Transaction history.

Use MySQL transactions.

Test rollback and insufficient-balance scenarios.

## 74. Assignment 5 – Deadlock Simulation

Create two database sessions.

Lock two records in opposite orders.

Observe the deadlock.

Study:

```sql
SHOW ENGINE INNODB STATUS;
```

Then fix the locking order.

## 75. Assignment 6 – Connection Pooling

Create a Node.js API using `mysql2`.

Implement:

- Connection pool.
- Pool-based queries.
- Transaction handling.
- Connection release.
- Error handling.

Test the application with multiple simultaneous requests.

## 76. Assignment 7 – Redis Caching

Integrate Redis with your Expense Tracker API.

Implement:

- User caching.
- Category caching.
- Monthly report caching.
- Cache expiration.
- Cache invalidation.

Measure response times with and without caching.

## 77. Assignment 8 – Database Replication

Set up a MySQL primary and replica in a controlled environment.

Understand:

- Binary logs.
- Replication configuration.
- Replication status.
- Replication lag.
- Read and write routing.

Do not perform production replication experiments on an unprotected database.

## 78. Assignment 9 – Partitioning

Create a large orders table.

Implement:

- Range partitioning.
- List partitioning.
- Hash partitioning.

Test partition pruning using EXPLAIN.

## 79. Assignment 10 – Backup and Recovery

Implement:

- Scheduled logical backups.
- Secure backup storage.
- Binary log retention.
- Restore procedure.
- Point-in-time recovery.

Document the RTO and RPO for your test system.

## 80. Assignment 11 – Database Sharding

Design a hypothetical application with three database shards.

Implement a basic shard-routing function.

Compare:

- Range sharding.
- Hash sharding.
- Consistent hashing.

Document the challenges of moving users between shards.

## 81. Assignment 12 – Complete Database Engineering Project

Build a scalable Expense Tracker database architecture.

### Required Features

**Database**

- Users.
- Categories.
- Expenses.
- Transactions.
- Reports.

**Performance**

- Composite indexes.
- Covering indexes.
- Query optimization.
- EXPLAIN ANALYZE.

**Concurrency**

- Transactions.
- Locking.
- Deadlock handling.
- Optimistic locking.

**Scaling**

- Connection pooling.
- Redis caching.
- Read replica architecture.

**Reliability**

- Database backups.
- Recovery testing.
- Replication monitoring.
- RTO and RPO definitions.

**Advanced Design**

- Partitioning strategy.
- Sharding design.
- Distributed consistency considerations.

---

# PART 25: QUICK REVISION

## 82. Important Concepts Summary

| Concept | Key Point |
|---|---|
| Database Architecture | Internal organization of database components |
| Storage Engine | Manages data storage and access |
| InnoDB | Transactional MySQL storage engine |
| Database Page | Fixed-size storage unit |
| Buffer Pool | Caches database pages in memory |
| Redo Log | Supports crash recovery |
| Undo Log | Supports rollback and MVCC |
| Binary Log | Supports replication and PITR |
| Query Optimizer | Chooses an execution strategy |
| EXPLAIN | Displays execution plan |
| EXPLAIN ANALYZE | Executes query and reports actual execution information |
| Full Table Scan | Examines all table rows |
| Index | Helps locate records efficiently |
| B-Tree | Supports ordered lookups and range queries |
| Hash Index | Supports equality lookups |
| Primary Key | Uniquely identifies records |
| Secondary Index | Additional search structure |
| Composite Index | Index containing multiple columns |
| Covering Index | Contains all data needed by a query |
| Selectivity | Ability to narrow matching records |
| Clustered Index | Organizes InnoDB table rows |
| Shared Lock | Allows compatible reads |
| Exclusive Lock | Protects modifications |
| Gap Lock | Prevents inserts into an index gap |
| Next-Key Lock | Record lock plus gap lock |
| MVCC | Maintains multiple row versions |
| Isolation Level | Controls transaction visibility |
| Deadlock | Circular transaction dependency |
| Replication | Copies database changes |
| Replication Lag | Delay in applying replicated changes |
| Partitioning | Divides a table into partitions |
| Sharding | Distributes data across database nodes |
| Connection Pool | Reuses database connections |
| Redis | In-memory data structure server |
| Cache-Aside | Check cache before database |
| Cache Invalidation | Removes or updates stale cache |
| Backup | Copy of data for recovery |
| PITR | Restore database to a specific time |
| High Availability | Maintains service during failures |
| Disaster Recovery | Restores systems after major failures |
| RTO | Target recovery time |
| RPO | Maximum acceptable data-loss window |
| 2PC | Distributed commit protocol |
| Saga | Local transactions with compensating actions |
| CAP | Consistency, Availability and Partition Tolerance |

---

# PART 26: OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

Use these resources to study the underlying concepts in more depth.

### MySQL Documentation

1. [MySQL 8.4 Reference Manual](https://dev.mysql.com/doc/refman/8.4/en/)
2. [MySQL Query Optimization](https://dev.mysql.com/doc/refman/8.4/en/optimization.html)
3. [MySQL Index Optimization](https://dev.mysql.com/doc/refman/8.4/en/optimization-indexes.html)
4. [InnoDB Architecture](https://dev.mysql.com/doc/refman/8.4/en/innodb-architecture.html)
5. [InnoDB Locking](https://dev.mysql.com/doc/refman/8.4/en/innodb-locking.html)
6. [Transaction Isolation Levels](https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-isolation-levels.html)
7. [MySQL Replication](https://dev.mysql.com/doc/refman/8.4/en/replication.html)

### Redis Documentation

1. [Redis Documentation](https://redis.io/docs/latest/)
2. [Redis Data Types](https://redis.io/docs/latest/develop/data-types/)
3. [Redis Caching](https://redis.io/docs/latest/develop/use/patterns/)
4. [Redis Eviction Policies](https://redis.io/docs/latest/develop/reference/eviction/)
5. [Redis Persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)

---

# PHASE 52 – COMPLETION CHECKLIST

Before considering this phase complete, verify that you understand the following concepts.

### Database Internals

- [ ] Understand database architecture.
- [ ] Understand MySQL storage engines.
- [ ] Understand InnoDB.
- [ ] Understand database pages.
- [ ] Understand the Buffer Pool.
- [ ] Understand Redo Logs.
- [ ] Understand Undo Logs.
- [ ] Understand Binary Logs.

### Query Optimization

- [ ] Understand query execution.
- [ ] Understand full table scans.
- [ ] Use EXPLAIN.
- [ ] Use EXPLAIN ANALYZE.
- [ ] Understand query access types.
- [ ] Optimize WHERE conditions.
- [ ] Optimize JOIN queries.
- [ ] Understand slow query logs.

### Indexing

- [ ] Understand B-Tree indexes.
- [ ] Understand hash indexes.
- [ ] Understand primary indexes.
- [ ] Understand secondary indexes.
- [ ] Understand unique indexes.
- [ ] Understand composite indexes.
- [ ] Understand covering indexes.
- [ ] Understand index selectivity.
- [ ] Understand the leftmost prefix rule.
- [ ] Understand clustered indexes.

### Transactions and Concurrency

- [ ] Understand database locking.
- [ ] Understand shared and exclusive locks.
- [ ] Understand record and gap locks.
- [ ] Understand pessimistic locking.
- [ ] Understand optimistic locking.
- [ ] Understand transaction isolation levels.
- [ ] Understand dirty reads.
- [ ] Understand non-repeatable reads.
- [ ] Understand phantom reads.
- [ ] Understand deadlocks.
- [ ] Implement deadlock handling.

### Database Scaling

- [ ] Understand replication.
- [ ] Understand replication lag.
- [ ] Understand synchronous and asynchronous replication.
- [ ] Understand partitioning.
- [ ] Understand partition pruning.
- [ ] Understand sharding.
- [ ] Understand connection pooling.

### Caching and Reliability

- [ ] Understand Redis.
- [ ] Understand cache-aside.
- [ ] Understand cache invalidation.
- [ ] Understand Redis eviction policies.
- [ ] Implement database backups.
- [ ] Understand point-in-time recovery.
- [ ] Understand high availability.
- [ ] Understand disaster recovery.
- [ ] Define RTO and RPO.

### Distributed Database Concepts

- [ ] Understand distributed transactions.
- [ ] Understand Two-Phase Commit.
- [ ] Understand Saga.
- [ ] Understand consistency models.
- [ ] Understand eventual consistency.
- [ ] Understand CAP Theorem.

---

# What Comes Next?

**Phase 53 – Microservices Architecture, Distributed Systems and Inter-Service Communication**

The next phase will cover:

1. Monolithic vs Microservices Architecture.
2. Microservices Fundamentals.
3. Service Decomposition.
4. Domain-Driven Design in Microservices.
5. Service-to-Service Communication.
6. REST-Based Communication.
7. gRPC.
8. Message Brokers.
9. RabbitMQ.
10. Apache Kafka.
11. Event-Driven Architecture.
12. Eventual Consistency.
13. Service Discovery.
14. API Gateway Architecture.
15. Centralized Configuration.
16. Distributed Logging.
17. Distributed Tracing.
18. Circuit Breaker Pattern.
19. Retry and Timeout Patterns.
20. Bulkhead Pattern.
21. Saga Pattern.
22. Outbox Pattern.
23. Idempotent Message Processing.
24. Microservices Security.
25. Docker-Based Microservices.
26. Microservices Deployment.
27. Monitoring and Observability.
28. Complete Production-Oriented Microservices Project.

**Phase 52 is complete. Say "OK, Phase 53" when you're ready to continue.**