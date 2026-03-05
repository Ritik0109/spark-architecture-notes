# Spark Architecture

## Deployment Modes

### Client Mode
Driver resides on client machine. Application Master is responsible for requesting for executors initially.
<img width="746" height="373" alt="image" src="https://github.com/user-attachments/assets/cb438ce7-fc2b-484d-ba9e-d047ce1aeb7d" />


### Cluster Mode
Driver resides on Cluster along with Application Master [AM]. When user submits a Spark Application using spark-submit utility, resource manager immediately deploys an AM along with Driver. Now Driver is responsible for requesting Executor nodes from Resource Manager [RM]. Spark will now pass instruction to drivers directly and executors will run tasks and report back to the driver which finally submit the output to the user.
<img width="757" height="373" alt="image" src="https://github.com/user-attachments/assets/dd140aee-ce2f-4ddd-9161-1d7cf138b3f9" />


### Local Mode
Spark will create a driver and executors in the same container on your PC inside a JVM. There is no requirement of a RM.

## Catalyst Optimizer

<img width="742" height="369" alt="image" src="https://github.com/user-attachments/assets/10d6cc35-9c3f-40ef-9d89-832bf5f1bdb4" />

Spark SQL uses the Catalyst optimizer to optimize structured queries expressed in SQL or via the DataFrame/Dataset APIs. When a query is submitted on the driver, Spark first creates an unresolved logical plan. The Analyzer then resolves references (columns, tables, functions, etc.) using the catalog to produce a resolved logical plan.

Next, Spark applies rule-based optimizations to generate an optimized logical plan (such as predicate pushdown, projection pruning, and constant folding). Based on this optimized logical plan, Spark generates one or more physical plans. Using a combination of heuristics and an optional cost-based optimizer (when statistics are available), Spark selects the most efficient physical plan by considering factors like scans, shuffles, and execution cost. Finally, the selected physical plan is executed across the executors.

## Internals of Spark Architecture

<img width="726" height="268" alt="image" src="https://github.com/user-attachments/assets/e9d67470-357e-436b-943a-f2623d7a61e1" />

Inside the Spark resides the DAG Scheduler & Task scheduler.

**DAG: Directed Acyclic Graphs** → graph of operations. This is created by Spark and only triggered during an action.

### Spark uses DAG to:
- Optimize execution
- Minimize data shuffling
- Combine transformations (pipeline)
- Improve fault tolerance

**DAG Scheduler** converts jobs to stages which is based on shuffle boundaries. It is responsible for making spark fault tolerant using the DAG lineages. If any partition fails, it uses DAG to recompute from parent transformations.

**Task Scheduler** takes the Stages from the DAG Scheduler and breaks them into tasks. Sends it to the executors. It keeps monitoring task execution. Running tasks in parallel via the executors. It is also responsible to rerun tasks in case of a failure. The tasks are launched via Cluster manager.

## Spark Optimization Techniques

### Prioritize the following:
- Reading as little data as possible through partitioning and efficient binary formats
- Making sure there is sufficient parallelism and no data skew on the cluster using partitioning
- Using high-level APIs such as the Structured APIs as much as possible to take already optimized code

### 1. OOM (Out of Memory) Issue

#### Overview
Spark OOM can happen either at Driver or Executor level. Driver OOM usually happens due to collect or large broadcast. Executor OOM is mostly due to skewed data, improper memory configuration, high concurrency, or caching large datasets.

#### Driver OOM Causes:
- Using Collect() for big files which pushes a driver OOM
- Broadcasting a big table compared to driver memory

#### Executor OOM Causes:
- Yarn memory overhead being small causing OOM
- Executor High concurrency. Insufficient distribution of memory in cases where number of cores are high
- Big Partition: Data skewness issue where spark is not able to spill to disk (eg: high number of data columns in same row cannot be split eg: text paragraphs), Skewed Joins, Repartitions
- Caching big data in memory (spark cannot spill data to disk)
- Cartesian join and Explode operations
- Long GC cycles

### 2. Memory in Executors
- **JPM process memory in executors** start Java heap memory
- **Memory Overhead** in Spark is the extra off-heap memory allocated to executors for shuffle, native libraries, Python processes, and network buffers. If it's too small, YARN kills the container even when heap memory is available.
- **On-heap Memory vs Off-heap Memory**

<img width="1094" height="604" alt="image" src="https://github.com/user-attachments/assets/056ca7a3-96d1-4aed-ad16-ed5ffbeac88f" />


## Other Topics

### 1. AQE (Adaptive Query Execution)
Adaptive Query Execution is an optimization in Apache Spark (3.0+) which uses actual runtime statistics to actively optimize the spark query plans during runtime. Traditionally, Spark generates a physical plan before execution using estimated statistics (like table size, row count, etc.). However, these estimates may be inaccurate. An example would be spark read and estimated a table to be very big, but it was small. If this is checked during runtime via statistics, spark will broadcast this table using AQE optimization.

