# Data Engineering Master Interview Questions Bank

A comprehensive collection of 351 real-world Data Engineering interview scenarios, production incident diagnostics, live coding challenges, and enterprise system designs.

All questions are organized by **Recommended Chronological Study Order** across the 10 canonical data engineering disciplines and partitioned into **Progressive Difficulty Tiers** (Foundations $\to$ Practical Analytics $\to$ Engine Internals $\to$ Enterprise Architecture & Incident Triage). Each question is accompanied by its difficulty rating and direct links to comprehensive, production-grade solutions in the companion deep-dive guides.

---

## Difficulty Legend & Study Tier Breakdown

| Difficulty Level | Scope & Expectations | Typical Experience Level |
| :--- | :--- | :--- |
| **`[Easy]`** | Core definitions, fundamental execution mechanics, syntax, relational concepts, standard APIs. | 0 – 2 Years / Junior Data Engineer |
| **`[Medium]`** | Analytical windowing, incremental loading, idempotency patterns, semi-structured shredding, pipeline design. | 2 – 5 Years / Mid-Level Data Engineer |
| **`[Hard]`** | Distributed engine internals, memory tuning, skew salting, AQE, file compaction, low-level concurrency. | 5 – 8 Years / Senior Data Engineer |
| **`[Expert]`** | Sub-second real-time event streaming, petabyte-scale lakehouse architecture, multi-tenant governance, Sev-1 triage. | 8+ Years / Staff & Principal Architect |

---

## Master Topic Navigation & Curriculum Index

