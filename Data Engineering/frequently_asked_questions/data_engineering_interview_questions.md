# **SQL – Real-Time & Advanced Scenarios**

1. A query is running for 30 minutes instead of 2 minutes. How would you troubleshoot it?
2. A query is joining 5 large tables and taking too long. How would you identify which join is causing the performance issue?
3. A table contains duplicate customer records. How would you identify and remove duplicates while keeping the latest record?
4. How would you find duplicate records in a table and delete only the duplicates while keeping one copy?
5. How would you find the second-highest salary in each department?
6. Write a SQL query to fetch the third highest salary from the Employee table without using TOP or LIMIT.
7. How would you find employees whose salary is higher than their department's average salary?
8. Write an SQL query to find employees who earn more than their direct manager.
9. You have NULL values in a critical column. How would you handle them?
10. How would you compare two tables and identify inserted, deleted, and modified records?
11. A table contains multiple records for the same employee. How would you identify the records that have changed between two dates?
12. How would you find customers who made purchases in every month of a year?
13. How would you find the top 3 products by revenue in each category?
14. You have transaction records with multiple updates for the same customer. How would you retrieve only the latest transaction for each customer?
15. A customer has multiple addresses. How would you retrieve only the most recently updated address for each customer?
16. A source table has records for 2024 and 2025, but the business wants customers whose 2025 spending is higher than their 2024 spending. How would you write the query?
17. How would you identify gaps in a sequence of transaction IDs?
18. Write a query to find gaps in a sequence of IDs / dates (islands & gaps problem).
19. A customer has transactions on multiple dates. How would you find the month-over-month change in their spending?
20. How would you find customers who have never made a purchase in the last 12 months?
21. Given two tables — Customers and Orders — write a query to find customers who haven't placed any orders in the last 6 months.
22. Difference between a CTE and a temp table in terms of plan reuse and recompiles.
23. How do you write a dynamic PIVOT when the column values aren't known ahead of time?
24. What is partition switching in SQL Server, and why is it near-instant?
25. Difference between a full outer join and a union of left + right anti joins.
26. Explain the difference between INNER JOIN, LEFT JOIN, and CROSS JOIN with an example.
27. Difference between GROUP BY and a window aggregation. When does each return more rows?
28. What is MAXRECURSION on a recursive CTE, and how do you prevent runaway recursion?
29. A table has a start_date and end_date column. How would you calculate the total number of overlapping days across all rows?
30. What is the difference between LAG and LEAD, with a churn or month-over-month use case?
31. Difference between WHERE and HAVING, and why HAVING can't always be replaced by WHERE.
32. Calculate total revenue per customer using Orders and Order_Items tables.
33. Write SQL to get customers who spent the most in the last month.
34. How do you find the second most recent login for every user using Window functions?
35. Explain the MERGE statement. How is it different from UPSERT?
36. From a transactions table, write a query to identify users who placed more than 20 critical orders every week for at least 1 month.
37. What is the logical SQL query execution order?
38. View vs Materialized View
39. Query Optimization (Index vs Query Rewrite vs Data Model Changes)

# **PySpark & Apache Spark Architecture**