### 2. Tungsten
Tungsten is Spark's optimized execution engine that improves performance by using off-heap memory management and whole-stage code generation.

**It improves:**
- **Memory Management** — Reduces JVM garbage collection overhead by leveraging off-heap memory management and efficient binary formats.
- **Whole-Stage Code Generation** — Generates much more optimized and efficient low-level machine bytecode at runtime and combine them into single optimized code block to optimize query execution.

### 3. UDF (User Defined Functions)
UDF allows you to write custom logic in Python and apply it to DataFrame columns when built-in Spark SQL functions are not sufficient.

- **Regular / Scalar UDF**: A regular UDF allows you to apply Python functions to columns in a DataFrame. They work at the row level, meaning each row is processed individually.
- **Pandas UDF**: Introduced in Spark 2.3, Pandas UDFs (also known as vectorized UDFs) provide a more efficient way to apply Python functions to Spark DataFrames. Pandas UDFs are faster than regular UDFs because they operate on batches of data (using pandas.Series), which is more efficient than operating row-by-row.

### 4. Managed & External Table
- **Managed Table**: All metadata and data files are managed by Spark. Once deleted everything is gone. Data stored at Default warehouse.
- **External Table**: Only metadata is maintained by Spark. Deleting does not affect the data files. Created using 'Location' keyword with Create table query and data is stored at user defined location.

### 5. Data Caching (Cache vs Persist)

#### Caching
An optimization method to cache / load the dataframe/RDD into spark memory. Useful when using the df in multiple places, helps avoid recomputing the df from start and saves time cost. Caching happens only after the first action is triggered. Avoid Caching if data is small, recomputation cost is low or dataset is big.

**Syntax:** `df.cache()`

#### Persist
Similar to Caching but here one can use a combination of Memory & Disk for loading data. Unpersist method to be run after job/session is no longer used. If memory is insufficient, data spills to disk, prevents recomputation.

**Storage Levels:** 
1. MEMORY_ONLY
2. MEMORY_AND_DISK
3. MEMORY_ONLY_SER
4. MEMORY_AND_DISK_SER
5. DISK_ONLY

**Syntax:** `df.persist(StorageLevel.MEMORY_AND_DISK)`, `df.unpersist()`

### 6. Repartition vs Coalesce

#### Repartition
A method which allows user to increase or decrease partitions by full shuffle and redistributes the data across partitions based on the number specified. Mostly used in cases to improve parallelism or where data is skewed so to evenly distribute the data across partitions. Repartition by column is allowed.

**Syntax:** `df.repartition(<NoOfPartitions>)`, `df.repartition(<NoOfPartitions>, <column>)`

#### Coalesce
A method used to decrease (reduce) the number of partitions by moving the data from one partition to another without creating a Shuffle in the partitions. A common use case would be output the data in one file for business usecase.

**Syntax:** `df.coalesce(<NoOfPartitions>)`

### 7. Broadcast Variables
This concept is similar to Broadcasting Joins, it caches a read-only variable on all worker nodes (executors), rather than sending a copy of it with every task to avoid repeated data transmission. **Usecase:** Small lookup tables

### 8. Accumulators
A shared value that can be accumulated. Worker tasks on a Spark cluster can add values to an Accumulator but only the driver program is allowed to access its value. Updates from the workers get propagated automatically to the driver program.

### 9. Action vs Lazy Evaluation

#### Lazy Evaluation
Spark lazily evaluates any query passed by the end user meaning it will not execute transformations immediately. It will lazily create a DAG and wait until an Action is being called.

**Importance:**
- Enables query optimization
- Reduces unnecessary computation
- Allows Spark to optimize the full DAG before execution

#### Action
An Action is taken by Spark only when an action keyword is invoked (such as collect(), count(), show(), save(), etc.).

### 10. Transformation Types: Narrow vs Wide
- **Narrow Transformation** is where a job can be processed without a data shuffle and usually completes in a single stage.
- **Wide Transformations** require a data shuffle across cluster and is broken down into stages. This is a very costly operation due to network I/O, disk I/O, and data serialization. This happens in cases of joins, groupby, sort, countDistinct where a key-based operation is performed.

### 11. Checkpoint vs Cache

#### Checkpointing
Checkpointing saves an RDD or DataFrame to reliable storage and truncates its lineage so that future references to this RDD point to those intermediate partitions on disk rather than recomputing the RDD from its original source.

### 12. Data Skewness
- A skew event occurs when a partition holds much more data than other partitions
- All partitions will be able to execute task quickly whereas the skewed partition will take longer to process causing job slowdown
- Salting is used to reduce the skewness and distribute the data more evenly across partitions

