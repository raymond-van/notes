# Parallel Programming
## Core Elements
### Task Decomposition
- art of breaking down a large problem into smaller, more manageable tasks that can be executed concurrently by multiple processors or threads
- Independent tasks
	- ideal scenario as these tasks can be executed in any order and allows for max concurrency and min coordination
- Dependent tasks
	- tasks that have dependencies on other tasks (could be partial dependency)
	- introduces constraints on order of executation and requires synchronization mechanisms (e.g.: barriers, semaphores)
	- complete dependence between two tasks cannot be parallelized
- **Goals:**
	- Maximize parallelism
		- Identify as many independent tasks as possible to keep all processors busy
	- Minimize dependencies
		- Reduce the need for tasks to wait for each other, as dependencies can lead to bottlenecks
	- Balance workload
		- Distribute tasks evenly to avoid some processors finishing much earlier than others
- **Strategies:**
	- Data Decomposition
		- Divide the input data into chunks and assign each chunk to a different processor (e.g., each processor works on a portion of an array)
	- Functional Decomposition
		- Split the problem into different functions or steps, each executed by a different processor (e.g., one processor handles image filtering, another handles edge detection)
	- Recursive Decomposition 
		- Divide a task recursively into smaller subtasks until they are small enough to be handled efficiently.
### Communication
- if there is shared memory, then use that
- if not, communication is done in the form of messages, potentially using collective operations (see below)
- need to consider latency (time), bandwidth (data transfer), synchronization
### Synchronization
- Coordinate the actions of parallel tasks, ensuring they access shared data or resources in a safe and orderly manner
#### Synchronization Mechanism
- Locks/Mutexes 
	- Prevent multiple tasks from accessing the same data simultaneously
- Barriers
	- Make all tasks wait until all have reached a certain point
- Semaphores
	- Control access to a limited number of resources
- Condition Variables
	- Allow tasks to wait for specific conditions to become true
### Amdahl's Law
- states that the potential speedup of a program is limited by the portion of the program that cannot be parallelized
- even with infinite parallel processing resources, the overall performance improvement is capped by the sequential (non-parallelizable) portion of the program
- expressed as:
	- S(N) = 1 / ((1 - P) + (P / N))
	- lim as N -> inf  =  1 / (1 - P)
	- Where:
		- S(N) is the theoretical maximum speedup using N processors
		- P is the proportion of the program that can be parallelized
		- (1 - P) is the proportion of the program that cannot be parallized
- diminishing returns because serial portion becomes bottleneck
- doesn't account for overheads like communication and synchronization between processors
## Challenges
### Race Conditions
- when multiple threads or processes try to modify the same data concurrently, leading to unpredictable results
### Deadlocks
- situations where two or more tasks are waiting for each other to release resources, causing a standstill
### Load Imbalance
- uneven distribution of work among processors, leading to underutilization of resources
### Communication Overhead
- time and resources spent on communication between parallel tasks can sometimes become a bottleneck
## Parallel paradigms
### SIMD
- **applies same instruction/operation to multiple data elements simultaneously**
- lockstep synchronizes all processors for consistency
- suitable for processing data streams where the same operation needs to be applied
- can perform an operation an a vector all at once
- **hardware** feature found in CPUs and GPUs
### SIMT (MD)
- similar to SIMD, but operates on threads rather than data elements
- **multiple threads execute the same instruction on different data**
- **software** execution model
	- a way of organizing computation on SIMD hardware
### STMD
- Nvidia
### SPMD
- a programming model where a (**single) program is ran on multiple processors working independently on different parts of the data**
- no lockstep, can progress through program at own pace
- more flexible than SIMD, can be implemented on both shared and distributed memory systems
### MIMD
- type of parallel architecture where different processors can execute different programs with different instructions on different data simultaneously
- facilitated by SPMD
## MPI (Message Passing Interface)
- https://mpitutorial.com/tutorials/
- distributed memory model
	- multiple processes each with its own independent memory space
		- single process would have its own memory space and no need for explicit communication mechanism (however in GPUs, multiprocessors (SMs) have shared memory)
	- no shared memory space for communication
	- so nodes communicate by sending messages through a channel which can be physical (e.g.: NV-Link) or logical channels (e.g.: TCP/IP)
- designed for **parallel computing on clusters of nodes** where each node has its own separate memory
- provides communication and synchronization between nodes in cluster through messages
- **Communicator**
	- a group of processes that have the ability to communicate with one another
	- each process in this group is assigned a **rank**
## OpenMP (Multi-processing)
- shared memory within a single machine
	- **multi-threads** within a single process, executing parallel tasks
	- enables parallelism within each node (multi-core CPU)
