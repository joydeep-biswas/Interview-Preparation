# 03. Python for Data Engineering

A comprehensive, production-grade guide to Python for high-performance data engineering, advanced language constructs (Generators, Decorators, Context Managers, Pydantic type validation), modern analytical engines (Polars vs. Pandas, DuckDB in-process OLAP, PyArrow), and production systems engineering (Structured Logging, Concurrency: Multiprocessing vs. Threading vs. Asyncio).

---

## Table of Contents
1. [Advanced Python Fundamentals](#1-advanced-python-fundamentals)
   - [1.1 Built-in Data Structures & Time Complexity](#11-built-in-data-structures--time-complexity)
   - [1.2 Comprehensions (List, Dict, Set)](#12-comprehensions-list-dict-set)
   - [1.3 Generators, Iterators, and Memory-Safe Streaming](#13-generators-iterators-and-memory-safe-streaming)
   - [1.4 Custom Decorators & Practical Engineering Use Cases](#14-custom-decorators--practical-engineering-use-cases)
   - [1.5 Context Managers & Deterministic Resource Cleanup](#15-context-managers--deterministic-resource-cleanup)
   - [1.6 Lambda Functions, `*args`, and `**kwargs`](#16-lambda-functions-args-and-kwargs)
   - [1.7 Robust Error Handling & Custom Domain Exceptions](#17-robust-error-handling--custom-domain-exceptions)
   - [1.8 Type Hinting & Pydantic Data Validation](#18-type-hinting--pydantic-data-validation)
2. [Data Processing Libraries & Modern Tooling](#2-data-processing-libraries--modern-tooling)
   - [2.1 Pandas vs Polars (Architecture & Benchmarks)](#21-pandas-vs-polars-architecture--benchmarks)
   - [2.2 Polars Deep-Dive (Rust-Based Multithreaded Lazy Execution)](#22-polars-deep-dive)
   - [2.3 DuckDB: The In-Process Analytical OLAP Engine](#23-duckdb-the-in-process-analytical-olap-engine)
   - [2.4 PyArrow & Parquet In-Memory Processing](#24-pyarrow--parquet-in-memory-processing)
   - [2.5 JSON & Nested Payload Manipulation](#25-json--nested-payload-manipulation)
   - [2.6 Date, Time & Timezone Management (`zoneinfo`)](#26-date-time--timezone-management-zoneinfo)
   - [2.7 Streaming Large Files Line-by-Line (Memory-Safe I/O)](#27-streaming-large-files-line-by-line-memory-safe-io)
   - [2.8 File Validation & Checksums (`hashlib`)](#28-file-validation--checksums-hashlib)
   - [2.9 Missing Data Imputation & Default Value Fallbacks](#29-missing-data-imputation--default-value-fallbacks)
3. [Production Python Systems Engineering](#3-production-python-systems-engineering)
   - [3.1 Environment & Package Management (`venv`, `poetry`)](#31-environment--package-management-venv-poetry)
   - [3.2 Structured Logging (`structlog` & JSON Formats)](#32-structured-logging-structlog--json-formats)
   - [3.3 Configuration Management (`pydantic-settings`)](#33-configuration-management-pydantic-settings)
   - [3.4 Concurrency: Multiprocessing vs Multithreading vs Asyncio](#34-concurrency-multiprocessing-vs-multithreading-vs-asyncio)
   - [3.5 Modern Static Analysis & Tooling (`ruff`, `mypy`, `black`)](#35-modern-static-analysis--tooling-ruff-mypy-black)

---

# 1. Advanced Python Fundamentals

---

### 1.1 Built-in Data Structures & Time Complexity

Choosing the correct built-in data structure is critical for pipeline performance. Using a list for lookups inside an iteration loop degrades algorithm runtime from linear $O(N)$ to quadratic $O(N^2)$.

| Data Structure | Mutability | Ordering | Lookup Complexity | Primary Data Engineering Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **`list`** | Mutable | Ordered | $O(N)$ (Search) / $O(1)$ (Index) | Ordered sequences of raw records |
| **`dict`** | Mutable | Insertion-ordered | **$O(1)$ (Average)** | Fast entity lookups, caches, JSON representation |
| **`set`** | Mutable | Unordered | **$O(1)$ (Average)** | Deduplicating primary keys; fast set intersections |
| **`tuple`** | **Immutable** | Ordered | $O(1)$ (Index) | Database cursor rows, immutable record identifiers |

#### References
- [GeeksforGeeks: Python Data Structures](https://www.geeksforgeeks.org/python-data-structures/)
- [DataCamp: Python Data Structures](https://www.datacamp.com/tutorial/data-structures-python)

---

### 1.2 Comprehensions (List, Dict, Set)

Comprehensions provide a concise, C-optimized syntax for transforming and filtering collections in a single step without imperative `for` loop overhead.

```python
# Clean, filter, and normalize raw customer records into a lookup dictionary
raw_users = [
    {"id": "usr_101", "email": " ALICE@corp.com ", "is_active": True},
    {"id": "usr_102", "email": "bob@corp.com", "is_active": False},
    {"id": "usr_103", "email": None, "is_active": True},
    {"id": "usr_104", "email": "charlie@corp.com", "is_active": True},
]

# Dictionary comprehension: Maps validated user_id -> standardized lowercase email
# Evaluates filter conditions to discard inactive users and missing emails in one pass
active_user_directory = {
    user["id"]: user["email"].strip().lower()                 # Key-value projection with string cleaning
    for user in raw_users                                     # Source iteration
    if user.get("is_active") and user.get("email")            # Two-fold guard: must be active and non-null
}

print(active_user_directory)
# Output: {'usr_101': 'alice@corp.com', 'usr_104': 'charlie@corp.com'}
```

---

### 1.3 Generators, Iterators, and Memory-Safe Streaming

#### Definition & Memory Mechanics
A Generator yields values on demand one item at a time using the `yield` statement, pausing its execution state between requests. Unlike lists, which materialize all elements in RAM simultaneously, generators consume **constant $O(1)$ memory**.

```mermaid
flowchart TD
    subgraph ListBlowout ["List Materialization: O(N) RAM (Blows up on 20GB files)"]
        DiskFile1[(20GB Raw Log File)] --> ReadAll["f.readlines() loads 100M strings into RAM"]
        ReadAll --> RAM1["RAM Explosion: 32GB Allocated -> OOM Kill!"]
    end

    subgraph GeneratorStream ["Generator Streaming: O(1) Constant RAM (< 50MB)"]
        DiskFile2[(20GB Raw Log File)] --> YieldLine["yield line (Yields 1 record at a time)"]
        YieldLine --> RAM2["RAM: Exactly 1 record in memory buffer"]
        RAM2 --> Consumer["Process record & discard"]
        Consumer --> YieldLine
    end

    style ListBlowout fill:#0f172a,stroke:#ef4444,stroke-width:1.5px,color:#fca5a5
    style GeneratorStream fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7

    style DiskFile1 fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style ReadAll fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style RAM1 fill:#7f1d1d,stroke:#ef4444,stroke-width:2px,color:#fecaca
    style DiskFile2 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style YieldLine fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style RAM2 fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
    style Consumer fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### Code Example
```python
from typing import Generator

def stream_large_file(filepath: str) -> Generator[str, None, None]:
    """
    Memory-safe generator streaming a multi-gigabyte log file line-by-line.
    
    Args:
        filepath: Local or mounted filesystem path to the target file.
        
    Yields:
        Cleaned, stripped string record one line at a time.
    """
    with open(filepath, mode="r", encoding="utf-8") as file_handle:
        for line in file_handle:                              # File iterator reads chunked buffers under the hood
            cleaned_line = line.strip()                       # Remove trailing newline and whitespaces
            if cleaned_line:                                  # Skip empty lines
                yield cleaned_line                            # Yield control and record back to caller

# Consumption loop: constant memory footprint regardless of file size
# Even on a 50GB file, memory consumption stays under 20MB RAM
for raw_log_line in stream_large_file("huge_web_traffic.log"):
    # Simulated processing logic (e.g. sending to ingestion queue)
    pass
```

> [!NOTE]
> For applying streaming patterns to end-to-end distributed ingestion pipelines, see [10. Data Pipeline Design & Ingestion](10_data_pipeline_design_and_ingestion.md).

---

### 1.4 Custom Decorators & Practical Engineering Use Cases

#### Definition
A Decorator wraps a target function to augment its behavior (logging, timing, caching, error recovery) without altering the target function's internal logic.

#### Production Retry Decorator with Exponential Backoff
```python
import time
from functools import wraps
from typing import Callable, Any

def retry_with_exponential_backoff(
    max_retries: int = 3, 
    initial_delay_sec: float = 1.0, 
    backoff_factor: float = 2.0
) -> Callable:
    """
    Decorator for robustly retrying transient network/database operations.
    
    Args:
        max_retries: Number of allowed attempts before raising final exception.
        initial_delay_sec: Base sleep duration before first retry.
        backoff_factor: Multiplier applied to delay after each failure.
    """
    def decorator(func: Callable) -> Callable:
        @wraps(func)                                          # Preserves function metadata (__name__, docstrings)
        def wrapper(*args: Any, **kwargs: Any) -> Any:
            attempt = 0
            current_delay = initial_delay_sec
            
            while True:
                try:
                    return func(*args, **kwargs)              # Attempt invoking primary target function
                except Exception as exc:
                    attempt += 1
                    if attempt > max_retries:
                        # Retries exhausted; re-raise the original exception
                        print(f"[RETRY FAILED] Function {func.__name__} failed after {max_retries} attempts.")
                        raise exc
                    
                    print(f"[RETRY] Error in {func.__name__}: '{exc}'. Retrying in {current_delay:.1f}s (Attempt {attempt}/{max_retries})...")
                    time.sleep(current_delay)                 # Wait before next attempt
                    current_delay *= backoff_factor           # Exponential delay scaling (1s -> 2s -> 4s)
        return wrapper
    return decorator

# Practical pipeline invocation
@retry_with_exponential_backoff(max_retries=3, initial_delay_sec=1.5, backoff_factor=2.0)
def extract_crm_data_via_api(endpoint_url: str) -> dict:
    """Fetches customer records from an external SaaS REST API prone to rate limits."""
    # Simulated network payload fetch
    return {"status": "SUCCESS", "records_retrieved": 500}
```

---

### 1.5 Context Managers & Deterministic Resource Cleanup

Context Managers guarantee that operating system resources (database cursors, socket connections, file handles, temporary scratch directories) are reliably released—even if unexpected runtime exceptions occur.

```python
from contextlib import contextmanager
from typing import Generator
import psycopg2

@contextmanager
def managed_database_transaction(connection_uri: str) -> Generator[Any, None, None]:
    """
    Context manager guaranteeing atomic database transaction boundaries.
    Automatically commits on success or issues a full rollback upon error.
    """
    connection = psycopg2.connect(connection_uri)             # Establish raw TCP database connection
    cursor = connection.cursor()                              # Allocate transactional cursor
    try:
        yield cursor                                          # Yield cursor to caller for execution
        connection.commit()                                   # Commit transaction if block exits cleanly
        print("[DB TRANSACTION] Transaction successfully committed.")
    except Exception as error:
        connection.rollback()                                 # Roll back all uncommitted mutations upon error
        print(f"[DB TRANSACTION] Rolled back transaction due to error: {error}")
        raise error                                           # Propagate exception up the stack
    finally:
        cursor.close()                                        # Guarantee cursor resource release
        connection.close()                                    # Guarantee connection termination

# Usage example:
# with managed_database_transaction("postgresql://user:pass@localhost:5432/core") as cur:
#     cur.execute("UPDATE accounts SET balance = balance - 100 WHERE id = 1;")
```

---

### 1.6 Lambda Functions, `*args`, and `**kwargs`

- `*args`: Collects an arbitrary sequence of positional arguments as a `tuple`.
- `**kwargs`: Collects arbitrary keyword arguments as a `dict`.

```python
def log_pipeline_metrics(metric_name: str, *tags: str, **attributes: Any) -> None:
    """Demonstrates dynamic parameter aggregation for telemetry collection."""
    print(f"Metric: {metric_name}")
    print(f"  Positional Tags (tuple): {tags}")               # e.g., ('prod', 'daily_batch')
    print(f"  Key-Value Attributes (dict): {attributes}")     # e.g., {'rows_inserted': 5000, 'duration_ms': 420}

log_pipeline_metrics("orders_ingested", "prod", "critical", rows_inserted=5000, duration_ms=420)
```

---

### 1.7 Robust Error Handling & Custom Domain Exceptions

Standard Python errors (`ValueError`, `RuntimeError`) are too generic for automated pipeline alerting. Defining domain-specific exceptions allows orchestrators and circuit breakers to distinguish between transient network blips and catastrophic schema violations.

```python
class DataQualityValidationError(Exception):
    """Raised when an ingested batch violates critical schema contracts or null thresholds."""
    def __init__(self, table_name: str, violation_count: int, threshold: int):
        self.table_name = table_name
        self.violation_count = violation_count
        self.threshold = threshold
        super().__init__(
            f"Table '{table_name}' failed quality gate: {violation_count} nulls detected (Threshold: {threshold})"
        )

def validate_staging_records(table_name: str, null_pk_count: int, max_allowed: int = 0) -> None:
    """Enforces zero-tolerance null checks on primary keys."""
    if null_pk_count > max_allowed:
        raise DataQualityValidationError(table_name, null_pk_count, max_allowed)
```

> [!NOTE]
> For incorporating data quality gates and circuit breakers into enterprise pipelines, see [14. Data Governance, Security & Observability](14_data_governance_security_and_observability.md#7-automated-anomaly-detection--circuit-breakers).

---

### 1.8 Type Hinting & Pydantic Data Validation

Pydantic enforces runtime type validation and automatic type coercion, guaranteeing that malformed inputs from REST APIs or message brokers are intercepted before landing in downstream databases.

```python
from pydantic import BaseModel, Field, EmailStr
from datetime import datetime
from typing import Optional

class InboundOrderEvent(BaseModel):
    """Strongly typed data model representing an e-commerce order event."""
    order_id: int                                            # Automatically coerces numeric strings "1001" to int
    customer_email: EmailStr                                 # Validates RFC email syntax
    order_amount: float = Field(gt=0.0, description="Order total must be strictly positive")
    currency_code: str = Field(min_length=3, max_length=3, default="USD")
    order_timestamp: datetime                                # Parses ISO-8601 strings into datetime objects
    tracking_number: Optional[str] = None                    # Nullable optional attribute

# Ingesting unvalidated raw dictionary payload
raw_api_payload = {
    "order_id": "9924",                                      # String that gets converted to int
    "customer_email": "purchaser@company.com",
    "order_amount": 149.99,
    "order_timestamp": "2024-10-01T14:30:00Z"
}

# Runtime validation and object instantiation
validated_order = InboundOrderEvent(**raw_api_payload)
print(f"Validated Order {validated_order.order_id}: ${validated_order.order_amount:.2f} at {validated_order.order_timestamp}")
```

---

# 2. Data Processing Libraries & Modern Tooling

---

### 2.1 Pandas vs Polars (Architecture & Benchmarks)

```mermaid
flowchart TD
    subgraph PandasArch ["Pandas (Legacy Python Engine)"]
        P1["Single-Threaded C/Python Core"]
        P2["Eager Evaluation: Every step materializes full dataframe in RAM"]
        P3["Memory Heavy: Requires 5x - 10x dataset size in RAM"]
    end

    subgraph PolarsArch ["Polars (Modern Rust Engine)"]
        PL1["Multi-Threaded Apache Arrow Core written in Rust"]
        PL2["Lazy Query Graph: Optimizes projection & filter pushdown before execution"]
        PL3["Streaming Engine: Can process datasets larger than available RAM"]
    end

    style PandasArch fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style PolarsArch fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7

    style P1 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style P2 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style P3 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style PL1 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style PL2 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style PL3 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

| Dimension | Pandas | Polars |
| :--- | :--- | :--- |
| **Language Core** | Python & C | **100% Rust** |
| **Parallelism** | Single-core CPU (Requires manual Dask/Ray) | **Native Multi-Core (Uses all CPU cores automatically)** |
| **Memory Model** | NumPy arrays (Heavy memory copies) | **Apache Arrow (Zero-copy in-memory format)** |
| **Execution** | Eager only | **Lazy + Eager + Streaming Engine** |
| **Speed Benchmark** | Baseline | **10x to 50x faster than Pandas** |

---

### 2.2 Polars Deep-Dive

Polars uses a **LazyFrame** API that builds an optimized execution plan (predicate pushdown, projection pruning) and runs multithreaded across all available CPU cores.

```python
import polars as pl

# 1. Construct a Lazy Query Plan (does NOT load files into RAM yet)
# Reads only Parquet metadata footers to plan the execution graph
lazy_pipeline = (
    pl.scan_parquet("data/orders/*.parquet")                  # Scan Parquet partition directory lazily
    .filter(pl.col("order_date") >= pl.date(2024, 1, 1))      # Predicate pushdown: skips row groups prior to 2024
    .filter(pl.col("status") == "COMPLETED")                  # Filters rows early
    .group_by("customer_id")                                  # Parallel hash grouping across all CPU cores
    .agg([
        pl.col("order_amount").sum().alias("total_revenue"),   # Aggregation: sum of revenue per customer
        pl.col("order_id").count().alias("transaction_count")  # Aggregation: total count of orders
    ])
    .sort("total_revenue", descending=True)                  # Parallel sort
    .limit(10)                                               # Restrict to top 10 highest-value customers
)

# 2. Execute the optimized query graph using Polars' multithreaded Rust engine
top_customers_df = lazy_pipeline.collect()
print(top_customers_df)
```

---

### 2.3 DuckDB: The In-Process Analytical OLAP Engine

DuckDB is an embedded in-process columnar SQL engine (often called the "SQLite for Analytics"). It requires zero background daemon setup, runs inside your Python process, and queries local or remote Parquet/CSV files directly using vectorized execution.

```python
import duckdb

# Connect to in-process in-memory DuckDB instance
db_connection = duckdb.connect()

# Execute high-speed vectorized analytical SQL directly against local Parquet files
# Leverages columnar scanning and projection pushdown without loading unneeded columns
aggregated_metrics = db_connection.execute("""
    SELECT 
        product_category,
        COUNT(order_id)                   AS total_orders,
        ROUND(SUM(order_amount), 2)       AS total_revenue,
        ROUND(AVG(order_amount), 2)       AS avg_ticket_size
    FROM read_parquet('data/sales_2024.parquet')              -- Direct Parquet file querying
    WHERE order_amount > 25.00                                -- Filter condition pushed down to Parquet row groups
    GROUP BY product_category
    ORDER BY total_revenue DESC
    LIMIT 5;
""").df()                                                     # Zero-copy export directly into Pandas / Arrow DataFrame

print(aggregated_metrics)
```

> [!NOTE]
> For foundational SQL query structure and clause evaluation order, see [01. SQL Fundamentals](01_sql_fundamentals.md). For physical columnar scanning and storage internals, see [07. File Formats & Storage](07_file_formats_and_storage.md).

---

### 2.4 PyArrow & Parquet In-Memory Processing

PyArrow provides Python bindings to the Apache Arrow columnar format, enabling high-performance reading and writing of Parquet datasets with zero serialization overhead and direct zero-copy memory mapping.

```mermaid
flowchart LR
    ParquetDisk[Parquet File on Disk] -->|Zero-Copy Memory Map| ArrowTable[PyArrow Table in RAM]
    ArrowTable -->|Zero-Copy Pointer| PolarsDF[Polars DataFrame]
    ArrowTable -->|Zero-Copy Pointer| DuckDBEngine[DuckDB Query Engine]

    style ParquetDisk fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style ArrowTable fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style PolarsDF fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style DuckDBEngine fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

```python
import pyarrow.parquet as pq
import pyarrow.compute as pc

# 1. Read metadata footer without reading data pages into memory
parquet_meta = pq.ParquetFile("data/transactions.parquet")
print(f"Row Groups: {parquet_meta.num_row_groups}, Columns: {parquet_meta.schema.names}")

# 2. Read specific columns with filter pushdown directly into an Apache Arrow Table
arrow_table = pq.read_table(
    "data/transactions.parquet",
    columns=["transaction_id", "customer_id", "amount", "status"], # Column pruning (skips unneeded columns)
    filters=[("amount", ">", 100.0), ("status", "==", "COMPLETED")] # Predicate pushdown (skips non-matching row groups)
)

# 3. Vectorized in-memory aggregation using PyArrow Compute C++ engine
total_volume = pc.sum(arrow_table["amount"]).as_py()
print(f"Total High-Value Volume: ${total_volume:,.2f}")
```

---

### 2.5 JSON & Nested Payload Manipulation

APIs often return deeply nested JSON structures that must be flattened into tabular rows before ingestion into relational tables or columnar formats.

```python
import json
from typing import Dict, Any

def flatten_nested_json(nested_dict: Dict[str, Any], parent_key: str = "", separator: str = "_") -> Dict[str, Any]:
    """
    Recursively flattens arbitrarily nested dictionaries into a single flat key-value dictionary.
    
    Args:
        nested_dict: The source dictionary containing nested dicts and arrays.
        parent_key: Accumulated prefix for nested keys.
        separator: Character separating hierarchy levels.
    """
    flattened_items = []
    for key, value in nested_dict.items():
        composite_key = f"{parent_key}{separator}{key}" if parent_key else key
        if isinstance(value, dict):
            # Recurse down into sub-dictionary
            flattened_items.extend(flatten_nested_json(value, composite_key, separator=separator).items())
        elif isinstance(value, list):
            # Convert list to stringified JSON representation
            flattened_items.append((composite_key, json.dumps(value)))
        else:
            flattened_items.append((composite_key, value))
    return dict(flattened_items)

# Nested API response sample
raw_api_response = {
    "event_id": "evt_9012",
    "customer": {
        "id": "c_44",
        "profile": {"name": "Alice", "country": "US"}
    },
    "payment": {"method": "credit_card", "amount": 250.0}
}

flat_row = flatten_nested_json(raw_api_response)
print("Flattened Row for Database Insertion:")
for col_name, val in flat_row.items():
    print(f"  {col_name}: {val}")
```

---

### 2.6 Date, Time & Timezone Management (`zoneinfo`)

#### Problem: Inconsistent Timestamps & Time Zone Ambiguity
Data pipelines ingest telemetry and transactions from distributed upstream sources operating in varied local timezones, differing serialization standards (UNIX epoch seconds vs milliseconds, ISO-8601 strings with varying offsets, naive datetimes), and Daylight Saving Time (DST) shifts. 

#### Solution: Time Standardization (UTC) & Time Zone Normalization
1. **Canonical UTC Rule:** Ingested datetimes must immediately be converted to timezone-aware UTC timestamps at the pipeline entry boundary (Bronze landing) before any persistence or partitioning. Downstream analytics, windowing, and joins must strictly operate on UTC.
2. **Daylight Saving Time (DST) Traps:**
   - **Spring Forward (Gap):** Local clocks jump forward 1 hour (e.g., from 01:59:59 to 03:00:00). Wall-clock timestamps falling in the skipped hour do not physically exist; parsing them without validation can result in silent timestamp shifting.
   - **Fall Back (Overlap / Ambiguous Time):** Local clocks turn backward 1 hour (e.g., repeating the hour from 01:00:00 to 01:59:59). Python's `zoneinfo` exposes the `fold` attribute (`fold=0` indicates the first occurrence during DST; `fold=1` indicates the second occurrence after the transition back to standard time).

```python
from datetime import datetime, timezone
from zoneinfo import ZoneInfo
from typing import Optional

def normalize_to_utc(
    raw_timestamp_str: str, 
    source_tz_name: str = "America/New_York",
    is_dst_second_occurrence: bool = False
) -> datetime:
    """
    Normalizes mixed or naive localized timestamp strings into canonical UTC datetimes,
    defensively handling ISO-8601 parsing and Daylight Saving Time (DST) overlaps.
    
    Args:
        raw_timestamp_str: Raw timestamp string (ISO-8601 or wall-clock format).
        source_tz_name: IANA timezone identifier of the generating source system.
        is_dst_second_occurrence: Flag resolving ambiguous fall-back DST hour (fold=1).
    Returns:
        Timezone-aware datetime in canonical UTC.
    """
    source_tz = ZoneInfo(source_tz_name)
    
    # Attempt parsing standardized ISO-8601 with explicit offset (e.g., "2024-10-03T14:30:00-04:00")
    try:
        dt = datetime.fromisoformat(raw_timestamp_str)
        if dt.tzinfo is not None:
            return dt.astimezone(timezone.utc)
    except ValueError:
        pass
    
    # Parse naive wall-clock string (e.g., "2024-11-03 01:30:00")
    dt_naive = datetime.strptime(raw_timestamp_str, "%Y-%m-%d %H:%M:%S")
    
    # Assign source timezone, utilizing fold to disambiguate fall-back DST hour
    fold_val = 1 if is_dst_second_occurrence else 0
    dt_localized = dt_naive.replace(tzinfo=source_tz, fold=fold_val)
    
    # Convert localized datetime to canonical UTC for Lakehouse persistence
    return dt_localized.astimezone(timezone.utc)

# 1. Parse an ambiguous wall-clock timestamp during "fall back"
first_pass = normalize_to_utc("2024-11-03 01:30:00", "America/New_York", is_dst_second_occurrence=False)
second_pass = normalize_to_utc("2024-11-03 01:30:00", "America/New_York", is_dst_second_occurrence=True)

print(f"DST First Occurrence (EDT UTC-4): {first_pass.isoformat()}")   # 2024-11-03T05:30:00+00:00
print(f"DST Second Occurrence (EST UTC-5): {second_pass.isoformat()}") # 2024-11-03T06:30:00+00:00
```

---

### 2.7 Streaming Large Files Line-by-Line (Memory-Safe I/O)

```python
import csv
from typing import Generator, Dict, List

def stream_csv_in_batches(
    file_path: str, 
    batch_size: int = 1000
) -> Generator[List[Dict[str, str]], None, None]:
    """
    Streams a multi-gigabyte CSV file in fixed micro-batches with constant O(1) RAM usage.
    
    Args:
        file_path: Filesystem path to the input CSV file.
        batch_size: Number of records accumulated before yielding.
    """
    with open(file_path, mode="r", encoding="utf-8") as f:
        reader = csv.DictReader(f)                            # Maps CSV headers to dict keys automatically
        current_batch = []
        for row in reader:
            current_batch.append(row)
            if len(current_batch) >= batch_size:
                yield current_batch                           # Yield batch when target size is reached
                current_batch = []                            # Reset accumulator
        if current_batch:                                     # Flush remaining trailing records
            yield current_batch
```

---

### 2.8 File Validation & Checksums (`hashlib`)

#### Problem: File Corruption During Landing & Transit
Distributed file ingress frequently experiences network drops, multi-part upload interruptions, or disk write truncations. Ingesting an unvalidated, corrupted file causes downstream Spark or Polars jobs to crash mid-pipeline or, worse, leads to silent record omissions.

#### Solution: File Validation & Checksums
Ingestion pipelines must perform pre-flight file validation by verifying file integrity against upstream cryptographic checksums (SHA-256, MD5) or fast CRC32 checksums before triggering ELT workloads.

```python
import hashlib
import os

def verify_file_checksum(
    file_path: str, 
    expected_hash: str, 
    algorithm: str = "sha256", 
    chunk_size_bytes: int = 65536
) -> bool:
    """
    Computes cryptographic checksum using streaming chunked I/O to validate file integrity
    with strict O(1) memory consumption regardless of file size.
    
    Args:
        file_path: Filesystem path to the landed data file.
        expected_hash: Pre-calculated checksum emitted by the upstream provider.
        algorithm: Hash algorithm (sha256, md5).
        chunk_size_bytes: Buffer size per read (64 KB default).
    Returns:
        True if computed digest matches expected digest; False otherwise.
    """
    if not os.path.exists(file_path):
        raise FileNotFoundError(f"Landing file not found: {file_path}")
        
    hasher = hashlib.new(algorithm)
    
    with open(file_path, "rb") as f:
        while chunk := f.read(chunk_size_bytes):
            hasher.update(chunk)                             # Iteratively stream bytes into hash context
            
    computed_digest = hasher.hexdigest().lower()
    is_valid = computed_digest == expected_hash.lower()
    
    if not is_valid:
        print(f"Checksum mismatch for {file_path}! Expected: {expected_hash}, Computed: {computed_digest}")
    return is_valid
```

---

### 2.9 Missing Data Imputation & Default Value Fallbacks

#### Problem: Missing Data & Null Values
Missing values, blank strings, and null attributes degrade machine learning feature distributions and trigger query execution errors in strict analytical operations. Indiscriminately dropping rows causes severe survivorship bias in analytical reports.

#### Solution: Imputation & Default Values
1. **Domain Default Fallbacks:** Replacing missing values with explicit sentinel values (e.g., `UNKNOWN` for categorical strings, `0` for numeric modifiers, `false` for booleans) to preserve relational integrity.
2. **Statistical Imputation:**
   - **Mean Imputation:** Best suited for normally distributed numeric variables without extreme skewness.
   - **Median Imputation:** Robust against skewed distributions and extreme outliers.
   - **Mode Imputation:** Applied to high-cardinality categorical variables.
3. **Sequential Imputation (Forward/Backward Fill):** Used for time-series and sensor data where the most recently observed value represents the best estimate for transient measurement dropouts.

```python
import polars as pl

# Sample operational dataset with missing and null values
data = {
    "device_id": ["sensor_A", "sensor_A", "sensor_A", "sensor_B", "sensor_B"],
    "temperature": [21.5, None, 22.0, None, 30.2],
    "status_code": ["OK", None, "OK", "ERROR", None],
    "voltage": [3.3, 3.2, None, 3.1, None]
}

df = pl.DataFrame(data)

# 1. Forward-fill sequential time-series sensor telemetry
df_ffill = df.with_columns(
    pl.col("temperature").forward_fill().over("device_id") # Imputes missing sensor reading with previous known value
)

# 2. Median imputation for numeric metrics & default value fallbacks for categorical codes
median_voltage = df.select(pl.col("voltage").median()).item()

df_clean = df_ffill.with_columns([
    pl.col("voltage").fill_null(median_voltage),           # Robust statistical imputation replacing NULLs with median
    pl.col("status_code").fill_null(pl.lit("UNKNOWN"))     # Domain default value fallback preserving categorical completeness
])

print(df_clean)
```

---

# 3. Production Python Systems Engineering

---

### 3.1 Environment & Package Management (`venv`, `poetry`)

- **`venv`:** Standard Python virtual environment isolation tool (`python3 -m venv .venv`).
- **`poetry`:** Dependency resolver that produces deterministic `poetry.lock` files, preventing upstream library breaking changes across CI/CD builds.

---

### 3.2 Structured Logging (`structlog`)

Unstructured text logs (`print()`, basic `logging.info()`) are difficult for log aggregators (Datadog, Elasticsearch, CloudWatch) to parse. **Structured Logging** outputs single-line JSON records with machine-readable contextual fields.

```python
import structlog

# Initialize structured logger
logger = structlog.get_logger()

# Emits clean machine-readable JSON:
# {"event": "Batch ingestion completed", "batch_id": 102, "rows_inserted": 54000, "duration_sec": 1.42, "level": "info"}
logger.info(
    "Batch ingestion completed", 
    batch_id=102, 
    rows_inserted=54000, 
    duration_sec=1.42
)
```

---

### 3.3 Configuration Management (`pydantic-settings`)

Validates environment variables and `.env` files at startup, failing immediately with descriptive errors if required secrets are missing.

```python
from pydantic_settings import BaseSettings
from pydantic import Field

class PipelineSettings(BaseSettings):
    """Loads and validates pipeline configuration from environment variables."""
    db_host: str = Field(description="Database hostname or IP")
    db_port: int = Field(default=5432, description="Database port number")
    api_auth_token: str = Field(description="Secret API authentication bearer token")
    batch_size: int = Field(default=1000, description="Processing batch size")

    class Config:
        env_file = ".env"                                     # Automatically reads from local .env if present
        case_sensitive = False

# Instantiating settings validates all variables at application boot
# settings = PipelineSettings()
```

---

### 3.4 Concurrency: Multiprocessing vs Multithreading vs Asyncio

```mermaid
flowchart TD
    TaskType{"What is the Primary Bottleneck?"}
    
    TaskType -- CPU-Bound (Data Parsing, Math, Cryptography) --> MP["Multiprocessing<br/>Spawns separate OS processes to bypass Python GIL"]
    TaskType -- I/O-Bound (Local Disk Reads, DB Connections) --> MT["Multithreading<br/>Threads release GIL during blocking OS I/O wait"]
    TaskType -- High-Concurrency Network (1,000+ REST APIs) --> AS["Asyncio<br/>Single-threaded cooperative event loop via coroutines"]

    style TaskType fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style MP fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style MT fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style AS fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
```

| Concurrency Model | Underlying Mechanism | Bypasses GIL? | Best Used For |
| :--- | :--- | :--- | :--- |
| **Multiprocessing** | Spawns separate OS processes | **Yes** | Heavy CPU tasks (parsing, compression, encryption) |
| **Multithreading** | Spawns threads within same process | No (Subject to GIL) | Blocking disk I/O, database writes |
| **Asyncio** | Single-threaded cooperative event loop | No | Scraping thousands of HTTP/REST endpoints concurrently |

#### Code Example 1: Asynchronous High-Concurrency API Ingestion (Asyncio)
```python
import asyncio
from typing import List, Dict, Any

async def fetch_page_async(semaphore: asyncio.Semaphore, page_id: int) -> Dict[str, Any]:
    """
    Simulates asynchronous HTTP request with semaphore rate-limiting.
    """
    async with semaphore:                                     # Limit concurrent active requests
        await asyncio.sleep(0.05)                             # Non-blocking cooperative I/O wait
        return {"page": page_id, "status": 200, "data": [f"item_{page_id}_1", f"item_{page_id}_2"]}

async def run_concurrent_ingestion(total_pages: int = 50, max_concurrency: int = 10) -> List[Dict[str, Any]]:
    """Runs concurrent async extraction across hundreds of pages."""
    rate_limiter = asyncio.Semaphore(max_concurrency)         # Prevent overwhelming upstream API
    tasks = [fetch_page_async(rate_limiter, p) for p in range(1, total_pages + 1)]
    results = await asyncio.gather(*tasks)                    # Concurrently execute and gather results
    return results

# Invocation:
# payload_results = asyncio.run(run_concurrent_ingestion())
```

#### Code Example 2: Parallel CPU-Bound Data Processing (Multiprocessing)
```python
from concurrent.futures import ProcessPoolExecutor
from typing import List

def parse_and_clean_record_chunk(records: List[str]) -> int:
    """CPU-intensive transformation task executing in a dedicated process."""
    valid_count = 0
    for rec in records:
        if rec and len(rec) > 10:
            valid_count += 1
    return valid_count

def parallel_process_records(record_chunks: List[List[str]], num_workers: int = 4) -> int:
    """Dispatches CPU-heavy chunks across multiple CPU cores via ProcessPoolExecutor."""
    total_valid = 0
    with ProcessPoolExecutor(max_workers=num_workers) as executor:
        # Maps work chunks across isolated OS worker processes (bypassing the GIL)
        results = executor.map(parse_and_clean_record_chunk, record_chunks)
        total_valid = sum(results)
    return total_valid
```
---

### 3.5 Modern Static Analysis & Tooling

- **`ruff`:** Ultra-fast linter and code formatter written in Rust (replaces Flake8, isort, and Black; 100x faster).
- **`mypy`:** Static type checker enforcing strict PEP 484 type annotations.
- **`pre-commit`:** Git hook framework ensuring all linters and tests pass before code is committed.

#### References
- [Python Official Documentation](https://www.python.org/doc/)
- [GeeksforGeeks: Python Multithreading vs Multiprocessing](https://www.geeksforgeeks.org/multithreading-or-multiprocessing-with-python/)
- [DataCamp: Python Concurrency](https://www.datacamp.com/tutorial/python-concurrency)
