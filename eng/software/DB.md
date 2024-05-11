## ACID
### Atomic
- a transaction is treated as an all-or-nothing operation, either all data modifications are executed or none of them are applied
- guarantees that if a system failure occurs during a transaction, the DB won't be left in a partially updated state
### Consistent
- a transaction can only take the DB from one valid state to another valid state
- valid state means that the DB follows the defined rules/schema, constraints and relationships within the DB
- guarantees data integrity is maintained (valid and correct)
### Isolation
- concurrent transactions are isolated from each other
- transactions should execute as if it's the only one running against the db (done by acquiring a lock)
- prevents inconsistencies that might occur if multiple transactions try to modify the same data simultaneously w/o proper coordination
### Durable
- once a transaction is committed, its changes are permanently saved in the database
- even with system crash / power outage, changes are recoverable (done by writing changes to log files)
- guarantees that the results of a successful transaction are never lost
## RDBMS
- Used for Consistency over availability in distributed systems
- have ACID properties
	- can get phantom reads, dirty reads (reading uncommitted data) or non-repeatable reads, among others if no ACID properties
- changes to DB typically get stored in a memory buffer for performance reasons (writing to disk takes longer)
### Performance
- replicate DBs + sharding (improves availability and scaling), caching frequent hits
- Use profiler, query logs, APM (app perf. monitoring) to find slow queries
- Memory buffers
	- reduce need for frequent Disk I/O operations + workspace
	- Data buffers
		- cache frequently read data
	- Write buffer
		- accumulate changes written to a database in a buffer
	- Log buffer
		- record of transactions
	- Working space for operations like sorts and joins
### Primary Key
- can be multiple columns, not null, uniquely identifies the row
#### Unique
- applies to one column, can be null
### Normalization
- process of structuring a relational database in order to reduce data redundancy (same info in multiple places) and improve data integrity (prevent anomalies, reduce risk of errors when modifying data)
	- comes at the cost of performance and more complex queries as they need to find data from different parts of the database
- for performance reasons, highly normalized database designs are selectively denormalized
- consider normalized if at least 3NF
	- done by getting rid of redundancies, partial dependencies, transitive dependencies, or multivalued dependencies (4NF) and enforcing FD rules (e.g. left side of FD must be a super key)
- Composite Primary key
	- multiple column PK
- Prime attribute:
	- part of the primary key, essential for uniquely identifying each row
- Non-Prime attribute:
	- not part of primary key
- Candidate key:
	- potentially a primary key
- Super key:
	- a less strict candidate key, may include non-prime attributes
- Functional dependency
	- constraint between two attributes / columns X and Y functionally determine each other if (X -> Y)
		- for each value of X, there's only **one** consistently associated value of Y
		- e.g.: 'Students' table, StudentID -> StudentName. Knowing the StudentID determines exactly one student's name
	- Analyzing functional dependencies helps identify potential places in a table where data could be broken down into smaller tables to achieve normalization
	- trivial FD if Y is a subset of X
#### 1NF
- each row has a unique identifier (primary key)
- no multi-valued attributes: columns should not have lists (e.g.: storing multiple skills in one column)
#### 2NF
- no partial dependencies
	- "Every non-prime attribute has a full functional dependency on Each candidate key"
		- columns should depend on the entire primary key, not just part of it
#### 3NF
- no transitive dependencies
	- non-key columns shouldn't depend on other non-key columns
		- Every non-trivial functional dependency either begins with a superkey or ends with a prime attribute (attributes depend only on candidate keys)
	- move one of the non-keys to separate table
#### BCNF
- Every functional dependency (X -> Y) in a table must satisfy one condition:
	- X (the determiner) must be a superkey (or potential superkey)
	- Every non-trivial functional dependency begins with a superkey (a stricter form of 3NF)
#### Anomalies
- Update
	- if same data is stored in multiple places, updating the data in one place, would leave the other instance with old / incorrect values
- Insertion
	- inability to insert data without additional unrelated data 
	- e.g.: A new author can't be added to the database unless they have at least one book associated with them (because foreign key dependencies necessitate it)
- Deletion
	- Unintended loss of data. Deleting a record might remove other valuable information unrelated to the intended deletion.
	- e.g.: Removing the only book by a specific author would also delete the record for that author entirely if the relationship between Books and Author requires that each author must have at least one book