- designed for parallel computing on systems where multiple threads share the same memory space
- uses compiler directives to guide compiler towards parallelism
## Network Topology
- optimal choice for topology depends on:
	- Scalability: How many nodes do you need to support?
	- Communication Patterns: What types of communication (point-to-point, collective) are most common in your application?
	- Fault Tolerance: How important is it to keep the system running even if some nodes fail?
	- Cost: How much are you willing to spend on network infrastructure?
	- Complexity: How difficult will it be to implement and manage the chosen topology?
- hybrid is often used to achieve the best balance of performance, scalability and cost
- dynamic topologies have changing topologies depending on current workload or availability of resources
### Point-to-point
- simplest topology with a dedicated link b etween two endpoints
- e.g.: in MPI, a process may send a message to another process by providing the rank of the process and a unique _tag_ to identify the message -> receiver can then post a receive for a message with a given tag (or it may not even care about the tag), and then handle the data accordingly
### Bus
- each node is connected to a single central cable
### Daisy Chain
- connecting each computer in series to the next
### Ring
- nodes are connected in circular chain
### Star
- a central node (hub) connects to all other nodes
### Mesh
- interconnected nodes in grid or irregular pattern
- can be fully or partially connected
- large scale
### Tree
- hierarchical arrangement of nodes, with root node and branches
### Hypercube 
- multi-dimensional cube
- each node conntected to other nodes in different dimensions
- large scale
- efficient broadcast, all-reduce and prefix sum
## Collective Operations
- patterns often used in SPMD algorithms in parallel programming
- provided by MPI
- e.g.: Broadcast, reduce, all-redice, prefix-sum, barrier, gather, all-gather, scatter, all-to-all
- Time complexity depends on various factors:
	- size of data being communicated (n)
	- number of processes involved (p)
	- network topology
	- specific implementation of operation
### Broadcast
- O(n + log p) best case
	- in a tree-structure, root sends data to its children, then those children forward it to their children -> balanced tree has depth log p
- distribute (same) data from one process (root) to all other processes in the group
- in message-passing system:
	- usually involves a tree-like communication pattern to minimize number of messages
- e.g.: 
	- sharing model parameters across different gpus
	- broadcasting synchronization signals
### Reduce
- O(n + log p) best case
- combine data elements from all processes into a **single result** using an associative and commutative operation
	- e.g: sum, product, max, min
- final result is available at root process
- performed in tree-like fasion
	- each internal tree nodeapplies operation to the data received from its children
#### All-Reduce
- O(n + log p) best case
- similar to reduce but final result is available to all processes
- synchronization communication
	- operation cannot begin until all processes have joined
- naive implementation: reduce -> broadcast
	- double the communication, more optimized ways
- sophisticated implementations are ring-based or tree-based
- e.g.: updating ml model weights
### Prefix-sum / Scan
- O(n + log p)
- compute cumulative sum (or other associative operation, see reduce) of a sequence of values
### Barrier
- O(log p) best case
- synchronizes all processes in a group
- ensures no process moves past the barrier until all processes have reached it
- each process will signal its arrival at a barrier point
- coordinator will track of the arrivals of processes, or each process will comunicate with each other to determine when everyone has reached barrier
### Gather
- O(n + p) worst case
- collect data from all processes into a **single array** on one designated process (root)
	- data is possibly different and uncombined unlike reduce
- each proces sends local data to root
	- root then assembles the received data
#### All-Gather
- O(n + p) worst case
- final result is available to all processes
- naive implementation: Gather -> broadcast
	- double the communication, more optimized ways
### Scatter
- O(n + log p) best case
- distribute (potentially different) data elements (chunks) from an array on one process (root) to all processes in the group
### All-to-all
- O(n + p log p) worst case
- exchange data elements between all processes, where each process sends data to and receives data from every other process
## MapReduce
- big data processing
- programming model for processing massive datasets in a parallel and distributed manner
- map and reduce tasks can be run in parallel on different machines in a cluster
- reducers can start working before all map tasks have finished
- Map -> Shuffle & Sort -> Reduce
### Map
- takes input data (key-value pairs) and processes them independently, producing intermediate key-value pairs
- each map task operates on a different chunk of the input data.
### Shuffle & Sort
- intermediate key-value pairs are shuffled across the network and sorted by key
- this ensures that all pairs with the same key end up on the same machine in sorted order which makes it easy for the reducer to aggregate
### Reduce
- takes the sorted key-value pairs and aggregates values associated with the same key, producing the final output (key-value pairs)
- each reduce task operates on a different set of keys