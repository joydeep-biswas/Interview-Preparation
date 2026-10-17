# 01. Core Python and Object-Oriented Programming (OOP)

A comprehensive, production-grade guide to Python language mechanics, CPython object memory representations, variable pointer semantics, dynamic typing, scoping (LEGB), closures, dunder data models, the object creation lifecycle (`__new__` vs. `__init__`), attribute descriptors, `__slots__` memory optimization, multiple inheritance (C3 MRO), metaclasses, and modern structural typing constructs. Designed to provide both foundational clarity for learners and architectural depth for senior engineers.

---

## Architectural Overview: CPython Object Memory & Type Dispatch System

In Python, **everything is an object allocated on the heap**—including numbers, strings, functions, modules, and classes themselves. Python variables never store raw data values directly; they are simply 64-bit pointer references holding memory addresses that point to object structures on the heap.

```mermaid
flowchart TD
    subgraph ExecutionContext ["1. Execution Context (Stack Frame / Local Scope)"]
        direction TB
        VarA["Variable Identifier 'a'<br/>(Pointer in locals() dictionary / fastlocals array)"]
        VarB["Variable Identifier 'b'<br/>(Pointer in locals() dictionary / fastlocals array)"]
    end

    subgraph CPythonHeap ["2. CPython Heap Allocation (Managed by PyMalloc / OS Malloc)"]
        direction TB
        subgraph PyObjectStruct ["PyObject Base Header (Minimum 16 Bytes on 64-bit OS)"]
            RefCnt["ob_refcnt (8 Bytes / ssize_t)<br/>Tracks active references for Garbage Collection"]
            TypePtr["ob_type (8 Bytes / struct _typeobject*)<br/>Pointer to the Type / Class Object in Metaspace"]
        end

        subgraph ObjectPayload ["Type-Specific Payload Data"]
            DataPayload["Instance Payload Data<br/>(e.g., ob_size, ob_digit, contiguous pointer array)"]
        end

        subgraph InstanceNamespace ["Instance Namespace (Dynamic Attributes)"]
            DynDict["Instance __dict__ (PyDictObject: ~152+ Bytes)<br/>Suppressed when __slots__ is defined"]
        end

        PyObjectStruct --- ObjectPayload
        ObjectPayload -.->|"Default attribute storage"| InstanceNamespace
    end

    subgraph MetaclassLayer ["3. Type Metadata Layer (Metaclass / Class Object)"]
        direction TB
        TypeObject["PyTypeObject (e.g., PyLong_Type, PyList_Type, UserClass)<br/>Defines behavior, operator dispatch, and C-level slots"]
        MROTuple["tp_mro<br/>Tuple defining Method Resolution Order (C3 Linearization)"]
        TypeObject --- MROTuple
    end

    VarA -->|"Holds 64-bit Memory Address (0x10A4F)"| PyObjectStruct
    VarB -->|"Holds Same 64-bit Memory Address (0x10A4F)"| PyObjectStruct
    TypePtr -->|"Directs method dispatch & dunder calls"| TypeObject

    style ExecutionContext fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style CPythonHeap fill:#0f172a,stroke:#06b6d4,stroke-width:1.5px,color:#67e8f9
    style PyObjectStruct fill:#1e293b,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style ObjectPayload fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style InstanceNamespace fill:#1e293b,stroke:#ef4444,stroke-width:1.5px,color:#fca5a5
    style MetaclassLayer fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c084fc
    style VarA fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style VarB fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style RefCnt fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style TypePtr fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style DataPayload fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style DynDict fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style TypeObject fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style MROTuple fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
```

---