### Locking mechanism
- enforces acid property
#### Shared Locks
- Multiple transactions can hold shared locks on the same data, **allowing concurrent reads**
- anyone can obtain if exclusive lock isn't being used
#### Exclusive Locks
- Only one transaction can hold an exclusive lock on **data being written**
- This prevents other transactions from reading or modifying the data until the lock is released
- can only obtain if no other shared/exclusive lock is being used
### Transactions
- a unit of work that groups together a series of db operations (inserts, updates, deletes)
- committing a transaction signifies successful completion of all its operations
- States: Active -> Partially committed (no commits yet, data written in buffer, buffer not yet written to disk) -> Committed (cannot rollback) -> Failed (can abort from active or part commit) -> Terminated
- Autocommit is when each individual SQL statement is treated as a single-operation transaction
### Cascades
- enforce referential integrity in a database by automatically propagating changes (or deletions) from a parent table to its related child tables
- **CASCADE DELETE:** When you delete a record in the parent table, any associated records in the child table with a foreign key reference are also automatically deleted
	- prevents orphaned child records
	- caution: can lead to significant deletion chains
- **CASCADE UPDATE:** When you update the primary key value in a parent table, the corresponding foreign key values in the child table are also automatically updated to reflect the change
- alternatives:
	- instead of deleting child record, set the foreign key in child table to:
		- SET NULL
		- SET DEFAULT
### DB Migrations
- see DB notes
- https://phauer.com/2015/databases-challenge-continuous-delivery/
- Any changes to the schema may require a DB migration as DBs have a strict schemas -> need to adapt existing data with schema changes and address compatibility with app code
	- adapting existing data can be difficult if there is a lot of data
	- if multiple apps use our DB, then you may need to assess compatibility with multiple apps
	- hard to roll back changes done in a DB
	- difficult to test as you would need DB similar to production DB
- If scheduled downtime is possible, then handling DB changes is simple
	- Downtime -> Make DB changes -> Update and deploy app -> Restart app
- DB migrations can be a challenge when trying to implementing continuous delivery
- Schema changes are ideally backwards compatible
- Each app / service ideally has its own DB, thus a DB change only needs to coordinate with a single app / service
- If no downtime is allowed -> do blue-green deployments -> make step-by-step DB and app changes to blue/green envs
### N+1 Query Problem
- Any time you loop over database results and immediately query for related data in each iteration, you open the door to the N+1 problem
	- can significantly slow down your application, especially as 'N' becomes large.
- Generally due to suboptimal queries -> use a single more complex queries with joins
- Common in ORMs as they do lazy loading
	- tell ORM to do eager loading (fetch related data during the initial query)
**Example**
1. Initial Query: You query your database to retrieve a set of 'N' records (for example, a list of blog posts).
2. Iterative Queries: Your application code then loops through the N results, and for each record, it triggers an additional query to fetch related data (e.g., loading the author information for each blog post).
- N + 1 Queries: 1 initial query + N additional queries triggered within the loop
## NoSQL
- Used for **Availability over consistency** in distributed systems (has eventual consistency)
- may **relax ACID properties** to prioritize availability and scalability
- **Denormalization is Common**
	- Strategic denormalization (purposeful duplication of data) to optimize for specific read patterns
	- goal is to avoid complex joins across multiple collections or documents that might hinder performance
- generally lightweight, open-source and often distributed
	- enables quick dev and ad-hoc data
### MongoDB
- stores data as JSON-like documents consisting of key-value pairs
- **Collections**
	- analgous to tables in RDBs
	- group related documents together
- **schema-less** so documents within a collection can have different fields and data types
	- allows for rapid development and flexibility
- Has a query language that lets you query your documents
- For performance and scalability, MongoDB supports
	- indexing, sharding, replication
- its flexibility (schemaless) makes it a good use case for when your data model is frequently changing (e.g.: user profiles)
### Redis
- an in-memory key-value data store (value can be an JSON like object)
	- keys always strings
- Redis Cluster
	- multiple redis nodes/servers that work together to form a single, large data store
	- high availability to improve fault tolerance
	- horizontal scaling
		- overcome memory limitations
	- may need to shard data, i.e. split data across the multiple nodes
- also offers pub/sub message queues, search engine
#### Writes & Persistence Mechanisms
- uses fork system call to write to disk
	- forks create a child process that is an exact copy of the parent process (including its memory)
		- however memory isn't duplicated due to Copy-on-Write (CoW) mechanism
	- minimizes disruption to parent serving clients