1. A Spark job has 200 tasks, but one task runs much longer than the others. What could be the reason?
2. How would you identify and handle data skew in a Spark join?
3. What is data skew in PySpark, and how would you handle skewed joins in large datasets (including Salting and AQE)?
4. When would you use Broadcast Join vs Sort-Merge Join?
5. What is a broadcast join, and when does the optimizer choose it over a shuffle join?
6. What are the different join strategies in Spark and when to use each?
7. What is the difference between repartition() and coalesce()?
8. Difference between repartition() and coalesce() with real production use cases.
9. A Spark job is taking 2 hours instead of 20 minutes. How would you troubleshoot it?
10. A Spark job works fine in development but is very slow in production. How would you troubleshoot it?
11. How would you optimize a slow PySpark job processing billions of records?
12. Scenario-based question: "You have a Spark pipeline failing randomly in production. How would you debug and fix it?"
13. When would you use cache() vs persist()?
14. How would you reduce unnecessary shuffle operations?
15. How would you identify whether a Spark job is suffering from excessive shuffling?
16. How does AQE (Adaptive Query Execution) improve Spark performance?
17. A PySpark job is running out of memory (OutOfMemoryError). What would you check first?
18. Your DataFrame has 500 partitions. How would you decide whether to increase or decrease them?
19. How would you process a 10 TB dataset efficiently using PySpark?
20. A Spark job generates thousands of small files. How would you identify and fix the issue?
21. What is the small-file problem and why does it hurt performance? How would you solve it?
22. A PySpark transformation creates a very large intermediate DataFrame and causes performance issues. How would you optimize it?
23. You have a 500 GB DataFrame and a 50 MB lookup DataFrame. How would you join them efficiently, and why?
24. A Spark job has frequent Garbage Collection and executor failures. What would you check and how would you fix it?
25. How would you handle a DataFrame with highly uneven partition sizes?
26. How would you optimize a PySpark job that performs multiple groupBy() operations?
27. A PySpark job uses a Python UDF and is running slowly. How would you identify the issue and optimize it?
28. A Spark join produces a huge increase in the number of records. How would you determine whether the join is creating duplicate/multiple matches?
29. A Spark job reads a large number of files with different schemas. How would you handle schema inconsistencies efficiently?
30. Spark Architecture (Driver, Executors, Cluster Manager, DAG Scheduler, and Task Scheduler)
31. How would you handle null values and incorrect casing in a PySpark DataFrame?
32. Write a PySpark logic to compute the difference in days between two status changes for each user.
33. How would you write a PySpark job to merge (upsert) data from a new file into an existing Delta table?
34. PySpark scenario: Join two DataFrames with different schemas using row numbers / Window functions.
35. PySpark live coding: Write code to find customers who spent more than ₹1000 from store S1 in the last month using Customer, Store, and Sales datasets (joins, add_months(), aggregation).
36. How does Spark memory management work? Explain caching, persistence, garbage collection, and executor memory tuning.
37. Write a PySpark query to rank employees department-wise using Window Functions.
38. Explain the difference between transform() and udf(). Which is more efficient?
39. Given a nested JSON file, how would you parse and flatten it in PySpark?
40. What is the difference between narrow and wide transformations in Spark? Can you give examples?
41. What is an RDD and why is it immutable?
42. reduceByKey() vs groupByKey() — why is one generally more efficient?
43. Explain the complete Spark execution flow from spark-submit to job completion (Jobs → Stages → Tasks & stage boundaries).
44. map() vs flatMap()
45. What is partition pruning?

# **Databricks & Delta Lake**

1. How would you implement an incremental load using Delta Lake?
2. You need to load only changed records from a source into a Delta table every day. How would you design the incremental load?
3. When would you use MERGE instead of INSERT?
4. How would you implement SCD Type 1 and Type 2 using Delta Lake?
5. What happens when multiple records match the same MERGE condition?
6. How would you recover accidentally deleted, overwritten, or modified Delta data (e.g. last 3 days)?
7. When would you use OPTIMIZE, Z-ORDER, and VACUUM?
8. A Databricks job suddenly starts taking 3× longer than usual. What would you investigate?
9. You notice a DataBricks job that runs fine in dev but takes 3× longer in production — how do you debug this?
10. A production job fails after processing 80% of the data. How would you restart it without processing everything again?
11. How would you design a Databricks solution for billions of records?
12. When would you use a Job Cluster vs an All-Purpose Cluster?
13. A Delta table has grown to several TB and queries are becoming slow. How would you optimize it?
14. How would you implement data quality checks before writing data into a Delta table?
15. A Delta table has frequent UPDATE and DELETE operations and is becoming slow. How would you optimize the table?
16. How would you implement a rollback strategy for a failed Delta Lake deployment?
17. How would you handle concurrent writes to the same Delta table?
18. How would you monitor and maintain Delta tables in a production environment?
19. A Delta table contains thousands of small files and query performance has degraded. How would you optimize it?
20. How would you use Delta Lake Change Data Feed (CDF) to capture INSERT, UPDATE, and DELETE changes?
21. A source schema changes unexpectedly during a Delta load. How would you handle schema enforcement versus schema evolution?
22. What is Delta Lake and what problems does it solve?

# **Auto Loader & Structured Streaming**