## Table of Contents
1. [Everything is an Object: The CPython Object Model](#1-everything-is-an-object-the-cpython-object-model)
   - [1.1 Conceptual Foundation: Sticky Notes vs. Boxes](#11-conceptual-foundation-sticky-notes-vs-boxes)
   - [1.2 C-Level Anatomy: `PyObject` and `PyVarObject`](#12-c-level-anatomy-pyobject-and-pyvarobject)
   - [1.3 Mutability vs. Immutability in Memory](#13-mutability-vs-immutability-in-memory)
   - [1.4 Equality (`==`) vs. Identity (`is`) and Caching Internals](#14-equality--vs-identity-is-and-caching-internals)
   - [1.5 Memory Copy Semantics: Shallow Copy vs. Deep Copy](#15-memory-copy-semantics-shallow-copy-vs-deep-copy)
2. [Built-in Collections Deep Dive: Lists, Tuples, Dicts & Sets](#2-built-in-collections-deep-dive-lists-tuples-dicts--sets)
   - [2.1 Lists: Dynamic Array Over-Allocation Mechanics](#21-lists-dynamic-array-over-allocation-mechanics)
   - [2.2 Tuples: Immutability, Struct Layout & Freelists](#22-tuples-immutability-struct-layout--freelists)
   - [2.3 Dictionaries: Compact Hash Table Architecture](#23-dictionaries-compact-hash-table-architecture)
   - [2.4 Sets: Open Addressing and Collision Resolution](#24-sets-open-addressing-and-collision-resolution)
3. [Scope Resolution (LEGB), Closures & Execution Context](#3-scope-resolution-legb-closures--execution-context)
   - [3.1 The LEGB Scoping Rule Explained Step-by-Step](#31-the-legb-scoping-rule-explained-step-by-step)
   - [3.2 Closures, Free Variables, and the Late-Binding Trap](#32-closures-free-variables-and-the-late-binding-trap)
   - [3.3 The Mutable Default Argument Pitfall](#33-the-mutable-default-argument-pitfall)
4. [Memory Optimization with `__slots__`](#4-memory-optimization-with-__slots__)
   - [4.1 Why Standard Classes Have High Memory Overhead (`__dict__`)](#41-why-standard-classes-have-high-memory-overhead-__dict__)
   - [4.2 How `__slots__` Replaces Dictionaries with Descriptor Arrays](#42-how-__slots__-replaces-dictionaries-with-descriptor-arrays)
   - [4.3 Practical Benchmarking: 80%+ Memory Reduction](#43-practical-benchmarking-80-memory-reduction)
5. [The Python Dunder Data Model](#5-the-python-dunder-data-model)
   - [5.1 Dunder Protocols: Mapping Syntax to Methods](#51-dunder-protocols-mapping-syntax-to-methods)
   - [5.2 String Representations: `__str__` vs. `__repr__`](#52-string-representations-__str__-vs-__repr__)
   - [5.3 The Hashing Contract: `__eq__` and `__hash__`](#53-the-hashing-contract-__eq__-and-__hash__)
6. [The Complete Object Lifecycle: `__new__` vs. `__init__`](#6-the-complete-object-lifecycle-__new__-vs-__init__)
   - [6.1 The Two-Phase Creation Process Explained](#61-the-two-phase-creation-process-explained)
   - [6.2 When and Why to Override `__new__` (Singletons & Immutables)](#62-when-and-why-to-override-__new__-singletons--immutables)
   - [6.3 Production Implementation: Thread-Safe Singleton & Value Normalization](#63-production-implementation-thread-safe-singleton--value-normalization)
7. [Attribute Management: Properties & Descriptors](#7-attribute-management-properties--descriptors)
   - [7.1 Encapsulating Invariants with `@property`](#71-encapsulating-invariants-with-property)
   - [7.2 The Descriptor Protocol: `__get__`, `__set__`, and `__delete__`](#72-the-descriptor-protocol-__get__-__set__-and-__delete__)
   - [7.3 The Complete Attribute Lookup Chain](#73-the-complete-attribute-lookup-chain)
8. [Multiple Inheritance & Method Resolution Order (C3 MRO)](#8-multiple-inheritance--method-resolution-order-c3-mro)
   - [8.1 The Diamond Problem](#81-the-diamond-problem)
   - [8.2 The C3 Linearization Algorithm Explained](#82-the-c3-linearization-algorithm-explained)
   - [8.3 Cooperative Multiple Inheritance with `super()`](#83-cooperative-multiple-inheritance-with-super)
9. [Metaprogramming: Classes as Objects, Metaclasses & `__init_subclass__`](#9-metaprogramming-classes-as-objects-metaclasses--__init_subclass__)
   - [9.1 Classes Are Objects Too: Dynamic Class Creation with `type()`](#91-classes-are-objects-too-dynamic-class-creation-with-type)
   - [9.2 What is a Metaclass? The Class of a Class](#92-what-is-a-metaclass-the-class-of-a-class)
   - [9.3 Metaclass Architecture & Lifecycle Hooks](#93-metaclass-architecture--lifecycle-hooks)
   - [9.4 The Modern Alternative: `__init_subclass__`](#94-the-modern-alternative-__init_subclass__)
10. [Modern Structural Constructs: Dataclasses, ABCs & Protocols](#10-modern-structural-constructs-dataclasses-abcs--protocols)
    - [10.1 Dataclasses (`slots=True`, `frozen=True`) vs. NamedTuples](#101-dataclasses-slotstrue-frozentrue-vs-namedtuples)
    - [10.2 Abstract Base Classes (`abc.ABC`) vs. Static Structural Protocols](#102-abstract-base-classes-abcabc-vs-static-structural-protocols)
11. [Summary Comparison & Engineering Reference Matrix](#11-summary-comparison--engineering-reference-matrix)
12. [References & Further Reading](#12-references--further-reading)

---

# 1. Everything is an Object: The CPython Object Model

### 1.1 Conceptual Foundation: Sticky Notes vs. Boxes

In languages like C or C++, a variable is often imagined as a **labeled box**. If you declare `int x = 42;`, the compiler allocates a 4-byte box in memory and places the binary representation of `42` directly inside that box. Assigning `int y = x;` creates a second box and copies the bytes into it.

In Python, **variables are not boxes; they are sticky notes (pointers) attached to objects**.
* When you write `x = [1, 2, 3]`, Python creates a list object somewhere in heap memory.
* It then sticks the label `"x"` onto that heap object.
* When you write `y = x`, Python does **not** copy the list; it simply attaches a second sticky note `"y"` to the **exact same object in memory**.

```python
# Demonstrating reference pointer semantics
a = [10, 20, 30]
b = a  # 'b' now points to the same memory location as 'a'

print(f"Memory address of a: {hex(id(a))}")
print(f"Memory address of b: {hex(id(b))}")
print(f"Do a and b share identity? {a is b}")  # Output: True

# Mutating the object via label 'b' reflects immediately through label 'a'
b.append(40)
print(f"Contents of a: {a}")  # Output: [10, 20, 30, 40]
```

---

### 1.2 C-Level Anatomy: `PyObject` and `PyVarObject`

CPython is written in C. Under the hood, every single entity you interact with in Python is represented by a C struct called `PyObject` (defined in CPython's `Include/object.h`):

```c
// Base C struct present at the start of EVERY Python object
typedef struct _object {
    _PyObject_HEAD_EXTRA // Double-linked list pointers used in debug builds
    Py_ssize_t ob_refcnt; // 64-bit integer tracking the reference count
    struct _typeobject *ob_type; // 64-bit pointer referencing the object's type/class
} PyObject;

// Header for variable-length collections (strings, lists, tuples, dicts)
typedef struct {
    PyObject ob_base;     // Includes ob_refcnt and ob_type
    Py_ssize_t ob_size;   // Number of items in collection (8 bytes)
} PyVarObject;
```

#### Memory Cost of a Simple Integer
In C, a 32-bit integer takes **4 bytes**. In Python, an integer is a full `PyLongObject` struct allocated on the heap:
```mermaid
flowchart LR
    subgraph PyLong ["PyLongObject Memory Layout (28 Bytes Minimum on 64-bit CPython)"]
        direction LR
        RefCnt["ob_refcnt<br/><b>8 Bytes</b><br/>Reference Counter"]
        TypePtr["ob_type<br/><b>8 Bytes</b><br/>PyTypeObject*"]
        Size["ob_size<br/><b>8 Bytes</b><br/>Sign & Digit Count"]
        Digits["ob_digit[1]<br/><b>4 Bytes</b><br/>uint32_t Value"]
        Align["Padding<br/><b>4 Bytes</b><br/>64-bit Alignment"]
        
        RefCnt --- TypePtr --- Size --- Digits --- Align
    end

    style PyLong fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style RefCnt fill:#1e293b,stroke:#3b82f6,stroke-width:1.5px,color:#fff
    style TypePtr fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#fff
    style Size fill:#1e293b,stroke:#f59e0b,stroke-width:1.5px,color:#fff
    style Digits fill:#1e293b,stroke:#ec4899,stroke-width:1.5px,color:#fff
    style Align fill:#1e293b,stroke:#64748b,stroke-width:1px,color:#94a3b8
```

| Struct Field | Size on 64-bit OS | C Data Type | Architectural Purpose |
| :--- | :---: | :--- | :--- |
| **`ob_refcnt`** | 8 Bytes | `Py_ssize_t` | Reference counter for deterministic reference-counting GC |
| **`ob_type`** | 8 Bytes | `PyTypeObject*` | Pointer to `PyLong_Type` (determines method dispatch and dunders) |
| **`ob_size`** | 8 Bytes | `Py_ssize_t` | Encodes integer sign ($>0$ positive, $<0$ negative) and digit array length |
| **`ob_digit[1]`** | 4 Bytes | `uint32_t` | Dynamic array holding 30-bit integer chunks (arbitrary precision) |
| **Padding** | 4 Bytes | Memory Alignment | Aligns struct to 64-bit (8-byte) word boundary in CPU cache lines |
| **Total Minimum** | **28 Bytes** | Heap Allocation | Baseline heap footprint for even the number `0` or `1` |


You can inspect the exact memory size of any Python object using `sys.getsizeof()`:
```python
import sys

val = 0
print(f"Size of integer 0: {sys.getsizeof(val)} bytes")  # Output: 28 bytes
val_large = 2 ** 100
print(f"Size of 2^100: {sys.getsizeof(val_large)} bytes")  # Output: 44 bytes (arbitrary precision)
```

---

### 1.3 Mutability vs. Immutability in Memory

Objects in Python are divided into two fundamental categories:

1. **Immutable Objects**: Once allocated in memory, their contents can **never** be changed.
   * Examples: `int`, `float`, `bool`, `str`, `tuple`, `frozenset`, `bytes`.
   * When you "modify" an immutable object (e.g., `x = x + 1` or `s = s + "world"`), Python does **not** change the original object; it allocates a brand new object in memory with the new value and moves the variable label to point to the new object.
2. **Mutable Objects**: Their internal payload can be mutated in-place without changing their memory address (`id()`).
   * Examples: `list`, `dict`, `set`, `bytearray`, and user-defined class instances.

```python
# Demonstrating immutability: re-binding creates a new object
num = 100
initial_address = id(num)
num += 1  # Allocates a new integer object (101)
print(f"Did integer address change? {id(num) != initial_address}")  # Output: True

# Demonstrating mutability: in-place modification preserves object address
names = ["Alice", "Bob"]
initial_list_address = id(names)
names.append("Charlie")  # Modifies internal memory buffer in-place
print(f"Did list address remain identical? {id(names) == initial_list_address}")  # Output: True
```

---

### 1.4 Equality (`==`) vs. Identity (`is`) and Caching Internals

* **Value Equality (`==`)**: Checks whether two objects contain the same data value by calling `__eq__()`.
* **Identity (`is`)**: Checks whether two variable labels point to the **exact same physical memory address** by comparing pointer addresses (`id(a) == id(b)`).

```python
list1 = [1, 2, 3]
list2 = [1, 2, 3]

print(list1 == list2)  # True: Semantic contents are equivalent
print(list1 is list2)  # False: Distinct heap allocations at different addresses
```

#### The Small Integer Cache (`[-5, 256]`)
To optimize speed and prevent thrashing the memory allocator, CPython pre-allocates an array of singletons for integers ranging from **-5 to 256** when the interpreter starts up. Any integer within this range references the exact same global singleton:

```python
x = 250 + 6  # 256
y = 256
print(f"256 is cached: {x is y}")  # True: Same object reference

large_x = 1000 + 1  # 1001
large_y = 1001
print(f"1001 is cached: {large_x is large_y}")  # False: Distinct objects outside [-5, 256]
```

> [!IMPORTANT]
> **Always use `is` for singletons**: Always use `if val is None:` instead of `if val == None:`. `is` cannot be overridden by user classes, executes in a single CPU instruction (`cmpq`), and prevents bugs caused by malicious or broken `__eq__` implementations.

---

### 1.5 Memory Copy Semantics: Shallow Copy vs. Deep Copy

When copying compound objects (objects containing other objects, like lists of dicts):

```mermaid
flowchart TD
    subgraph OriginalContainer ["Original List [Outer]"]
        Orig["List (0x1000)<br/>refs: [0x2000, 0x3000]"]
    end

    subgraph ShallowCopyContainer ["Shallow Copy (copy.copy)"]
        Shallow["New List (0x4000)<br/>refs: [0x2000, 0x3000]"]
    end

    subgraph DeepCopyContainer ["Deep Copy (copy.deepcopy)"]
        Deep["New List (0x5000)<br/>refs: [0x6000, 0x7000]"]
    end

    subgraph NestedHeapObjects ["Nested Heap Objects"]
        Child1["Inner Dict A (0x2000)"]
        Child2["Inner Dict B (0x3000)"]
    end

    subgraph ClonedHeapObjects ["Cloned Nested Heap Objects"]
        Child1_Clone["Cloned Dict A (0x6000)"]
        Child2_Clone["Cloned Dict B (0x7000)"]
    end

    Orig --> Child1
    Orig --> Child2

    Shallow -->|"Shares original inner pointers"| Child1
    Shallow -->|"Shares original inner pointers"| Child2

    Deep --> Child1_Clone
    Deep --> Child2_Clone

    style OriginalContainer fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style ShallowCopyContainer fill:#0f172a,stroke:#f59e0b,stroke-width:1.5px,color:#fde68a
    style DeepCopyContainer fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style NestedHeapObjects fill:#0f172a,stroke:#ef4444,stroke-width:1.5px,color:#fca5a5
    style ClonedHeapObjects fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style Orig fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Shallow fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style Deep fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Child1 fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style Child2 fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style Child1_Clone fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Child2_Clone fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

* **Shallow Copy (`copy.copy()`, `list.copy()`, `[:]`)**: Allocates a new outer container, but copies **only the pointer references** to the child objects. Modifying an inner child through the shallow copy mutates the original object!
* **Deep Copy (`copy.deepcopy()`)**: Allocates a new outer container and recursively allocates fresh copies of every child object, maintaining an internal memo dictionary to prevent infinite loops in cyclic references.

```python
import copy

original = [{"role": "admin"}, {"role": "viewer"}]

# 1. Shallow Copy
shallow = copy.copy(original)
shallow[0]["role"] = "superadmin"  # Mutates the shared inner dictionary!
print(f"Original after shallow modification: {original[0]['role']}")  # 'superadmin' (Leaked!)

# 2. Deep Copy
pristine_original = [{"role": "admin"}, {"role": "viewer"}]
deep = copy.deepcopy(pristine_original)
deep[0]["role"] = "superadmin"
print(f"Original after deep modification: {pristine_original[0]['role']}")  # 'admin' (Isolated!)
```

---

# 2. Built-in Collections Deep Dive: Lists, Tuples, Dicts & Sets

### 2.1 Lists: Dynamic Array Over-Allocation Mechanics

A Python `list` is not a linked list; it is a **dynamic array of 64-bit object pointers**.
* To provide $O(1)$ amortized append operations, CPython **over-allocates** memory capacity whenever the array becomes full.
* The growth formula in CPython source (`Objects/listobject.c`) is roughly:
  $$\text{new\_allocated} = \text{newsize} + (\text{newsize} \gg 3) + (\text{newsize} < 9 \ ? \ 3 : 6)$$
* **Performance Profile**:
  * `list.append(x)`: $O(1)$ amortized.
  * `list.pop()`: $O(1)$.
  * `list.insert(0, x)` or `list.pop(0)`: **$O(N)$** because all $N$ pointers must be shifted right or left in memory. (For $O(1)$ FIFO queues, always use `collections.deque`).

---

### 2.2 Tuples: Immutability, Struct Layout & Freelists

* A `tuple` is a fixed-size contiguous array of object pointers. Because tuples cannot change size after allocation, they do not require over-allocation buffers.
* **CPython Small-Tuple Freelist**: When a tuple of size 1 to 20 is garbage-collected, CPython preserves the allocated struct in an internal freelist instead of releasing it to the OS. The next time a tuple of that size is allocated, it is instantly reused without a memory allocation call.

---

### 2.3 Dictionaries: Compact Hash Table Architecture

Since Python 3.6 (formally specified in 3.7), dictionaries maintain a **compact hash table layout** that preserves insertion order while reducing memory usage by 30–40%:

```
Compact Dict Memory Layout:
1. Hash Indices Table (Sparse array of small integer offsets):
   [None, 1, None, 0, None, 2, None]

2. Entries Table (Dense, packed array preserving insertion order):
   Index 0: [hash_code, key_pointer, value_pointer]
   Index 1: [hash_code, key_pointer, value_pointer]
   Index 2: [hash_code, key_pointer, value_pointer]
```

* **Lookup Mechanics**:
  1. Computes `hash(key)`.
  2. Masks hash to locate slot in the sparse indices table.
  3. If occupied, fetches entry index and compares `hash` and key value via `==`.
  4. If a hash collision occurs, CPython uses **open addressing with pseudorandom perturbation**:
     $$j = ((5 \times j) + 1 + \text{perturb}) \pmod{\text{size}}$$

---

### 2.4 Sets: Open Addressing and Collision Resolution

A `set` is internally implemented identically to a `dict`, but without the value pointer array. Looking up, adding, or removing an element in a set is average-case **$O(1)$**, making sets ideal for membership testing (`if item in my_set:`).

---

# 3. Scope Resolution (LEGB), Closures & Execution Context

### 3.1 The LEGB Scoping Rule Explained Step-by-Step

Whenever Python resolves a variable name, it searches through four sequential scopes (**LEGB**):

```mermaid
flowchart TD
    VarLookup["Variable Lookup (e.g., 'print(counter)')"]
    --> L_Scope["1. Local Scope (Inside current function body)"]

    L_Scope --> Found1{"Found?"}
    Found1 -- Yes --> Resolved["Access Variable via LOAD_FAST"]
    Found1 -- No --> E_Scope["2. Enclosing Scope (Outer enclosing functions / closures)"]

    E_Scope --> Found2{"Found?"}
    Found2 -- Yes --> ResolvedDeref["Access Variable via LOAD_DEREF"]
    Found2 -- No --> G_Scope["3. Global Scope (Top-level module namespace / globals())"]

    G_Scope --> Found3{"Found?"}
    Found3 -- Yes --> ResolvedGlobal["Access Variable via LOAD_GLOBAL"]
    Found3 -- No --> B_Scope["4. Built-in Scope (Python builtins: len, int, range, etc.)"]

    B_Scope --> Found4{"Found?"}
    Found4 -- Yes --> ResolvedBuiltin["Access Built-in"]
    Found4 -- No --> Error["Raise NameError: name 'counter' is not defined"]

    style VarLookup fill:#1f2937,stroke:#64748b,stroke-width:2px,color:#fff
    style L_Scope fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style E_Scope fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style G_Scope fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style B_Scope fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style Error fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style Resolved fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style ResolvedDeref fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style ResolvedGlobal fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style ResolvedBuiltin fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
```

* `global`: Tells Python to bind assignments directly to the module-level global namespace.
* `nonlocal`: Introduced in Python 3; tells Python to bind assignments to the nearest enclosing (outer function) scope, enabling stateful closures.

```python
def outer_service():
    request_count = 0  # Enclosing scope variable

    def handle_request():
        nonlocal request_count  # Enables mutating enclosing scope
        request_count += 1
        return f"Handled request #{request_count}"

    return handle_request

handler = outer_service()
print(handler())  # Handled request #1
print(handler())  # Handled request #2
```

---

### 3.2 Closures, Free Variables, and the Late-Binding Trap

A **closure** is a function that retains access to variables from its enclosing lexical scope, even after the outer function has completed execution and its stack frame has been destroyed. CPython preserves these variables inside special **cell objects** (`__closure__`).

#### The Famous Late-Binding Trap in Loops
A classic interview question and frequent production bug:

```python
# ❌ ANTI-PATTERN: Late-binding trap
# All 4 lambda functions point to the SAME variable 'i', which is evaluated at call time
handlers = [lambda x: x + i for i in range(4)]
print([h(10) for h in handlers])  # Output: [13, 13, 13, 13] (Because loop finished at i = 3!)

# PRODUCTION FIX 1: Default argument binding (evaluates eagerly at definition time)
handlers_fixed = [lambda x, current_i=i: x + current_i for i in range(4)]
print([h(10) for h in handlers_fixed])  # Output: [10, 11, 12, 13] (Correct!)

# PRODUCTION FIX 2: Factory function creates a fresh closure scope per iteration
def make_handler(offset: int):
    return lambda x: x + offset

handlers_factory = [make_handler(i) for i in range(4)]
print([h(10) for h in handlers_factory])  # Output: [10, 11, 12, 13]
```

---

### 3.3 The Mutable Default Argument Pitfall

> [!CAUTION]
> In Python, default arguments in function definitions are **evaluated exactly once when the module is imported/loaded**, NOT each time the function is called! If you use a mutable object (like a list or dict) as a default argument, all callers share the exact same object instance.

```python
# ❌ DANGEROUS ANTI-PATTERN: Shared mutable state across all callers
def append_audit_event(event_name: str, log_list: list = []) -> list:
    log_list.append(event_name)
    return log_list

# In a web server, user 2 sees user 1's audit logs!
print(append_audit_event("Login"))       # Output: ['Login']
print(append_audit_event("ViewProfile")) # Output: ['Login', 'ViewProfile'] (Bug!)

# PRODUCTION STANDARD: The Sentinel Pattern with None
from typing import Any

def append_audit_event_safe(event_name: str, log_list: list[str] | None = None) -> list[str]:
    """Fresh list allocated per call when argument is omitted."""
    actual_list = [] if log_list is None else log_list
    actual_list.append(event_name)
    return actual_list
```

---

# 4. Memory Optimization with `__slots__`

### 4.1 Why Standard Classes Have High Memory Overhead (`__dict__`)

By default, every user-defined class instance in Python maintains an internal dictionary called `__dict__`. This dictionary allows you to dynamically attach arbitrary new attributes at runtime (e.g., `user.custom_field = 123`).

However, this flexibility comes at a severe memory cost:
* A `PyDictObject` takes a minimum of **152 to 280 bytes** of memory per object instance!
* If you have an API service that caches 1,000,000 data transfer objects (DTOs) in memory, storing empty or small `__dict__` structures wastes over **150 to 250 MB of RAM** purely on dictionary overhead.

---

### 4.2 How `__slots__` Replaces Dictionaries with Descriptor Arrays

By defining `__slots__`, you explicitly tell Python: *"This class only has these specific attributes. Do NOT create an instance `__dict__`."*
* Attributes are mapped directly to fixed C-level array descriptor slots.
* Memory drops from $\sim 312\text{ bytes}$ down to **$\sim 48\text{--}64\text{ bytes}$ per instance** (an **80–85% reduction**).

---

### 4.3 Practical Benchmarking: 80%+ Memory Reduction

```python
import sys

class StandardTelemetryPoint:
    def __init__(self, device_id: str, timestamp: float, reading: float) -> None:
        self.device_id = device_id
        self.timestamp = timestamp
        self.reading = reading

class SlottedTelemetryPoint:
    # Explicitly suppresses __dict__ and reserves fixed descriptor pointers
    __slots__ = ("device_id", "timestamp", "reading")

    def __init__(self, device_id: str, timestamp: float, reading: float) -> None:
        self.device_id = device_id
        self.timestamp = timestamp
        self.reading = reading

std_obj = StandardTelemetryPoint("DEV-01", 1729000000.0, 98.6)
slot_obj = SlottedTelemetryPoint("DEV-01", 1729000000.0, 98.6)

# Standard object has an instance __dict__ overhead
std_total_mem = sys.getsizeof(std_obj) + sys.getsizeof(std_obj.__dict__)
slot_total_mem = sys.getsizeof(slot_obj)

print(f"Standard Instance + __dict__: {std_total_mem} bytes")
print(f"Slotted Instance Memory:      {slot_total_mem} bytes")
print(f"Memory Savings:               {(1 - slot_total_mem / std_total_mem) * 100:.1f}%")
print(f"Does Slotted have __dict__?   {hasattr(slot_obj, '__dict__')}")  # False
```

---

# 5. The Python Dunder Data Model

### 5.1 Dunder Protocols: Mapping Syntax to Methods

Python uses double-underscore ("dunder") methods to hook into language primitives:

| Python Syntax | Dispatched Dunder Method | Purpose |
| :--- | :--- | :--- |
| `str(x)` / `print(x)` | `x.__str__()` | User-friendly, human-readable string representation. |
| `repr(x)` / Debugger | `x.__repr__()` | Unambiguous developer representation (`eval(repr(x)) == x`). |
| `len(x)` | `x.__len__()` | Returns non-negative collection length. |
| `x == y` | `x.__eq__(y)` | Equality comparison. |
| `hash(x)` / Set / Dict | `x.__hash__()` | Returns 64-bit integer hash for hash table lookups. |
| `x[key]` | `x.__getitem__(key)` | Index or key lookup. |
| `x[key] = val` | `x.__setitem__(key, val)` | Index or key assignment. |
| `with x:` | `x.__enter__()`, `x.__exit__()` | Context manager lifecycle. |
| `x()` | `x.__call__(*args, **kwargs)`| Makes instance callable like a standard function. |

---

### 5.2 String Representations: `__str__` vs. `__repr__`

* **`__repr__`**: Designed for developers and debugging. Should be unambiguous and, if possible, look like valid Python code that could recreate the object.
* **`__str__`**: Designed for end-users, logging, and user interfaces.

```python
class AccountID:
    def __init__(self, prefix: str, number: int) -> None:
        self.prefix = prefix
        self.number = number

    def __repr__(self) -> str:
        # Developer representation: looks like constructor call
        return f"AccountID(prefix={self.prefix!r}, number={self.number!r})"

    def __str__(self) -> str:
        # User representation: human-friendly formatted ID
        return f"{self.prefix}-{self.number:06d}"

acc = AccountID("CORP", 42)
print(repr(acc))  # Output: AccountID(prefix='CORP', number=42)
print(str(acc))   # Output: CORP-000042
```

---

### 5.3 The Hashing Contract: `__eq__` and `__hash__`

> [!IMPORTANT]
> **The Fundamental Hashing Contract**:
> If two objects are equal according to `__eq__`, they **MUST** produce the exact same integer hash from `__hash__`:
> $$a == b \implies \text{hash}(a) == \text{hash}(b)$$
> If you override `__eq__` in a class without defining `__hash__`, Python automatically sets `__hash__ = None`, making instances **unhashable** (they will raise `TypeError: unhashable type` if placed in a `set` or used as a `dict` key).

```python
class TenantKey:
    """Immutable, hashable value object safe for dictionary keys and sets."""
    __slots__ = ("_tenant_id", "_region", "_cached_hash")

    def __init__(self, tenant_id: str, region: str) -> None:
        self._tenant_id = tenant_id
        self._region = region
        self._cached_hash = hash((self._tenant_id, self._region))

    def __eq__(self, other: object) -> bool:
        if not isinstance(other, TenantKey):
            return NotImplemented
        return self._tenant_id == other._tenant_id and self._region == other._region

    def __hash__(self) -> int:
        return self._cached_hash

# Validated in hash table lookups
cache = {TenantKey("org_100", "us-east-1"): "Active"}
print(TenantKey("org_100", "us-east-1") in cache)  # Output: True
```

---

# 6. The Complete Object Lifecycle: `__new__` vs. `__init__`

### 6.1 The Two-Phase Creation Process Explained

Most programmers mistakenly think `__init__` is the "constructor" of a class. It is **not**. In Python, object creation is a **two-phase process**:

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'actorBkg': '#1e293b', 'actorBorder': '#3b82f6', 'actorTextColor': '#ffffff', 'signalColor': '#93c5fd', 'signalTextColor': '#ffffff', 'noteBkgColor': '#1e293b', 'noteBorderColor': '#f59e0b', 'noteTextColor': '#fde68a', 'activationBkgColor': '#3b82f6', 'sequenceNumberColor': '#ffffff' }}}%%
sequenceDiagram
    autonumber
    actor Caller as Caller Code: obj = User("Alice")
    participant Metaclass as Metaclass (type)
    participant NewMethod as Class.__new__(cls, *args)
    participant HeapAllocator as CPython Heap Memory
    participant InitMethod as instance.__init__(self, *args)

    Caller->>Metaclass: Invokes User("Alice")
    activate Metaclass
    Metaclass->>NewMethod: 1. Calls User.__new__(User, "Alice")
    activate NewMethod
    Note over NewMethod: ALLOCATION PHASE:<br/>Must allocate and return raw instance
    NewMethod->>HeapAllocator: Allocates PyObject struct on heap
    HeapAllocator-->>NewMethod: Returns raw uninitialized instance pointer
    NewMethod-->>Metaclass: Returns instance 'self'
    deactivate NewMethod

    opt If returned object is an instance of User
        Metaclass->>InitMethod: 2. Calls self.__init__("Alice")
        activate InitMethod
        Note over InitMethod: INITIALIZATION PHASE:<br/>Populates attributes on self
        InitMethod-->>Metaclass: Returns None (Mandatory)
        deactivate InitMethod
    end

    Metaclass-->>Caller: Returns fully constructed object
    deactivate Metaclass
```

1. **Phase 1: Memory Allocation (`__new__`)**:
   * A static method (takes `cls` as its first parameter).
   * Responsible for **allocating memory on the heap** and returning the brand new, empty object instance.
2. **Phase 2: Attribute Initialization (`__init__`)**:
   * An instance method (takes `self` as its first parameter).
   * Receives the instance returned by `__new__` and initializes attributes. It **must return `None`**.

---

### 6.2 When and Why to Override `__new__` (Singletons & Immutables)

You almost never need to override `__new__` for standard business classes. You only override `__new__` for:
1. **Subclassing Immutable Built-in Types**: Built-ins like `int`, `str`, or `tuple` cannot be modified after creation. In `__init__`, the object is already created and immutable. Therefore, customizations must happen in `__new__` before allocation.
2. **Implementing Singletons**: Intercepting creation to return an existing instance rather than allocating a new one.
3. **Object Caching / Factory Pooling**: Reusing instances from a cache.

---

### 6.3 Production Implementation: Thread-Safe Singleton & Value Normalization

#### Example 1: Subclassing an Immutable Built-in (`str`)
```python
class UpperString(str):
    """Subclassing immutable str: must transform value in __new__!"""
    def __new__(cls, value: str) -> "UpperString":
        # Transform value BEFORE allocating the immutable string
        transformed = str(value).upper()
        # Allocate and return instance using base str.__new__
        return super().__new__(cls, transformed)

s = UpperString("hello world")
print(f"UpperString value: {s!r}")  # Output: 'HELLO WORLD'
```

#### Example 2: Thread-Safe Singleton Pattern
```python
import threading

class DatabaseConnectionPool:
    """Thread-safe Singleton using __new__."""
    _instance: "DatabaseConnectionPool | None" = None
    _lock: threading.Lock = threading.Lock()

    def __new__(cls, *args, **kwargs) -> "DatabaseConnectionPool":
        if cls._instance is None:
            with cls._lock:
                # Double-checked locking pattern
                if cls._instance is None:
                    print("[Singleton] Allocating new DatabaseConnectionPool instance...")
                    cls._instance = super().__new__(cls)
                    cls._instance._initialized = False
        return cls._instance

    def __init__(self, dsn: str = "postgresql://localhost:5432/db") -> None:
        if getattr(self, "_initialized", False):
            return  # Prevent re-initializing singleton on subsequent calls
        self.dsn = dsn
        self._initialized = True
        print(f"[Singleton] Initialized pool with DSN: {self.dsn}")

pool1 = DatabaseConnectionPool()
pool2 = DatabaseConnectionPool()
print(f"Are pool1 and pool2 identical? {pool1 is pool2}")  # Output: True
```

---

# 7. Attribute Management: Properties & Descriptors

### 7.1 Encapsulating Invariants with `@property`

The `@property` decorator allows getters, setters, and deleters to look like standard attribute access while maintaining full invariant protection:

```python
class BankAccount:
    def __init__(self, owner: str, initial_balance: float) -> None:
        self.owner = owner
        self._balance = 0.0
        self.balance = initial_balance  # Triggers setter validation

    @property
    def balance(self) -> float:
        """The balance property getter."""
        return self._balance

    @balance.setter
    def balance(self, value: float) -> None:
        """Enforces domain invariant: balance cannot be negative."""
        if not isinstance(value, (int, float)) or value < 0:
            raise ValueError(f"Balance must be a non-negative number; got {value}")
        self._balance = float(value)

account = BankAccount("Alice", 100.0)
account.balance = 250.0  # Calls setter
# account.balance = -50  # Raises ValueError: Balance must be a non-negative number
```

---

### 7.2 The Descriptor Protocol: `__get__`, `__set__`, and `__delete__`

While `@property` works for a single class, what if you have 20 different classes that all need positive float validation? Copy-pasting properties across 20 classes creates massive code duplication.

A **Descriptor** is an object attribute whose access is overridden by implementing the descriptor protocol:
* `__get__(self, instance, owner=None)`
* `__set__(self, instance, value)`
* `__delete__(self, instance)`
* `__set_name__(self, owner, name)` (Introduced in Python 3.6 to automatically capture the attribute name).

```python
class PositiveNumber:
    """Reusable Data Descriptor enforcing positive numeric validation across any class."""
    def __set_name__(self, owner: type, name: str) -> None:
        # Automatically captures attribute name (e.g., 'price' -> '_price')
        self.private_name = f"_{name}"

    def __get__(self, instance: object, owner: type) -> float:
        if instance is None:
            return self  # Accessed on class level (e.g., Product.price)
        return getattr(instance, self.private_name)

    def __set__(self, instance: object, value: float) -> None:
        if not isinstance(value, (int, float)) or value <= 0:
            raise ValueError(f"Attribute {self.private_name} must be positive; got {value}")
        setattr(instance, self.private_name, float(value))

class Product:
    # Reusable descriptor validation instances
    price = PositiveNumber()
    tax = PositiveNumber()
    weight = PositiveNumber()

    def __init__(self, name: str, price: float, tax: float, weight: float) -> None:
        self.name = name
        self.price = price      # Dispatches to PositiveNumber.__set__
        self.tax = tax          # Dispatches to PositiveNumber.__set__
        self.weight = weight    # Dispatches to PositiveNumber.__set__

item = Product("Laptop", 1299.99, 120.0, 2.1)
print(f"Product: {item.name}, Price: ${item.price}")
# item.price = -10  # Raises ValueError: Attribute _price must be positive
```

---

### 7.3 The Complete Attribute Lookup Chain

When Python evaluates `obj.attr`, it executes this deterministic 5-step search chain:
1. **Data Descriptors**: Looks in the class hierarchy for a descriptor implementing `__set__` or `__delete__`. If found, calls its `__get__`.
2. **Instance `__dict__`**: Looks in `obj.__dict__['attr']`. (This is where standard instance variables live).
3. **Non-Data Descriptors**: Looks in the class hierarchy for a descriptor implementing only `__get__` (e.g., regular methods, `@classmethod`, `@staticmethod`). Calls its `__get__`.
4. **Class Attributes**: Looks in the class and base classes' `__dict__` for standard attributes.
5. **Fallback (`__getattr__`)**: If all above fail, calls `obj.__getattr__('attr')` if defined. Otherwise, raises `AttributeError`.

---

# 8. Multiple Inheritance & Method Resolution Order (C3 MRO)

### 8.1 The Diamond Problem

In languages that support multiple inheritance, the **Diamond Problem** occurs when a class $D$ inherits from classes $B$ and $C$, which both inherit from a common base class $A$:

```mermaid
classDiagram
    class BaseService {
        +execute()
    }
    class AuditingMixin {
        +execute()
    }
    class RateLimitingMixin {
        +execute()
    }
    class OrderPaymentService {
        +execute()
    }

    BaseService <|-- AuditingMixin
    BaseService <|-- RateLimitingMixin
    AuditingMixin <|-- OrderPaymentService
    RateLimitingMixin <|-- OrderPaymentService

    style BaseService fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style AuditingMixin fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style RateLimitingMixin fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style OrderPaymentService fill:#1f2937,stroke:#ec4899,stroke-width:2px,color:#fff
```

If both `B` and `C` override a method from `A`, which one does `D` call? In naive systems, `A`'s method might be called twice or in the wrong order.

---

### 8.2 The C3 Linearization Algorithm Explained

Python resolves this using the mathematically rigorous **C3 Linearization Algorithm** (PEP 275). It guarantees three invariant rules:
1. **Children precede parents**: Subclasses are checked before base classes.
2. **Order of declaration is preserved**: In `class D(B, C):`, `B` is checked before `C`.
3. **Monotonicity**: If $B$ precedes $C$ in the MRO of any parent class, $B$ must precede $C$ in all derived classes.

You can inspect any class's calculated MRO via `Class.__mro__`:
```python
for idx, cls in enumerate(OrderPaymentService.__mro__):
    print(f"Order {idx}: {cls.__name__}")
```

---

### 8.3 Cooperative Multiple Inheritance with `super()`

> [!IMPORTANT]
> In Python, `super()` does **not** simply call the immediate parent class. **`super()` calls the NEXT class in the runtime MRO tuple!**
> For cooperative inheritance to work, every method in the chain must call `super()` with matching arguments.

```python
class BaseService:
    def execute(self, payload: dict) -> None:
        print("[BaseService] Writing payload to database...")

class AuditingMixin(BaseService):
    def execute(self, payload: dict) -> None:
        print("[Auditing] Logged transaction start")
        super().execute(payload)  # Delegates to NEXT in MRO
        print("[Auditing] Logged transaction completion")

class RateLimitingMixin(BaseService):
    def execute(self, payload: dict) -> None:
        print("[RateLimiter] Token bucket capacity verified")
        super().execute(payload)  # Delegates to NEXT in MRO

class OrderPaymentService(AuditingMixin, RateLimitingMixin):
    def execute(self, payload: dict) -> None:
        print("[OrderPaymentService] Validating payment data...")
        super().execute(payload)

service = OrderPaymentService()
service.execute({"order_id": 101})
```

#### Execution Output Trace:
```text
[OrderPaymentService] Validating payment data...
[Auditing] Logged transaction start
[RateLimiter] Token bucket capacity verified
[BaseService] Writing payload to database...
[Auditing] Logged transaction completion
```

---

# 9. Metaprogramming: Classes as Objects, Metaclasses & `__init_subclass__`

### 9.1 Classes Are Objects Too: Dynamic Class Creation with `type()`

In Python, classes are **not** static compiler declarations. **Classes are runtime objects!**
* When Python encounters a `class User: ...` block, it executes the block and creates an object representing the class.
* What is the type of a class? A metaclass! By default, the metaclass is **`type`**.

You can create a complete, working class dynamically at runtime using `type(name, bases, dict)`:

```python
def speak_method(self) -> str:
    return f"Hello, I am {self.name}"

# Dynamically constructing a class at runtime without 'class' keyword:
DynamicUser = type(
    "DynamicUser",           # Class Name
    (object,),               # Base Classes (Tuple)
    {"species": "Homo sapiens", "speak": speak_method}  # Attributes & Methods
)

u = DynamicUser()
u.name = "Alice"
print(f"Dynamic class name: {DynamicUser.__name__}")  # DynamicUser
print(f"Invoking dynamic method: {u.speak()}")          # Hello, I am Alice
```

---

### 9.2 What is a Metaclass? The Class of a Class

* An **object** is an instance of a **class**:
  $$x = \text{User}() \implies \text{type}(x) \text{ is } \text{User}$$
* A **class** is an instance of a **metaclass**:
  $$\text{type}(\text{User}) \text{ is } \text{type}$$
* A **metaclass** is the blueprint used to construct classes. Metaclasses allow you to intercept class creation, validate class attributes, register plugins, or auto-generate methods at the moment the class is defined (module import time).

---

### 9.3 Metaclass Architecture & Lifecycle Hooks

When a class using a custom metaclass is defined:
1. `Metaclass.__prepare__(name, bases)`: Returns the dictionary (namespace) used to execute the class body.
2. The class body code executes within that namespace.
3. `Metaclass.__new__(mcls, name, bases, namespace)`: Allocates and returns the new Class object.
4. `Metaclass.__init__(cls, name, bases, namespace)`: Initializes the newly created Class object.

#### Practical Example: Metaclass Enforcing API Documentation Contracts
```python
class APIRouteMeta(type):
    """Metaclass that verifies all service methods have docstrings at import time."""
    def __new__(mcls, name: str, bases: tuple, namespace: dict):
        # Scan methods in the class definition
        for attr_name, attr_val in namespace.items():
            if callable(attr_val) and not attr_name.startswith("_"):
                if not getattr(attr_val, "__doc__", None):
                    raise TypeError(
                        f"Architecture Violation: Method '{name}.{attr_name}' "
                        f"must have a docstring for API documentation!"
                    )
        print(f"[Metaclass] Class '{name}' successfully validated and constructed.")
        return super().__new__(mcls, name, bases, namespace)

# Testing validation:
class BillingService(metaclass=APIRouteMeta):
    def process_invoice(self, invoice_id: str) -> bool:
        """Process recurring customer invoice."""
        return True

# If you uncomment this, Python raises TypeError IMMEDIATELY on module import!
# class NonCompliantService(metaclass=APIRouteMeta):
#     def delete_account(self, user_id: str):
#         pass # Missing docstring!
```

---

### 9.4 The Modern Alternative: `__init_subclass__`

In Python 3.6+, PEP 487 introduced `__init_subclass__`. It provides 90% of metaclass capabilities with clean, readable syntax and **zero metaclass conflict hazards**:

```python
from typing import Callable, ClassVar

class BasePlugin:
    """Plugin framework base registering all subclasses automatically."""
    registry: ClassVar[dict[str, type["BasePlugin"]]] = {}

    def __init_subclass__(cls, plugin_name: str, **kwargs) -> None:
        super().__init_subclass__(**kwargs)
        cls.registry[plugin_name] = cls
        print(f"[Plugin Registry] Registered '{plugin_name}' -> {cls.__name__}")

# Subclasses register automatically via class definition arguments
class StripePaymentPlugin(BasePlugin, plugin_name="stripe"):
    pass

class PayPalPaymentPlugin(BasePlugin, plugin_name="paypal"):
    pass

print(f"Active plugins: {list(BasePlugin.registry.keys())}")
# Output: ['stripe', 'paypal']
```

---

# 10. Modern Structural Constructs: Dataclasses, ABCs & Protocols

### 10.1 Dataclasses (`slots=True`, `frozen=True`) vs. NamedTuples

Introduced in Python 3.7 (and enhanced with `slots=True`, `kw_only=True` in 3.10), `@dataclass` auto-generates boilerplate `__init__`, `__repr__`, `__eq__`, and `__hash__` methods:

```python
from dataclasses import dataclass, field
from datetime import datetime, timezone
import uuid

@dataclass(frozen=True, slots=True, kw_only=True)
class TenantConfig:
    tenant_id: uuid.UUID = field(default_factory=uuid.uuid4)
    name: str
    max_connections: int = field(default=50)
    created_at: datetime = field(default_factory=lambda: datetime.now(timezone.utc))

    def __post_init__(self) -> None:
        if self.max_connections <= 0:
            raise ValueError("max_connections must be positive")

config = TenantConfig(name="Acme Corp", max_connections=100)
print(config)
# config.name = "New Name"  # FrozenInstanceError: cannot assign to field 'name'
```

---

### 10.2 Abstract Base Classes (`abc.ABC`) vs. Static Structural Protocols

* **Nominal Subtyping (`abc.ABC`)**: Requires explicit class inheritance (`class MyService(BaseService):`). Checked via `isinstance()`.
* **Structural Subtyping (`typing.Protocol`)**: Evaluates type compatibility purely on object shape (methods and attributes) without requiring inheritance (**Static Duck Typing** via mypy):

```python
from typing import Protocol, runtime_checkable
from abc import ABC, abstractmethod

# 1. Nominal Abstraction (Requires explicit inheritance)
class StorageEngine(ABC):
    @abstractmethod
    def persist(self, key: str, data: bytes) -> None:
        """Save data to persistent storage."""

# 2. Structural Subtyping (No inheritance needed)
@runtime_checkable
class CacheClient(Protocol):
    def get(self, key: str) -> bytes | None: ...
    def set(self, key: str, value: bytes) -> None: ...

# Standalone adapter satisfying CacheClient without inheriting from it!
class InMemoryCache:
    def __init__(self) -> None:
        self._data: dict[str, bytes] = {}

    def get(self, key: str) -> bytes | None:
        return self._data.get(key)

    def set(self, key: str, value: bytes) -> None:
        self._data[key] = value

cache = InMemoryCache()
print(f"Satisfies CacheClient Protocol? {isinstance(cache, CacheClient)}")  # True
```

---

# 11. Summary Comparison & Engineering Reference Matrix

| Feature / Primitive | Underlying Mechanism | Memory Footprint | Primary Production Fit | Key Trade-off / Gotcha |
| :--- | :--- | :---: | :--- | :--- |
| **Standard Class** | `PyObject` + `__dict__` hash table | High ($\approx 312\text{B}+$) | General domain entities with dynamic attributes | High memory overhead; unconstrained attribute addition. |
| **`__slots__` Class** | Fixed array of C descriptors | Low ($\approx 48\text{--}64\text{B}$) | High-throughput DTOs, cached models, telemetry points | Cannot add dynamic attributes unless `'__dict__'` is in slots. |
| **`@dataclass(slots=True)`** | Auto-generated dunders + slotted descriptors | Low | API request/response models, domain value objects | Inheritance with non-default arguments requires careful ordering. |
| **`NamedTuple`** | `tuple` subclass contiguous pointer array | Ultra-low ($\approx 48\text{B}$) | Lightweight read-only records, database tuples | Immutable; tuple unpacking can cause subtle bugs if fields reordered. |
| **Descriptor** | Binding behavior (`__get__`/`__set__`) | Negligible | Reusable cross-field validation, ORM column mappings | Must be declared at class level, not inside `__init__`. |
| **Metaclass** | Subclass of `type` | Negligible | Deep framework hooks, schema generators, strict validators | High cognitive complexity; easily leads to metaclass conflict errors. |
| **`__init_subclass__`** | Class creation hook (PEP 487) | Zero overhead | Plugin registries, route registration, lightweight validation | Simpler, safer, and cleaner replacement for 90% of metaclass use cases. |
| **`typing.Protocol`** | Structural subtyping (PEP 544) | Zero runtime cost | Interface decoupling, Repository/Service ports | Evaluated statically by mypy/Pyright; requires `@runtime_checkable` for `isinstance`. |

---

# 12. References & Further Reading

1. **Python Data Model Documentation**: [https://docs.python.org/3/reference/datamodel.html](https://docs.python.org/3/reference/datamodel.html)
2. **CPython Source Code (`Include/object.h`)**: [https://github.com/python/cpython/blob/main/Include/object.h](https://github.com/python/cpython/blob/main/Include/object.h)
3. **PEP 487 – Simpler customization of class creation (`__init_subclass__`)**: [https://peps.python.org/pep-0487/](https://peps.python.org/pep-0487/)
4. **PEP 544 – Protocols: Structural Subtyping (Static Duck Typing)**: [https://peps.python.org/pep-0544/](https://peps.python.org/pep-0544/)
5. **Python C3 Linearization Algorithm (MRO)**: [https://www.python.org/download/releases/2.3/mro/](https://www.python.org/download/releases/2.3/mro/)
6. **Related Guides**:
   - See [02. Advanced Python Programming Constructs](02_advanced_python_programming_constructs.md) for iterators, generators, and decorators.
   - See [03. CPython Runtime, Memory, and Performance](03_cpython_runtime_memory_and_performance.md) for PyMalloc, GC, and GIL internals.
   - See [06. Design Patterns, SOLID, and Software Architecture](06_design_patterns_solid_and_software_architecture.md) for GoF patterns and Clean Architecture.
