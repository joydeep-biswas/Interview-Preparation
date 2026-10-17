# 02. Python & Programming Concepts: Interview Questions & Deep-Dive Solutions

A comprehensive, production-grade guide answering Python and core programming interview scenarios for Data Engineers. Covers large file streaming, chunked memory optimization, nested JSON flattening, decorators, exception handling patterns, deduplication algorithms, and prefix sums.

---

## Table of Contents & Question Mapping

1. **Large File Processing & 10GB CSV Memory Optimization**
   - [Q1. Chunked CSV Reading & Filtering Output Stream](#q1-chunked-csv-reading--filtering-output-stream)
   - [Q4. Memory Optimization Strategies for 10GB CSV Processing](#q4-memory-optimization-strategies-for-10gb-csv-processing)
2. **Nested JSON Flattening & Shredding into Pandas**
   - [Q2. Nested JSON Flattening & Shredding into Pandas](#q2-nested-json-flattening--shredding-into-pandas)
3. **Deduplication Algorithms: Python Sequences & Pandas**
   - [Q3. Order-Preserving List Deduplication](#q3-order-preserving-list-deduplication)
   - [Q5. High-Performance Deduplication in Pandas](#q5-high-performance-deduplication-in-pandas)
4. **Python Decorators & Production Engineering Wrappers**
   - [Q6. Python Decorators in Production Data Pipelines](#q6-python-decorators-in-production-data-pipelines)
5. **Variable-Length Arguments (*args & **kwargs)**
   - [Q7. Variable-Length Arguments (*args & **kwargs) in Pipeline Design](#q7-variable-length-arguments-args--kwargs-in-pipeline-design)
6. **Enterprise Exception Handling & Dead-Letter Patterns**
   - [Q8. Enterprise Exception Handling & Dead-Letter Queue Patterns](#q8-enterprise-exception-handling--dead-letter-queue-patterns)
7. **Algorithmic Foundations: Prefix Sum Implementation & Complexity**
   - [Prefix Sum Array Transformation & Algorithmic Complexity](#prefix-sum-array-transformation--algorithmic-complexity)
8. **Real-Time Stream Structures & High-Performance Caching**
   - [Continuous Stream Running Median via Dual Heaps](#continuous-stream-running-median-via-dual-heaps)
   - [Least Recently Used (LRU) Cache Architecture](#least-recently-used-lru-cache-architecture)
9. **Graph Algorithms & Network Topology in Data Engineering**
   - [Cycle Detection in Directed Transportation Networks](#cycle-detection-in-directed-transportation-networks)
10. **Merging & Streak Tracking in Distributed Data**
    - [Merge K Sorted Streams / Linked Lists](#merge-k-sorted-streams--linked-lists)
    - [Longest Continuous Ride Streak / Consecutive Sequence](#longest-continuous-ride-streak--consecutive-sequence)
11. **Python Concurrency Architecture: Threading vs Multiprocessing vs AsyncIO**
    - [Multithreading vs Multiprocessing under the Python GIL](#multithreading-vs-multiprocessing-under-the-python-gil)
12. **Core Python Idioms & Algorithmic Problem Solving**
    - [Infinite Generator Streaming: Fibonacci Series](#infinite-generator-streaming-fibonacci-series)
    - [Sorting Without Built-In Sort: Pure Python Merge Sort](#sorting-without-built-in-sort-pure-python-merge-sort)
    - [Two-Pointer Palindrome Verification](#two-pointer-palindrome-verification)
    - [Factorial Calculation: Iterative vs Memoized](#factorial-calculation-iterative-vs-memoized)
    - [Word Frequency Tokenizer & Counter](#word-frequency-tokenizer--counter)
    - [Single-Pass Extreme Value Detection (Min and Max)](#single-pass-extreme-value-detection-min-and-max)
13. **Enterprise String, List, and Dictionary Coding Scenarios**
    - [Q9. Advanced Data Manipulation on Strings, Lists, and Dictionaries](#q9-advanced-data-manipulation-on-strings-lists-and-dictionaries)
14. **Disjoint Collections, String Parsing & Internal Memory Mechanics**
    - [Q10. Pairing Common Keys Between Disjoint Lists & Dictionary Intersection](#q10-pairing-common-keys-between-disjoint-lists--dictionary-intersection)
    - [Q11. Character Frequency & Duplicate Detection in High-Volume Strings](#q11-character-frequency--duplicate-detection-in-high-volume-strings)
    - [Q12. Python List Appending Mechanics: append() vs extend() vs + vs In-Place Slicing](#q12-python-list-appending-mechanics-append-vs-extend-vs--vs-in-place-slicing)
    - [Q13. Email Username Extraction with Resilient Regex & RFC Validation](#q13-email-username-extraction-with-resilient-regex--rfc-validation)
15. **Monotonic Stacks & High-Scale Log Stream Analysis**
    - [Q14. The Stock Span Problem via Monotonic Stack](#q14-the-stock-span-problem-via-monotonic-stack)
    - [Q15. Large-Scale Log File Analysis & Most Frequently Accessed Database Tables](#q15-large-scale-log-file-analysis--most-frequently-accessed-database-tables)
---

# 1. Large File Processing & 10GB CSV Memory Optimization

---

### Q1. Chunked CSV Reading & Filtering Output Stream
**Topic Covered:** Streaming I/O, Generators & Chunked Data Ingestion  
**Question:** How would you read a large CSV file in chunks, filter only required rows, and write the output into a new file using Python?

#### Problem Statement & Memory Constraints
A standard Python process attempting to execute `pd.read_csv("10gb_file.csv")` on a machine with 8GB or 16GB of RAM causes an immediate operating system crash (`MemoryError` or Linux Out-Of-Memory OOM Killer). In memory, Pandas DataFrames often consume **2× to 3× the raw CSV file size on disk** due to Python object overhead and string boxing.

#### Core Optimization Strategies
```mermaid
flowchart TD
    subgraph LargeFile10GB ["Processing a 10GB File in Python: Memory Optimization Matrix"]
        A["Processing a 10GB File"] --> B["Strategy 1: Native Streaming (Generators / csv.reader)<br/>Memory: O(1) row buffer (~10 KB)"]
        A --> C["Strategy 2: Pandas Chunksize<br/>Process batch-by-batch (chunksize=100_000)"]
        A --> D["Strategy 3: Downcasting & Category Types<br/>int64 -> int16, float64 -> float32, object -> category"]
        A --> E["Strategy 4: Column Projection (usecols)<br/>Discard unneeded columns before parsing"]
        A --> F["Strategy 5: Out-of-Core / Modern Engines<br/>Polars Streaming Engine or PyArrow Datasets"]
    end

    style LargeFile10GB fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style A fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style B fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style C fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style D fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style E fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style F fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
```

#### Production Implementation: Chunked Filtering with Pandas (Q1)
```python
import os
import pandas as pd

def process_large_csv_in_chunks(
    input_file: str, 
    output_file: str, 
    filter_column: str, 
    target_value: str, 
    chunk_size: int = 100_000
) -> int:
    """
    Reads a large CSV in chunks, filters rows matching criteria, 
    and writes to an output CSV in append mode without loading the full dataset.
    """
    total_retained_rows = 0
    header_written = False

    # Remove existing output file if present
    if os.path.exists(output_file):
        os.remove(output_file)

    # Stream the file in batches
    with pd.read_csv(input_file, chunksize=chunk_size, low_memory=False) as reader:
        for chunk in reader:
            # Vectorized Boolean filtering
            filtered_chunk = chunk[chunk[filter_column] == target_value]
            
            if not filtered_chunk.empty:
                total_retained_rows += len(filtered_chunk)
                # Write to disk: write header only for the very first chunk
                filtered_chunk.to_csv(
                    output_file, 
                    mode="a", 
                    index=False, 
                    header=not header_written
                )
                header_written = True

    return total_retained_rows
```

---

### Q4. Memory Optimization Strategies for 10GB CSV Processing
**Topic Covered:** Out-of-Core Processing, Downcasting, Categoricals & Polars  
**Question:** In Python, how would you optimize data transformations when processing a 10GB CSV file?

#### Production Implementation: Memory Optimization Playbook for 10GB CSV (Q4)

##### Downcasting Schema & PyArrow Engine
```python
import polars as pl
import pyarrow.dataset as ds

# Method A: Polars Out-Of-Core Streaming
def process_10gb_with_polars(input_path: str, output_path: str):
    """
    Polars uses Apache Arrow memory layout and a Rust-backed streaming engine
    capable of executing queries out-of-core with minimal RAM.
    """
    query = (
        pl.scan_csv(input_path)
        .filter(pl.col("status") == "COMPLETED")
        .select([
            pl.col("customer_id").cast(pl.Int32),
            pl.col("transaction_date").str.to_date(),
            pl.col("amount").cast(pl.Float32)
        ])
        .group_by("customer_id")
        .agg(pl.col("amount").sum().alias("total_spend"))
    )
    
    # Executes in streaming mode with low memory footprint
    query.sink_parquet(output_path)
```

##### Pandas Memory Profiling & Type Optimization
```python
import numpy as np
import pandas as pd

def optimize_pandas_memory(df: pd.DataFrame) -> pd.DataFrame:
    """Downcasts numeric types and converts repeated strings to category."""
    for col in df.columns:
        col_type = df[col].dtype
        if col_type == "int64":
            df[col] = pd.to_numeric(df[col], downcast="integer")
        elif col_type == "float64":
            df[col] = pd.to_numeric(df[col], downcast="float")
        elif col_type == "object" and df[col].nunique() / len(df[col]) < 0.2:
            # Low cardinality string -> category reduces memory by 80-90%
            df[col] = df[col].astype("category")
    return df
```

---

# 2. Nested JSON Flattening & Shredding into Pandas

---

### Q2. Nested JSON Flattening & Shredding into Pandas
**Topic Covered:** Semi-Structured Data Ingestion & JSON Normalization  
**Question:** You have a nested dictionary of JSON records from an API. How would you flatten it and load it into a Pandas DataFrame?

#### Core Concept: Semi-Structured JSON Shredding
REST APIs commonly return hierarchical payloads containing nested dictionaries and embedded arrays of objects. For relational analytical modeling, these structures must be unnested (shredded) into tabular rows and columns.

#### Visual Architecture
```json
{
  "user_id": 101,
  "profile": {"name": "Alice", "country": "US"},
  "orders": [
    {"order_id": 5001, "amount": 120.0},
    {"order_id": 5002, "amount": 85.5}
  ]
}
```

$$\Big\downarrow \quad \text{\small \texttt{pd.json\_normalize(record\_path='orders', meta=['user\_id', ['profile', 'name']])}}$$

| `user_id` | `profile.name` | `order_id` | `amount` |
| :---: | :--- | :---: | :---: |
| 101 | Alice | 5001 | 120.0 |
| 101 | Alice | 5002 | 85.5 |

#### Production Implementation
```python
import json
import pandas as pd

api_payload = [
    {
        "user_id": 101,
        "profile": {
            "first_name": "Alice",
            "last_name": "Smith",
            "location": {"city": "Seattle", "state": "WA"}
        },
        "orders": [
            {"order_id": "ORD-1", "sku": "TECH-01", "price": 49.99},
            {"order_id": "ORD-2", "sku": "BOOK-05", "price": 14.50}
        ]
    },
    {
        "user_id": 102,
        "profile": {
            "first_name": "Bob",
            "last_name": "Jones",
            "location": {"city": "Austin", "state": "TX"}
        },
        "orders": [
            {"order_id": "ORD-3", "sku": "HOME-09", "price": 120.00}
        ]
    }
]

# Scenario 1: Flattening nested dictionary properties (No Array Unnesting)
flat_users_df = pd.json_normalize(api_payload, sep="_")
print(flat_users_df.columns.tolist())
# Output: ['user_id', 'orders', 'profile_first_name', 'profile_last_name', 'profile_location_city', 'profile_location_state']

# Scenario 2: Unnesting Embedded Arrays while Retaining Parent Metadata
flat_orders_df = pd.json_normalize(
    api_payload,
    record_path=["orders"],
    meta=[
        "user_id",
        ["profile", "first_name"],
        ["profile", "location", "city"]
    ],
    sep="_"
)
print(flat_orders_df)
# Output columns: ['order_id', 'sku', 'price', 'user_id', 'profile_first_name', 'profile_location_city']
```

---

# 3. Deduplication Algorithms: Python Sequences & Pandas

---

### Q3. Order-Preserving List Deduplication
**Topic Covered:** In-Memory Deduplication & Hash Set Lookups  
**Question:** Write a Python script to remove duplicates from a list while maintaining the original order.

#### Question 3: Order-Preserving List Deduplication

##### Why `list(set(seq))` Fails
Calling `set()` strips out duplicates, but sets in Python are **unordered hash tables**. Converting back to a list corrupts the chronological or sequential ordering of events.

##### Optimal Solution: `dict.fromkeys()` (Python 3.7+)
Since Python 3.7, dictionaries maintain insertion order by standard language specification. `dict.fromkeys(seq)` deduplicates keys in $O(N)$ time while preserving the original sequence.

```python
def deduplicate_preserve_order(sequence: list) -> list:
    """
    Removes duplicates from a sequence while maintaining insertion order.
    Time Complexity: O(N) amortized
    Space Complexity: O(N)
    """
    return list(dict.fromkeys(sequence))

# Test Example:
raw_event_ids = ["e3", "e1", "e2", "e1", "e4", "e3", "e5"]
deduped = deduplicate_preserve_order(raw_event_ids)
print(deduped)
# Result: ['e3', 'e1', 'e2', 'e4', 'e5'] (Exact original order maintained!)
```

---

### Q5. High-Performance Deduplication in Pandas
**Topic Covered:** Vectorized DataFrame Deduplication & Deterministic Retain Rules  
**Question:** How do you handle and remove duplicates efficiently using pandas?

#### Question 5: Production Deduplication in Pandas
In distributed ETL pipelines, duplicate records can arrive due to network retries, streaming replays, or source system syncs.

```python
import pandas as pd

df = pd.DataFrame({
    "customer_id": [1, 2, 1, 3, 2],
    "email": ["a@test.com", "b@test.com", "a@test.com", "c@test.com", "b@test.com"],
    "updated_at": ["2024-01-01", "2024-01-05", "2024-01-08", "2024-01-02", "2024-01-03"],
    "score": [10, 20, 15, 30, 25]
})

# 1. Identify Duplicates (Audit logging)
duplicate_mask = df.duplicated(subset=["customer_id"], keep=False)
print(f"Detected {duplicate_mask.sum()} duplicate rows:")
print(df[duplicate_mask])

# 2. Sort to guarantee determinism before deduplicating
df["updated_at"] = pd.to_datetime(df["updated_at"])
df_sorted = df.sort_values(by=["customer_id", "updated_at"], ascending=[True, False])

# 3. Retain the single latest record per customer
df_deduped = df_sorted.drop_duplicates(
    subset=["customer_id"], 
    keep="first", 
    ignore_index=True
)
print("Deduplicated Result (Latest Record Kept):")
print(df_deduped)
```

---

# 4. Python Decorators & Production Engineering Wrappers

---

### Q6. Python Decorators in Production Data Pipelines
**Topic Covered:** First-Class Functions, Closures & Production Wrapper Syntaxes  
**Questions Covered:**
- Explain Python Decorators with an example.
- What are decorators?

#### Definition & Core Concepts
A **decorator** is a callable design pattern that takes another function as an argument, extends or alters its behavior without modifying its source code, and returns the modified wrapper function. It relies on Python closures and first-class functions.

#### Real-World Production Use Cases in Data Pipelines
1. **Execution Profiling:** Logging query runtime and memory consumption.
2. **Transient Error Retries:** Exponential backoff retry logic for REST APIs or cloud database connections.
3. **Data Quality Assertion:** Validating DataFrame schemas before and after transformation execution.

#### Production Implementation: Production Retry & Latency Tracker
```python
import functools
import logging
import time
from typing import Any, Callable

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("ETL_Pipeline")

def retry_with_backoff(max_retries: int = 3, initial_delay: float = 1.0, backoff_factor: float = 2.0):
    """
    Enterprise decorator: Automatically retries failing API/DB calls with exponential backoff.
    """
    def decorator(func: Callable) -> Callable:
        @functools.wraps(func)
        def wrapper(*args: Any, **kwargs: Any) -> Any:
            delay = initial_delay
            last_exception = None
            
            for attempt in range(1, max_retries + 1):
                try:
                    start_time = time.perf_counter()
                    result = func(*args, **kwargs)
                    elapsed = time.perf_counter() - start_time
                    logger.info(f"Function '{func.__name__}' succeeded on attempt {attempt} in {elapsed:.3f}s")
                    return result
                except Exception as exc:
                    last_exception = exc
                    logger.warning(
                        f"Attempt {attempt}/{max_retries} failed for '{func.__name__}' with error: {exc}. "
                        f"Retrying in {delay}s..."
                    )
                    time.sleep(delay)
                    delay *= backoff_factor
                    
            logger.error(f"Function '{func.__name__}' exhausted all {max_retries} retries.")
            raise last_exception
        return wrapper
    return decorator

# Usage in Data Ingestion
@retry_with_backoff(max_retries=3, initial_delay=0.5)
def fetch_api_records(endpoint_url: str) -> dict:
    # Simulated network call
    return {"status": 200, "data": [1, 2, 3]}
```

---

# 5. Variable-Length Arguments (*args & **kwargs)

---

### Q7. Variable-Length Arguments (*args & **kwargs) in Pipeline Design
**Topic Covered:** Positional & Keyword Argument Packing/Unpacking  
**Question:** Explain *args and **kwargs.

#### Core Concept & Mechanics
In Python, `*args` and `**kwargs` allow a function to accept an arbitrary, unspecified number of arguments:
- `*args` (Non-Keyword Arguments): Packs positional arguments into a **tuple**.
- `**kwargs` (Keyword Arguments): Packs named key-value arguments into a **dictionary**.

```mermaid
flowchart TD
    Call["Function Invocation:<br/><code>my_func('bronze', 100, cluster='prod', retries=3)</code>"]
    
    Call -->|"Positional Arguments"| Args["(args (Tuple); <code>('bronze', 100)</code>)"]
    Call -->|"Keyword Arguments"| Kwargs["(*kwargs (Dict); <code>{'cluster': 'prod', 'retries': 3}</code>)"]

    classDef inv fill:#1e293b,stroke:#8b5cf6,stroke-width:2px,color:#f8fafc;
    classDef tupleNode fill:#1e293b,stroke:#3b82f6,stroke-width:2px,color:#f8fafc;
    classDef dictNode fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#f8fafc;

    class Call inv;
    class Args tupleNode;
    class Kwargs dictNode;
```

#### Production Data Engineering Pipeline Pattern
In production frameworks, `*args` and `**kwargs` allow creating generic orchestration wrappers and pluggable hook systems:

```python
from typing import Callable, Any

def execute_pipeline_task(task_name: str, task_fn: Callable, *args: Any, **kwargs: Any) -> Any:
    """
    Generic task executor that passes positional and keyword parameters 
    transparently to downstream ingestion functions.
    """
    print(f"[START] Executing task: {task_name}")
    print(f"  Positional args: {args}")
    print(f"  Keyword options: {kwargs}")
    
    # Unpack arguments directly into the target function
    result = task_fn(*args, **kwargs)
    
    print(f"[COMPLETED] Task: {task_name}")
    return result

def load_table(schema: str, table: str, mode: str = "append", validate_nulls: bool = True):
    return f"Loaded {schema}.{table} (mode={mode}, validate={validate_nulls})"

# Dynamic invocation:
execute_pipeline_task(
    "LoadCustomerGold",
    load_table,
    "gold_db", "dim_customers", # Passed as *args
    mode="overwrite", validate_nulls=False # Passed as **kwargs
)
```

---

# 6. Enterprise Exception Handling & Dead-Letter Patterns

---

### Q8. Enterprise Exception Handling & Dead-Letter Queue Patterns
**Topic Covered:** Defensive Data Pipelines, Fault Tolerance & Quarantine Queues  
**Question:** Explain a real scenario where you implemented exception handling.

#### Real Production Scenario: Ingesting 100,000 JSON Records with Corrupt Payloads
During nightly ingestion from a vendor third-party webhook, records can contain corrupt formats (e.g., malformed JSON, missing critical keys, invalid dates). 

**The Anti-Pattern:** A single unhandled exception halts the pipeline after processing 80,000 records, discarding the entire run.

**The Solution:** Implement a **Dead-Letter Queue (DLQ)** pattern with structured `try...except...finally` error handling:
1. Valid records are batched and written to the primary Gold destination.
2. Malformed records are caught, enriched with metadata (error trace, timestamp, raw payload), and diverted to a Dead-Letter storage path for debugging.
3. System resources (database connections, file handles) are deterministically closed in `finally`.

```mermaid
flowchart TD
    subgraph DLQFlow ["Enterprise Dead-Letter Queue (DLQ) Defensive Exception Architecture"]
        A["Incoming Raw Payloads"] --> B["Iterate Records"]
        B --> C{"try: Parse & Validate"}
        C -->|Success| D["Append to Valid Batch<br/>(Write to Primary Table)"]
        C -->|Exception| E["catch DataQualityError<br/>Enrich with Error Stack"]
        E --> F["Append to Quarantine Batch<br/>(Dead-Letter Queue S3/ADLS)"]
        D & F --> G["finally: Flush Buffers & Log Metrics"]
    end

    style DLQFlow fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c084fc
    style A fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style B fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style C fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style D fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
    style E fill:#7f1d1d,stroke:#ef4444,stroke-width:2px,color:#fecaca
    style F fill:#7f1d1d,stroke:#ef4444,stroke-width:2px,color:#fecaca
    style G fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
```

#### Production Implementation
```python
import json
import logging
from datetime import datetime

logger = logging.getLogger("DeadLetterPipeline")

def process_event_stream_with_dlq(raw_events: list[str], valid_sink, dlq_sink):
    success_count = 0
    failure_count = 0
    
    for raw_record in raw_events:
        try:
            # 1. Parse JSON payload
            record = json.loads(raw_record)
            
            # 2. Schema validation
            if "event_id" not in record or "timestamp" not in record:
                raise ValueError("Missing mandatory keys: 'event_id' or 'timestamp'")
            
            # 3. Type parsing
            event_time = datetime.fromisoformat(record["timestamp"])
            
            # Valid record saved to primary sink
            valid_sink.write({"event_id": record["event_id"], "timestamp": event_time})
            success_count += 1
            
        except json.JSONDecodeError as jde:
            # Quarantine malformed JSON
            failure_count += 1
            dlq_sink.write({
                "raw_payload": raw_record,
                "error_type": "JSON_CORRUPT",
                "error_message": str(jde),
                "quarantine_time": datetime.utcnow().isoformat()
            })
            
        except (ValueError, KeyError) as ve:
            # Quarantine schema validation violations
            failure_count += 1
            dlq_sink.write({
                "raw_payload": raw_record,
                "error_type": "SCHEMA_VIOLATION",
                "error_message": str(ve),
                "quarantine_time": datetime.utcnow().isoformat()
            })
            
        except Exception as unk:
            # Catch unexpected system faults
            logger.critical(f"Unexpected fatal error: {unk}", exc_info=True)
            raise
            
        finally:
            # Periodic heartbeats / telemetry
            pass
            
    logger.info(f"Ingestion finished: {success_count} succeeded, {failure_count} sent to DLQ.")
```

---

# 7. Algorithmic Foundations: Prefix Sum Implementation & Complexity

---

### Prefix Sum Array Transformation & Algorithmic Complexity
**Topic Covered:** Cumulative Range Aggregation & In-Place Pointer Mathematics  
**Covered Problem:**
- **Given**: `[1, 2, 3, 4]`
- **Generate**: `[1, 3, 6, 10]`
- **Follow-ups**: Explain the approach, time complexity, and space complexity.

#### Algorithmic Definition
A **Prefix Sum** (cumulative sum array) is an algorithm where each element at index $i$ in the output array is the sum of all elements from index $0$ to index $i$ of the input array:
$$P[i] = \sum_{k=0}^{i} A[k] = P[i-1] + A[i]$$

#### Why Prefix Sums Matter in Data Engineering
1. **$O(1)$ Range Sum Queries:** Once a prefix sum array is precomputed in $O(N)$, the sum of any arbitrary slice from index $L$ to $R$ can be evaluated in $O(1)$ constant time:
   $$\text{Sum}(L, R) = P[R] - P[L-1]$$
2. **Cumulative Financial & Analytical Aggregations:** Computes running balances, rolling revenue, and cumulative user retention.

#### Code Implementations

##### Approach 1: Out-of-Place Iterative Accumulation ($O(N)$ Time, $O(N)$ Space)
```python
def compute_prefix_sum(nums: list[int]) -> list[int]:
    """
    Computes prefix sum array out-of-place.
    Time Complexity: O(N) - single linear pass through the array.
    Space Complexity: O(N) - allocating a new output array of length N.
    """
    if not nums:
        return []
    
    prefix = [0] * len(nums)
    prefix[0] = nums[0]
    
    for i in range(1, len(nums)):
        prefix[i] = prefix[i - 1] + nums[i]
        
    return prefix

# Test:
arr = [1, 2, 3, 4]
print(compute_prefix_sum(arr))  # Output: [1, 3, 6, 10]
```

##### Approach 2: In-Place Mutation ($O(N)$ Time, $O(1)$ Auxiliary Space)
```python
def compute_prefix_sum_inplace(nums: list[int]) -> list[int]:
    """
    Mutates the array in-place to achieve O(1) auxiliary memory.
    Time Complexity: O(N)
    Space Complexity: O(1) auxiliary memory (excluding input array).
    """
    for i in range(1, len(nums)):
        nums[i] += nums[i - 1]
    return nums

# Test:
arr = [1, 2, 3, 4]
compute_prefix_sum_inplace(arr)
print(arr)  # Output: [1, 3, 6, 10]
```

##### Approach 3: Python Standard Library (`itertools.accumulate`)
```python
import itertools

arr = [1, 2, 3, 4]
prefix_gen = list(itertools.accumulate(arr))
print(prefix_gen)  # Output: [1, 3, 6, 10]
```

#### Complexity Analysis Matrix
| Approach | Time Complexity | Auxiliary Space Complexity | Mutation Risk |
| :--- | :--- | :--- | :--- |
| **Out-of-Place Iterative** | $O(N)$ | $O(N)$ | Safe (Leaves input unchanged) |
| **In-Place Mutation** | $O(N)$ | $O(1)$ | High (Modifies input array directly) |
| **`itertools.accumulate`** | $O(N)$ (C-optimized) | $O(N)$ or $O(1)$ if generator | Safe |


---

# 8. Real-Time Stream Structures & High-Performance Caching

---

### Continuous Stream Running Median via Dual Heaps
**Topic Covered:** Streaming Analytics, Online Median Computation & Heap Invariants  
**Questions Covered:**
- Given a stream of integers, design a data structure to return the median at any time.
- Given an unbounded stream of integers, design a data structure to return the running median at any time in $O(1)$ lookup time and $O(\log N)$ insertion time.

#### Core Dilemma & Mathematical Mechanics
In high-throughput sensor telemetry or live financial trade pipelines, computing the median of $N$ items naively requires sorting ($O(N \log N)$), which is impossible for continuous streams.
To maintain the median dynamically:
- Maintain two balanced priority queues (heaps):
  1. **Max-Heap (`small`):** Stores the smaller half of numbers. Max-heap root gives the largest of the small numbers.
  2. **Min-Heap (`large`):** Stores the larger half of numbers. Min-heap root gives the smallest of the large numbers.
- **Invariants:**
  1. Every element in `small` $\le$ every element in `large`.
  2. Balancing condition: `len(small) == len(large)` (even elements) OR `len(small) == len(large) + 1` (odd elements).

#### Visual Flow
```mermaid
flowchart TD
    Num["Incoming Number: x"] --> Check{"x <= max(small)?"}
    Check -->|Yes| PushSmall["heappush(small, -x)"]
    Check -->|No| PushLarge["heappush(large, x)"]
    PushSmall & PushLarge --> Balance["Rebalance Heaps<br/>len(small) in [len(large), len(large)+1]"]
    Balance --> Median{"len(small) > len(large)?"}
    Median -->|Yes: Odd Elements| R1["Median = -small[0]"]
    Median -->|No: Even Elements| R2["Median = (-small[0] + large[0]) / 2.0"]

    style Num fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Balance fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style R1 fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
    style R2 fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
```

#### Production Implementation
```python
import heapq

class MedianFinder:
    """
    Maintains a continuous stream running median using two balanced heaps.
    Time Complexity:
      - add_num: O(log N)
      - find_median: O(1)
    Space Complexity: O(N) to store incoming elements.
    """
    def __init__(self):
        # Max-heap for the smaller half (Python only has min-heap, so negate values)
        self.small = [] 
        # Min-heap for the larger half
        self.large = [] 

    def add_num(self, num: int) -> None:
        # Step 1: Push to max-heap (small half)
        if not self.small or num <= -self.small[0]:
            heapq.heappush(self.small, -num)
        else:
            heapq.heappush(self.large, num)

        # Step 2: Rebalance heaps to satisfy invariant: len(small) == len(large) or len(large) + 1
        if len(self.small) > len(self.large) + 1:
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        elif len(self.large) > len(self.small):
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -val)

    def find_median(self) -> float:
        if not self.small and not self.large:
            raise ValueError("No elements in stream.")
        
        if len(self.small) > len(self.large):
            return float(-self.small[0])
        else:
            return (-self.small[0] + self.large[0]) / 2.0

# Driver Test:
mf = MedianFinder()
for n in [5, 15, 1, 3]:
    mf.add_num(n)
    print(f"Added {n}, Current Median: {mf.find_median()}")
# Output:
# Added 5, Current Median: 5.0
# Added 15, Current Median: 10.0
# Added 1, Current Median: 5.0
# Added 3, Current Median: 4.0
```

---

### Least Recently Used (LRU) Cache Architecture
**Topic Covered:** In-Memory Caching, Doubly Linked Lists & Hash Maps  
**Questions Covered:**
- Implement an LRU (Least Recently Used) cache.
- Implement an LRU (Least Recently Used) cache with $O(1)$ `get()` and `put()` operations from scratch.

#### Architectural Principles
1. **Hash Map (`dict`):** Provides $O(1)$ key lookup to locate nodes in memory.
2. **Doubly Linked List (DLL):** Enables $O(1)$ node removal and insertion at head/tail without shifting array elements.
3. **Head:** Most Recently Used (MRU) sentinel.
4. **Tail:** Least Recently Used (LRU) sentinel. Evictions always occur at `tail.prev`.

#### Production Implementation from Scratch (Without `collections.OrderedDict`)
```python
from typing import Optional

class Node:
    def __init__(self, key: int = 0, value: int = 0):
        self.key = key
        self.value = value
        self.prev: Optional['Node'] = None
        self.next: Optional['Node'] = None

class LRUCache:
    """
    Least Recently Used Cache with strict O(1) get and put operations.
    Built using a Hash Map + Doubly Linked List.
    """
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache: dict[int, Node] = {}
        
        # Sentinels (dummy head and tail to eliminate null pointer checks)
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node: Node) -> None:
        """Splices node out of the doubly linked list."""
        prev_node = node.prev
        next_node = node.next
        prev_node.next = next_node
        next_node.prev = prev_node

    def _add_to_head(self, node: Node) -> None:
        """Inserts node immediately after head sentinel (MRU position)."""
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        node = self.cache[key]
        # Move node to head (MRU)
        self._remove(node)
        self._add_to_head(node)
        return node.value

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            node = self.cache[key]
            node.value = value
            self._remove(node)
            self._add_to_head(node)
        else:
            if len(self.cache) >= self.capacity:
                # Evict LRU node (node right before tail)
                lru_node = self.tail.prev
                self._remove(lru_node)
                del self.cache[lru_node.key]

            new_node = Node(key, value)
            self.cache[key] = new_node
            self._add_to_head(new_node)

# Test Verification:
cache = LRUCache(2)
cache.put(1, 10)
cache.put(2, 20)
print(cache.get(1))    # returns 10 (key 1 is now MRU)
cache.put(3, 30)       # evicts key 2
print(cache.get(2))    # returns -1 (evicted!)
```

---

# 9. Graph Algorithms & Network Topology in Data Engineering

---

### Cycle Detection in Directed Transportation Networks
**Topic Covered:** Directed Graphs, Pipeline DAG Validation, Cycle Detection & Topological Sort  
**Questions Covered:**
- Detect a cycle in a directed graph representing trip routes between cities.
- Detect a cycle in a directed graph representing trip routes between cities or dependency tasks in an orchestration engine.

#### Real-World Data Engineering Relevance
1. **Pipeline DAG Scheduling:** An orchestrator (like Apache Airflow or dbt) must detect circular dependencies (`A -> B -> C -> A`) prior to compilation.
2. **Transportation Network Auditing:** Identifying infinite routing loops in ride-sharing transit networks.

#### Algorithms:
- **Kahn's Algorithm (BFS via In-Degree):** If topological sort processes fewer nodes than $|V|$, a cycle exists.
- **Three-Color DFS:** Nodes are marked `0` (Unvisited), `1` (Visiting/In Recursion Stack), or `2` (Visited). If DFS reaches a node marked `1`, a back-edge (cycle) is detected.

#### Production Implementation: Three-Color DFS
```python
from collections import defaultdict
from typing import Dict, List, Tuple

def has_route_cycle(routes: List[Tuple[str, str]]) -> Tuple[bool, List[str]]:
    """
    Detects if a directed route graph contains cycles using 3-color DFS.
    Returns (has_cycle, cycle_path_preview).
    Time Complexity: O(V + E)
    Space Complexity: O(V + E)
    """
    adj: Dict[str, List[str]] = defaultdict(list)
    nodes = set()
    for origin, dest in routes:
        adj[origin].append(dest)
        nodes.add(origin)
        nodes.add(dest)

    # 0 = WHITE (unvisited), 1 = GRAY (visiting), 2 = BLACK (finished)
    color: Dict[str, int] = {node: 0 for node in nodes}
    parent: Dict[str, str] = {}
    cycle_nodes: List[str] = []

    def dfs(u: str) -> bool:
        color[u] = 1 # Mark as visiting
        for v in adj[u]:
            if color[v] == 1:
                # Cycle found! Back-edge detected: u -> v
                cycle_nodes.append(f"{u} -> {v}")
                return True
            if color[v] == 0:
                parent[v] = u
                if dfs(v):
                    return True
        color[u] = 2 # Mark as fully processed
        return False

    for node in nodes:
        if color[node] == 0:
            if dfs(node):
                return True, cycle_nodes

    return False, []

# Test Case 1: Cyclical Route (NYC -> BOS -> CHI -> NYC)
routes_with_cycle = [("NYC", "BOS"), ("BOS", "CHI"), ("CHI", "NYC"), ("NYC", "MIA")]
has_cycle, edge = has_route_cycle(routes_with_cycle)
print(f"Graph 1 Has Cycle: {has_cycle}, Back-edge: {edge}")  # True

# Test Case 2: Acyclic DAG (NYC -> BOS -> CHI, NYC -> MIA)
routes_acyclic = [("NYC", "BOS"), ("BOS", "CHI"), ("NYC", "MIA")]
has_cycle, _ = has_route_cycle(routes_acyclic)
print(f"Graph 2 Has Cycle: {has_cycle}")  # False
```

---

# 10. Merging & Streak Tracking in Distributed Data

---

### Merge K Sorted Streams / Linked Lists
**Topic Covered:** Multi-Way External Merge, Min-Heaps & Priority Queues  
**Questions Covered:**
- Merge K sorted linked lists.
- Merge $K$ sorted streams or linked lists into a single sorted stream. Analyze time and space complexity.
*(Grouped together: Both address merging multiple sorted sequences/partitions using min-heaps in external merge scenarios)*

#### Data Engineering Context
External merge sort in distributed engines (e.g., Spark sort-merge joins, MapReduce spill files, Delta compaction) repeatedly merges $K$ sorted partition files into a single sorted partition.

#### Production Implementation: Min-Heap External Stream Merge
```python
import heapq
from typing import List

def merge_k_sorted_streams(streams: List[List[int]]) -> List[int]:
    """
    Merges K sorted arrays into a single sorted array using a Min-Heap.
    Time Complexity: O(N * log K), where N is total items and K is number of streams.
    Space Complexity: O(K) heap memory.
    """
    min_heap = []
    
    # Initialize heap with (first_val, stream_idx, element_idx)
    for stream_idx, stream in enumerate(streams):
        if stream:
            heapq.heappush(min_heap, (stream[0], stream_idx, 0))

    result = []
    
    while min_heap:
        val, s_idx, elem_idx = heapq.heappop(min_heap)
        result.append(val)
        
        # Advance in the stream from which the min element was taken
        if elem_idx + 1 < len(streams[s_idx]):
            next_val = streams[s_idx][elem_idx + 1]
            heapq.heappush(min_heap, (next_val, s_idx, elem_idx + 1))
            
    return result

# Test:
k_partitions = [
    [10, 25, 40],
    [5, 15, 30],
    [12, 18, 50]
]
print("Merged Output:", merge_k_sorted_streams(k_partitions))
# Output: [5, 10, 12, 15, 18, 25, 30, 40, 50]
```

---

### Longest Continuous Ride Streak / Consecutive Sequence
**Topic Covered:** Streak Calculations, Hash Set $O(N)$ Lookups & Sequence Extraction  
**Questions Covered:**
- Given a list of ride timestamps, find the longest continuous ride streak.
- Given an unsorted list of ride dates or day timestamps, find the length of the longest continuous ride streak in $O(N)$ time.
*(Grouped together: Finding longest consecutive sequence from timestamp/date streams in O(N) using hash sets)*

#### Algorithmic Strategy: Hash Set Boundary Detection
Sorting the list takes $O(N \log N)$. An optimal $O(N)$ solution converts the list into a Python `set`.
- For each number `x`, check if `x - 1` is in the set.
- If `x - 1` exists, `x` is **not** the beginning of a streak (skip it).
- If `x - 1` is NOT in the set, `x` is the **streak start**! Count forward: `x + 1, x + 2, ...` to compute streak length.
- Because each sequence is entered only once at its beginning, total traversal across all numbers is strictly $O(N)$.

#### Production Implementation
```python
from typing import List

def longest_continuous_streak(day_numbers: List[int]) -> int:
    """
    Calculates the longest consecutive sequence in O(N) time and O(N) space.
    """
    num_set = set(day_numbers)
    max_streak = 0

    for num in num_set:
        # Check if num is the start of a streak
        if num - 1 not in num_set:
            current_num = num
            current_streak = 1

            while current_num + 1 in num_set:
                current_num += 1
                current_streak += 1

            max_streak = max(max_streak, current_streak)

    return max_streak

# Test with ride timestamps converted to epoch days:
ride_days = [100, 4, 200, 1, 3, 2, 5, 201]
print("Longest Ride Streak:", longest_continuous_streak(ride_days))
# Subsequence [1, 2, 3, 4, 5] -> Output: 5
```

---

# 11. Python Concurrency Architecture: Threading vs Multiprocessing vs AsyncIO

---

### Multithreading vs Multiprocessing under the Python GIL
**Topic Covered:** Global Interpreter Lock (GIL), CPU-Bound vs I/O-Bound ETL & Parallel Execution  
**Questions Covered:**
- Explain Multithreading vs Multiprocessing and when each approach is useful.
- Explain the difference between Multithreading and Multiprocessing in Python. When is each approach useful for data engineering?
*(Grouped together: In-depth comparison of Python threading vs multiprocessing, GIL mechanics, and compute vs I/O selection)*

#### The Python Global Interpreter Lock (GIL)
The GIL is a mutex in CPython that prevents multiple native threads from executing Python bytecodes simultaneously.
- **Consequence:** Multithreading in Python **cannot utilize multiple CPU cores** for pure Python compute.
- **I/O-Bound Work:** When a thread performs network I/O (e.g., downloading files from S3/ADLS or making REST API calls), it releases the GIL, allowing concurrent thread execution.
- **CPU-Bound Work:** Transforming data in memory, parsing heavy JSON, or running mathematical calculations requires `multiprocessing` to bypass the GIL by creating independent OS processes.

#### Architecture Comparison Matrix
| Dimension | Multithreading (`threading`, `ThreadPoolExecutor`) | Multiprocessing (`multiprocessing`, `ProcessPoolExecutor`) |
| :--- | :--- | :--- |
| **Process Count** | 1 OS process (shared memory space). | $N$ separate OS processes (isolated memory). |
| **GIL Impact** | Bound by GIL. True concurrency only on I/O. | Bypasses GIL. True parallelism across multiple CPU cores. |
| **Memory Overhead**| Very low (~KB per thread). Shared memory. | High (each process forks Python runtime & duplicate memory). |
| **IPC Overhead** | Zero (shared pointers, requires threading locks). | High (requires serialization/pickling between processes). |
| **Primary Use Case**| Ingesting 1,000 files from S3, querying REST APIs, DB reads. | Heavy CSV parsing, image resizing, feature engineering. |

#### Production Implementation: Hybrid Concurrency Pipeline
```python
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor
import time

def io_bound_api_fetch(page_id: int) -> dict:
    """I/O-Bound: Downloads metadata payload from external API."""
    time.sleep(0.05) # Simulating network latency (GIL is released!)
    return {"page_id": page_id, "data": [i for i in range(1000)]}

def cpu_bound_transformation(payload: dict) -> int:
    """CPU-Bound: Heavy parsing & computation (Runs in separate core)."""
    return sum(x ** 2 for x in payload["data"])

def run_concurrent_pipeline():
    page_ids = list(range(20))
    
    # Step 1: ThreadPool for concurrent network fetches
    with ThreadPoolExecutor(max_workers=10) as thread_pool:
        fetched_payloads = list(thread_pool.map(io_bound_api_fetch, page_ids))
    print(f"Successfully fetched {len(fetched_payloads)} payloads via ThreadPool.")

    # Step 2: ProcessPool for CPU-heavy transformations across CPU cores
    with ProcessPoolExecutor(max_workers=4) as process_pool:
        results = list(process_pool.map(cpu_bound_transformation, fetched_payloads))
    print(f"Processed sum total: {sum(results)} via ProcessPool.")
```

---

# 12. Core Python Idioms & Algorithmic Problem Solving

---

### Infinite Generator Streaming: Fibonacci Series
**Topic Covered:** Python Generators, `yield` Keyword, $O(1)$ Memory Lazy Evaluation  
**Questions Covered:**
- Generate an Infinite Fibonacci Series Using a Generator.
- Generate an infinite Fibonacci series using a Python generator.
*(Grouped together: Infinite series generator evaluation with O(1) space)*

#### Memory Architecture
A recursive or array-based Fibonacci implementation storing $N$ numbers consumes $O(N)$ memory and will trigger stack overflow or out-of-memory errors. A generator evaluates lazily, maintaining only two integer pointers in memory ($O(1)$ space).

```python
from typing import Generator

def infinite_fibonacci() -> Generator[int, None, None]:
    """
    Generates an infinite stream of Fibonacci numbers with O(1) memory overhead.
    """
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# Driver: Read only required items
fib_gen = infinite_fibonacci()
first_10 = [next(fib_gen) for _ in range(10)]
print("First 10 Fibonacci Numbers:", first_10)
# Output: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

---

### Sorting Without Built-In Sort: Pure Python Merge Sort
**Topic Covered:** Divide and Conquer Algorithms, Stable Sorting & Time Complexity  
**Questions Covered:**
- Sort a List Without Using the sort Keyword
- Sort a list without using the `sort()` method or `sorted()` function.
*(Grouped together: Manual sorting implementation without built-in sort methods)*

#### Production Implementation: Merge Sort ($O(N \log N)$ Guaranteed)
```python
def merge_sort(arr: list[int]) -> list[int]:
    """
    Pure Python Merge Sort implementation.
    Time Complexity: O(N log N) in all cases (worst, average, best).
    Space Complexity: O(N) auxiliary memory.
    """
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])

    return _merge(left, right)

def _merge(left: list[int], right: list[int]) -> list[int]:
    sorted_arr = []
    i = j = 0

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            sorted_arr.append(left[i])
            i += 1
        else:
            sorted_arr.append(right[j])
            j += 1

    sorted_arr.extend(left[i:])
    sorted_arr.extend(right[j:])
    return sorted_arr

# Test:
unsorted = [64, 34, 25, 12, 22, 11, 90]
print("Sorted Result:", merge_sort(unsorted))
# Output: [11, 12, 22, 25, 34, 64, 90]
```

---

### Two-Pointer Palindrome Verification
**Topic Covered:** String Processing, In-Place Two-Pointer Scanning, Whitespace Handling  
**Questions Covered:**
- Check Whether a String is a Palindrome or Not
- Check whether a string is a palindrome, ignoring non-alphanumeric characters and casing, in $O(1)$ extra space.
*(Grouped together: In-place two-pointer palindrome detection and verification)*

```python
def is_palindrome(s: str) -> bool:
    """
    Two-pointer in-place palindrome check.
    Time Complexity: O(N)
    Space Complexity: O(1) auxiliary space (no string reversal copy).
    """
    left = 0
    right = len(s) - 1

    while left < right:
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1

        if s[left].lower() != s[right].lower():
            return False

        left += 1
        right -= 1

    return True

print(is_palindrome("A man, a plan, a canal: Panama"))  # True
print(is_palindrome("race a car"))                      # False
```

---

### Factorial Calculation: Iterative vs Memoized
**Topic Covered:** Recursion Limits, Iterative Accumulation & Memoization  
**Question:** Write a Python program to find the factorial of a number. Compare iterative vs recursive with memoization.

```python
from functools import lru_cache

# 1. Iterative O(N) Time, O(1) Space (Prevents recursion depth limits)
def factorial_iterative(n: int) -> int:
    if n < 0:
        raise ValueError("Factorial is not defined for negative numbers.")
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

# 2. Memoized Recursive
@lru_cache(maxsize=None)
def factorial_memoized(n: int) -> int:
    if n < 0:
        raise ValueError("Factorial is not defined for negative numbers.")
    if n <= 1:
        return 1
    return n * factorial_memoized(n - 1)

print("Factorial 6 (Iterative):", factorial_iterative(6))   # 720
print("Factorial 6 (Memoized):", factorial_memoized(6))     # 720
```

---

### Word Frequency Tokenizer & Counter
**Topic Covered:** String Normalization, Tokenization & Hash Map Frequency Distribution  
**Question:** Calculate the frequency of words in a sentence without high-level external libraries.

```python
import re
from typing import Dict

def get_word_frequencies(sentence: str) -> Dict[str, int]:
    """
    Tokenizes text, strips punctuation, and builds frequency map in O(N) time.
    """
    freq_map: Dict[str, int] = {}
    words = re.findall(r'\b\w+\b', sentence.lower())

    for word in words:
        freq_map[word] = freq_map.get(word, 0) + 1

    return freq_map

test_sentence = "Data Engineering builds pipelines. High quality data requires robust pipelines!"
print("Word Frequencies:", get_word_frequencies(test_sentence))
# Output: {'data': 2, 'engineering': 1, 'builds': 1, 'pipelines': 2, 'high': 1, 'quality': 1, 'requires': 1, 'robust': 1}
```

---

### Single-Pass Extreme Value Detection (Min and Max)
**Topic Covered:** Single-Pass Invariants, Comparison Reduction ($3N/2$ Comparisons)  
**Question:** Find the smallest and largest number in an array in a single traversal.

```python
from typing import List, Tuple

def find_min_max(arr: List[int]) -> Tuple[int, int]:
    """
    Finds min and max in a single pass O(N) time and O(1) memory.
    """
    if not arr:
        raise ValueError("Array cannot be empty.")

    min_val = arr[0]
    max_val = arr[0]

    for val in arr[1:]:
        if val < min_val:
            min_val = val
        elif val > max_val:
            max_val = val

    return min_val, max_val

# Test:
numbers = [23, 5, 89, -12, 45, 102, 0]
min_res, max_res = find_min_max(numbers)
print(f"Min: {min_res}, Max: {max_res}")  # Min: -12, Max: 102
```


---

# 13. Enterprise String, List, and Dictionary Coding Scenarios

---

### Q9. Advanced Data Manipulation on Strings, Lists, and Dictionaries
**Topic Covered:** String Normalization, List Matrix Transposition, Dictionary Inversion & Merging  
**Questions Covered:**
- Solve Python coding questions on Strings, Lists, and Dictionaries.
- Solve Python coding questions on Strings, Lists, and Dictionaries commonly asked in data engineering interviews.
*(Grouped together: Enterprise coding patterns covering strings, lists, dictionaries, chunking, and recursion)*

#### Definition & Core Concepts
Data engineering transformations frequently involve sanitizing raw string payloads, pivoting and chunking lists of records, and aggregating nested JSON dictionaries into indexed lookup structures.

#### Visual Flow
```mermaid
flowchart TD
    Raw["Raw Payloads"] --> S["1. String: Anagram Grouping & Compression"]
    Raw --> L["2. List: Batch Chunking & Matrix Transpose"]
    Raw --> D["3. Dict: Inverted Index & Deep Recursive Merge"]
    S & L & D --> Clean["Clean Normalized Tabular Records"]

    style Raw fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style S fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style L fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style D fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style Clean fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
```

#### Production Implementation / Code Solution
```python
from collections import defaultdict
from typing import Any, Dict, List

# Scenario 1: Strings - Group Anagrams in O(N * K) time
def group_anagrams(words: List[str]) -> List[List[str]]:
    """Groups words that contain identical character frequencies."""
    anagram_map = defaultdict(list)
    for word in words:
        # 26-element character count tuple as immutable hashable key
        count = [0] * 26
        for char in word.lower():
            if 'a' <= char <= 'z':
                count[ord(char) - ord('a')] += 1
        anagram_map[tuple(count)].append(word)
    return list(anagram_map.values())

# Scenario 2: Lists - High-Performance Batch Chunking for API Ingestion
def chunk_list(records: List[Any], chunk_size: int) -> List[List[Any]]:
    """Splits an unbounded list into deterministic batch chunks."""
    return [records[i:i + chunk_size] for i in range(0, len(records), chunk_size)]

# Scenario 3: Dictionaries - Deep Recursive Merge of Nested Configs
def deep_merge_dicts(dict_a: Dict[str, Any], dict_b: Dict[str, Any]) -> Dict[str, Any]:
    """Recursively merges dict_b into dict_a without overwriting nested keys."""
    merged = dict_a.copy()
    for key, value in dict_b.items():
        if key in merged and isinstance(merged[key], dict) and isinstance(value, dict):
            merged[key] = deep_merge_dicts(merged[key], value)
        else:
            merged[key] = value
    return merged

# Test Verification:
print("Anagrams:", group_anagrams(["eat", "tea", "tan", "ate", "nat", "bat"]))
print("Batches:", chunk_list([1, 2, 3, 4, 5, 6, 7], 3))
print("Merged Config:", deep_merge_dicts({"db": {"host": "localhost", "port": 5432}}, {"db": {"user": "admin"}}))
```
- **Time Complexity:**
  - `group_anagrams`: $O(N \cdot K)$ where $N$ is word count and $K$ is max length.
  - `chunk_list`: $O(N)$ slicing.
  - `deep_merge_dicts`: $O(K)$ total keys.
- **Space Complexity:** $O(N)$ auxiliary memory.

#### Engineering Implications & Production Pitfalls
1. **Mutable Default Argument Trap:** In Python functions, declaring `def process(data, cache={})` binds `cache` to the function object at definition time. Across multiple calls, mutations persist! Always declare `cache: Optional[dict] = None` and initialize inside the function (`if cache is None: cache = {}`).
2. **Interview Talking Point:** For dictionary lookups over 10M keys in memory, standard Python `dict` incurs substantial hash table bucket resizing overhead. In extreme memory environments, using `pyarrow.Table` or `marisa-trie` reduces RAM footprint by $>80\%$.

---

### Q10. Pairing Common Keys Between Disjoint Lists & Dictionary Intersection
**Topic Covered:** Hash Tables, Set Intersections, Key-Value Pairing & $O(N)$ Lookups  
**Question:** Given two lists of key-value tuples or dictionaries, find common keys between them and pair their corresponding values into a combined mapping.

#### Definition & Core Concepts
In ETL enrichment pipelines, datasets often arrive as lists of record tuples or key-value structures from separate upstream APIs. Pairing common keys requires finding the set intersection of keys and building a consolidated dictionary `key: (val_a, val_b)`.
- Naive approach: Nested loops $O(N \cdot M)$.
- Production approach: Hash set / dictionary indexing $O(N + M)$ with collision handling.

#### Production Implementation / Code Solution
```python
from typing import Any, Dict, List, Tuple

def pair_common_keys(
    list_a: List[Tuple[str, Any]], 
    list_b: List[Tuple[str, Any]]
) -> Dict[str, Tuple[Any, Any]]:
    """
    Pairs corresponding values for keys common to both lists in O(N + M) time.
    """
    # Step 1: Ingest list_a into hash map in O(N)
    map_a: Dict[str, Any] = dict(list_a)
    paired_result: Dict[str, Tuple[Any, Any]] = {}

    # Step 2: Iterate through list_b and pair if key exists in map_a in O(M)
    for key, val_b in list_b:
        if key in map_a:
            paired_result[key] = (map_a[key], val_b)

    return paired_result

# Example Driver Execution
items_source1 = [("k1", 100), ("k2", 200), ("k3", 300), ("k4", 400)]
items_source2 = [("k2", "Beta"), ("k4", "Delta"), ("k5", "Epsilon")]

result = pair_common_keys(items_source1, items_source2)
print("Paired Common Keys:", result)
# Output: {'k2': (200, 'Beta'), 'k4': (400, 'Delta')}
```
- **Time Complexity:** $O(N + M)$ single-pass hash lookups.
- **Space Complexity:** $O(N)$ auxiliary hash table for `list_a`.

---

### Q11. Character Frequency & Duplicate Detection in High-Volume Strings
**Topic Covered:** String Normalization, Hash Aggregations & Duplicate Counting  
**Question:** Write a Python function to find all duplicate characters in a string along with their occurrence counts.

#### Production Implementation / Code Solution
```python
from collections import Counter
from typing import Dict

def find_duplicate_characters(payload: str) -> Dict[str, int]:
    """
    Identifies duplicate characters and their exact counts in O(N) time.
    Case-insensitive, ignores leading and trailing whitespace.
    """
    # Normalize string: lowercase and remove extraneous non-printable noise
    clean_chars = [ch for ch in payload.lower() if not ch.isspace()]
    char_counts = Counter(clean_chars)

    # Filter to only characters appearing > 1 time
    return {char: count for char, count in char_counts.items() if count > 1}

# Test Verification
sample_string = "Data Engineering Architecture in Python"
duplicates = find_duplicate_characters(sample_string)
print("Duplicates with Counts:", duplicates)
# Output: {'a': 4, 't': 3, 'e': 4, 'n': 4, 'g': 2, 'i': 4, 'r': 3, 'c': 2, 'h': 2, 'o': 2}
```
- **Time Complexity:** $O(N)$ single pass over characters.
- **Space Complexity:** $O(K)$ where $K \le 256$ for ASCII/Unicode distinct character buckets.

---

### Q12. Python List Appending Mechanics: append() vs extend() vs + vs In-Place Slicing
**Topic Covered:** CPython Memory Allocation, PyListObject Over-Allocation & Time Complexity  
**Question:** Explain the difference between appending a list to an existing list using `append()`, `extend()`, `+`, and slice assignment. Detail their memory layout and performance characteristics.

#### Definition & Core Concepts
In CPython, a `list` is implemented as an array of pointers (`PyListObject`). To avoid allocating memory on every item addition, CPython uses an **over-allocation formula** ($allocated \approx size + (size \gg 3) + 6$).

```mermaid
flowchart TD
    subgraph ListPlus ["1. Concatenation: list_c = list_a + list_b (Out-of-Place)"]
        direction TB
        AllocC["Allocate Brand New PyListObject<br/>Buffer Capacity = Exactly (N + K)"]
        CopyA["Memcpy Pointers from list_a (N Items)"]
        CopyB["Memcpy Pointers from list_b (K Items)"]
        AllocC --> CopyA --> CopyB
    end

    subgraph ListExtend ["2. In-Place Extension: list_a.extend(list_b)"]
        direction TB
        CheckCap{"Buffer Capacity Available?<br/>allocated >= (N + K)?"}
        ReallocA["list_resize() Geometrically:<br/>Capacity ~ (N+K) + ((N+K) >> 3) + 6"]
        CopyElements["Memmove Pointers from list_b<br/>directly into list_a contiguous array"]
        CheckCap -->|"No (Buffer Exhausted)"| ReallocA --> CopyElements
        CheckCap -->|"Yes (Spare Capacity)"| CopyElements
    end

    style ListPlus fill:#0f172a,stroke:#ef4444,stroke-width:1.5px,color:#fca5a5
    style ListExtend fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style AllocC fill:#1f2937,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style CopyA fill:#1f2937,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style CopyB fill:#1f2937,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style CheckCap fill:#1e293b,stroke:#f59e0b,stroke-width:2px,color:#fde68a
    style ReallocA fill:#1f2937,stroke:#f59e0b,stroke-width:1.5px,color:#fff
    style CopyElements fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
```

#### List Expansion & Mutation Engineering Reference Matrix

| Operation Syntax | CPython Memory Mechanics | In-Place Mutation | Time Complexity | Space Overhead | Production Recommendation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`list_a.append(item)`** | Inserts pointer at index $N$; amortized geometric resizing via `list_resize()`. | **Yes** (Mutates in-place) | $\mathcal{O}(1)$ amortized | $\mathcal{O}(1)$ spare capacity | Ideal for progressive single-element streaming accumulation. |
| **`list_a.extend(iterable)`** | Pre-calculates target size; single geometric reallocation if capacity is exceeded; direct `memcpy`. | **Yes** (Mutates in-place) | $\mathcal{O}(K)$ where $K = \text{len}(b)$ | $\mathcal{O}(1)$ auxiliary | **Mandatory** for batch ingestion; avoids intermediary object creation. |
| **`list_c = list_a + list_b`** | Allocates an entirely new `PyListObject`; copies $N$ pointers from $a$, then $K$ pointers from $b$. | **No** (Creates new list) | $\mathcal{O}(N + K)$ | $\mathcal{O}(N + K)$ new list heap | **Anti-pattern** in loops; causes quadratic allocation $\mathcal{O}(N^2)$ and GC memory bloat. |
| **`list_a[len(list_a):] = list_b`** | Invokes slice assignment C-API; internally calls identical logic to `extend()`. | **Yes** (Mutates in-place) | $\mathcal{O}(K)$ | $\mathcal{O}(1)$ auxiliary | Functionally identical to `extend()`; prefer `extend()` for clarity. |

#### Production Implementation
```python
import sys

# Demonstration of CPython list memory reallocation
numbers = []
print(f"Empty list size: {sys.getsizeof(numbers)} bytes")

for i in range(10):
    numbers.append(i)
    # Observe length vs internal allocated buffer size
    print(f"Length: {len(numbers)}, RAM Size: {sys.getsizeof(numbers)} bytes")

# Production Rule: Use extend() or itertools.chain for combining large batches
batch_a = [1, 2, 3]
batch_b = [4, 5, 6]
batch_a.extend(batch_b)  # In-place O(K) without allocating new intermediate list
```

---

### Q13. Email Username Extraction with Resilient Regex & RFC Validation
**Topic Covered:** String Normalization, Regex Boundaries & Edge Case Sanitization  
**Question:** Write a production-grade Python function to extract usernames from email addresses, handling subaddressing (plus tags), uppercase letters, and malformed inputs.

#### Production Implementation / Code Solution
```python
import re
from typing import Optional

EMAIL_USERNAME_REGEX = re.compile(
    r'^[a-zA-Z0-9_.+-]+(?=@(?:[a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}$)'
)

def extract_clean_username(email: Optional[str], strip_plus_tags: bool = True) -> Optional[str]:
    """
    Extracts normalized username from an email address.
    Handles 'john.doe+newsletters@company.com' -> 'john.doe'
    Returns None for invalid or missing emails.
    """
    if not email or not isinstance(email, str):
        return None

    clean_email = email.strip()
    match = EMAIL_USERNAME_REGEX.match(clean_email)
    if not match:
        return None

    raw_username = match.group(0).lower()

    if strip_plus_tags and '+' in raw_username:
        # Strip subaddressing plus-tag (e.g., user+finance@domain.com -> user)
        raw_username = raw_username.split('+', 1)[0]

    return raw_username

# Test Verification
print("Standard:", extract_clean_username("Joydeep.Biswas@domain.com"))          # joydeep.biswas
print("Plus-Tag:", extract_clean_username("developer+alerts@databricks.net"))   # developer
print("Invalid:", extract_clean_username("invalid_email_at_domain.com"))        # None
```
- **Time Complexity:** $O(L)$ where $L$ is email string length.
- **Space Complexity:** $O(1)$ constant memory.

---

### Q14. The Stock Span Problem via Monotonic Stack
**Topic Covered:** Monotonic Stack, Lookback Stream Analytics & Linear Time Optimization  
**Question:** Implement the Stock Span Problem in Python using a stack to process daily prices in $O(N)$ linear time.

#### Definition & Core Concepts
Given an array of daily stock prices, find the span of stock's price for all days. The span $S_i$ on day $i$ is defined as the maximum number of consecutive days just before day $i$, for which the price was less than or equal to day $i$'s price.
- A brute-force two-loop comparison runs in $O(N^2)$ time.
- A **Monotonic Decreasing Stack** stores indices of prices in strictly decreasing order. Each price is pushed and popped at most once, reducing total processing time to **amortized $O(N)$**.

#### Production Implementation / Code Solution
```python
from typing import List, Tuple

def calculate_stock_spans(prices: List[float]) -> List[int]:
    """
    Calculates consecutive lookback price spans in linear O(N) time.
    Stack stores (price, original_index) in strictly decreasing order.
    """
    if not prices:
        return []

    n = len(prices)
    spans: List[int] = [1] * n
    # Stack stores tuples: (price, index)
    stack: List[Tuple[float, int]] = []

    for i, current_price in enumerate(prices):
        # Pop previous days whose price is less than or equal to current day
        while stack and stack[-1][0] <= current_price:
            stack.pop()

        # If stack is empty, current price is higher than all prior prices
        spans[i] = i + 1 if not stack else i - stack[-1][1]

        # Push current day onto monotonic stack
        stack.append((current_price, i))

    return spans

# Test Driver:
prices = [100, 80, 60, 70, 60, 75, 85]
print("Daily Prices:", prices)
print("Stock Spans: ", calculate_stock_spans(prices))
# Output: [1, 1, 1, 2, 1, 4, 6]
```
- **Time Complexity:** $O(N)$ amortized. Each day index is pushed and popped exactly once.
- **Space Complexity:** $O(N)$ auxiliary stack memory.

---

### Q15. Large-Scale Log File Analysis & Most Frequently Accessed Database Tables
**Topic Covered:** Memory-Bounded File Streaming, Regex Tokenization & Frequency Analysis  
**Question:** Write a Python program to parse a 100GB database system access log and determine the top 10 most frequently accessed database tables without causing out-of-memory errors.

#### Definition & Core Concepts
When analyzing multi-gigabyte or terabyte server logs:
1. **Never use `file.readlines()` or `pd.read_csv()`**: Loading the file at once materializes all lines in RAM, triggering immediate memory exhaustion.
2. **Use Generator Streaming**: Read line-by-line using standard file iterator (`for line in file:`), buffering only 64KB chunks in the OS I/O cache.
3. **Precompile Regex**: Avoid recompiling regex patterns for millions of iterations.
4. **Bounded Aggregation**: Track table names in `collections.Counter` (table names are finite strings, consuming $<10\text{MB}$ total memory even for billions of log lines).

#### Visual Flow
```mermaid
flowchart LR
    DiskLog["100 GB Server Log<br/>/var/log/query_audit.log"] --> Stream["1. Line Generator<br/>Buffering: 64 KB in RAM"]
    Stream --> Regex["2. Precompiled Regex<br/>(Extracts FROM/JOIN/INTO/UPDATE)"]
    Regex --> HashCounter["3. collections.Counter<br/>Fixed RAM Footprint (<10 MB)"]
    HashCounter --> TopK["4. most_common(10)<br/>Heap-based Top K Output"]

    style DiskLog fill:#0f172a,stroke:#64748b,stroke-width:1.5px,color:#cbd5e1
    style Stream fill:#1e293b,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Regex fill:#1e293b,stroke:#f59e0b,stroke-width:2px,color:#fff
    style HashCounter fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#fff
    style TopK fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
```

#### Production Implementation / Code Solution
```python
import re
from collections import Counter
from typing import Generator, List, Tuple
from pathlib import Path

# Match standard ANSI SQL access verbs followed by table name (supports schema.table)
TABLE_PATTERN = re.compile(
    r'\b(?:FROM|JOIN|INTO|UPDATE)\s+[`"\[]?([a-zA-Z0-9_]+(?:\.[a-zA-Z0-9_]+)?)[`"\]]?',
    re.IGNORECASE
)

def stream_log_lines(log_file_path: Path, chunk_buffer: int = 64 * 1024) -> Generator[str, None, None]:
    """Generates individual lines from disk with constant O(1) memory footprint."""
    with open(log_file_path, "r", encoding="utf-8", buffering=chunk_buffer) as f:
        for line in f:
            yield line

def get_most_frequently_accessed_tables(log_file_path: Path, top_n: int = 10) -> List[Tuple[str, int]]:
    """
    Parses arbitrarily large log files to find the top accessed tables.
    Memory footprint remains strictly bounded under 20MB regardless of file size.
    """
    table_frequency = Counter()

    for line in stream_log_lines(log_file_path):
        matches = TABLE_PATTERN.findall(line)
        for table in matches:
            clean_table_name = table.strip().lower()
            table_frequency[clean_table_name] += 1

    return table_frequency.most_common(top_n)
```
- **Time Complexity:** $O(L)$ where $L$ is the total number of characters across the log file.
- **Space Complexity:** $O(T)$ where $T$ is the number of distinct database table names (typically $<10,000$ unique tables $\to <5\text{MB}$ RAM).

#### Engineering Implications & Production Pitfalls
1. **Schema Qualification:** A query referencing `orders` and another referencing `gold.orders` refer to the same logical table. Normalize table names by lowercasing and stripping quotes/brackets.
2. **Subqueries & Aliases:** Naive regex matching can accidentally capture subquery aliases (e.g. `FROM (SELECT * FROM a) t`). Enhance regex to ignore SQL reserved keywords (`LATERAL`, `UNNEST`) and subquery parentheses.