1. How would you design a pipeline to process millions of files arriving continuously?
2. What happens if a streaming job fails in the middle of processing?
3. How does checkpointing help Structured Streaming?
4. How would you handle schema evolution with Auto Loader?
5. An Auto Loader pipeline needs to handle files with new columns without stopping the pipeline. How would you implement schema evolution?
6. How would you prevent duplicate records in a streaming pipeline?
7. A streaming pipeline suddenly starts producing duplicate records. How would you identify and prevent them?
8. A streaming pipeline starts building a large processing backlog. How would you troubleshoot it?
9. Files are arriving continuously in ADLS, but the Auto Loader pipeline is processing them slower than they arrive. How would you troubleshoot the backlog?
10. How would you handle late-arriving events in Spark Structured Streaming?
11. A streaming pipeline restarts after a failure and you notice some records are processed again. How would you ensure exactly-once or effectively-once processing?
12. How would you process only specific file types from an ADLS folder using Auto Loader?
13. How would you monitor whether a streaming pipeline is keeping up with incoming data?
14. How would you design a streaming pipeline so that bad records do not stop the entire stream?
15. What is the difference between Auto Loader's directory listing mode and file notification mode, and when would you use each?
16. How would you control the number of files processed in each micro-batch when the incoming file rate is very high?
17. How would you handle unexpected or malformed columns using Auto Loader's rescuedDataColumn?

# **Unity Catalog**

1. How would you implement different access levels for different teams?
2. How would you secure sensitive columns such as PII?
3. A developer can access a table but should not be able to see sensitive columns. How would you implement this requirement?
4. A user needs access to a table but should only see selected columns. How would you design this using Unity Catalog?
5. How would you troubleshoot a user who suddenly loses access to a table?
6. A user can access a catalog but cannot query a particular table. How would you troubleshoot the permissions?
7. How would you use data lineage and audit logs during troubleshooting?
8. Your company has Development, Testing, and Production environments. How would you design Unity Catalog catalogs, schemas, and access permissions for them?
9. How would you give a team read access to multiple tables without granting unnecessary permissions?
10. How would you implement row-level security for different users or departments?
11. How would you track which users accessed sensitive data in Unity Catalog?
12. How would you configure Storage Credentials and External Locations to securely access ADLS data through Unity Catalog?
13. How would you use groups and service principals to manage access for developers, testers, and production jobs?
14. What is Unity Catalog, data lineage, and what do OPTIMIZE vs VACUUM actually do?

# **Azure Data Factory (ADF) & Integration**

1. How would you design an ADF pipeline that triggers Databricks notebooks dynamically?
2. If one activity fails in a pipeline containing 20 activities, how would you continue processing the remaining activities?
3. How would you implement retry and failure handling in ADF?
4. How would you pass parameters from ADF to Databricks?
5. You need to pass table name, load type, source path, and execution date from ADF to Databricks. How would you design the parameter flow?
6. How would you design a metadata-driven pipeline instead of creating separate pipelines for every table (e.g. 100+ tables)?
7. A pipeline succeeds, but the target table contains incomplete or 0 records. How would you troubleshoot it?
8. An ADF pipeline fails randomly. How would you determine whether the problem is with ADF, the source, or the target?
9. How would you implement logging and auditing for every pipeline execution?
10. An ADF Copy Activity suddenly becomes very slow. What would you investigate?
11. How would you design an ADF pipeline that can restart from the failed point instead of processing everything again?
12. ADF successfully triggers a Databricks notebook, but the notebook fails. How would you capture the error and make the ADF pipeline fail correctly?
13. How can you dynamically copy multiple tables from an on-prem SQL Server to Azure Data Lake using ADF?
14. You need to delete files from multiple containers daily before writing new data. ADF doesn't support wildcards across containers in one delete activity. How would you design this?
15. How do you handle parameterization and dynamic content in ADF pipelines for reusability?
16. Describe how you implement error handling, alerting, and monitoring in ADF.
17. What is the difference between an Azure Integration Runtime (IR) and a Self-Hosted IR?

# **Data Modeling & Warehousing**

1. Design a Vendor Payment System (Fact vs Dimension, Business Events mapping, Granularity, Payment Batch vs Order-Level Tracking).
2. Star vs Snowflake Schema — differences and when to choose one over the other.
3. Design a Data Model for a Lending Book.
4. Normal Forms and Normalization vs Denormalization (why warehouses denormalize on purpose).
5. SCD (Slowly Changing Dimensions) Types (Type 1, 2, 3) and implementation in Spark/Delta Lake.
6. Choosing the right schema for a given data application + justification.
7. Row vs. Columnar Storage
8. How do you handle a fact that arrives with an unknown dimension key (inferred / late-arriving member)?
9. Difference between OLTP and OLAP systems — and why you don't report directly off OLTP.
10. How do you handle currency conversion in a global sales model with daily rates?
11. How do you design a data lake vs a data warehouse, and when would you use each?
12. Explain dimensional modeling and how you would design a sales fact table with product and customer dimensions.