### 13. Salting and Types

#### Overview
A technique to solution the problem of data skewness. A salt key is created using a combination of skewed join key and random value.

**Example:** `salted_key = skewed_key + "_" + rand(0, N)`

Non-skewed keys remain mostly unchanged to avoid unnecessary reshuffling of unaffected partitions. Salted key is used for partitioning, which improves data distribution across partitions.

#### Drawback:
- Introduces additional overhead in terms of an extra key dimension
- After join or aggregation, a desalting step may be needed to restore the original keys

#### Types: [Not for interview POV]
- **Random Number Salting**: Append a random number to the skewed key
- **Hash Based Salting**: Use a hash function to generate salt values
- **Round-Robin Salting**: Assign salt values in a round-robin manner across N partitions
- **Composite Salting**: Combine the skewed key with another column or multiple salts

### 14. Bucketing vs Partitioning

#### Bucketing
An optimization where data is distributed across multiple buckets or files based on the hash of a column value. It works by specifying a column and number of buckets during the creation of the DataFrame. Spark then applies a hash function to the specified column and divides the data into buckets corresponding to the hash values.

#### Partitioning
Refers to the division of data into smaller, more manageable chunks known as partitions. Partitions are the basic units of parallelism in Spark, and they allow the framework to process different portions of the data simultaneously on different nodes in a cluster.

**Types:**
1. **Partitioning in memory**: can be done using repartition or coalesce
2. **Partitioning on disk**: Physical partitioning of data files using partitionBy()

### 15. File Formats & IO
- CSV
- JSON
- Columnar (Parquet/ORC)
- Delta

### 16. Types of Joins [Not for interview POV]

#### Shuffle Hash Join (SHJ)
- Join tables are shuffled across cluster based on hash of join keys
- Rows with the same join key hash value are sent to the same partition
- Within each partition, Spark builds a hash table on the smaller side and probes it using the larger side

#### Broadcast Join
- Broadcasts the smaller table/DataFrames to all the partitions i.e. a local copy of smaller table
- By default, works on tables having data ≤ 10MB, but this threshold can be increased
- No Shuffle is needed

#### Sort Merge Join (SMJ)
- Used for joining large tables where broadcast is not feasible
- Both tables are shuffled on the join key and sorted within each partition and finally merged by scanning the sorted data
- **Bucketed Sort Merge Joining Strategy [Optimization]**:
  - Optimization where Spark avoids full shuffle if both tables are bucketed on the same join key with the same number of buckets
  - **Conditions**: tables are bucketed, bucket column is join key, same no of buckets
  - Implemented when writing table `bucketBy(<column name>)`

#### Broadcast Nested Loop Join

#### Cartesian Join

### 17. Types of Partitioners [Not for interview POV]
- **Range Partition**: based on range of key values
- **Hash Partition**: based on hash key values (hash(key) % numPartitions)
- **Round Robin Partition**: random distribution of data in partitions
- **Single Partition**: whole data in single partition

### 18. Spark Submit Flow & Serialization (PySpark Internals)

#### Spark Submit Flow
spark-submit is the client program that starts your application.

**Flow:** spark-submit → driver → jobs → stages → tasks → executors → serialization/deserialization → results

#### Serialization
Serialization occurs when converting objects into a stream of bytes and vice-versa (De-Serialization) in an optimal way to transfer over nodes of network or to store it in file/memory buffer. Spark provides two serialization libraries: Java (default), Kyro (recommended).

Serialization is mostly done by the driver and executors. Each task is serialized and sent to executors. Executors run the tasks on worker nodes and results are sent back to the driver if needed. Spark handles deserialization of results on the driver side.

### 19. Spark Context vs Spark Session
- **Spark Context**: Entry point for RDD, Low-level APIs and other methods as separate [primary entry point for below Spark 2.0]
- **Spark Session**: Entry point for High-level APIs [unified entry point]. Internally creates and manages a SparkContext.

### 20. Missing & Duplicates Values
- **Missing values**: `df.fillNa()`, `df.na.drop()`, `df.na.replace(to_replace, value, subset=[...])`
- **Duplicates**: `df.dropDuplicates()`

### 21. Watermarks and checkpointLocation

#### Watermarking
Watermarking is used in event-time processing to handle late-arriving data. Without watermarking, Spark would need to wait indefinitely for late data. We can define a watermark threshold. Spark keeps track of the maximum event time seen so far. It calculates a watermark = max event time – allowed delay. Any data older than the watermark is considered too late and is dropped.

#### checkpointLocation
checkpointLocation is used to enable fault tolerance and state recovery in Structured Streaming. Spark needs to know what data was already processed. It must resume from the last successful state.
