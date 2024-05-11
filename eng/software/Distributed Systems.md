## CAP Theorem
- foundational principle in distributed systems theory
- states that a distributed data store can guarantee only two out of the following three properties:
	- **Consistency:** Every read receives the most recent write or an error. This means all clients accessing the data see the same, up-to-date version at all times. (**Data accuracy**)
		- when someone writes to a server, that server should replicate its value to other servers before sending an ack to the client
	- **Availability:** **Every request receives a non-error** response, regardless of the state of any individual node in the system. This means clients can always read and write data.
	- **Partition Tolerance:** The system continues to operate even if the **network between nodes fails** or messages are lost or delayed. The system can withstand communication breakdowns within the distributed system itself.
	- Proof: https://mwhittaker.github.io/blog/an_illustrated_proof_of_the_cap_theorem/
- No single "ideal" solution for a distributed system -> you make trade-offs based on your priorities
- assume network failures will happen, thus choice between consistency and availability when a partition occurs (damage to physical components, traffic spike, memory leak, software bugs, power issue, malicious activity, environment)
- Applies to more than just distributed databases:
	- Caches, microservices
### CP (Consistency over Availability)
- when a network partition happens, the system might become unavailable for writes to ensure no inconsistent data is introduced.
- reads will always be accurate
- Traditional RDBMS are preferred
	- **ACID**
		- **Atomicity:** Transactions are all-or-nothing. If any part of a transaction fails, the entire transaction is rolled back, preventing data inconsistencies.
		- **Consistency:** The database strictly maintains a valid state at all times during and after transactions. Referential integrity and other database constraints are always upheld.
		- **Isolation:** Transactions execute independently, avoiding concurrency issues that could lead to inconsistent data. Multiple clients won't see partial results of each other's work.
		- **Durability:** Committed changes are persisted durably (stored on disk) to prevent data loss even in the event of system failures.
	- Strict schemas and can make immediate updates within a transaction
- **Financial Transactions:** Bank transfers or stock trades absolutely require consistency. Withdrawing money unavailable in your account or selling shares you no longer own create unacceptable situations.
- **Reservation/Booking Systems:** Airline booking systems, hotel reservations, etc., often prioritize strict consistency to prevent double booking issues or incorrect information leading to conflicts.
### AP (Availability over Consistency)
- resilience even during network failures, constant uptime
- Some **NoSQL databases** favor availability. They'll still accept reads and writes during a partition, even though it might lead to clients temporarily seeing different (inconsistent) versions of the data
	- This inconsistency will be resolved later through a process called **eventual consistency**.
	- e.g.: Cassandra, Redis, DynamoDB, MongoDB
- **Social Media Feeds:** A slightly stale post appearing temporarily isn't as detrimental as the entire feed being unavailable due to a network issue.
- **IoT Sensor Data:** Real-time analytics dashboards might favor getting a flow of sensor readings, even if some data points are slightly delayed, as compared to complete unavailability of data.
#### Eventual Consistency
- **Replication:** Data is replicated across multiple nodes (servers) within the distributed system. Data is versioned using timestamps or vector clocks (special data structs)
- **Last Write Wins (LWW):** The update with the most recent timestamp is considered authoritative. This is simple but can lead to unintentional data loss.
- **Read Repair:** When a client reads data, the system can compare multiple replicas, returning the most recent version and updating older replicas in the background.