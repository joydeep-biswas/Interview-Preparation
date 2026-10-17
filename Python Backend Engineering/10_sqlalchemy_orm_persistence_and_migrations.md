# 10. SQLAlchemy ORM, Persistence, and Migrations

A comprehensive, production-grade guide to database persistence in Python with SQLAlchemy 2.0, Core vs. ORM architecture, type-annotated declarative models with `Mapped[T]`, asynchronous session management with `asyncpg`, Identity Map and Unit of Work internals, eliminating the N+1 query problem with eager loading strategies (`selectinload`/`joinedload`), optimistic vs. pessimistic locking, engine connection pooling, database migrations with Alembic, and a detailed engineering comparison with Pyway and Flyway-style SQL migrations for Senior Backend Engineers and Database Architects.

---

## Architectural Overview: SQLAlchemy 2.0 Async Pipeline & Identity Map

```mermaid
flowchart TD
    subgraph AppServiceLayer ["Application & Domain Service Layer"]
        ServiceLogic["Business Use Case / Service Function"]
        SessionCtx["AsyncSession Context (async with async_session())"]
    end

    subgraph SQLAlchemy_Engine ["SQLAlchemy 2.0 ORM Engine"]
        direction TB
        IdentityMap["Identity Map (In-Memory Cache)<br/>Guarantees exactly 1 Python Object per Database PK"]
        UoWTracker["Unit of Work State Tracker<br/>Tracks: Pending, Persistent, Dirty, Deleted"]
        StatementCompiler["SQLAlchemy Core Compiler<br/>(Compiles select() constructs to dialect SQL)"]
    end

    subgraph AsyncDriverTier ["Asynchronous Driver & Pooling Tier"]
        AsyncEngine["AsyncEngine (create_async_engine)"]
        GreenletBridge["Greenlet Context Switcher (SQLAlchemy <-> asyncpg)"]
        QueuePoolEngine["QueuePool (pool_size=20, max_overflow=10, pre_ping=True)"]
        AsyncPG["asyncpg Driver (High-performance binary protocol)"]
    end

    subgraph MigrationTier ["Database Migration Subsystems"]
        AlembicRunner["Alembic (Python-Driven DAG Revisions)<br/>(Autogenerate from Base.metadata, Programmatic Python Data Backfills, Branch Merging (alembic merge heads))"]
        PywayRunner["Pyway / Flyway (Pure Raw SQL Versioning)<br/>(Linear Version Scripts (V1__init.sql), Language-Agnostic / Polyglot Stacks, Strict DBA DDL Control)"]
    end

    subgraph PostgreSQL_Server ["PostgreSQL Database Server"]
        PostgresInstance["PostgreSQL Engine & Active Transactions"]
        SchemaHistory["alembic_version / pyway_schema_history"]
    end

    ServiceLogic --> SessionCtx
    SessionCtx --> IdentityMap
    SessionCtx --> UoWTracker
    UoWTracker -->|session.flush()| StatementCompiler
    StatementCompiler --> AsyncEngine
    AsyncEngine --> GreenletBridge
    GreenletBridge --> QueuePoolEngine
    QueuePoolEngine --> AsyncPG
    AsyncPG --> PostgresInstance

    AlembicRunner -->|Executes DDL| PostgresInstance
    AlembicRunner --> SchemaHistory
    PywayRunner -->|Executes Raw SQL| PostgresInstance
    PywayRunner --> SchemaHistory

    style AppServiceLayer fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style SQLAlchemy_Engine fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style AsyncDriverTier fill:#0f172a,stroke:#f59e0b,stroke-width:1.5px,color:#fde68a
    style MigrationTier fill:#0f172a,stroke:#06b6d4,stroke-width:1.5px,color:#67e8f9
    style PostgreSQL_Server fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c084fc
    style ServiceLogic fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style SessionCtx fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style IdentityMap fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style UoWTracker fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style StatementCompiler fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style AsyncEngine fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style GreenletBridge fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style QueuePoolEngine fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style AsyncPG fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style AlembicRunner fill:#1f2937,stroke:#06b6d4,stroke-width:2px,color:#fff
    style PywayRunner fill:#1f2937,stroke:#06b6d4,stroke-width:2px,color:#fff
    style PostgresInstance fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style SchemaHistory fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
```

---