| Topic # | Engineering Discipline | Total Questions | Difficulty Spread | Companion Deep-Dive Guide |
| :---: | :--- | :---: | :--- | :--- |
| **01** | [SQL – Real-Time & Advanced Scenarios](#01-sql--real-time--advanced-scenarios) | 67 Questions | Easy: 14 \| Medium: 30 \| Hard: 17 \| Expert: 6 | [`01_sql_faq.md`](01_sql_faq.md) |
| **02** | [Python & Programming Concepts](#02-python--programming-concepts) | 28 Questions | Easy: 10 \| Medium: 9 \| Hard: 7 \| Expert: 2 | [`02_python_and_programming_faq.md`](02_python_and_programming_faq.md) |
| **03** | [Data Modeling & Warehousing](#03-data-modeling--warehousing) | 25 Questions | Easy: 5 \| Medium: 10 \| Hard: 8 \| Expert: 2 | [`03_data_modeling_and_warehousing_faq.md`](03_data_modeling_and_warehousing_faq.md) |
| **04** | [Cloud & Distributed Systems](#04-cloud--distributed-systems) | 16 Questions | Easy: 3 \| Medium: 7 \| Hard: 4 \| Expert: 2 | [`04_cloud_and_distributed_systems_faq.md`](04_cloud_and_distributed_systems_faq.md) |
| **05** | [Apache Spark & PySpark Architecture](#05-apache-spark--pyspark-architecture) | 65 Questions | Easy: 10 \| Medium: 26 \| Hard: 21 \| Expert: 8 | [`05_apache_spark_and_pyspark_faq.md`](05_apache_spark_and_pyspark_faq.md) |
| **06** | [Databricks & Delta Lake](#06-databricks--delta-lake) | 32 Questions | Easy: 4 \| Medium: 14 \| Hard: 10 \| Expert: 4 | [`06_databricks_and_delta_lake_faq.md`](06_databricks_and_delta_lake_faq.md) |
| **07** | [Unity Catalog & Data Governance](#07-unity-catalog--data-governance) | 15 Questions | Easy: 3 \| Medium: 6 \| Hard: 4 \| Expert: 2 | [`07_unity_catalog_faq.md`](07_unity_catalog_faq.md) |
| **08** | [Auto Loader & Structured Streaming](#08-auto-loader--structured-streaming) | 29 Questions | Easy: 3 \| Medium: 12 \| Hard: 10 \| Expert: 4 | [`08_auto_loader_and_structured_streaming_faq.md`](08_auto_loader_and_structured_streaming_faq.md) |
| **09** | [Azure Data Factory (ADF) & ETL Pipelines](#09-azure-data-factory-adf--etl-pipelines) | 53 Questions | Easy: 5 \| Medium: 23 \| Hard: 17 \| Expert: 8 | [`09_etl_data_pipelines_and_adf_faq.md`](09_etl_data_pipelines_and_adf_faq.md) |
| **10** | [Data Engineering Architecture & System Design](#10-data-engineering-architecture--system-design) | 21 Questions | Medium: 3 \| Hard: 9 \| Expert: 9 | [`10_system_design_and_architecture_faq.md`](10_system_design_and_architecture_faq.md) |
| **Total** | **Master Question Collection** | **351 Questions** | **All 10 Modules Fully Answered & Indexed** | |

---

# **01. SQL – Real-Time & Advanced Scenarios**
*Companion Deep-Dive Guide: [`01_sql_faq.md`](01_sql_faq.md) (67 Questions & Production Solutions)*


### Level 1: Relational Foundations & Core Architecture (Easy / Medium)
1. **`[Easy]`** What is the logical SQL query execution order? → [Solution](01_sql_faq.md#q37-logical-sql-query-execution-order)
2. **`[Easy]`** View vs Materialized View — architectural differences and refresh mechanics. → [Solution](01_sql_faq.md#q38-view-vs-materialized-view)
3. **`[Easy]`** Explain the difference between INNER JOIN, LEFT JOIN, and CROSS JOIN with an example. → [Solution](01_sql_faq.md#q26-inner-join-vs-left-join-vs-cross-join-mechanics)
4. **`[Easy]`** Difference between WHERE and HAVING, and why HAVING can't always be replaced by WHERE. → [Solution](01_sql_faq.md#q31-where-vs-having-semantics-and-filter-scoping)
5. **`[Easy]`** Difference between GROUP BY and a window aggregation. When does each return more rows? → [Solution](01_sql_faq.md#q27-group-by-vs-window-aggregation-and-row-cardinality)
6. **`[Easy]`** You need to retrieve the names and salaries of employees from the employees table, but only for those working in the Finance department, and increase IT salaries by 10%. → [Solution](01_sql_faq.md#q55-filtered-projections--conditional-in-place-dml-updates)
7. **`[Easy]`** Write a SQL query to find all duplicate emails in a table named Person. → [Solution](01_sql_faq.md#q56-email-deduplication-in-person-table--relational-deletions)
8. **`[Medium]`** You have NULL values in a critical column. How would you handle them defensively? → [Solution](01_sql_faq.md#q9-defensive-handling-of-nulls-in-critical-columns)
9. **`[Medium]`** Difference between a full outer join and a union of left + right anti joins. → [Solution](01_sql_faq.md#q25-full-outer-join-vs-union-of-left-and-right-anti-joins)
10. **`[Medium]`** Explain the MERGE statement. How is it different from UPSERT? → [Solution](01_sql_faq.md#q35-merge-statement-semantics-vs-ansi-upsert)
11. **`[Medium]`** Difference between ROW_NUMBER(), RANK(), and DENSE_RANK() with identical data values. → [Solution](01_sql_faq.md#q54-window-ranking-mechanics-row_number-vs-rank-vs-dense_rank)
12. **`[Medium]`** What is the difference between LAG and LEAD, with a churn or month-over-month use case? → [Solution](01_sql_faq.md#q19--q30-month-over-month-spending-growth--lag-vs-lead-functions)
13. **`[Medium]`** Difference between a CTE and a temp table in terms of plan reuse and recompiles. → [Solution](01_sql_faq.md#q22-cte-vs-temp-table-execution-plan-reuse--recompiles)
14. **`[Medium]`** Write an SQL query to get the second highest salary from the Employee table with strict NULL fallback. → [Solution](01_sql_faq.md#q57-second-highest-salary-from-employee-table-with-strict-null-fallback)

### Level 2: Analytical Queries, Windowing & Aggregations (Medium)
15. **`[Medium]`** How would you find duplicate records in a table and delete only the duplicates while keeping one copy? → [Solution](01_sql_faq.md#q3--q4-identifying-and-purging-duplicate-records-while-keeping-one-copy)
16. **`[Medium]`** Write an efficient query to detect duplicates based on customer ID and timestamp / UpdatedDate and retain only the latest record. → [Solution](01_sql_faq.md#q60-customer-deduplication-by-timestamp--updateddate-deterministic-winner-selection)
17. **`[Medium]`** You have transaction records with multiple updates for the same customer. How would you retrieve only the latest transaction for each customer? → [Solution](01_sql_faq.md#q14-retrieving-only-the-latest-transaction-per-customer)
18. **`[Medium]`** A customer has multiple addresses. How would you retrieve only the most recently updated address for each customer? → [Solution](01_sql_faq.md#q15-retrieving-only-the-most-recently-updated-address-per-customer)
19. **`[Medium]`** How would you find the second-highest salary in each department? → [Solution](01_sql_faq.md#q5-second-highest-salary-in-each-department)
20. **`[Medium]`** Write a SQL query to fetch the third highest salary from the Employee table without using TOP or LIMIT. → [Solution](01_sql_faq.md#q6-third-highest-salary-without-top-or-limit)
21. **`[Medium]`** How would you find the top 3 products by revenue in each category? → [Solution](01_sql_faq.md#q13-top-3-products-by-revenue-in-each-category)
22. **`[Medium]`** How do you find the second most recent login for every user using Window functions? → [Solution](01_sql_faq.md#q34-second-most-recent-login-per-user-using-window-functions)
23. **`[Medium]`** How would you find employees whose salary is higher than their department's average salary? → [Solution](01_sql_faq.md#q7-employees-earning-more-than-department-average)
24. **`[Medium]`** Given the Employee table, write a SQL query that finds out employees who earn more than their direct manager. → [Solution](01_sql_faq.md#q8-employees-earning-more-than-their-direct-manager)
25. **`[Medium]`** How would you compare two tables and identify inserted, deleted, and modified records? → [Solution](01_sql_faq.md#q10-table-comparison-identifying-inserted-deleted-and-modified-records)
26. **`[Medium]`** A table contains multiple records for the same employee. How would you identify the records that have changed between two dates? → [Solution](01_sql_faq.md#q11-identifying-employee-records-changed-between-two-dates)
27. **`[Medium]`** A source table has records for 2024 and 2025, but the business wants customers whose 2025 spending is higher than their 2024 spending. How would you write the query? → [Solution](01_sql_faq.md#q16-customers-with-2025-spending-higher-than-2024-spending)
28. **`[Medium]`** A customer has transactions on multiple dates. How would you find the month-over-month change in their spending? → [Solution](01_sql_faq.md#q19--q30-month-over-month-spending-growth--lag-vs-lead-functions)
29. **`[Medium]`** Calculate total revenue per customer using Orders and Order_Items tables. → [Solution](01_sql_faq.md#q32-total-revenue-per-customer-from-orders-and-order-items)
30. **`[Medium]`** Write SQL to get customers who spent the most in the last month. → [Solution](01_sql_faq.md#q33-highest-spending-customers-in-the-last-month)
31. **`[Medium]`** Write an SQL query to find the top 3 drivers by average rating, only including drivers with more than 100 trips. → [Solution](01_sql_faq.md#q59-aggregated-driver-ratings-with-minimum-trip-thresholds-100-trips)
32. **`[Medium]`** Explain Window Functions and write a query to rank transactions by customer. → [Solution](01_sql_faq.md#q61-window-functions-customer-transaction-ranking--partitioned-over-clauses)
33. **`[Medium]`** Write a SQL query to calculate a running account balance for each customer transaction. → [Solution](01_sql_faq.md#q50-running-account-balance-calculation-with-explicit-frame-semantics)
34. **`[Medium]`** Find the difference in trip counts between two consecutive weeks for each driver. → [Solution](01_sql_faq.md#q44-driver-trip-count-variance-between-consecutive-weeks)
35. **`[Medium]`** Calculate the average number of user sessions over a specified 30-day window using SQL sessionization. → [Solution](01_sql_faq.md#q64-average-number-of-sessions-per-user-over-a-specified-window)
36. **`[Medium]`** Extract non-matching records between two tables (e.g., users in table A not in table B) using Anti-Joins. → [Solution](01_sql_faq.md#q65-anti-join-mechanics-extracting-non-matching-rows-across-datasets)
37. **`[Medium]`** Find the highest-grossing movie of the current month with multi-movie tie handling using Window Functions. → [Solution](01_sql_faq.md#q66-highest-grossing-movie-of-the-current-month--booking-system-analytics)
38. **`[Medium]`** Compute multi-level periodic sales rollups across daily, monthly, and yearly granularities using `GROUPING SETS` and `ROLLUP`. → [Solution](01_sql_faq.md#q67-periodic-sales-rollup-by-product-vendor-and-location)

### Level 3: Advanced Optimization, Gaps & Islands, and Storage Mechanics (Hard)
39. **`[Hard]`** How would you identify gaps in a sequence of transaction IDs? → [Solution](01_sql_faq.md#q17--q18-gaps-in-a-sequence-of-ids-and-consecutive-islands)
40. **`[Hard]`** Write a query to find gaps in a sequence of IDs / dates (islands & gaps problem). → [Solution](01_sql_faq.md#q17--q18-gaps-in-a-sequence-of-ids-and-consecutive-islands)
41. **`[Hard]`** Write an SQL query to find all numbers that appear at least three times consecutively. → [Solution](01_sql_faq.md#q40-numbers-appearing-at-least-three-times-consecutively)
42. **`[Hard]`** A table has a start_date and end_date column. How would you calculate the total number of overlapping days across all rows? → [Solution](01_sql_faq.md#q29-calculating-total-number-of-overlapping-days-across-date-ranges)
43. **`[Hard]`** How would you find customers who have never made a purchase in the last 12 months (or last 6 / 90 days)? → [Solution](01_sql_faq.md#q20--q21-identifying-customers-with-no-purchases-in-6-to-12-months)
44. **`[Hard]`** Write a SQL query to identify customers who have not performed any transactions in the last 90 days (Anti-Join vs NOT EXISTS). → [Solution](01_sql_faq.md#q62-90-day-dormant-customer-inactivity-detection-anti-join-vs-not-exists)
45. **`[Hard]`** Identify users with consecutive active months streaks (Gaps and Islands streak analysis via date normalization). → [Solution](01_sql_faq.md#q63-consecutive-month-order-detection--customer-streak-analysis)
46. **`[Hard]`** How would you find customers who made purchases in every month of a year (Relational Division)? → [Solution](01_sql_faq.md#q12-customers-purchasing-in-every-month-of-a-year-relational-division)
47. **`[Hard]`** Find customers who purchased in every single category at least once in last 90 days. How do you optimize this query for 500M customer records? → [Solution](01_sql_faq.md#q47-relational-division-at-massive-scale-purchased-in-every-category-across-500m-records)
48. **`[Hard]`** From a transactions table, write a query to identify users who placed more than 20 critical orders every week for at least 1 month. → [Solution](01_sql_faq.md#q36-users-placing-20-critical-orders-every-week-for--1-month)
49. **`[Hard]`** Write a query to find restaurants with a rating drop of more than 0.5 stars in the last 30 days compared to previous 30 days. → [Solution](01_sql_faq.md#q41-restaurant-rating-drop-analysis-across-consecutive-30-day-windows)
50. **`[Hard]`** Find users who booked a ride more than once within a 10-minute window (Event proximity via self-join vs window functions). → [Solution](01_sql_faq.md#q42-event-proximity-detection-users-booking-multiple-rides-within-10-minutes)
51. **`[Hard]`** Find top 3 cuisines by repeat order rate per city (repeat order = same user ordering from same cuisine within 7 days). → [Solution](01_sql_faq.md#q43-cohort-repeat-order-rates-top-3-cuisines-ordered-by-same-user-within-7-days)
52. **`[Hard]`** Given a rides table, find the most frequently traveled route in the past 30 days (bidirectional route normalization). → [Solution](01_sql_faq.md#q45-most-frequently-traveled-route-in-30-days-bidirectional-normalization)
53. **`[Hard]`** Write a query to find products that were viewed more than 100 times but never purchased in the last 30 days, ranking by view count per category. → [Solution](01_sql_faq.md#q46-high-interest-unconverted-products-viewed-100-times-without-purchase)
54. **`[Hard]`** Given a click-stream dataset, how would you compute per-session duration by device type from play/pause/stop events? → [Solution](01_sql_faq.md#q48-per-session-duration-by-device-type-from-clickstream-playpausestop-events)
55. **`[Hard]`** Write a SQL query to identify the top 10 users with the most watch hours in the last month. → [Solution](01_sql_faq.md#q49-top-10-users-by-video-watch-hours-in-the-last-month)

### Level 4: Enterprise Troubleshooting, Query Optimization & Physical Internals (Hard / Expert)
56. **`[Hard]`** What is MAXRECURSION on a recursive CTE, and how do you prevent runaway recursion? → [Solution](01_sql_faq.md#q28-recursive-ctes-maxrecursion--preventing-runaway-recursion)
57. **`[Hard]`** Write a recursive SQL query to analyse hierarchical employee data, management chains, and depth. → [Solution](01_sql_faq.md#q51-recursive-multi-level-organizational-hierarchy-analysis)
58. **`[Hard]`** How do you write a dynamic PIVOT when the column values aren't known ahead of time? → [Solution](01_sql_faq.md#q23-dynamic-pivot-when-column-values-are-unknown-ahead-of-time)
59. **`[Hard]`** What is partition switching in SQL Server / analytical databases, and why is it near-instant? → [Solution](01_sql_faq.md#q24-partition-switching-mechanics--near-instant-metadata-swaps)
60. **`[Hard]`** A SQL query using OFFSET 100000 takes several seconds on a large warehouse table. Why, and what pagination strategy would you use instead (keyset pagination)? → [Solution](01_sql_faq.md#q52-deep-pagination-performance-offset-100000-vs-keyset--seek-pagination)
61. **`[Hard]`** You add an index to improve a SQL query, but performance gets worse. How can indexing increase query cost? → [Solution](01_sql_faq.md#q53-why-adding-an-index-can-degrade-query-performance)
62. **`[Expert]`** A query is running for 30 minutes instead of 2 minutes. How would you troubleshoot it? → [Solution](01_sql_faq.md#q1-troubleshooting-a-slow-running-query-30m-to-2m)
63. **`[Expert]`** A query is joining 5 large tables and taking too long. How would you identify which join is causing the performance issue? → [Solution](01_sql_faq.md#q2-identifying-bottlenecks-across-5-joined-large-tables)
64. **`[Expert]`** Query Optimization: Indexes vs Query Rewrite vs Data Model Changes — when to choose which? → [Solution](01_sql_faq.md#q39-query-optimization-indexes-vs-query-rewrites-vs-data-model-changes)
65. **`[Expert]`** A source table has duplicate customer records arriving continuously due to upstream glitches. How do you design an automated, performant deduplication pipeline without dropping valid updates? → [Solution](01_sql_faq.md#q3--q4-identifying-and-purging-duplicate-records-while-keeping-one-copy)
66. **`[Expert]`** Relational division on 500M rows with category cardinalities changing dynamically: Explain bloom filters vs bitmap indices vs hash semi-joins. → [Solution](01_sql_faq.md#q47-relational-division-at-massive-scale-purchased-in-every-category-across-500m-records)
67. **`[Expert]`** Sessionization over 1B clickstream records: Compare SQL window function lag/lead vs session gap thresholding vs Spark stateful sessionization. → [Solution](01_sql_faq.md#q48-per-session-duration-by-device-type-from-clickstream-playpausestop-events)


---

# **02. Python & Programming Concepts**
*Companion Deep-Dive Guide: [`02_python_and_programming_faq.md`](02_python_and_programming_faq.md) (28 Questions & Production Solutions)*

### Level 1: Core Python Primitives & Idioms (Easy / Medium)
1. **`[Easy]`** Write a Python script to remove duplicates from a list while maintaining the original order. → [Solution](02_python_and_programming_faq.md#q3-order-preserving-list-deduplication)
2. **`[Easy]`** Check whether a string is a palindrome or not using an optimal two-pointer approach. → [Solution](02_python_and_programming_faq.md#two-pointer-palindrome-verification)
3. **`[Easy]`** Python Program to Find the Factorial of a Number (iterative vs memoized recursion). → [Solution](02_python_and_programming_faq.md#factorial-calculation-iterative-vs-memoized)
4. **`[Easy]`** Frequency of Words in a Sentence (string tokenization and frequency counting). → [Solution](02_python_and_programming_faq.md#word-frequency-tokenizer--counter)
5. **`[Easy]`** Find the smallest and largest number in an array in a single pass ($O(N)$). → [Solution](02_python_and_programming_faq.md#single-pass-extreme-value-detection-min-and-max)
6. **`[Easy]`** Write a Python program to pair common keys between two dictionaries and combine their values into tuples. → [Solution](02_python_and_programming_faq.md#q10-pairing-common-keys-between-disjoint-lists--dictionary-intersection)
7. **`[Easy]`** Write a Python program to find the frequency of duplicate characters in a given string. → [Solution](02_python_and_programming_faq.md#q11-character-frequency--duplicate-detection-in-high-volume-strings)
8. **`[Easy]`** What is the difference between `append()`, `extend()`, and `+` concatenation on a Python list in terms of memory reallocation and performance? → [Solution](02_python_and_programming_faq.md#q12-python-list-appending-mechanics-append-vs-extend-vs--vs-in-place-slicing)
9. **`[Easy]`** Write a Python function to extract the username from an email address using string manipulation vs regex. → [Solution](02_python_and_programming_faq.md#q13-email-username-extraction-with-resilient-regex--rfc-validation)
10. **`[Medium]`** Explain *args and **kwargs and their architectural utility in building reusable pipeline components. → [Solution](02_python_and_programming_faq.md#q7-variable-length-arguments-args--kwargs-in-pipeline-design)
11. **`[Medium]`** What are decorators? Explain with an example in the context of data engineering pipelines (e.g. retry decorator, timing wrapper). → [Solution](02_python_and_programming_faq.md#q6-python-decorators-in-production-data-pipelines)
12. **`[Medium]`** Explain a real scenario where you implemented exception handling and dead-letter queues (DLQ) in Python. → [Solution](02_python_and_programming_faq.md#q8-enterprise-exception-handling--dead-letter-queue-patterns)

### Level 2: Data Manipulation, Parsing & Algorithmic Foundations (Medium / Hard)
13. **`[Medium]`** You have a nested dictionary of JSON records from an API. How would you flatten it and load it into a Pandas DataFrame? → [Solution](02_python_and_programming_faq.md#q2-nested-json-flattening--shredding-into-pandas)
14. **`[Medium]`** How do you handle and remove duplicates efficiently using pandas? → [Solution](02_python_and_programming_faq.md#q5-high-performance-deduplication-in-pandas)
15. **`[Medium]`** Given: [1, 2, 3, 4], Generate the prefix sum: [1, 3, 6, 10]. Explain the approach and its time/space complexity. → [Solution](02_python_and_programming_faq.md#prefix-sum-array-transformation--algorithmic-complexity)
16. **`[Medium]`** Solve advanced Python coding questions on Strings, Lists, and Dictionaries in high-throughput pipelines. → [Solution](02_python_and_programming_faq.md#q9-advanced-data-manipulation-on-strings-lists-and-dictionaries)
17. **`[Medium]`** Sort a List Without Using the built-in sort Keyword (Pure Python Merge Sort $O(N \log N)$). → [Solution](02_python_and_programming_faq.md#sorting-without-built-in-sort-pure-python-merge-sort)
18. **`[Medium]`** Solve the Stock Span Problem in $O(N)$ time using a Monotonic Decreasing Stack. → [Solution](02_python_and_programming_faq.md#q14-the-stock-span-problem-via-monotonic-stack)
19. **`[Hard]`** How would you read a large CSV file in chunks, filter only required rows, and write the output into a new file using Python? → [Solution](02_python_and_programming_faq.md#q1-chunked-csv-reading--filtering-output-stream)
20. **`[Hard]`** In Python, how would you optimize data transformations when processing a 10GB CSV file (chunking, PyArrow engine, dtype downcasting)? → [Solution](02_python_and_programming_faq.md#q4-memory-optimization-strategies-for-10gb-csv-processing)
21. **`[Hard]`** Write a Python program to identify the most frequently accessed database tables from 100GB server logs without running out of memory. → [Solution](02_python_and_programming_faq.md#q15-large-scale-log-file-analysis--most-frequently-accessed-database-tables)
22. **`[Hard]`** Generate an Infinite Fibonacci Series Using a Python Generator (memory mechanics of iterators). → [Solution](02_python_and_programming_faq.md#infinite-generator-streaming-fibonacci-series)

### Level 3: Advanced Data Structures & Concurrency (Hard / Expert)
23. **`[Hard]`** Given a stream of integers, design a data structure to return the running median at any time (Dual Heaps). → [Solution](02_python_and_programming_faq.md#continuous-stream-running-median-via-dual-heaps)
24. **`[Hard]`** Implement an LRU (Least Recently Used) cache from scratch without using built-in OrderedDict (Doubly Linked List + Hash Map). → [Solution](02_python_and_programming_faq.md#least-recently-used-lru-cache-architecture)
25. **`[Hard]`** Detect a cycle in a directed graph representing pipeline task dependencies or trip routes between cities (Three-Color DFS). → [Solution](02_python_and_programming_faq.md#cycle-detection-in-directed-transportation-networks)
26. **`[Hard]`** Merge K sorted linked lists / data streams efficiently using a min-heap ($O(N \log K)$). → [Solution](02_python_and_programming_faq.md#merge-k-sorted-streams--linked-lists)
27. **`[Hard]`** Given a list of ride timestamps, find the longest continuous ride streak in $O(N)$ time using a hash set. → [Solution](02_python_and_programming_faq.md#longest-continuous-ride-streak--consecutive-sequence)
28. **`[Expert]`** Explain Multithreading vs Multiprocessing under the Python Global Interpreter Lock (GIL) and when to use each in data systems. → [Solution](02_python_and_programming_faq.md#multithreading-vs-multiprocessing-under-the-python-gil)


---

# **03. Data Modeling & Warehousing**
*Companion Deep-Dive Guide: [`03_data_modeling_and_warehousing_faq.md`](03_data_modeling_and_warehousing_faq.md) (25 Questions & Production Solutions)*

### Level 1: Warehousing Foundations & Schema Paradigms (Easy / Medium)
1. **`[Easy]`** Difference between OLTP and OLAP systems — and why you don't report directly off OLTP. → [Solution](03_data_modeling_and_warehousing_faq.md#q9-oltp-vs-olap-architecture--why-analytical-queries-bypass-oltp)
2. **`[Easy]`** Normal Forms and Normalization vs Denormalization (why analytical data warehouses denormalize on purpose). → [Solution](03_data_modeling_and_warehousing_faq.md#q4-normalization-vs-denormalization-in-data-warehouses)
3. **`[Easy]`** Star vs Snowflake Schema — differences and when to choose one over the other. → [Solution](03_data_modeling_and_warehousing_faq.md#q2--q6-star-schema-vs-snowflake-schema-architecture--selection-criteria)
4. **`[Easy]`** Row-Oriented vs Columnar Storage — physical storage mechanics and query execution profiles. → [Solution](03_data_modeling_and_warehousing_faq.md#q7-row-oriented-vs-columnar-physical-storage-mechanics)
5. **`[Medium]`** Explain the differences between Data Lake, Data Warehouse, and modern Data Lakehouse. → [Solution](03_data_modeling_and_warehousing_faq.md#q11-data-lake-vs-data-warehouse-vs-data-lakehouse-architecture)
6. **`[Medium]`** Explain the Medallion Architecture (Bronze, Silver, Gold) in a modern Lakehouse, and what are its advantages? → [Solution](03_data_modeling_and_warehousing_faq.md#q17-medallion-architecture-bronze-silver-gold-in-the-modern-lakehouse)
7. **`[Medium]`** What is the difference between a Microsoft Fabric Lakehouse and a Fabric Warehouse, and when should you choose each? → [Solution](03_data_modeling_and_warehousing_faq.md#q23-microsoft-fabric-lakehouse-vs-fabric-warehouse-architectural-comparison)

### Level 2: Dimensional Design & Fact/Dimension Mechanics (Medium / Hard)
8. **`[Medium]`** Explain dimensional modeling and how you would design a sales fact table with product and customer dimensions. → [Solution](03_data_modeling_and_warehousing_faq.md#q12-sales-fact-table-design-with-product-and-customer-dimensions)
9. **`[Medium]`** How would you design a sales fact table that supports both daily transaction grain and monthly snapshot aggregations? → [Solution](03_data_modeling_and_warehousing_faq.md#q20-dimensional-data-modeling-for-multi-level-sales-product-vendor-location)
10. **`[Medium]`** Choosing the right schema for a given data application + architectural justification. → [Solution](03_data_modeling_and_warehousing_faq.md#q2--q6-star-schema-vs-snowflake-schema-architecture--selection-criteria)
11. **`[Medium]`** Slowly Changing Dimensions (SCD Type 1, Type 2, and Type 3) and their implementation in Spark / Delta Lake. → [Solution](03_data_modeling_and_warehousing_faq.md#q5-slowly-changing-dimensions-scd-types-1-2-3-in-spark--delta-lake)
12. **`[Medium]`** How do you handle a fact that arrives with an unknown dimension key (inferred / late-arriving member pattern)? → [Solution](03_data_modeling_and_warehousing_faq.md#q8-handling-inferred-members--late-arriving-dimensions)
13. **`[Medium]`** How do you reduce cloud storage costs for petabyte-scale Delta/Parquet data lakes (storage tiering, small file compaction, and VACUUM policies)? → [Solution](03_data_modeling_and_warehousing_faq.md#q22-enterprise-cloud-storage-cost-optimization-in-data-lakes--warehouses)
14. **`[Hard]`** How do you handle currency conversion in a global sales model with daily exchange rates? → [Solution](03_data_modeling_and_warehousing_faq.md#q10-multi-currency-sales-fact-modeling--daily-exchange-rate-joins)
15. **`[Hard]`** What is the difference between partitioning and bucketing in big data systems, and when would you prefer one over the other? → [Solution](03_data_modeling_and_warehousing_faq.md#q18-partitioning-vs-bucketing-in-big-data-systems)

### Level 3: Enterprise Domain Modeling & Scale Architecture (Hard / Expert)
16. **`[Hard]`** Design a Vendor Payment System (Fact vs Dimension, Business Events mapping, Granularity, Payment Batch vs Order-Level Tracking). → [Solution](03_data_modeling_and_warehousing_faq.md#q1-end-to-end-data-model-design-vendor-payment-system)
17. **`[Hard]`** Design a Data Model for a Lending Book (loans, repayments, interest accruals, charge-offs). → [Solution](03_data_modeling_and_warehousing_faq.md#q3-end-to-end-data-model-design-lending-book-architecture)
18. **`[Hard]`** Design a data model for storing user watch-history at scale for global users, and explain your choice of schema design. → [Solution](03_data_modeling_and_warehousing_faq.md#q13-global-user-watch-history-data-model-at-scale)
19. **`[Hard]`** How would you design a data model for storing large-scale logs that need fast write speed and moderately fast reads? → [Solution](03_data_modeling_and_warehousing_faq.md#q14-high-throughput-log-data-modeling-fast-writes-vs-fast-reads)
20. **`[Hard]`** A Delta Lake table contains over 1 billion rows and query performance is degrading. How would you diagnose and optimize it? → [Solution](03_data_modeling_and_warehousing_faq.md#q21-managing-and-querying-billion-row-tables-in-analytical-warehouses)
21. **`[Hard]`** Design a high-concurrency relational database schema for a movie-booking system to prevent double-booking of seats during peak ticket sales. → [Solution](03_data_modeling_and_warehousing_faq.md#q24-end-to-end-database-design-movie-ticket-booking-platform)
22. **`[Hard]`** You need to make significant changes to an existing production data model. How do you implement this safely without breaking downstream pipelines (Expand-Contract pattern)? → [Solution](03_data_modeling_and_warehousing_faq.md#q25-schema-evolution--safe-data-model-migration-in-active-pipelines)
23. **`[Expert]`** How would you manage schema evolution in a data warehouse used by multiple analytics teams, and design a versioned schema system for backward-incompatible changes? → [Solution](03_data_modeling_and_warehousing_faq.md#q15-managing-schema-evolution--versioning-across-multiple-analytics-teams)
24. **`[Expert]`** How would you design an audit framework to track data lineage and transformations, and guarantee data consistency between OLTP and OLAP? → [Solution](03_data_modeling_and_warehousing_faq.md#q16-enterprise-data-governance-lineage--consistency-guarantees)
25. **`[Expert]`** How do you design a system to secure PII and enforce row-level access controls in a modern data lake / Lakehouse architecture? → [Solution](03_data_modeling_and_warehousing_faq.md#q19-securing-sensitive-customer-data-pii--enforcing-row-level-access-controls)


---

# **04. Cloud & Distributed Systems**
*Companion Deep-Dive Guide: [`04_cloud_and_distributed_systems_faq.md`](04_cloud_and_distributed_systems_faq.md) (16 Questions & Production Solutions)*

### Level 1: Distributed Computing Foundations (Easy / Medium)
1. **`[Easy]`** What are the core Hadoop components and their responsibilities, and what limitations of MapReduce did Spark address? → [Solution](04_cloud_and_distributed_systems_faq.md#q11-hadoop-ecosystem-core-components--mapreduce-vs-spark-architecture)
2. **`[Easy]`** What is a RecordReader and Combiner in distributed processing frameworks? → [Solution](04_cloud_and_distributed_systems_faq.md#q12-recordreader-and-combiner-internal-mechanics)
3. **`[Easy]`** What is the difference between Hive external and managed tables? → [Solution](04_cloud_and_distributed_systems_faq.md#q16-hive-metastore-architecture-managed-vs-external-tables)
4. **`[Medium]`** Partitioning Strategies, Data Sharding, and Bucketing — which improves join performance better and why? → [Solution](04_cloud_and_distributed_systems_faq.md#q1-distributed-data-organization-partitioning-vs-sharding-vs-bucketing)
5. **`[Medium]`** Consistent Hashing and Distributed Ring Topology in distributed data systems. → [Solution](04_cloud_and_distributed_systems_faq.md#q2-consistent-hashing--distributed-ring-topology)
6. **`[Medium]`** Distributed Streaming Decision Matrix: Kafka vs Kinesis vs Pub/Sub for high-throughput streaming ingestion. → [Solution](04_cloud_and_distributed_systems_faq.md#q3-distributed-streaming-decision-matrix-kafka-vs-kinesis-vs-pubsub)

### Level 2: Cloud Data Platforms (Azure & AWS) (Medium / Hard)
7. **`[Medium]`** What are the key differences between Dedicated SQL Pool and Serverless SQL Pool in Synapse Analytics? → [Solution](04_cloud_and_distributed_systems_faq.md#q5-azure-synapse-analytics-dedicated-vs-serverless-sql-pools)
8. **`[Medium]`** You are asked to ingest and transform 100GB of CSV files daily in Synapse. What is your architectural approach? → [Solution](04_cloud_and_distributed_systems_faq.md#q6-ingesting--transforming-100gb-of-csv-files-daily-in-synapse)
9. **`[Medium]`** How would you design an ADF pipeline to move data from multiple on-prem SQL databases to Azure Data Lake and then load into Synapse Analytics? → [Solution](04_cloud_and_distributed_systems_faq.md#q7-hybrid-adf-ingestion-pipeline-on-prem-sql-to-adls-to-synapse)
10. **`[Medium]`** Explain your approach for incremental and Change Data Capture (CDC) data loading in Azure cloud platforms. → [Solution](04_cloud_and_distributed_systems_faq.md#q8-cloud-change-data-capture-cdc-strategies-in-azure)
11. **`[Hard]`** Amazon Athena vs Amazon Redshift — architectural trade-offs, and what are the different Redshift distribution styles? → [Solution](04_cloud_and_distributed_systems_faq.md#q14-amazon-athena-vs-amazon-redshift--distribution-styles)
12. **`[Hard]`** Your Python AWS Lambda needs a library not available in the default runtime. How would you package dependencies and deploy Lambda Layers? → [Solution](04_cloud_and_distributed_systems_faq.md#q15-packaging-custom-dependencies-in-aws-lambda-layers)
13. **`[Hard]`** Explain an end-to-end AWS Glue ETL pipeline you have worked on (validation & cleaning using PySpark in Glue). → [Solution](04_cloud_and_distributed_systems_faq.md#q13-end-to-end-aws-glue-pyspark-etl-pipeline)

### Level 3: Cost Optimization & Multi-Cloud Architecture (Hard / Expert)
14. **`[Hard]`** How would you design a cost-efficient pipeline in Azure using ADF, Databricks, and ADLS Gen2? → [Solution](04_cloud_and_distributed_systems_faq.md#q9-cost-efficient-pipeline-architecture-adf--databricks--adls)
15. **`[Expert]`** Explain how you would build a Medallion Architecture (Bronze-Silver-Gold) in Databricks / Data Lakehouse to process daily sales data. → [Solution](04_cloud_and_distributed_systems_faq.md#q4-lakehouse-medallion-architecture-for-daily-sales-ingestion)
16. **`[Expert]`** Suppose your pipeline needs to run across AWS and Azure together (multi-cloud). How would you design secure, performant, and cost-effective data federation? → [Solution](04_cloud_and_distributed_systems_faq.md#q10-multi-cloud-architecture-aws--azure-secure-federation)

---

# **05. Apache Spark & PySpark Architecture**
*Companion Deep-Dive Guide: [`05_apache_spark_and_pyspark_faq.md`](05_apache_spark_and_pyspark_faq.md) (65 Questions & Production Solutions)*

### Level 1: Engine Architecture, Execution Flow & RDD Foundations (Easy / Medium)
1. **`[Easy]`** Spark Physical Cluster Architecture (Driver, Executors, Cluster Manager, Worker Nodes). → [Solution](05_apache_spark_and_pyspark_faq.md#q30-spark-physical-cluster-architecture)
2. **`[Easy]`** Explain the complete Spark execution flow from spark-submit to job completion (Jobs $\to$ Stages $\to$ Tasks & stage boundaries). → [Solution](05_apache_spark_and_pyspark_faq.md#q43-spark-execution-flow-from-spark-submit-to-completion)
3. **`[Easy]`** What is the difference between transformations and actions in Spark? Lazy evaluation mechanics. → [Solution](05_apache_spark_and_pyspark_faq.md#q40-narrow-vs-wide-transformations--stage-boundaries)
4. **`[Easy]`** What is the difference between narrow and wide transformations in Spark? Can you give examples? → [Solution](05_apache_spark_and_pyspark_faq.md#q40-narrow-vs-wide-transformations--stage-boundaries)
5. **`[Easy]`** What is an RDD and why is it immutable? Lineage graphs and fault tolerance. → [Solution](05_apache_spark_and_pyspark_faq.md#q41-rdd-immutability--lineage-based-fault-tolerance)
6. **`[Easy]`** reduceByKey() vs groupByKey() — why is one generally more efficient? Map-side combine mechanics. → [Solution](05_apache_spark_and_pyspark_faq.md#q42-reducebykey-vs-groupbykey-efficiency)
7. **`[Easy]`** map() vs flatMap() internal mechanics and output record cardinality. → [Solution](05_apache_spark_and_pyspark_faq.md#q44-map-vs-flatmap-mechanics)
8. **`[Medium]`** What are the differences between RDDs, DataFrames, and Datasets in PySpark? Catalyst & Tungsten internals. → [Solution](05_apache_spark_and_pyspark_faq.md#rdds-vs-dataframes-vs-datasets-catalyst--tungsten-internals)
9. **`[Medium]`** Explain SparkContext vs SparkSession from a modern data architecture perspective. → [Solution](05_apache_spark_and_pyspark_faq.md#sparkcontext-vs-sparksession-from-a-modern-data-architecture-perspective)
10. **`[Medium]`** What is partition pruning and how does Catalyst push it down to storage? → [Solution](05_apache_spark_and_pyspark_faq.md#q45-partition-pruning-mechanics)
11. **`[Medium]`** How does Spark's lazy evaluation work, and how does the Catalyst Optimizer transform an unresolved logical plan into executed physical code? → [Solution](05_apache_spark_and_pyspark_faq.md#q50-spark-lazy-evaluation-lineage-graph-construction-pipelining--fault-recovery)
12. **`[Medium]`** Explain the decoupled architecture between Parquet storage, Delta Lake transaction metadata, and Spark executor compute. → [Solution](05_apache_spark_and_pyspark_faq.md#q51-decoupled-architecture-apache-parquet-vs-delta-lake-vs-spark-executors)

### Level 2: Distributed Joins, Shuffling & Partition Optimization (Medium / Hard)
13. **`[Medium]`** What are the different join strategies in Spark and when to use each (BHJ, SMJ, SHJ, Broadcast Nested Loop)? → [Solution](05_apache_spark_and_pyspark_faq.md#q4-q5--q6-spark-join-strategies--optimizer-selection-matrix)
14. **`[Medium]`** What is a broadcast join, and when does the optimizer choose it over a shuffle join? → [Solution](05_apache_spark_and_pyspark_faq.md#q4-q5--q6-spark-join-strategies--optimizer-selection-matrix)
15. **`[Medium]`** Explain broadcast variables — what are they, how does the BitTorrent protocol work, and when to use them? → [Solution](05_apache_spark_and_pyspark_faq.md#broadcast-variables-internals--torrent-protocol)
16. **`[Medium]`** You join a 500 GB fact table with a 50 MB lookup DataFrame. How would you join them efficiently, and why? → [Solution](05_apache_spark_and_pyspark_faq.md#q23-joining-500-gb-dataframe-with-50-mb-lookup-dataframe)
17. **`[Medium]`** What is the difference between repartition() and coalesce()? Real production use cases. → [Solution](05_apache_spark_and_pyspark_faq.md#q7--q8-repartition-vs-coalesce-architectural-comparison)
18. **`[Medium]`** Your DataFrame has 500 partitions. How would you decide whether to increase or decrease them? Sizing heuristics. → [Solution](05_apache_spark_and_pyspark_faq.md#q18-partition-sizing-heuristics-increasing-vs-decreasing)
19. **`[Medium]`** A Spark job generates thousands of small files. What is the small-file problem, why does it hurt performance, and how do you fix it? → [Solution](05_apache_spark_and_pyspark_faq.md#q20--q21-the-small-file-problem--resolution-in-spark)
20. **`[Medium]`** How does Adaptive Query Execution (AQE) improve Spark performance (dynamic coalesce, skew join, join conversion)? → [Solution](05_apache_spark_and_pyspark_faq.md#q16-adaptive-query-execution-aqe-3-pillar-architecture)
21. **`[Medium]`** How would you identify whether a Spark job is suffering from excessive shuffling, and how do you reduce shuffle operations? → [Solution](05_apache_spark_and_pyspark_faq.md#q14--q15-identifying--reducing-excessive-shuffling)
22. **`[Medium]`** How would you optimize a PySpark job that performs multiple groupBy() operations or creates very large intermediate DataFrames? → [Solution](05_apache_spark_and_pyspark_faq.md#q22--q26-optimizing-large-intermediate-dataframes--multiple-groupby-operations)
23. **`[Medium]`** How do window functions affect Spark performance, and why is an unpartitioned `Window.partitionBy()` clause catastrophic? → [Solution](05_apache_spark_and_pyspark_faq.md#q49-performance-impact-of-window-functions-in-distributed-spark-execution)
24. **`[Hard]`** A Spark join produces a huge increase in the number of records. How do you diagnose join record multipliers and cartesian explodes? → [Solution](05_apache_spark_and_pyspark_faq.md#q28-diagnosing-join-record-multipliers--cartesian-explodes)
25. **`[Hard]`** PySpark scenario: Join two DataFrames with different schemas using row numbers / Window functions. → [Solution](05_apache_spark_and_pyspark_faq.md#q34-joining-two-dataframes-with-different-schemas-via-row-number)
26. **`[Hard]`** How can Bloom Filters help reduce unnecessary data scans in Spark and Delta Lake? → [Solution](05_apache_spark_and_pyspark_faq.md#bloom-filters-in-spark--delta-lake-for-runtime-file-skipping)
27. **`[Hard]`** You need to join two tables with a big size difference: one is 2 TB, the other is 200 MB. How would you optimize this join? → [Solution](05_apache_spark_and_pyspark_faq.md#q47-joining-2-tb-fact-table-with-200-mb-dimension-table)
28. **`[Hard]`** How would you join two massive datasets that are too large to fit into memory efficiently in a distributed environment (bucketing, pre-sorting)? → [Solution](05_apache_spark_and_pyspark_faq.md#joining-two-massive-multi-terabyte-datasets-beyond-memory-spills)

### Level 3: Transformations, Live Coding & Memory Tuning (Medium / Hard)
29. **`[Medium]`** Explain the difference between transform(), Python UDF, and Pandas UDF (Vectorized Arrow). Which is more efficient? → [Solution](05_apache_spark_and_pyspark_faq.md#q27--q38-built-in-transform-vs-python-udf-vs-pandas-udf)
30. **`[Medium]`** How would you handle null values and incorrect casing in a PySpark DataFrame? → [Solution](05_apache_spark_and_pyspark_faq.md#q29--q31-handling-inconsistent-schemas-null-values--incorrect-casing)
31. **`[Medium]`** A Spark job reads a large number of files with different schemas. How would you handle schema inconsistencies efficiently? → [Solution](05_apache_spark_and_pyspark_faq.md#q29--q31-handling-inconsistent-schemas-null-values--incorrect-casing)
32. **`[Medium]`** Given a nested JSON file, how would you parse and flatten it in PySpark? → [Solution](05_apache_spark_and_pyspark_faq.md#q39-parsing--flattening-nested-json-in-pyspark)
33. **`[Medium]`** How do you remove duplicate records in a PySpark DataFrame? → [Solution](05_apache_spark_and_pyspark_faq.md#pyspark-live-coding-dataframe-deduplication--department-sales-aggregations)
34. **`[Medium]`** How do you clean incoming streaming events with null `user_id` values before writing to Bronze/Silver, and route corrupt records to a dead-letter quarantine? → [Solution](05_apache_spark_and_pyspark_faq.md#q52-production-data-cleansing-filtering-null-identifiers--dead-letter-routing)
35. **`[Medium]`** Write a PySpark transformation to calculate total sales by department. → [Solution](05_apache_spark_and_pyspark_faq.md#pyspark-live-coding-dataframe-deduplication--department-sales-aggregations)
36. **`[Medium]`** Write a PySpark query to rank employees department-wise using Window Functions. → [Solution](05_apache_spark_and_pyspark_faq.md#q37-pyspark-live-coding-department-wise-employee-ranking)
37. **`[Medium]`** Write PySpark logic to compute the difference in days between two status changes for each user. → [Solution](05_apache_spark_and_pyspark_faq.md#q32-pyspark-live-coding-days-between-status-changes-per-user)
38. **`[Medium]`** How would you write a PySpark job to merge (upsert) data from a new file into an existing Delta table? → [Solution](05_apache_spark_and_pyspark_faq.md#q33-pyspark-live-coding-upserting-new-files-into-delta-tables)
39. **`[Medium]`** Write PySpark code to find customers who spent > ₹1000 from store S1 in the last month using Customer, Store, and Sales datasets. → [Solution](05_apache_spark_and_pyspark_faq.md#q35-pyspark-live-coding-store-spending--1000-in-the-last-month)
40. **`[Medium]`** Write Python/PySpark code to join two datasets and rank records using window functions. → [Solution](05_apache_spark_and_pyspark_faq.md#pyspark-live-coding-dataframe-deduplication--department-sales-aggregations)
41. **`[Hard]`** Solve data transformation tasks without an IDE, demonstrating clear logic and accurate syntax. → [Solution](05_apache_spark_and_pyspark_faq.md#q46-solving-data-transformations-without-an-ide-whiteboarding-best-practices)
42. **`[Hard]`** When would you use cache() vs persist()? Explain storage levels (MEMORY_ONLY, MEMORY_AND_DISK, OFF_HEAP). → [Solution](05_apache_spark_and_pyspark_faq.md#q13-cache-vs-persist-storage-levels)
43. **`[Hard]`** How does Spark memory management work? Explain unified memory, execution vs storage, GC, and executor memory tuning. → [Solution](05_apache_spark_and_pyspark_faq.md#q36-spark-unified-memory-architecture-breakdown)
44. **`[Hard]`** What are the best practices for analysing Snowflake data using Python and Snowpark? → [Solution](05_apache_spark_and_pyspark_faq.md#best-practices-for-analysing-snowflake-data-using-python--snowpark)

### Level 4: Data Skew, Stragglers & Production Incident Diagnostics (Hard / Expert)
45. **`[Hard]`** A Spark job has 200 tasks, but one task runs much longer than the others. What could be the reason? Root causes of the 1-task straggler. → [Solution](05_apache_spark_and_pyspark_faq.md#q1-root-causes-of-the-1-task-straggler)
46. **`[Hard]`** How would you identify data skew in a Spark join using the Spark Web UI? → [Solution](05_apache_spark_and_pyspark_faq.md#q2-identifying-data-skew-in-the-spark-web-ui)
47. **`[Hard]`** What is data skew in PySpark, and how would you handle skewed joins in large datasets (Salting keys + AQE Skew Join)? → [Solution](05_apache_spark_and_pyspark_faq.md#q3--q25-mitigating-data-skew-via-key-salting--aqe-skew-join)
48. **`[Hard]`** One partition contains 40% of the entire dataset. What happens to stages, tasks, and overall job execution? → [Solution](05_apache_spark_and_pyspark_faq.md#the-40-skew-partition-breakdown-stages-tasks-and-execution-dynamics)
49. **`[Hard]`** How do you handle a DataFrame with highly uneven partition sizes? → [Solution](05_apache_spark_and_pyspark_faq.md#q3--q25-mitigating-data-skew-via-key-salting--aqe-skew-join)
50. **`[Hard]`** How do you strategically manage data partitioning in a petabyte-scale dataset to avoid data skew and optimize query performance? → [Solution](05_apache_spark_and_pyspark_faq.md#petabyte-scale-strategic-partitioning-heuristics)
51. **`[Hard]`** Every Spark executor is waiting while reading from the database. What would you check first: connection limits, partitioning, query performance, or source-system bottlenecks? → [Solution](05_apache_spark_and_pyspark_faq.md#spark-executor-jdbc-read-stalls-the-4-step-diagnosis)
52. **`[Hard]`** A Spark job runs efficiently on 1 million records but slows down dramatically or crashes when scaled to 100 million records. How would you investigate? → [Solution](05_apache_spark_and_pyspark_faq.md#q53-diagnosing-spark-job-slowdown-scaling-from-1m-to-100m-records)
53. **`[Hard]`** A PySpark job is running out of memory (OutOfMemoryError: Driver OOM vs Executor OOM). What would you check first? → [Solution](05_apache_spark_and_pyspark_faq.md#q17-outofmemoryerror-oom-diagnostic-playbook)
54. **`[Hard]`** A Spark job has frequent Garbage Collection (GC) pauses and executor failures. What would you check and how would you fix it? → [Solution](05_apache_spark_and_pyspark_faq.md#q24-garbage-collection-gc-optimization--executor-sizing)
55. **`[Hard]`** Your PySpark job memory usage keeps increasing after every batch. What do you suspect, and which metrics or Spark UI details would you inspect? → [Solution](05_apache_spark_and_pyspark_faq.md#progressive-memory-creep--py4j-object-retention-debugging)
56. **`[Expert]`** A Spark job is taking 2 hours instead of 20 minutes. How would you troubleshoot it systematically? → [Solution](05_apache_spark_and_pyspark_faq.md#q9-q11--q12-production-troubleshooting-playbook-for-2hr-vs-20min-slowdowns)
57. **`[Expert]`** A Spark job works fine in development but is very slow in production. How would you troubleshoot it? → [Solution](05_apache_spark_and_pyspark_faq.md#q10-why-spark-jobs-run-fine-in-dev-but-crash-in-production)
58. **`[Expert]`** How would you optimize a slow PySpark job processing billions of records? → [Solution](05_apache_spark_and_pyspark_faq.md#q9-q11--q12-production-troubleshooting-playbook-for-2hr-vs-20min-slowdowns)
59. **`[Expert]`** Scenario-based question: "You have a Spark pipeline failing randomly in production. How would you debug and fix it?" → [Solution](05_apache_spark_and_pyspark_faq.md#q9-q11--q12-production-troubleshooting-playbook-for-2hr-vs-20min-slowdowns)
60. **`[Expert]`** How would you process a 10 TB dataset efficiently using PySpark? Complete architecture playbook. → [Solution](05_apache_spark_and_pyspark_faq.md#q19-scaling-playbook-for-processing-10-tb-datasets)
61. **`[Expert]`** How would you investigate high memory usage and slow data processing? 4-Stage Diagnostic Framework. → [Solution](05_apache_spark_and_pyspark_faq.md#q48-high-memory-usage--slow-data-processing-diagnostic-framework)
62. **`[Expert]`** How would you systematically debug a Spark job that is running significantly slower in production than in testing? → [Solution](05_apache_spark_and_pyspark_faq.md#q10-why-spark-jobs-run-fine-in-dev-but-crash-in-production)
63. **`[Expert]`** You've discovered duplicate records in a critical production dataset. What is your immediate triage plan, and what long-term strategy would you implement to prevent recurrence? → [Solution](05_apache_spark_and_pyspark_faq.md#pyspark-live-coding-dataframe-deduplication--department-sales-aggregations)
64. **`[Expert]`** Progressive memory leak in long-running Spark jobs: Py4J gateway references, broadcast cleanup, and off-heap retention debugging. → [Solution](05_apache_spark_and_pyspark_faq.md#progressive-memory-creep--py4j-object-retention-debugging)
65. **`[Expert]`** Executor CPU starvation vs network spill: Reading execution profiles from the Spark event log and timeline views. → [Solution](05_apache_spark_and_pyspark_faq.md#q9-q11--q12-production-troubleshooting-playbook-for-2hr-vs-20min-slowdowns)


---

# **06. Databricks & Delta Lake**
*Companion Deep-Dive Guide: [`06_databricks_and_delta_lake_faq.md`](06_databricks_and_delta_lake_faq.md) (32 Questions & Production Solutions)*

### Level 1: Delta Lake Architecture & Core Primitives (Easy / Medium)
1. **`[Easy]`** What is Delta Lake, and what are its advantages? ACID reliability challenges solved. → [Solution](06_databricks_and_delta_lake_faq.md#q22-delta-lake-architecture--acid-transaction-internals)
2. **`[Easy]`** Explain the architecture of Delta Lake and how the transaction log (`_delta_log`) works under the hood. → [Solution](06_databricks_and_delta_lake_faq.md#q22-delta-lake-architecture--acid-transaction-internals)
3. **`[Easy]`** When would you use a Job Cluster vs an All-Purpose Cluster in Azure Databricks? → [Solution](06_databricks_and_delta_lake_faq.md#q12-job-clusters-vs-all-purpose-clusters)
4. **`[Easy]`** When would you use MERGE instead of INSERT? → [Solution](06_databricks_and_delta_lake_faq.md#q3-when-to-use-merge-vs-insert)
5. **`[Medium]`** How would you implement an incremental load using Delta Lake? → [Solution](06_databricks_and_delta_lake_faq.md#q1--q2-incremental-loading-architecture--cdc-ingestion)
6. **`[Medium]`** You need to load only changed records from a source into a Delta table every day. How would you design the incremental load? → [Solution](06_databricks_and_delta_lake_faq.md#q1--q2-incremental-loading-architecture--cdc-ingestion)
7. **`[Medium]`** How would you implement Slowly Changing Dimensions (SCD Type 1 and Type 2) using Delta Lake? → [Solution](06_databricks_and_delta_lake_faq.md#q4-slowly-changing-dimensions-scd-types-1--2-in-delta-lake)
8. **`[Medium]`** What are Delta Lake Time Travel and Versioning, and when would you use them? → [Solution](06_databricks_and_delta_lake_faq.md#q6-recovering-deleted-or-overwritten-delta-data-via-time-travel)
9. **`[Medium]`** What is Databricks Lakeflow, and how does it modernize data ingestion, transformation pipelines, and job orchestration alongside Asset Bundles (DABs)? → [Solution](06_databricks_and_delta_lake_faq.md#q25-databricks-lakeflow-architecture--databricks-asset-bundles-dabs)
10. **`[Medium]`** Why is reading a table via Unity Catalog (`spark.read.table()`) faster and more secure than reading directly from its cloud storage path (`spark.read.load("abfss://...")`)? → [Solution](06_databricks_and_delta_lake_faq.md#q26-catalog-table-referencing-vs-direct-file-path-ingestion-in-delta-lake)
11. **`[Medium]`** How would you manually implement versioning in Parquet or Delta files, and why does Delta Lake's ACID transaction log eliminate the need for manual version folders? → [Solution](06_databricks_and_delta_lake_faq.md#q29-manual-versioning--snapshots-in-parquet-vs-delta-acid-transaction-logs)

### Level 2: Physical Table Maintenance, Concurrency & Governance (Medium / Hard)
12. **`[Medium]`** When would you use OPTIMIZE, Z-ORDER, and VACUUM? Table compaction mechanics. → [Solution](06_databricks_and_delta_lake_faq.md#q7-physical-table-maintenance-optimize-z-order--vacuum)
13. **`[Medium]`** How would you recover accidentally deleted, overwritten, or modified Delta data (e.g. last 3 days)? → [Solution](06_databricks_and_delta_lake_faq.md#q6-recovering-deleted-or-overwritten-delta-data-via-time-travel)
14. **`[Medium]`** A source schema changes unexpectedly during a Delta load. How would you handle schema enforcement versus schema evolution (`mergeSchema`)? → [Solution](06_databricks_and_delta_lake_faq.md#q21-schema-enforcement-vs-schema-evolution)
15. **`[Medium]`** How would you use Delta Lake Change Data Feed (CDF) to capture INSERT, UPDATE, and DELETE changes? → [Solution](06_databricks_and_delta_lake_faq.md#q20-delta-lake-change-data-feed-cdf-implementation)
16. **`[Medium]`** How would you implement data quality checks and table constraints before writing data into a Delta table? → [Solution](06_databricks_and_delta_lake_faq.md#q14-enforcing-data-quality-constraints-in-delta-lake)
17. **`[Hard]`** What happens when multiple records match the same MERGE condition? The Multiple-Match MERGE Trap and resolution. → [Solution](06_databricks_and_delta_lake_faq.md#q5-the-multiple-match-merge-trap--resolution)
18. **`[Hard]`** How would you handle concurrent writes to the same Delta table? Optimistic Concurrency Control (OCC) and write conflict resolution. → [Solution](06_databricks_and_delta_lake_faq.md#q17-concurrent-writes--optimistic-concurrency-control-occ)
19. **`[Hard]`** How would you monitor and maintain Delta tables in a production environment (vacuuming schedules, file counts, stale commits)? → [Solution](06_databricks_and_delta_lake_faq.md#q18-monitoring-and-maintaining-delta-tables-in-production)
20. **`[Hard]`** How would you implement a rollback strategy for a failed Delta Lake deployment? → [Solution](06_databricks_and_delta_lake_faq.md#q16-automated-rollback-strategy-for-failed-deployments)
21. **`[Hard]`** Your ADLS folder contains millions of small files. What problems can this cause in Databricks, and how would you solve them? → [Solution](06_databricks_and_delta_lake_faq.md#q23-millions-of-small-files-in-adls-root-causes--resolution-in-databricks)
22. **`[Hard]`** Explain partitioning in Azure Databricks. How do you decide the appropriate partition column vs Liquid Clustering? → [Solution](06_databricks_and_delta_lake_faq.md#q24-partitioning-strategy--liquid-clustering-in-azure-databricks)
23. **`[Hard]`** How would you validate source and target data during a large-scale database migration to Delta Lake (row counts, column hashes, and distribution profiling)? → [Solution](06_databricks_and_delta_lake_faq.md#q27-source-to-target-data-validation-framework-for-delta-lake-migrations)
24. **`[Hard]`** Queries against your Gold-layer Delta table feeding executive dashboards have slowed down significantly. How do you identify bottlenecks and optimize query speed? → [Solution](06_databricks_and_delta_lake_faq.md#q28-diagnosing-and-tuning-gold-layer-delta-table-dashboard-performance)

### Level 3: Multi-TB Optimization, CI/CD & Production Incident Diagnostics (Hard / Expert)
25. **`[Hard]`** A Delta table has grown to several TB and queries are becoming slow. How would you optimize it? → [Solution](06_databricks_and_delta_lake_faq.md#q13-q15--q19-optimizing-multi-tb-tables-with-frequent-mutations--small-files)
26. **`[Hard]`** A Delta table has frequent UPDATE and DELETE operations and is becoming slow. How would you optimize the table? → [Solution](06_databricks_and_delta_lake_faq.md#q13-q15--q19-optimizing-multi-tb-tables-with-frequent-mutations--small-files)
27. **`[Hard]`** A Delta table contains thousands of small files and query performance has degraded. How would you optimize it? → [Solution](06_databricks_and_delta_lake_faq.md#q13-q15--q19-optimizing-multi-tb-tables-with-frequent-mutations--small-files)
28. **`[Hard]`** How do you secure secrets and credentials in Azure Databricks using Azure Key Vault-backed secret scopes? → [Solution](06_databricks_and_delta_lake_faq.md#databricks-secret-scopes--azure-key-vault-integration)
29. **`[Expert]`** A Databricks job suddenly starts taking 3× longer than usual. What would you investigate? Root-cause analysis playbook. → [Solution](06_databricks_and_delta_lake_faq.md#q8--q9-root-cause-analysis-for-3x-production-job-slowdowns)
30. **`[Expert]`** You notice a Databricks job that runs fine in dev but takes 3× longer in production — how do you debug this? → [Solution](06_databricks_and_delta_lake_faq.md#q8--q9-root-cause-analysis-for-3x-production-job-slowdowns)
31. **`[Expert]`** A production job fails after processing 80% of the data. How would you restart it without processing everything again? → [Solution](06_databricks_and_delta_lake_faq.md#q10-restarting-failed-jobs-without-reprocessing-from-scratch)
32. **`[Expert]`** How would you design a multi-tenant data platform and implement CI/CD for Databricks notebooks, workflows, and pipelines using Databricks Asset Bundles (DABs)? → [Solution](06_databricks_and_delta_lake_faq.md#designing-a-multi-tenant-lakehouse-platform-on-databricks)


---

# **07. Unity Catalog & Data Governance**
*Companion Deep-Dive Guide: [`07_unity_catalog_faq.md`](07_unity_catalog_faq.md) (15 Questions & Production Solutions)*

### Level 1: Governance Architecture & Access Control (Easy / Medium)
1. **`[Easy]`** What is Unity Catalog, data lineage, and what do OPTIMIZE vs VACUUM actually do? → [Solution](07_unity_catalog_faq.md#q8-q13--q14-unity-catalog-3-level-namespace--multi-environment-architecture)
2. **`[Easy]`** How would you implement different access levels for different teams using Role-Based Access Control (RBAC)? → [Solution](07_unity_catalog_faq.md#q1-role-based-access-control--privilege-hierarchy)
3. **`[Easy]`** How would you give a team read access to multiple tables without granting unnecessary permissions? → [Solution](07_unity_catalog_faq.md#q9-granting-read-access-across-multiple-tables-cleanly)
4. **`[Medium]`** Your company has Development, Testing, and Production environments. How would you design Unity Catalog catalogs, schemas, and access permissions for them? → [Solution](07_unity_catalog_faq.md#q8-q13--q14-unity-catalog-3-level-namespace--multi-environment-architecture)
5. **`[Medium]`** How would you use groups and service principals to manage access for developers, testers, and automated production jobs? → [Solution](07_unity_catalog_faq.md#q8-q13--q14-unity-catalog-3-level-namespace--multi-environment-architecture)
6. **`[Medium]`** How would you configure Storage Credentials and External Locations to securely access ADLS data through Unity Catalog without hardcoding storage keys? → [Solution](07_unity_catalog_faq.md#q12-storage-credentials--external-locations-cloud-security)

### Level 2: Fine-Grained Security & Permission Troubleshooting (Medium / Hard)
7. **`[Medium]`** How would you secure sensitive columns such as PII? Dynamic column masking implementation. → [Solution](07_unity_catalog_faq.md#q2-q3--q4-dynamic-column-masking-for-pii-protection)
8. **`[Medium]`** A developer can access a table but should not be able to see sensitive columns. How would you implement this requirement? → [Solution](07_unity_catalog_faq.md#q2-q3--q4-dynamic-column-masking-for-pii-protection)
9. **`[Medium]`** A user needs access to a table but should only see selected columns. How would you design this using Unity Catalog? → [Solution](07_unity_catalog_faq.md#q2-q3--q4-dynamic-column-masking-for-pii-protection)
10. **`[Hard]`** How would you implement row-level security for different users or departments using Unity Catalog row filters? → [Solution](07_unity_catalog_faq.md#q10-row-level-security-with-dynamic-user-and-group-filtering)
11. **`[Hard]`** A user can access a catalog but cannot query a particular table. How would you troubleshoot the permissions? The 4-step diagnostic chain. → [Solution](07_unity_catalog_faq.md#q5--q6-permission-diagnostics--4-step-access-denial-troubleshooting)
12. **`[Hard]`** How would you troubleshoot a user who suddenly loses access to a table? → [Solution](07_unity_catalog_faq.md#q5--q6-permission-diagnostics--4-step-access-denial-troubleshooting)
13. **`[Hard]`** Finance, Marketing, and Operations teams consume the same curated customer dataset, but have different security restrictions. How do you implement least-privilege row and column access using Unity Catalog? → [Solution](07_unity_catalog_faq.md#q15-multi-department-data-governance-finance-marketing--operations-security)

### Level 3: Audit Lineage & Enterprise Governance (Hard / Expert)
14. **`[Expert]`** How would you use automated data lineage and system audit logs during troubleshooting? → [Solution](07_unity_catalog_faq.md#q7-automated-data-lineage-for-root-cause-analysis)
15. **`[Expert]`** How would you track which users accessed sensitive data in Unity Catalog? System tables (`system.access.audit`) querying. → [Solution](07_unity_catalog_faq.md#q11-auditing-sensitive-pii-access-via-system-tables)


---

# **08. Auto Loader & Structured Streaming**
*Companion Deep-Dive Guide: [`08_auto_loader_and_structured_streaming_faq.md`](08_auto_loader_and_structured_streaming_faq.md) (29 Questions & Production Solutions)*

### Level 1: Streaming Foundations & Ingestion Discovery Modes (Easy / Medium)
1. **`[Easy]`** What are the differences between batch and streaming pipelines? Give real-life examples. → [Solution](08_auto_loader_and_structured_streaming_faq.md#streaming-q1-batch-vs-streaming-pipelines-architectural-comparison)
2. **`[Easy]`** Explain batch vs stream processing in the context of a video-streaming platform. → [Solution](08_auto_loader_and_structured_streaming_faq.md#streaming-q1-batch-vs-streaming-pipelines-architectural-comparison)
3. **`[Easy]`** Explain the difference between batch processing, micro-batch processing, and continuous streaming in Spark. → [Solution](08_auto_loader_and_structured_streaming_faq.md#spark-streaming-execution-modes-micro-batch-vs-continuous-processing)
4. **`[Medium]`** How do you choose between Apache Kafka, AWS Kinesis, Azure Event Hubs, and Google Cloud Pub/Sub for enterprise streaming ingestion? → [Solution](08_auto_loader_and_structured_streaming_faq.md#streaming-q3-streaming-ingestion-engine-evaluation-apache-kafka-vs-aws-kinesis-vs-azure-event-hubs-vs-gcp-pubsub)
5. **`[Medium]`** How would you design a pipeline to process millions of files arriving continuously? Auto Loader CloudFiles overview. → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q1-ingesting-millions-of-continuously-arriving-files)
6. **`[Medium]`** What is the difference between Auto Loader's directory listing mode and file notification mode, and when would you use each? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q15-directory-listing-vs-file-notification-mode)
7. **`[Medium]`** How would you process only specific file types from an ADLS folder using Auto Loader? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q12--q16-file-type-filtering--micro-batch-rate-limiting)
8. **`[Medium]`** How would you control the number of files processed in each micro-batch when the incoming file rate is very high? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q12--q16-file-type-filtering--micro-batch-rate-limiting)

### Level 2: Checkpointing, Schema Evolution & Rescued Data (Medium / Hard)
9. **`[Medium]`** How does checkpointing help Structured Streaming? Checkpoint internals (offsets, commits, state stores). → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q3-structured-streaming-checkpointing-architecture)
10. **`[Medium]`** What happens if a streaming job fails in the middle of processing? Safe crash recovery. → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q2--q11-stream-failure-recovery--end-to-end-exactly-once-processing)
11. **`[Medium]`** A streaming pipeline restarts after a failure and you notice some records are processed again. How would you ensure exactly-once or effectively-once processing? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q2--q11-stream-failure-recovery--end-to-end-exactly-once-processing)
12. **`[Medium]`** How would you handle schema evolution with Auto Loader? Schema evolution modes (`addNewColumns`, `rescue`, `failOnNewColumns`). → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q4--q5-schema-evolution-modes-in-auto-loader)
13. **`[Medium]`** An Auto Loader pipeline needs to handle files with new columns without stopping the pipeline. How would you implement schema evolution? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q4--q5-schema-evolution-modes-in-auto-loader)
14. **`[Medium]`** How would you handle unexpected or malformed columns using Auto Loader's rescuedDataColumn (`_rescued_data`)? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q17-the-rescued-data-column-pattern-for-malformed-data)
15. **`[Hard]`** Your ADLS pipeline ingests millions of files daily with late arrivals, reprocessing bursts, and failures. How do you design an idempotent ingestion architecture using Auto Loader and Delta Lake MERGE? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q18-resilient-high-scale-adls-ingestion-auto-loader--checkpointed-delta-merge)
16. **`[Hard]`** How would you prevent duplicate records in a streaming pipeline? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q6--q7-stream-deduplication--state-store-memory-management)
17. **`[Hard]`** A streaming pipeline suddenly starts producing duplicate records. How would you identify and prevent them? State store watermarking. → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q6--q7-stream-deduplication--state-store-memory-management)
18. **`[Hard]`** How do you handle late-arriving data or out-of-order events in streaming (event-time watermarks and state store cleanup)? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q10--streaming-q2-late-arriving-data--event-time-watermarking)
19. **`[Hard]`** How would you design a streaming pipeline so that bad records do not stop the entire stream? Poison pill quarantine and Dead-Letter Stream routing. → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q14-poison-pill-quarantine--dead-letter-stream-handling)

### Level 3: Kafka Distributed Log, Deduplication & Peak Backlog Troubleshooting (Hard / Expert)
20. **`[Hard]`** Write an end-to-end streaming pipeline in PySpark that ingests clickstream from Kafka, applies watermarking and stateful deduplication, and streams into Delta Lake. → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q19-end-to-end-streaming-pipeline-kafka-to-spark-structured-streaming-to-delta-lake)
21. **`[Hard]`** Your Kafka topic has 12 partitions and 15 consumers in the same consumer group. What are the remaining 3 consumers doing? → [Solution](08_auto_loader_and_structured_streaming_faq.md#kafka-consumer-group-allocation-12-partitions-vs-15-consumers)
22. **`[Hard]`** After a Kafka consumer rebalance, the same event gets processed twice. How can this happen, and how would you make the pipeline idempotent? → [Solution](08_auto_loader_and_structured_streaming_faq.md#kafka-rebalance-event-duplication--idempotent-consumer-design)
23. **`[Hard]`** Your Kafka topic receives restaurant menu update events. Same restaurant sends 50 duplicate updates in 10 seconds. How do you handle deduplication at scale? → [Solution](08_auto_loader_and_structured_streaming_faq.md#burst-deduplication-50-duplicate-updates-in-10-seconds)
24. **`[Hard]`** A streaming job is writing duplicate records to a Delta table. How would you prevent and resolve the issue? → [Solution](08_auto_loader_and_structured_streaming_faq.md#preventing-and-resolving-duplicate-records-in-streaming-writes-to-delta)
25. **`[Hard]`** How would you monitor whether a streaming pipeline is keeping up with incoming data? `StreamingQueryProgress` telemetry. → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q13-monitoring-streaming-progress-via-streamingqueryprogress)
26. **`[Expert]`** A streaming pipeline starts building a large processing backlog. How would you troubleshoot it? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q8--q9-diagnosing--troubleshooting-streaming-backlogs)
27. **`[Expert]`** Files are arriving continuously in ADLS, but the Auto Loader pipeline is processing them slower than they arrive. How would you troubleshoot the backlog? → [Solution](08_auto_loader_and_structured_streaming_faq.md#auto-loader-q8--q9-diagnosing--troubleshooting-streaming-backlogs)
28. **`[Expert]`** A Structured Streaming job falls behind during peak traffic and cannot keep up with incoming events. What is your diagnostic RCA playbook? → [Solution](08_auto_loader_and_structured_streaming_faq.md#structured-streaming-falling-behind-during-traffic-bursts-rca-playbook)
29. **`[Expert]`** How would you implement end-to-end exactly-once delivery across a Kafka + Spark Structured Streaming + Delta Lake setup? → [Solution](08_auto_loader_and_structured_streaming_faq.md#end-to-end-exactly-once-processing-across-kafka-spark--lakehouse)


---

# **09. Azure Data Factory (ADF) & ETL Pipelines**
*Companion Deep-Dive Guide: [`09_etl_data_pipelines_and_adf_faq.md`](09_etl_data_pipelines_and_adf_faq.md) (53 Questions & Production Solutions)*

### Level 1: Core Pipeline Foundations & ADF Mechanics (Easy / Medium)
1. **`[Easy]`** What is the difference between an Azure Integration Runtime (IR) and a Self-Hosted IR (SHIR)? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q17-azure-integration-runtime-ir-vs-self-hosted-ir-shir)
2. **`[Easy]`** How do you handle parameterization and dynamic content in ADF pipelines for reusability? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q1-q4-q5--q15-dynamic-databricks-invocation--parameter-passing)
3. **`[Easy]`** How would you pass parameters from ADF to Databricks notebooks? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q1-q4-q5--q15-dynamic-databricks-invocation--parameter-passing)
4. **`[Easy]`** You need to pass table name, load type, source path, and execution date from ADF to Databricks. How would you design the parameter flow? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q1-q4-q5--q15-dynamic-databricks-invocation--parameter-passing)
5. **`[Easy]`** How would you implement retry and failure handling in ADF activities? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q2--q3-activity-dependency-branching-rules--retry-handling)
6. **`[Medium]`** If one activity fails in a pipeline containing 20 activities, how would you continue processing the remaining activities? Dependency branching rules. → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q2--q3-activity-dependency-branching-rules--retry-handling)
7. **`[Medium]`** Describe how you implement error handling, alerting, and monitoring in ADF pipelines. → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q11-q12--q16-capturing-databricks-notebook-errors-in-adf--alerting)
8. **`[Medium]`** ADF successfully triggers a Databricks notebook, but the notebook fails. How would you capture the error and make the ADF pipeline fail correctly? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q11-q12--q16-capturing-databricks-notebook-errors-in-adf--alerting)
9. **`[Medium]`** How would you design an ADF pipeline that triggers Databricks notebooks dynamically? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q1-q4-q5--q15-dynamic-databricks-invocation--parameter-passing)
10. **`[Medium]`** You need to delete files from multiple containers daily before writing new data. ADF doesn't support wildcards across containers in one delete activity. How would you design this? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q14-deleting-files-across-multiple-containers-daily)
11. **`[Medium]`** How would you design an ADF pipeline that can restart from the failed point instead of processing everything again? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q3-designing-restartability-via-checkpointing)
12. **`[Medium]`** A user accidentally triggered the same ADF pipeline twice, resulting in duplicate records in the target table. How would you resolve this and prevent concurrent duplicate runs? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q18-accidental-duplicate-triggering-root-cause-analysis-mitigation--idempotency)
13. **`[Medium]`** How do you design granular retry logic in an ADF pipeline without reprocessing already completed child activities (Checkpoint Control Tables)? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q20-resilient-sub-task-retry-logic-without-full-pipeline-reprocessing)
14. **`[Medium]`** How would you implement logging and auditing for every pipeline execution? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q7-q10--q11-dimension-audit-columns-late-arriving-data--schema-evolution)

### Level 2: Idempotency, Incremental Ingestion & Transformation Engineering (Medium / Hard)
15. **`[Medium]`** End-to-End Design of Data Pipelines: Architectural trade-offs, scaling considerations, and failure recovery. → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q1--q2-end-to-end-batch-data-pipeline-architecture)
16. **`[Medium]`** Designing a resilient batch data pipeline. → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q1--q2-end-to-end-batch-data-pipeline-architecture)
17. **`[Medium]`** What is idempotency in a data pipeline and why does it matter for safe reruns? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q8--q9-pipeline-idempotency--re-execution-safety)
18. **`[Medium]`** How would you make a data pipeline idempotent? Give examples of production patterns. → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q8--q9-pipeline-idempotency--re-execution-safety)
19. **`[Medium]`** Scenario: A source sends the same file twice on some days. How do you dedupe idempotently at load time? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q5-deduplicating-duplicate-source-files-idempotently)
20. **`[Medium]`** Scenario: A nightly load must be restartable from the point of failure. How do you design checkpointing? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q3-designing-restartability-via-checkpointing)
21. **`[Medium]`** How do you handle duplicate records and late-arriving data in ETL pipelines? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q7-q10--q11-dimension-audit-columns-late-arriving-data--schema-evolution)
22. **`[Medium]`** How would you implement audit columns and change tracking in a dimension load? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q7-q10--q11-dimension-audit-columns-late-arriving-data--schema-evolution)
23. **`[Medium]`** What is dbt, and what problem does it solve in the transformation layer? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q4-the-dbt-transformation-framework--elt-benefits)
24. **`[Medium]`** How do you monitor pipeline SLAs and alert on data-freshness breaches? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q6-q12--q13-the-5-essential-data-quality-gates--sla-observability)
25. **`[Medium]`** What are common data quality checks you implement in production pipelines? The 5 essential quality gates. → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q6-q12--q13-the-5-essential-data-quality-gates--sla-observability)
26. **`[Medium]`** How do you manage secrets securely in a production pipeline using Key Vault? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q15-enterprise-secret-management-in-production-pipelines)
27. **`[Medium]`** How do you handle schema evolution in data pipelines (Avro, Parquet, JSON)? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q7-q10--q11-dimension-audit-columns-late-arriving-data--schema-evolution)
28. **`[Hard]`** How would you design a metadata-driven pipeline in ADF instead of creating separate pipelines for every table (100+ tables)? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q6--q13-metadata-driven-ingestion-architecture)
29. **`[Hard]`** How can you dynamically copy multiple tables from an on-prem SQL Server to Azure Data Lake using ADF? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q6--q13-metadata-driven-ingestion-architecture)
30. **`[Hard]`** How would you implement an incremental load from Azure SQL to ADLS when there is a LastModifiedDate column (High-watermark extraction)? → [Solution](09_etl_data_pipelines_and_adf_faq.md#high-watermark-incremental-load-from-azure-sql-to-adls-via-lastmodifieddate)
31. **`[Hard]`** How would you implement incremental data loading in ADF when the source database table has no LastModifiedDate or timestamp column (SQL Change Tracking & Hash Diffing)? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q19-incremental-loading-without-timestamps-change-tracking--hash-diffing)
32. **`[Hard]`** How do you implement Slowly Changing Dimensions (SCD Type 2) in Azure Data Factory using Mapping Data Flows? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q21-slowly-changing-dimensions-scd-type-2-in-adf-mapping-data-flows)
33. **`[Hard]`** Design an ADF pipeline to ingest data from multiple on-premises SQL databases into ADLS Gen2 via Self-Hosted IR and load into Synapse Dedicated SQL Pools. → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q22-multi-database-on-prem-sql-ingestion-to-adls-gen2--azure-synapse-analytics)
34. **`[Hard]`** How would you design an ADF pipeline to process 1,000 files arriving asynchronously at different times in ADLS Gen2? → [Solution](09_etl_data_pipelines_and_adf_faq.md#processing-1000-asynchronously-arriving-files-in-adls-gen2-with-adf)
35. **`[Hard]`** If you are asked to ingest PDF and image files into your pipeline and make them queryable, how would you design it? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q16-unstructured-data-pipeline-design-ingesting-pdfs--images)
36. **`[Hard]`** Explain Repartition vs Coalesce and their use cases in large-scale ETL pipelines. → [Solution](09_etl_data_pipelines_and_adf_faq.md#q33-repartition-vs-coalesce-in-etl-pipelines)
37. **`[Hard]`** How would you implement Change Data Capture (CDC) in a large-scale data pipeline using Debezium and Delta Lake MERGE? → [Solution](09_etl_data_pipelines_and_adf_faq.md#q31-log-based-change-data-capture-cdc-in-large-scale-pipelines)
38. **`[Hard]`** How would you design and enforce data governance using Unity Catalog across multi-team ETL pipelines? → [Solution](09_etl_data_pipelines_and_adf_faq.md#q32-enterprise-data-governance--access-control-using-unity-catalog)
39. **`[Hard]`** How would you design a data quality framework that checks freshness, uniqueness, null-rates, and data drift? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q6-q12--q13-the-5-essential-data-quality-gates--sla-observability)

### Level 3: Orchestrator Concurrency, API Self-Defense & Schema Migrations (Hard / Expert)
40. **`[Hard]`** Your Airflow DAG runs on 3 workers and triggers the same load multiple times. How would you prevent duplicate processing and race conditions? → [Solution](09_etl_data_pipelines_and_adf_faq.md#airflow-multi-worker-duplicate-execution-prevention--distributed-locks)
41. **`[Hard]`** A downstream API used by your ETL pipeline slows to 10 seconds per request and blocks the entire workflow. What three controls should protect the pipeline? → [Solution](09_etl_data_pipelines_and_adf_faq.md#the-3-core-controls-circuit-breakers-token-bucket-rate-limiters--dead-letter-backoff)
42. **`[Hard]`** Rename a critical warehouse column used by multiple downstream pipelines with zero downtime. Walk through the expand/contract migration strategy. → [Solution](09_etl_data_pipelines_and_adf_faq.md#zero-downtime-warehouse-column-renaming-expandcontract-migration-strategy)
43. **`[Hard]`** A source system sends duplicate transactions due to a technical glitch. How would you prevent duplicate records from impacting downstream systems? → [Solution](09_etl_data_pipelines_and_adf_faq.md#immediate-triage--long-term-containment-of-production-duplicate-data)
44. **`[Hard]`** You have a pipeline that runs every hour, but the downstream table shows duplicate records. Walk through your debugging approach step by step. → [Solution](09_etl_data_pipelines_and_adf_faq.md#immediate-triage--long-term-containment-of-production-duplicate-data)
45. **`[Hard]`** An ADF Copy Activity suddenly becomes very slow. What would you investigate? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q7-q8--q10-copy-activity-performance-bottlenecks--failure-isolation)
46. **`[Hard]`** An ADF pipeline fails randomly. How would you determine whether the problem is with ADF, the source, or the target? → [Solution](09_etl_data_pipelines_and_adf_faq.md#adf-q7-q8--q10-copy-activity-performance-bottlenecks--failure-isolation)
47. **`[Hard]`** Your pipeline is stuck in a Queued state. What are the top 3 things you would check? → [Solution](09_etl_data_pipelines_and_adf_faq.md#etl-q14-pipeline-stuck-in-queued-state-top-3-checks)

### Level 4: Production Incident RCA Playbooks & Disaster Containment (Expert)
48. **`[Expert]`** A pipeline succeeds in ADF, but the target table contains incomplete or 0 records. How would you troubleshoot this silent data loss? → [Solution](09_etl_data_pipelines_and_adf_faq.md#silent-data-loss-pipeline-succeeds-in-adf-but-target-table-has-incomplete-data)
49. **`[Expert]`** Your daily ETL pipeline suddenly takes 3 hours instead of 30 minutes. No task failure appears in Airflow. Where do you investigate first? → [Solution](09_etl_data_pipelines_and_adf_faq.md#systematic-rca-playbook-daily-job-exploding-from-30m-to-3h-with-zero-failures)
50. **`[Expert]`** A monthly data load that normally takes 2 hours suddenly takes 8 hours. How would you analyze the bottleneck? → [Solution](09_etl_data_pipelines_and_adf_faq.md#systematic-rca-playbook-daily-job-exploding-from-30m-to-3h-with-zero-failures)
51. **`[Expert]`** The average order delivery time increased by 8 minutes on weekends only. How do you find the root cause? Metric drift RCA playbook. → [Solution](09_etl_data_pipelines_and_adf_faq.md#weekend-metric-drift-rca-delivery-times-spiking-by-8-minutes)
52. **`[Expert]`** What is your emergency action plan if a critical data pipeline fails during peak business hours? The Severity-1 Action Runbook. → [Solution](09_etl_data_pipelines_and_adf_faq.md#peak-business-hour-critical-pipeline-failure-the-severity-1-action-runbook)
53. **`[Expert]`** Describe a large-scale ETL you worked on and explain how you improved performance or reduced cost (End-to-End Enterprise Case Study). → [Solution](09_etl_data_pipelines_and_adf_faq.md#large-scale-production-etl-case-study-cost--performance-optimization)


---

# **10. Data Engineering Architecture & System Design**
*Companion Deep-Dive Guide: [`10_system_design_and_architecture_faq.md`](10_system_design_and_architecture_faq.md) (21 Questions & Production Solutions)*

### Level 1: Core High-Throughput & Fault-Tolerant Pipeline Design (Medium / Hard)
1. **`[Medium]`** How would you design a pipeline for millions of records arriving every hour? Ingest $\to$ Transform $\to$ Serve architecture. → [Solution](10_system_design_and_architecture_faq.md#q1-high-throughput-pipeline-architecture-millions-of-records-per-hour)
2. **`[Medium]`** Explain how you would build a near real-time ingestion system for millions of events per minute. Which components would you choose and why? → [Solution](10_system_design_and_architecture_faq.md#q1-high-throughput-pipeline-architecture-millions-of-records-per-hour)
3. **`[Medium]`** How would you handle late-arriving data in pipeline design? Multi-tier watermarking and backfill reconciliation. → [Solution](10_system_design_and_architecture_faq.md#q2-comprehensive-strategy-for-late-arriving--out-of-order-data)
4. **`[Hard]`** How would you design a pipeline that can recover automatically after failure? Self-healing architecture and stateful replay. → [Solution](10_system_design_and_architecture_faq.md#q3-self-healing--automated-failure-recovery-pipelines)
5. **`[Hard]`** Design an end-to-end architecture for user-watch-history and recommendation feed for global streaming devices. → [Solution](10_system_design_and_architecture_faq.md#global-video-watch-history-lakehouse-architecture-ingest---transform---serve)
6. **`[Hard]`** Design an end-to-end e-commerce data platform architecture handling order placement CDC, inventory updates, clickstream ingestion, and dual-layer analytical serving. → [Solution](10_system_design_and_architecture_faq.md#high-scale-e-commerce-data-platform-architecture)
7. **`[Hard]`** How would you design a data pipeline to integrate structured (RDBMS), semi-structured (JSON/Avro), and unstructured (PDF/images) data with unified quality and schema rescue? → [Solution](10_system_design_and_architecture_faq.md#multi-source-pipeline-design-integrating-structured-semi-structured--unstructured-data)
8. **`[Hard]`** You are responsible for maintaining a production pipeline processing 100 GB daily. How would you optimize execution, minimize shuffles, and implement automated SLA alerting? → [Solution](10_system_design_and_architecture_faq.md#production-pipeline-optimization--observability-for-100-gb-daily-processing-under-strict-slas)

### Level 2: Low-Latency Systems, Recommendation Engines & Spatial Stream Processing (Hard / Expert)
9. **`[Hard]`** Design a distributed pipeline to detect ride anomalies in real time using stream processing. → [Solution](10_system_design_and_architecture_faq.md#real-time-fraud--anomaly-detection-architecture-500ms-sla-3m-daily-orders)
10. **`[Hard]`** A restaurant processes 3 million orders daily. Design a real-time pipeline to detect delivery partner fraud (fake deliveries) with latency under 500ms. → [Solution](10_system_design_and_architecture_faq.md#real-time-fraud--anomaly-detection-architecture-500ms-sla-3m-daily-orders)
11. **`[Hard]`** Design an event-driven pipeline for surge pricing based on live order demand and delivery partner availability with real-time price adjustment per zone (Uber H3 Spatial Indexing). → [Solution](10_system_design_and_architecture_faq.md#event-driven-real-time-surge-pricing-engine-with-spatial-indexing)
12. **`[Hard]`** How would you scale a pipeline that ingests 100M GPS events per day and still serves analytics queries under 1 second? → [Solution](10_system_design_and_architecture_faq.md#high-throughput-gps-stream-ingestion-100m-gps-pingsday-under-1-second-analytics)
13. **`[Expert]`** Design a real-time personalized recommendation engine for 80M to 400M users with sub-200ms latency, handling cold start for new users. → [Solution](10_system_design_and_architecture_faq.md#personalized-real-time-recommendation-engine-at-scale-80m-to-400m-users-200ms)
14. **`[Expert]`** Design a real-time inventory management system for 50 million products where inventory reflects within 500ms of a purchase and overselling is prevented during flash sales. → [Solution](10_system_design_and_architecture_faq.md#real-time-flash-sale-inventory-management-50m-products--zero-overselling)

### Level 3: Emergency Scaling, Disaster Incidents & Multi-Tenant Frameworks (Expert)
15. **`[Expert]`** A sale starts in 48 hours. Orders per second jumped from 500 to 50,000. Your pipeline is not designed for this. What do you do right now? Emergency 100x surge architecture. → [Solution](10_system_design_and_architecture_faq.md#emergency-48-hour-100x-traffic-surge-architecture-500-to-50000-orderssec)
16. **`[Expert]`** A deployment or pricing algorithm bug gave 90% discount on 10,000 products for 20 minutes, generating 800,000 orders. How do you detect this in real-time, stop it, and handle the orders in production? → [Solution](10_system_design_and_architecture_faq.md#algorithmic-pricing-bug-containment-800000-faulty-orders-in-20-minutes)
17. **`[Expert]`** A seller analytics pipeline ingests data from 500,000 sellers daily, each with a different data format. Design a scalable ingestion framework without 500,000 separate pipelines (Metadata-Driven Engine). → [Solution](10_system_design_and_architecture_faq.md#metadata-driven-multi-tenant-seller-ingestion-engine)
18. **`[Expert]`** You are designing a data pipeline integrating data from multiple engineering teams with conflicting schedules and formats. How do you govern dependencies using Data Contracts and decoupled event triggers? → [Solution](10_system_design_and_architecture_faq.md#multi-team-dependency-management-data-contracts--pipeline-sla-governance)
19. **`[Expert]`** How would you design a data platform to support regulatory and compliance reporting in a banking environment (BCBS 239 / Basel III, Bitemporal Data Modeling)? → [Solution](10_system_design_and_architecture_faq.md#enterprise-regulatory-data-platform-bitemporal-modeling--audit-lineage)
20. **`[Expert]`** A critical regulatory report contains inconsistent customer balances. How would you investigate and resolve the issue? Regulatory incident containment. → [Solution](10_system_design_and_architecture_faq.md#enterprise-regulatory-data-platform-bitemporal-modeling--audit-lineage)
21. **`[Expert]`** Enterprise System Design Capstone: Global Real-Time Analytics Lakehouse — complete multi-region ingestion, medallion transformation, and ultra-low-latency serving blueprint. → [Solution](10_system_design_and_architecture_faq.md#enterprise-system-design-capstone-global-real-time-analytics-lakehouse)
\n