# **ETL & Data Pipelines**

1. End-to-End Design of Data Pipelines (Trade-offs, Scaling, Challenges & Handling).
2. Designing a batch data pipeline
3. Scenario: A nightly load must be restartable from the point of failure. How do you design checkpointing?
4. What is dbt, and what problem does it solve in the transformation layer?
5. Scenario: A source sends the same file twice on some days. How do you dedupe idempotently at load time?
6. How do you monitor pipeline SLAs and alert on data-freshness breaches?
7. How would you implement audit columns and change tracking in a dimension load?
8. What is idempotency in a data pipeline and why does it matter for reruns?
9. How would you make a data pipeline idempotent?
10. How do you handle duplicate records and late-arriving data in ETL pipelines?
11. How do you handle schema evolution in data pipelines (like Avro, Parquet, or JSON)?
12. What are common data quality checks you implement in pipelines?
13. How do you ensure pipeline reliability and monitoring in production?
14. Your pipeline is stuck in a Queued state. What are the top 3 things you would check?
15. How do you manage secrets securely in a production pipeline?
16. If you are asked to ingest PDF and image files into your pipeline and make them queryable, how would you design it?

# **Data Engineering Architecture & System Design**

1. How would you design a pipeline for millions of records arriving every hour?
2. How would you handle late-arriving data in pipeline design?
3. How would you design a pipeline that can recover automatically after failure?

# **Cloud & Distributed Systems**

1. Partitioning Strategies, Data Sharding, and Bucketing (which improves join performance better and why).
2. Consistent Hashing
3. Kafka Overview and how to decide between using Kafka, Kinesis, or Pub/Sub for streaming ingestion.
4. Explain how you would build a Medallion Architecture (Bronze-Silver-Gold) in Databricks / Data Lakehouse to process daily sales data.
5. What are the key differences between Dedicated SQL Pool and Serverless SQL Pool in Synapse?
6. You are asked to ingest and transform 100GB of CSV files daily in Synapse. What is your approach?
7. How would you design an ADF pipeline to move data from multiple on-prem SQL databases to Azure Data Lake and then load into Synapse Analytics?
8. Explain your approach for incremental and CDC (Change Data Capture) data loading in Azure.
9. How would you design a cost-efficient pipeline in Azure using adf, databricks, and adls?
10. Suppose your pipeline needs to run across AWS and Azure together (multi-cloud). How would you design secure and cost-effective data access?
11. What are the core Hadoop components and their responsibilities, and what limitations of MapReduce did Spark address?
12. What is a RecordReader and Combiner?
13. Explain an end-to-end AWS Glue ETL pipeline you have worked on (validation & cleaning using PySpark in Glue).
14. Athena vs Redshift — where have you used them, and what are the different Redshift distribution styles?
15. Your Python Lambda needs a library that isn't available in the default runtime. How would you deploy it? (packaging dependencies/Lambda Layers, handling psycopg2/pandas)
16. What is the difference between Hive external and managed tables?

# **Streaming & Real-Time Processing**

1. What are the differences between batch and streaming pipelines? Give real-life examples.
2. How do you handle late-arriving data or out-of-order events in streaming?

# **Python & Programming Concepts**

1. How would you read a large CSV file in chunks, filter only required rows, and write the output into a new file using Python?
2. You have a nested dictionary of JSON records from an API. How would you flatten it and load it into a Pandas DataFrame?
3. Write a Python script to remove duplicates from a list while maintaining the original order.
4. In Python, how would you optimize data transformations when processing a 10GB CSV file?
5. How do you handle and remove duplicates efficiently using pandas?
6. What are decorators?
7. Explain \*args and \*\*kwargs.
8. Explain a real scenario where you implemented exception handling.

Given: \[1, 2, 3, 4\], Generate the prefix sum: \[1, 3, 6, 10\]. Follow-ups: Explain the approach and its time complexity.