- writes between snapshots or AOF rewrites are stored in memory to minimize disk i/o
	- OS eventually flushes memory buffer to disk
##### RDB Snapshotting
- point-in-time snapshots of your entire data at specific intervals 
- good for periodic backups
##### Append-only file (AOF)
- logs every write to the server -> grows indefinitely
	- if too large, redis does a background rewrite of AOF file to make it more compact by removing redundant information
- replays AOF file to rebuild dataset during server restart
### Cassandra
- Wide-column store, uses tables, rows, columns like RDBs
	- unlike RDBs, columns can vary from row to row in same table
- distributed master-less architecture enables linear scalability, high availability and fault tolerance
- nodes are organized in a ring
	- pointless to run on a single machine
#### Gossip
- peer-to-peer communication mechanism between nodes
- a node shares / gossips with randomly selected nodes
	- exchange metadata about themselves and other nodes they know about
- metadata includes:
	- state: state of node (normal, leaving, joining)
	- load: how much data the node is storing
	- version: for data sync
- provides **eventual consistency** of cluster information
- surpasses RDBs for write throughput
### Firebase Realtime DB
- cloud hosted
- stores JSON
- designed for real-time data synchronization -> listens for event / data changes then pushes data changes to all connected clients instantaneously
- functions even when users go offline
	- Firebase maintains a local data cache and pushes when connectivity resumes
### ElasticSearch
- distributed search & analytics engine built on top of Apache Lucene
- stores JSON documents with nested structures
- Mappings
	- define the structure / fields and data types within a document
- Index
	- collection of related documents with a common set of fields (analagous to a DB in relational DBs)
- Inverted Index
	- maps each unique term to all the documents in which that term appears
	- exists within an index
- Indexing:
	- process of analyzing and storing data in an optimized format for fast search and retrieval
#### Scalability and Availability
- Distributed cluster is a group of nodes each running an instance of Elastic search working together to provide scalability and fault tolerance
- An index can be split into shards, increasing scalability and availability
- Replicas
## Indexes
- performed on a specific (or combination of) columns
- purpose is to speed up data retrieval, otherwise may need to scan entire table to find row
- Use for columns frequently used in search conditions (WHERE clauses)
- can think of the index at the back of a book
	- In a book, the index lists important terms and their corresponding page numbers for quick reference
	- A database index does something similar: it creates a special data structure that maps specific values in a database column to their locations, allowing you to find information much faster.
**How Database Indexes Work**
1. **Creation:** You create an index on a specific column (or a combination of columns) within a database table.
2. **Structure:** The database engine builds a separate data structure (often a B-tree or a hash table) that stores the indexed values along with pointers back to the original rows in the table.
3. **Queries:** When you run a query that includes a search condition on the indexed column, the database can use the index to quickly pinpoint the exact locations of the matching data rather than scanning the whole table
## Sharding
- a horizontal partitioning strategy
- It involves splitting a large database into smaller, more manageable chunks called shards
- Each shard is an independent database in itself and is held on a separate database server
- These shards collectively represent the entire dataset
**Why Database Sharding?**
- **Scalability:** Sharding allows you to distribute your dataset across multiple machines, handling more data and requests than a single database server could handle.
- **Performance:** Queries can be routed to specific shards where the data resides, reducing the amount of data each server needs to process. This can lead to significant performance gains.
	- **Geographic Distribution:** You can place shards closer to users in different regions to improve query response times.
- **Availability:** If one shard fails, the others might still function, maintaining a degree of system availability.
### Sharding Strategies
**Key-Based or Range-Based Sharding**
- **Concept:** Data is partitioned based on ranges of values within a selected column (the shard key). For example, customer data could be sharded based on customer ID (1-1000 on shard 1, 1001-2000 on shard 2, etc.) or geographical region (East Coast customers on one shard, West Coast on another), or customers with first names A-M in one shard.
- potentially uneven, but data location is predictable based on the shard key
**Hash-Based Sharding**
- **Concept:** A hash function is applied to the shard key to calculate a hash value. This value determines the shard where the data will reside.
- great for even distribution
## ORMs
- acts as a bridge or a translation layer between object-oriented programming (OOP) languages and relational databases
- lets you query and manipulate data from a database as objects, using OOP
