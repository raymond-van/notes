# Hadoop
- framework that implements MapReduce
- designed to work on distributed datasets across multiple machines
- leverages data locality, where tasks are scheduled to run on the same machine as the data they need to process, reducing network traffic
- disk-based which gets out performed by Spark's in-memory processing
## Hadoop Distributed File System
# Spark
- in-memory processing of large datasets
- can work with distributed datasets across multiple machines
- can spill to disk of data doesn't fit in memory
- complements well with Hadoop
	- reads and writes data from Hadoop's HDFS