## Table of Contents
1. [SQLAlchemy 2.0 Architecture: Core vs. ORM](#1-sqlalchemy-20-architecture-core-vs-orm)
   - [1.1 Conceptual Foundations: Why Object-Relational Mappers Exist](#11-conceptual-foundations-why-object-relational-mappers-exist)
   - [1.2 The 2.0 Unification: Deprecating `session.query()` in Favor of `select()`](#12-the-20-unification-deprecating-sessionquery-in-favor-of-select)
   - [1.3 SQL Expression Language (Core) vs. Object Relational Mapper (ORM)](#13-sql-expression-language-core-vs-object-relational-mapper-orm)
2. [Modern Declarative Models with `Mapped[T]` and `mapped_column`](#2-modern-declarative-models-with-mappedt-and-mapped_column)
   - [2.1 Type-Safe Models: `DeclarativeBase` and PEP 484 Annotations](#21-type-safe-models-declarativebase-and-pep-484-annotations)
   - [2.2 Primary Keys, Indexes, Constraints, and Defaults](#22-primary-keys-indexes-constraints-and-defaults)
   - [2.3 Custom Type Decorators & PostgreSQL JSONB Mapping](#23-custom-type-decorators--postgresql-jsonb-mapping)
   - [2.4 Polymorphic Table Inheritance: Single Table vs. Joined Table](#24-polymorphic-table-inheritance-single-table-vs-joined-table)
3. [Asynchronous Database Operations with `asyncpg`](#3-asynchronous-database-operations-with-asyncpg)
   - [3.1 Configuring `create_async_engine` & Driver Flags](#31-configuring-create_async_engine--driver-flags)
   - [3.2 The Greenlet Bridge Internals](#32-the-greenlet-bridge-internals)
   - [3.3 The `AsyncSession` Lifecycle & `async_sessionmaker`](#33-the-asyncsession-lifecycle--async_sessionmaker)
4. [Unit of Work, Identity Map & Session Lifecycles](#4-unit-of-work-identity-map--session-lifecycles)
   - [4.1 The Identity Map: Object Uniqueness Guarantees](#41-the-identity-map-object-uniqueness-guarantees)
   - [4.2 The Four Object States: Transient, Pending, Persistent, and Detached](#42-the-four-object-states-transient-pending-persistent-and-detached)
   - [4.3 Session State Operations: `flush()`, `commit()`, `rollback()`, and `refresh()`](#43-session-state-operations-flush-commit-rollback-and-refresh)
5. [Relationship Mapping & The N+1 Query Disaster](#5-relationship-mapping--the-n1-query-disaster)
   - [5.1 One-to-Many, Many-to-One, and Many-to-Many Mappings](#51-one-to-many-many-to-one-and-many-to-many-mappings)
   - [5.2 Anatomy of the N+1 Query Problem](#52-anatomy-of-the-n1-query-problem)
   - [5.3 Eager Loading Strategies: `selectinload()`, `joinedload()`, and `contains_eager()`](#53-eager-loading-strategies-selectinload-joinedload-and-contains_eager)
   - [5.4 Production Guardrail: `lazy='raise'` in Asynchronous Runtimes](#54-production-guardrail-lazyraise-in-asynchronous-runtimes)
6. [Engine Connection Pooling & Resilience](#6-engine-connection-pooling--resilience)
   - [6.1 Pool Architecture: `QueuePool`, `NullPool`, and Sizing Formulas](#61-pool-architecture-queuepool-nullpool-and-sizing-formulas)
   - [6.2 Connection Liveness: `pool_pre_ping=True` & `pool_recycle`](#62-connection-liveness-pool_pre_pingtrue--pool_recycle)
   - [6.3 Pairing SQLAlchemy with External Proxies (PgBouncer)](#63-pairing-sqlalchemy-with-external-proxies-pgbouncer)
7. [Concurrency Control: Pessimistic vs. Optimistic Locking](#7-concurrency-control-pessimistic-vs-optimistic-locking)
   - [7.1 Pessimistic Row Locking (`SELECT ... FOR UPDATE / SKIP LOCKED`)](#71-pessimistic-row-locking-select--for-update--skip-locked)
   - [7.2 Optimistic Concurrency Control with `version_id_col`](#72-optimistic-concurrency-control-with-version_id_col)
8. [Database Migrations with Alembic](#8-database-migrations-with-alembic)
   - [8.1 Alembic Architecture: `alembic.ini` and `env.py`](#81-alembic-architecture-alembicini-and-envpy)
   - [8.2 Configuring Asynchronous Migrations in `env.py`](#82-configuring-asynchronous-migrations-in-envpy)
   - [8.3 Autogenerating Revisions & Safe Review Practices](#83-autogenerating-revisions--safe-review-practices)
   - [8.4 Migration Branching, Merge Heads, and Offline SQL Generation](#84-migration-branching-merge-heads-and-offline-sql-generation)
9. [Alembic vs. Pyway & Flyway: The Database Migration Paradigm Showdown](#9-alembic-vs-pyway--flyway-the-database-migration-paradigm-showdown)
   - [9.1 What is Pyway? Flyway-Style Pure SQL Migrations for Python](#91-what-is-pyway-flyway-style-pure-sql-migrations-for-python)
   - [9.2 Architectural Comparison: Python-Code-Driven vs. Pure SQL Migrations](#92-architectural-comparison-python-code-driven-vs-pure-sql-migrations)
   - [9.3 What is Used Generally in Industry and Why?](#93-what-is-used-generally-in-industry-and-why)
   - [9.4 Zero-Downtime Migration Patterns: Expand and Contract](#94-zero-downtime-migration-patterns-expand-and-contract)
   - [9.5 Non-Blocking Index Creation in PostgreSQL: `CREATE INDEX CONCURRENTLY`](#95-non-blocking-index-creation-in-postgresql-create-index-concurrently)
10. [Summary Comparison & Engineering Reference Matrix](#10-summary-comparison--engineering-reference-matrix)
11. [References & Further Reading](#11-references--further-reading)

---

# 1. SQLAlchemy 2.0 Architecture: Core vs. ORM

### 1.1 Conceptual Foundations: Why Object-Relational Mappers Exist

#### Intuitive Analogy: The Translator Between Two Alien Civilizations
Imagine two civilizations that must cooperate:
* **The Object-Oriented Realm (Python)**: Thinks in graphs of rich objects, memory pointers, polymorphism, encapsulation, and inheritance (`customer.orders[0].items`).
* **The Relational Realm (PostgreSQL)**: Thinks in mathematical sets, two-dimensional tables, foreign key constraints, joins, and disk blocks.

An **Object-Relational Mapper (ORM)** is the master diplomat standing between them:
1. It translates Python class attribute mutations (`order.status = "PAID"`) into ANSI SQL commands (`UPDATE orders SET status = 'PAID' WHERE id = ...`).
2. It translates raw tabular database row bytes into rich Python objects.
3. It prevents security vulnerabilities like SQL Injection by compiling parameters through safe database drivers.

---

### 1.2 The 2.0 Unification: Deprecating `session.query()` in Favor of `select()`

In legacy SQLAlchemy 1.x, there was a confusing, fragmented API split:
* If you wrote low-level SQL, you used SQLAlchemy Core (`select([users_table]).where(...)`).
* If you used models, you used a completely different API: `session.query(User).filter(...)`.

SQLAlchemy 2.0 completely deprecated `session.query()`, unifying both paradigms around a single, highly expressive, type-safe construct: **`select()`**.

```python
# ==================== LEGACY 1.x (DEPRECATED) ====================
# users = session.query(User).filter(User.age > 21).all()

# ==================== MODERN 2.0 (STANDARD) ====================
from sqlalchemy import select

# Constructs an abstract Syntax Tree of the SQL statement
stmt = (
    select(User)
    .where(User.age > 21)
    .order_by(User.created_at.desc())
    .limit(50)
)

# Execute query asynchronously
result = await session.scalars(stmt)
users = result.all()
```

---

### 1.3 SQL Expression Language (Core) vs. Object Relational Mapper (ORM)

* **SQLAlchemy Core**: The SQL Expression Language provides an abstraction over database dialects, query compilation, and schema definitions (`Table`, `Column`). It returns lightweight `Row` tuples with zero ORM overhead.
* **SQLAlchemy ORM**: Built directly on top of Core, the ORM adds the **Identity Map**, relationship graphs, change tracking (Unit of Work), and automatic persistence of domain models.

---

# 2. Modern Declarative Models with `Mapped[T]` and `mapped_column`

### 2.1 Type-Safe Models: `DeclarativeBase` and PEP 484 Annotations

SQLAlchemy 2.0 introduces native type-hinted mapping via `Mapped[T]` and `mapped_column()`:
* Full static type validation with `mypy` and IDE autocompletion.
* Eliminates boilerplate `Column(Integer, nullable=False)`.

```python
from datetime import datetime
from decimal import Decimal
import uuid

from sqlalchemy import String, Numeric, DateTime, ForeignKey, Index, func
from sqlalchemy.dialects.postgresql import UUID, JSONB
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship

class Base(DeclarativeBase):
    """Base class for all declarative models in the application."""
    type_annotation_map = {
        dict: JSONB, # Map Python dicts to PostgreSQL JSONB by default
    }

class Customer(Base):
    __tablename__ = "customers"

    id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True), primary_key=True, default=uuid.uuid4
    )
    email: Mapped[str] = mapped_column(String(255), unique=True, index=True, nullable=False)
    full_name: Mapped[str] = mapped_column(String(100), nullable=False)
    is_active: Mapped[bool] = mapped_column(default=True, server_default="true")
    created_at: Mapped[datetime] = mapped_column(
        DateTime(timezone=True), server_default=func.now()
    )

    # 1-to-Many Relationship
    orders: Mapped[list["Order"]] = relationship(
        back_populates="customer", cascade="all, delete-orphan", lazy="raise"
    )

class Order(Base):
    __tablename__ = "orders"

    id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True), primary_key=True, default=uuid.uuid4
    )
    customer_id: Mapped[uuid.UUID] = mapped_column(
        ForeignKey("customers.id", ondelete="CASCADE"), index=True, nullable=False
    )
    total_amount: Mapped[Decimal] = mapped_column(Numeric(12, 2), nullable=False)
    status: Mapped[str] = mapped_column(String(32), default="PENDING", index=True)
    metadata_payload: Mapped[dict] = mapped_column(default=dict)

    # Many-to-1 Relationship
    customer: Mapped[Customer] = relationship(back_populates="orders", lazy="raise")

    # Composite Table Index
    __table_args__ = (
        Index("idx_orders_customer_status", "customer_id", "status"),
    )
```

---

### 2.2 Primary Keys, Indexes, Constraints, and Defaults

* **Python-Side Defaults (`default=...`)**: Evaluated in Python memory prior to SQL compilation (e.g. `default=uuid.uuid4`).
* **Database-Side Defaults (`server_default=...`)**: Executed directly by the PostgreSQL engine (e.g. `server_default=func.now()` or `server_default="gen_random_uuid()"`). Always use server defaults for timestamps and database sequences!

---

### 2.3 Custom Type Decorators & PostgreSQL JSONB Mapping

To store custom Python Value Objects (like encrypted strings or money objects) cleanly:

```python
from sqlalchemy.types import TypeDecorator, String
import base64

class EncryptedString(TypeDecorator):
    """Custom TypeDecorator that encrypts data before storing in DB."""
    impl = String
    cache_ok = True

    def process_bind_param(self, value: str | None, dialect) -> str | None:
        if value is not None:
            # Encrypt string prior to sending to PostgreSQL
            return base64.b64encode(value.encode("utf-8")).decode("utf-8")
        return None

    def process_result_value(self, value: str | None, dialect) -> str | None:
        if value is not None:
            # Decrypt string when retrieved from PostgreSQL
            return base64.b64decode(value.encode("utf-8")).decode("utf-8")
        return None
```

---

### 2.4 Polymorphic Table Inheritance: Single Table vs. Joined Table

When modeling class hierarchies in SQLAlchemy 2.0 (such as `PaymentMethod` with subclasses `CreditCard` and `CryptoWallet`):

#### 1. Single Table Inheritance (Optimal for Low Subclass Variance)
All subclasses share a single database table. A discriminator column indicates the concrete type:

```python
class PaymentMethod(Base):
    __tablename__ = "payment_methods"

    id: Mapped[int] = mapped_column(primary_key=True)
    customer_id: Mapped[str] = mapped_column(String(50))
    type: Mapped[str] = mapped_column(String(30))  # Discriminator column

    __mapper_args__ = {
        "polymorphic_on": "type",
        "polymorphic_identity": "base",
    }

class CreditCard(PaymentMethod):
    # Specific fields can be nullable in the shared table
    last_four: Mapped[str | None] = mapped_column(String(4))
    exp_month: Mapped[int | None] = mapped_column()

    __mapper_args__ = {
        "polymorphic_identity": "credit_card",
    }

class CryptoWallet(PaymentMethod):
    wallet_address: Mapped[str | None] = mapped_column(String(128))

    __mapper_args__ = {
        "polymorphic_identity": "crypto_wallet",
    }
```

#### 2. Joined Table Inheritance (Optimal for High Subclass Variance)
Each subclass has its own dedicated table containing only its unique columns, joined via a foreign key to the base table's primary key:

```python
class Employee(Base):
    __tablename__ = "employees"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100))
    role_type: Mapped[str] = mapped_column(String(30))
    __mapper_args__ = {"polymorphic_on": "role_type", "polymorphic_identity": "employee"}

class Engineer(Employee):
    __tablename__ = "engineers"
    id: Mapped[int] = mapped_column(ForeignKey("employees.id"), primary_key=True)
    primary_language: Mapped[str] = mapped_column(String(50))
    __mapper_args__ = {"polymorphic_identity": "engineer"}
```

---

# 3. Asynchronous Database Operations with `asyncpg`

### 3.1 Configuring `create_async_engine` & Driver Flags

`asyncpg` is the fastest PostgreSQL driver in the Python ecosystem, communicating directly in PostgreSQL's native binary protocol:

```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession, async_sessionmaker

DATABASE_URL = "postgresql+asyncpg://postgres:secret@localhost:5432/enterprise_db"

async_engine = create_async_engine(
    DATABASE_URL,
    echo=False,                  # Set to True only during local debugging
    pool_size=20,                # Steady-state connections in pool
    max_overflow=10,             # Spike connections above pool_size
    pool_timeout=30.0,           # Seconds to wait for available connection
    pool_recycle=1800,           # Recycle connections every 30 minutes
    pool_pre_ping=True,          # Test socket health with 'SELECT 1' before checkout
    connect_args={
        "server_settings": {
            "application_name": "payment_service_worker",
            "jit": "off",        # Disable Postgres JIT for fast OLTP queries
        }
    },
)
```

---

### 3.2 The Greenlet Bridge Internals

SQLAlchemy's ORM operations are inherently synchronous (navigating relationships, instrumenting attributes).
To make synchronous ORM code run on top of an asynchronous `asyncpg` socket driver without rewriting the entire ORM from scratch, SQLAlchemy uses the **Greenlet C-extension (`greenlet`)**:
1. When your code awaits an async query (`await session.execute(...)`), SQLAlchemy spawns a lightweight greenlet coroutine.
2. If the ORM triggers a database query, it yields control across the greenlet boundary back to the AsyncIO event loop.
3. The event loop awaits the `asyncpg` network socket via `epoll`.
4. When socket data arrives, greenlet resumes the ORM execution frame seamlessly!

---

### 3.3 The `AsyncSession` Lifecycle & `async_sessionmaker`

```python
# Session factory singleton
async_session_factory = async_sessionmaker(
    bind=async_engine,
    class_=AsyncSession,
    expire_on_commit=False,  # CRITICAL: Prevents detached attribute reload errors!
    autoflush=False,         # Explicit flush control
)

async def get_db_session() -> AsyncSession:
    """Dependency injection generator for FastAPI."""
    async with async_session_factory() as session:
        try:
            yield session
            await session.commit()
        except Exception:
            await session.rollback()
            raise
```
> [!IMPORTANT]
> **Why `expire_on_commit=False` is Essential in Async**:
> By default, SQLAlchemy expires all attributes on model instances after `session.commit()`.
> In synchronous code, accessing an expired attribute later automatically issues a lazy-load SQL query.
> In **asynchronous** code, accessing an expired attribute outside an active awaitable query raises **`MissingGreenlet: greenlet_spawn has not been spawned`**! Setting `expire_on_commit=False` guarantees that committed objects keep their in-memory state accessible.

---

# 4. Unit of Work, Identity Map & Session Lifecycles

### 4.1 The Identity Map: Object Uniqueness Guarantees

The **Identity Map** is an in-memory hash table inside the `AsyncSession` that maps `(ModelClass, PrimaryKey)` to the exact Python instance:

```python
# Querying the exact same customer twice in the same session:
stmt = select(Customer).where(Customer.id == cust_id)
c1 = (await session.scalars(stmt)).first()
c2 = (await session.scalars(stmt)).first()

# Both variables reference the EXACT SAME in-memory object!
assert c1 is c2
```
*Benefits*:
1. Eliminates redundant database reads for objects already loaded.
2. Prevents conflicting mutations where two separate instances of the same database row overwrite each other!

---

### 4.2 The Four Object States: Transient, Pending, Persistent, and Detached

```mermaid
stateDiagram-v2
    [*] --> Transient: Customer(name="Alice")
    note right of Transient: In Python memory only.<br/>No database identity. Not in session.

    Transient --> Pending: session.add(customer)
    note right of Pending: Added to session.<br/>No SQL emitted yet. No primary key.

    Pending --> Persistent: await session.flush() / commit()
    note right of Persistent: Row exists in DB.<br/>Primary key populated. Tracked by Identity Map.

    Persistent --> Detached: await session.close()
    note right of Detached: Session closed.<br/>Object in memory, but detached from active DB connection.

    Persistent --> Deleted: session.delete(customer)
    Deleted --> [*]: await session.commit()
```

---

### 4.3 Session State Operations: `flush()`, `commit()`, `rollback()`, and `refresh()`

* **`session.flush()`**: Compiles all pending insertions, updates, and deletions into SQL statements and sends them to PostgreSQL over the wire. **Does NOT commit the transaction!** Database triggers execute and auto-generated Primary Keys are populated on instances.
* **`session.commit()`**: Issues `COMMIT` to PostgreSQL, making all flushed changes permanently durable.
* **`session.rollback()`**: Issues `ROLLBACK` to PostgreSQL, aborting the transaction and reverting in-memory attributes.
* **`session.refresh(obj)`**: Discards in-memory state and re-reads the row fresh from PostgreSQL.

---

# 5. Relationship Mapping & The N+1 Query Disaster

### 5.1 One-to-Many, Many-to-One, and Many-to-Many Mappings

```python
# Many-to-Many Association Table
from sqlalchemy import Table, Column

order_tags_association = Table(
    "order_tags",
    Base.metadata,
    Column("order_id", ForeignKey("orders.id", ondelete="CASCADE"), primary_key=True),
    Column("tag_id", ForeignKey("tags.id", ondelete="CASCADE"), primary_key=True),
)

class Tag(Base):
    __tablename__ = "tags"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50), unique=True)
```

---

### 5.2 Anatomy of the N+1 Query Problem

Suppose you fetch 100 customers and iterate through their orders:

```python
# ANTI-PATTERN: The N+1 Query Disaster
stmt = select(Customer).limit(100)
customers = (await session.scalars(stmt)).all()

for customer in customers:
    # If relationship is lazily loaded:
    # 1 query to fetch 100 customers + 100 queries for each customer's orders = 101 queries!
    print(len(customer.orders))
```
At scale, the N+1 problem overwhelms connection pools and multiplies API latency by $10\times$ to $50\times$!

---

### 5.3 Eager Loading Strategies: `selectinload()`, `joinedload()`, and `contains_eager()`

SQLAlchemy provides explicit query loading options to eliminate N+1 queries completely:

#### 1. `selectinload()` (Optimal for One-to-Many Collections)
Issues exactly **two queries**:
1. Query 1: `SELECT * FROM customers LIMIT 100;`
2. Query 2: `SELECT * FROM orders WHERE customer_id IN ('id_1', 'id_2', ..., 'id_100');`
SQLAlchemy automatically stitches the collections together in memory!

```python
from sqlalchemy.orm import selectinload

stmt = (
    select(Customer)
    .options(selectinload(Customer.orders))
    .limit(100)
)
customers = (await session.scalars(stmt)).all() # Exactly 2 queries executed!
```

#### 2. `joinedload()` (Optimal for Many-to-One and One-to-One)
Executes a single query with a `LEFT OUTER JOIN`:

```python
from sqlalchemy.orm import joinedload

# Fetch orders with their parent customer in a single SQL query
stmt = select(Order).options(joinedload(Order.customer)).limit(100)
orders = (await session.scalars(stmt)).all() # Exactly 1 query executed!
```

---

### 5.4 Production Guardrail: `lazy='raise'` in Asynchronous Runtimes

> [!CAUTION]
> In asynchronous code, standard lazy loading (`lazy="select"`) is **physically impossible** because Python cannot perform an implicit `await` inside an attribute access like `customer.orders`!
> If you omit eager loading on an async relationship, SQLAlchemy raises:
> `sqlalchemy.exc.InvalidRequestError: Cannot perform operation in asynchronous mode; attribute is not loaded`.
> 
> **Production Standard**: Always configure all relationships with **`lazy="raise"`**. This forces queries to explicitly state their loading strategy, raising an immediate error in testing if a developer forgets to eager-load!

---

# 6. Engine Connection Pooling & Resilience

### 6.1 Pool Architecture: `QueuePool`, `NullPool`, and Sizing Formulas

* **`QueuePool`**: The default connection pool. Maintains a fixed pool of persistent TCP connections, allowing temporary overflow during load bursts.
* **`NullPool`**: Disables connection pooling inside SQLAlchemy entirely. Opens and closes a fresh socket on every session checkout.

```
Total Max App Connections = pool_size + max_overflow
```
If you run 4 Kubernetes pods, each running 4 Gunicorn workers with `pool_size=20, max_overflow=10`:
$$\text{Max PostgreSQL Connections} = 4 \text{ pods} \times 4 \text{ workers} \times (20 + 10) = 480 \text{ connections!}$$
Ensure your PostgreSQL `max_connections` setting can comfortably accommodate this ceiling!

---

### 6.2 Connection Liveness: `pool_pre_ping=True` & `pool_recycle`

Cloud load balancers and firewalls (such as AWS NAT Gateways or Azure load balancers) silently terminate idle TCP sockets after 350 seconds without sending a TCP RST packet.
When the application attempts to use that dead socket, it hangs until the OS TCP timeout (15 minutes).

* **`pool_pre_ping=True`**: Before handing a connection to your code, SQLAlchemy issues a fast `SELECT 1` ping. If the socket is dead, it drops it and connects fresh without raising an exception to the user!
* **`pool_recycle=1800`**: Automatically retires and reconnects sockets older than 30 minutes.

---

### 6.3 Pairing SQLAlchemy with External Proxies (PgBouncer)

When scaling to hundreds of application workers, bypass SQLAlchemy's internal pooling and let **PgBouncer** handle pooling at the network edge:

```python
from sqlalchemy.pool import NullPool

# When connecting through PgBouncer in Transaction Pooling Mode:
engine = create_async_engine(
    "postgresql+asyncpg://user:pass@pgbouncer-host:6432/db",
    poolclass=NullPool, # Let PgBouncer manage connection reuse!
)
```

---

# 7. Concurrency Control: Pessimistic vs. Optimistic Locking

### 7.1 Pessimistic Row Locking (`SELECT ... FOR UPDATE / SKIP LOCKED`)

Pessimistic locking prevents concurrent transactions from modifying target rows by acquiring exclusive row-level locks in PostgreSQL:

```python
# 1. Standard Pessimistic Lock: Blocks concurrent transactions until commit
stmt = (
    select(Order)
    .where(Order.id == order_id)
    .with_for_update(nowait=False)
)
order = (await session.scalars(stmt)).one()

# 2. Worker Queue Pattern: SKIP LOCKED allows multiple workers to consume tasks concurrently without collisions!
queue_stmt = (
    select(Order)
    .where(Order.status == "QUEUED")
    .order_by(Order.created_at)
    .limit(1)
    .with_for_update(skip_locked=True) # Skips rows already locked by other workers!
)
```

---

### 7.2 Optimistic Concurrency Control with `version_id_col`

Pessimistic locking reduces database throughput by holding row locks.
**Optimistic Concurrency Control** allows concurrent transactions to proceed without locks, validating at commit time that no other transaction modified the record:

```python
class Inventory(Base):
    __tablename__ = "inventories"

    id: Mapped[int] = mapped_column(primary_key=True)
    sku: Mapped[str] = mapped_column(String(50), unique=True)
    stock: Mapped[int] = mapped_column()
    
    # Optimistic locking version counter
    version: Mapped[int] = mapped_column(default=1)

    __mapper_args__ = {
        "version_id_col": version, # SQLAlchemy automatically appends: WHERE version = <current>
    }
```

When an update is flushed, SQLAlchemy executes:
```sql
UPDATE inventories SET stock = 45, version = 2 WHERE id = 101 AND version = 1;
```
If another transaction updated the row first, the `WHERE version = 1` clause matches 0 rows. SQLAlchemy immediately raises **`StaleDataError`**, protecting your application from silent overwrites!

#### Production Optimistic Locking Auto-Retry Pattern:
```python
from sqlalchemy.orm.exc import StaleDataError
from tenacity import retry, retry_if_exception_type, stop_after_attempt, wait_random_exponential

@retry(
    retry=retry_if_exception_type(StaleDataError),
    stop=stop_after_attempt(5),
    wait=wait_random_exponential(min=0.01, max=0.5),
    reraise=True,
)
async def decrement_inventory_optimistic(session_factory, sku: str, quantity: int) -> int:
    """
    Attempts to decrement inventory. If a concurrent transaction modifies the row
    before commit, catches StaleDataError and transparently retries with fresh data!
    """
    async with session_factory() as session:
        async with session.begin():
            stmt = select(Inventory).where(Inventory.sku == sku)
            item = (await session.scalars(stmt)).one()

            if item.stock < quantity:
                raise ValueError(f"Insufficient stock for {sku}. Available: {item.stock}")

            item.stock -= quantity
            # session.commit() will execute UPDATE ... WHERE version = <item.version>
            # Raises StaleDataError if concurrent write occurred!
            await session.commit()
            return item.stock
```

---

# 8. Database Migrations with Alembic

### 8.1 Alembic Architecture: `alembic.ini` and `env.py`

Alembic is the standard database migration tool for SQLAlchemy:
* **`alembic.ini`**: Main configuration file (sets migration script directory).
* **`alembic/env.py`**: Python script executed on every migration command. Wires database connections and links `target_metadata = Base.metadata` for autogeneration.

---

### 8.2 Configuring Asynchronous Migrations in `env.py`

Because Alembic's core runner is synchronous, running migrations with `asyncpg` requires using `async_engine.connect()` paired with `run_sync`:

```python
# In alembic/env.py:
import asyncio
from sqlalchemy.ext.asyncio import create_async_engine
from enterprise_service.infrastructure.db.models import Base

target_metadata = Base.metadata

def do_run_migrations(connection):
    context.configure(
        connection=connection,
        target_metadata=target_metadata,
        compare_type=True, # Detects column type changes (e.g. String(50) -> String(100))
    )
    with context.begin_transaction():
        context.run_migrations()

async def run_async_migrations():
    connectable = create_async_engine(get_database_url())
    async with connectable.connect() as connection:
        await connection.run_sync(do_run_migrations)
    await connectable.dispose()

def run_migrations_online():
    asyncio.run(run_async_migrations())
```

---

### 8.3 Autogenerating Revisions & Safe Review Practices

```bash
# 1. Autogenerate migration script comparing models against active database
alembic revision --autogenerate -m "create_customers_and_orders_table"

# 2. Upgrade database to the newest migration
alembic upgrade head

# 3. Rollback the most recent migration
alembic downgrade -1
```

> [!WARNING]
> **Never Blindly Trust Autogenerated Migrations**:
> Alembic autogeneration cannot detect:
> 1. Column renames (it interprets them as a `DROP COLUMN` followed by `ADD COLUMN`, which drops production data!).
> 2. Table renames.
> 3. Custom enum modifications.
> Always manually review and edit migration files before applying them in staging and production!

---

### 8.4 Migration Branching, Merge Heads, and Offline SQL Generation

When two developers create separate migrations on different Git branches, Alembic detects multiple heads on merge:
```bash
# Detect multiple migration heads
alembic heads

# Merge branches into a unified revision
alembic merge heads -m "merge_auth_and_billing_branches"

# Generate offline SQL script for DBA security review without connecting to DB
alembic upgrade head --sql > release_v1.0_migration.sql
```

---

# 9. Alembic vs. Pyway & Flyway: The Database Migration Paradigm Showdown

### 9.1 What is Pyway? Flyway-Style Pure SQL Migrations for Python

In many enterprises (especially financial institutions, healthcare, and polyglot organizations), software architectures feature microservices written in Java (Spring Boot), Kotlin, Go, and Python.
In the Java ecosystem, **Flyway** is the undisputed king of database migrations.
**Pyway** (and similar tools like `yoyo-migrations` or running Flyway CLI directly) brings the **Flyway paradigm to Python**.

#### How Flyway / Pyway Operates:
Instead of writing Python migration files with SQLAlchemy operation calls, **you write pure, raw ANSI SQL scripts** stored in a migrations directory:

```mermaid
graph TD
    Root["migrations/"] --> V1["V1.0__create_customers_table.sql (Versioned Baseline)"]
    Root --> V2["V1.1__create_orders_table.sql (Versioned DDL)"]
    Root --> V3["V1.2__add_index_on_customer_email.sql (Versioned Index)"]
    Root --> R1["R__recreate_order_summary_view.sql (Repeatable View/Procedure)"]

    style Root fill:#0f172a,stroke:#3b82f6,stroke-width:2px,color:#f8fafc
    style V1 fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#f8fafc
    style V2 fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#f8fafc
    style V3 fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#f8fafc
    style R1 fill:#1e293b,stroke:#f59e0b,stroke-width:2px,color:#f8fafc
```

| Migration Prefix | Classification | Execution Semantics | Use Case |
| :--- | :--- | :--- | :--- |
| **`V<Version>__`** | Versioned Migration | Executed exactly once in strict numerical order; checksum saved in history | Schema creation (`CREATE TABLE`), altering columns, adding indexes |
| **`U<Version>__`** | Undo Migration | Reverts corresponding versioned migration | Automated rollbacks in continuous deployment pipelines |
| **`R__`** | Repeatable Migration | Re-executed every time its file checksum changes, after all versioned scripts | Views (`CREATE OR REPLACE VIEW`), stored procedures, triggers |

#### Example Raw SQL Migration Script (`V1.1__create_orders_table.sql`):
```sql
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_id UUID NOT NULL REFERENCES customers(id) ON DELETE CASCADE,
    total_amount NUMERIC(12, 2) NOT NULL,
    status VARCHAR(32) NOT NULL DEFAULT 'PENDING',
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_orders_customer_id ON orders (customer_id);
```

#### The `pyway_schema_history` Metadata Table:
When Pyway executes, it creates a table in PostgreSQL named `pyway_schema_history` (or `flyway_schema_history`):
* Computes a **SHA-256 cryptographic checksum** of every `.sql` file.
* Records: `installed_rank`, `version`, `description`, `script_name`, `checksum`, `installed_by`, `installed_on`, `execution_time_ms`, `success`.
* **Tamper Protection**: If a developer modifies `V1.0__create_customers_table.sql` after it has already run in production, Pyway detects that the local file checksum does not match the database checksum and **aborts immediately**, preventing schema drift!

---

### 9.2 Architectural Comparison: Python-Code-Driven vs. Pure SQL Migrations

```mermaid
flowchart TD
    subgraph AlembicParadigm ["Alembic Paradigm (Python ORM Model Centric)"]
        direction TB
        PyModel["SQLAlchemy DeclarativeBase Models"]
        AlembicDiff["Alembic Autogenerate Engine<br/>(Diffs Python Models vs live DB)"]
        PyRev["Revision File (.py)<br/>op.create_table(...)<br/>op.add_column(...)"]
        PyModel --> AlembicDiff
        AlembicDiff --> PyRev
    end

    subgraph PywayParadigm ["Pyway / Flyway Paradigm (Raw SQL Centric)"]
        direction TB
        RawSQL["Handcrafted DDL Scripts (.sql)<br/>V1.0__create_tables.sql<br/>V1.1__add_indexes.sql"]
        PywayEngine["Pyway Migration Runner<br/>(Calculates SHA-256 Checksums)"]
        DBHistory["pyway_schema_history Table"]
        RawSQL --> PywayEngine
        PywayEngine --> DBHistory
    end

    style AlembicParadigm fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style PywayParadigm fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style PyModel fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style AlembicDiff fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style PyRev fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style RawSQL fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style PywayEngine fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style DBHistory fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### Detailed Head-to-Head Comparison Matrix:

| Feature / Dimension | Alembic (SQLAlchemy Standard) | Pyway / Flyway (SQL Script Standard) |
| :--- | :--- | :--- |
| **Migration Format** | **Python code** (`.py`) invoking `op.*` methods | **Raw ANSI SQL** (`.sql`) files |
| **Autogeneration** | **Yes (`--autogenerate`)**: Compares Python models against live DB | **No**: Developers must write every `CREATE TABLE` and `ALTER` by hand |
| **Polyglot Stacks** | Python only (Tightly bound to SQLAlchemy) | **Universal**: Java, Go, Python, and Node share the exact same `.sql` scripts |
| **DBA Governance** | DBAs must read Python code or generate offline SQL | **Loved by DBAs**: Direct review of exact DDL statements and execution plans |
| **Branching / Merging** | **DAG-based Revisions**: Supports multiple heads (`alembic merge heads`) | **Strict Linear Versioning (`V1.0, V1.1`)**: Branch conflicts cause version collisions |
| **Data Backfills / Python Logic** | **First-Class**: Can import Python modules, call external APIs, hash passwords | **Restricted**: Limited to SQL statements or database stored procedures (PL/pgSQL) |
| **Downgrades (Rollbacks)** | Supports explicit `downgrade()` functions | Undo scripts require paid Flyway Pro or manual rollback scripts |
| **Checksum Verification** | Does not check file checksums after execution | **Strict SHA-256 verification**: Catches retroactive script tampering |

---

### 9.3 What is Used Generally in Industry and Why?

#### 1. The Python Ecosystem Standard: Alembic
In $85\text{--}90\%$ of professional Python backend engineering teams (FastAPI, Flask, SQLAlchemy):
* **Alembic is the undisputed industry standard**.
* **Why Alembic Dominates**:
  - **Single Source of Truth**: The developer modifies their Python `Mapped[T]` declarative models. Running `alembic revision --autogenerate` writes $95\%$ of the migration boilerplate automatically!
  - **Branch Merging in Large Teams**: When 20 developers work on separate Git feature branches, two developers might create migrations simultaneously. Pyway would fail because both create `V2.1__something.sql`. Alembic uses unique revision hashes (`1a2b3c4d5e`), allowing Git branches to be merged seamlessly with `alembic merge heads`.
  - **Programmatic Data Migrations**: Need to encrypt all existing email addresses using a KMS key during a migration? In Alembic, you write a standard Python loop with chunked pagination directly inside the `upgrade()` function!

#### 2. When is Pyway / Flyway Used and Why is it Better in Specific Contexts?
* **Enterprise Polyglot Microservices**: In organizations where the backend is a mixture of Java (Spring Boot) and Python (FastAPI), running Flyway or Pyway means **the entire company uses one unified migration toolchain**.
* **Strict DBA Teams**: In banking and insurance, database administrators (DBAs) will **never** allow an ORM to emit DDL. They mandate pure SQL scripts so they can inspect lock levels, table rewrites, and `EXPLAIN` execution plans before approving pull requests.
* **Complex Database Features**: Advanced PostgreSQL features (custom partitioning schemes, complex triggers, row-level security policies, foreign data wrappers) are awkward to express in SQLAlchemy's `op.*` syntax, but trivial in raw SQL.

---

### 9.4 Zero-Downtime Migration Patterns: Expand and Contract

> [!CAUTION]
> ### The Naive Migration Deployment Outage
> Suppose you rename a column from `full_name` to `display_name` in PostgreSQL:
> 1. Migration runs: `ALTER TABLE users RENAME COLUMN full_name TO display_name;`.
> 2. For the next 3 minutes while Kubernetes performs a rolling update, **all older active pods immediately crash with `UndefinedColumn: column "full_name" does not exist`**!
> 
> In 24/7 high-availability systems, **never execute breaking schema changes in a single step!**

#### The Three-Phase Expand-and-Contract Pattern:

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'actorBkg': '#1e293b', 'actorBorder': '#3b82f6', 'actorTextColor': '#ffffff', 'signalColor': '#93c5fd', 'signalTextColor': '#ffffff', 'noteBkgColor': '#1e293b', 'noteBorderColor': '#f59e0b', 'noteTextColor': '#fde68a', 'activationBkgColor': '#3b82f6', 'sequenceNumberColor': '#ffffff' }}}%%
sequenceDiagram
    autonumber
    participant AppOld as Old App Pods (v1.0)
    participant DB as PostgreSQL Database
    participant AppNew as New App Pods (v2.0)

    Note over DB: PHASE 1: EXPAND
    Note over DB: Run Migration: ADD COLUMN display_name NULLABLE;
    Note over AppOld,DB: Both v1.0 and v2.0 pods run simultaneously.
    Note over AppNew,DB: v2.0 pods write to BOTH full_name AND display_name (Dual-Write)!

    Note over DB: PHASE 2: BACKFILL
    Note over DB: Background worker batches: UPDATE users SET display_name = full_name WHERE display_name IS NULL;

    Note over DB: PHASE 3: CONTRACT
    Note over AppNew: Deploy v2.1: App reads ONLY from display_name.
    Note over AppOld: All v1.0 pods fully decommissioned.
    Note over DB: Run Migration: ALTER TABLE users DROP COLUMN full_name;
```

---

### 9.5 Non-Blocking Index Creation in PostgreSQL: `CREATE INDEX CONCURRENTLY`

In production, running a standard `CREATE INDEX idx_orders_status ON orders (status);` acquires an **`ACCESS EXCLUSIVE` lock**, completely locking the table against all incoming `INSERT`, `UPDATE`, and `DELETE` operations! On a 100-million-row table, this causes a 20-minute production outage.

#### The Safe Solution: `CREATE INDEX CONCURRENTLY`
PostgreSQL allows non-blocking index creation using `CONCURRENTLY`:
* Does not acquire an exclusive lock; incoming reads and writes proceed uninterrupted.
* **The Catch**: Cannot run inside an active transaction block (`BEGIN ... COMMIT`).

#### How to Execute `CONCURRENTLY` Safely in Alembic:
You must instruct Alembic to disable its automatic transaction wrapper using `autocommit_block()`:

```python
"""add_orders_status_index_concurrently

Revision ID: 9f8a7b6c5d4e
"""
from alembic import op

def upgrade() -> None:
    # Disable Alembic's transaction block so PostgreSQL can run CONCURRENTLY!
    with op.get_context().autocommit_block():
        op.execute(
            "CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_orders_status ON orders (status);"
        )

def downgrade() -> None:
    with op.get_context().autocommit_block():
        op.execute(
            "DROP INDEX CONCURRENTLY IF EXISTS idx_orders_status;"
        )
```

---

# 10. Summary Comparison & Engineering Reference Matrix

| Feature / Technique | Alembic (Python ORM Standard) | Pyway / Flyway (Raw SQL Standard) |
| :--- | :--- | :--- |
| **Primary Domain Fit** | Python-centric backends (FastAPI, Flask, SQLAlchemy) | Polyglot microservices, Java/Go/Python hybrid fleets |
| **Schema Definition** | Python `Mapped[T]` declarative models | Pure ANSI SQL DDL scripts |
| **Autogeneration** | **Yes (`alembic revision --autogenerate`)** | None (100% handcrafted SQL) |
| **Team Branch Merging** | **Non-linear DAG (`alembic merge heads`)** | Linear versioning (Prone to version collisions) |
| **Complex Data Migrations** | Python scripts with external APIs/KMS | SQL updates or PL/pgSQL procedures |
| **DBA Approval Flow** | Requires `alembic upgrade --sql` export | Direct Git diff on `.sql` files |
| **Tamper Detection** | None by default | Cryptographic SHA-256 checksum verification |
| **Non-blocking Indexing** | Requires `autocommit_block()` | Native SQL: `CREATE INDEX CONCURRENTLY` |

---

# 11. References & Further Reading

1. **SQLAlchemy 2.0 Official Documentation**: [https://docs.sqlalchemy.org/en/20/](https://docs.sqlalchemy.org/en/20/)
2. **Alembic Database Migration Tool**: [https://alembic.sqlalchemy.org/en/latest/](https://alembic.sqlalchemy.org/en/latest/)
3. **Pyway: Python Database Migration Tool**: [https://github.com/flyway/flyway](https://github.com/flyway/flyway)
4. **Evolutionary Database Design (Martin Fowler & Pramod Sadalage)**: [https://martinfowler.com/articles/evodb.html](https://martinfowler.com/articles/evodb.html)
5. **Zero-Downtime Database Migrations**: [https://stripe.com/blog/online-migrations](https://stripe.com/blog/online-migrations)
6. **Related Guides**:
   - See [09. Relational Databases and SQL](09_relational_databases_and_sql.md) for database internals, ACID, and indexing.
   - See [14. Testing, Observability, and Performance Engineering](14_testing_observability_and_performance_engineering.md) for database test isolation with SAVEPOINTs.
