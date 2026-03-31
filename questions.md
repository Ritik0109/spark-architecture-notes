# EPAM Systems:
### YOE: 4+
### CTC: 22LPA+


## L0
The first round is an unmonitored online test on Codility, which includes coding problems, SQL queries, and multiple-choice questions (MCQs)


## L1
### Spark / PySpark
1.	What is Salting in Spark and how it works? 
2.	How to calculate number of stages, jobs and tasks 
3.	Cache vs Persist 
4.	How to release the cache data once it's done (unpersist) 
5.	What is data skew? 
6.	Repartition vs Coalesce 
7.	sparkContext vs sparkSession 
8.	Broadcast join – If default size is 10 MB for small table but we have 2 tables of 5GB and 1GB, what to do? How to check if broadcast join can be done (executor memory)? 
9.	Explain Spark Architecture 
10.	How Spark achieves fault tolerance 
11.	Join strategies in Spark 
12.	Partition pruning 
13.	How UDF affects performance 
14.	What happens if storage level is memory + disk and data doesn’t fit in memory? 
15.	RDD vs DataFrame (use cases) 
16.	Map vs FlatMap 

### Databricks / Delta Lake
1.	What is Delta Table? 
2.	Hive Metastore vs Unity Catalog 
3.	Optimizations in Databricks / Delta (Z-order, OPTIMIZE, VACUUM, etc.) 
4.	Types of clusters 
5.	Photon in cluster 
6.	Databricks execution shows “stages skipped” – what is it? 
7.	What is Service Principal? 
8.	How do you check for data quality? 
9.	Data governance 

### Azure (ADF / ADLS / Cloud)
1.	Explain ADF project 
2.	What is ADLS? 
3.	Storage tiers in Azure 
4.	Tumbling trigger vs Schedule trigger 
5.	How do you send mail for error in ADF? 
6.	How will you debug and resolve ADF pipeline errors? 
7.	How will you enable logging in ADF pipelines? 
8.	Can we customize alerts in ADF? 
9.	If there is no data in source and pipeline fails, how to handle it? 
10.	Will copy activity fail if no data in source? 
11.	How to ingest from on-prem to Azure Blob Storage and implement incremental load? 
12.	If there is no watermark/timestamp column, how do you implement incremental load? 
13.	How to copy only CSV files from ADLS when multiple formats exist? 
14.	How do you run and validate transformations in notebooks? 

### SQL / Database / Data Warehousing
1.	What is indexing? 
2.	What is deadlock in SQL? 
3.	CTE vs Subquery – which is efficient? 
4.	WHERE vs HAVING – can both be used together? 
5.	Explain ACID transactions 
6.	Data Warehouse vs Data Lake 
7.	SCD Type 1 vs Type 2 – how it works and implementation 
8.	CDC vs SCD 
9.	Fact vs Dimension table 
10.	Star vs Snowflake schema 
11.	IN vs EXISTS 
12.	How do you optimize SQL queries? 
13.	Normalization 
14.	Aggregate query vs Analytical query 
15.	Correlated vs Non-correlated subquery 
16.	Surrogate key 
17.	How do you handle NULL values in aggregate functions? 
18.	Stored procedure 
19.	Data integrity 
20.	Window functions – Rank vs Dense Rank (use cases) 
21.	Given two tables – output of inner join, left join, right join, full outer join 
22.	Find highest salary from each department and employee count 
23.	Find employees whose salary increased from previous year 

### Python
1.	Inheritance and method overriding 
2.	Tuple vs List vs Set 
3.	Decorators (with real-life use cases) 
4.	*args vs **kwargs 
5.	Exception handling 
6.	init and name 
7.	Deep copy vs Shallow copy 
8.	Multithreading 
9.	Generators 

### PySpark / Coding / Practical
Python Coding:
1.	Count vowels in each string from a list 
2.	Substring index problem:
txt = 'Atlassian is ssiamazing'
pat = 'ssi'
output = 4
3.	2D array output-based question 
SQL Coding:
1.	Find sum of digits of a number 
2.	Analytical queries using window functions 

### Data Engineering Concepts / Scenarios
1.	Data skew handling techniques 
2.	Handling large joins with duplicate keys 
3.	20 TB data transfer with vs without partitioning – performance comparison 
4.	Data modelling scenario (e.g., Pharma client) 
5.	How to check data quality in pipelines 
6.	CI/CD in Azure (ADF/Databricks) 
7.	UAT vs Dev vs Prod – deployment strategy 
8.	How to deploy only your changes when multiple developers are working on same branch

## L2
### Project / Scenario-Based
1.	Explain your recent project 
2.	How to ensure data quality from client? 
3.	How to gather requirements from client and communicate effectively? 
4.	How do you validate that the solution matches client requirements? 
5.	How to organize testing of Databricks workflows? 
6.	How do you manage reusable code across multiple notebooks (common functions)? 
7.	Given a large PySpark code (~40–50 lines), how will you optimize it for production? 

### Agile / Delivery (kept minimal, relevant)
1.	Which development methodology are you using? (Agile – Scrum) 
2.	Why Scrum? 
3.	What are Story Points? Story Points vs Time 

### Spark / PySpark
1.	How to find Jobs, Stages, and Tasks in Spark? 
2.	What is AQE (Adaptive Query Execution)? 
3.	What is Salting and how it works? 
4.	AQE vs Salting 
5.	Which is better: AQE or Salting and why? 
6.	In your project, what did you use (AQE/Salting) and why? 

### Distributed Systems (Relevant Concept)
1.	Explain CAP Theorem 
2.	Consistency vs Availability – what should we choose and why? 
3.	Why is it impossible to achieve both Consistency and Availability fully? 

### SQL / Database
1.	Types of indexes 
2.	Clustered vs Non-clustered index 
3.	How to ensure query uses index? 
4.	SQL problem: 
o	Find top 3 students with most house points in each house 
o	Output: house name, student name, points 

### Python
1.	OOP-based coding problem: 
o	Create base class Account (owner, balance, deposit, withdraw) 
o	Create subclass SavingsAccount (interest_rate, add_interest method) 
o	Demonstrate usage with deposit, withdrawal, interest calculation 

### Data Engineering Concepts
1.	Is Landing Zone part of Medallion Architecture? If not, why?


### Other Project related scenario based:
1.	How would you investigate and troubleshoot if a Spark job is running very slowly in production?
2.	A Spark job is failing with an OutOfMemory error during execution. How would you debug and resolve the issue?
3.	You need to join two very large datasets in Spark, and the join is taking a long time. How would you optimize it?
4.	During job execution you observe that one task takes significantly longer than others in the same stage. What could be the reason and how would you fix it?
5.	A pipeline running on Spark works correctly but takes too long to process daily data. What steps would you take to optimize the pipeline performance?
