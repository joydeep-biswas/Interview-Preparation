# 01. Core Java, Object-Oriented Programming & Core Algorithms

A comprehensive study guide covering Core Java language mechanics, JVM object internals, Object-Oriented principles, core algorithmic challenges, class loading, memory layout, reflection, NIO, and modern Java (Java 17–21) features.

---

## Architectural Overview: Object Execution & Method Dispatch

```mermaid
flowchart TD
    subgraph StackFrame["Thread Stack (Stack Frame)"]
        RefVar["Reference Variable<br/>(Compressed OOP 4 bytes)"]
    end

    subgraph HeapMemory["JVM Heap Memory"]
        subgraph ObjectInstance["Object Instance"]
            MarkWord["Mark Word (8B): Hash, Lock Bits, Age"]
            KlassPtr["Klass Word (4B): Points to Metaspace"]
            Fields["Instance Fields & Primitives"]
            Padding["Alignment Padding (8-byte boundary)"]
        end
    end

    subgraph MetaspaceArea["JVM Metaspace (Class Metadata)"]
        KlassData["Klass Instance Structure"]
        VTable["vtable (Virtual Method Table)<br/>Direct JIT Native Pointers"]
    end

    RefVar -->|"Dereferences Address"| ObjectInstance
    KlassPtr -->|"Metadata Reference"| KlassData
    KlassData --> VTable

    style StackFrame fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style HeapMemory fill:#0f172a,stroke:#06b6d4,stroke-width:1.5px,color:#67e8f9
    style ObjectInstance fill:#1e293b,stroke:#06b6d4,stroke-width:1px,color:#e2e8f0
    style MetaspaceArea fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c4b5fd
    style RefVar fill:#1f2937,stroke:#3b82f6,stroke-width:1px,color:#fff
    style MarkWord fill:#1f2937,stroke:#3b82f6,stroke-width:1px,color:#fff
    style KlassPtr fill:#1f2937,stroke:#8b5cf6,stroke-width:1px,color:#fff
    style Fields fill:#1f2937,stroke:#06b6d4,stroke-width:1px,color:#fff
    style VTable fill:#1f2937,stroke:#8b5cf6,stroke-width:1px,color:#fff
```

---

## Table of Contents & Question Mapping

1. **Algorithmic Foundations & String Manipulation**
   - [Q1. Reverse a String Without Built-ins](#q1-reverse-a-string-without-built-ins)
   - [Q2. Convert First Half to Lowercase & Second Half to Uppercase](#q2-convert-first-half-to-lowercase--second-half-to-uppercase)
   - [Q3. First Non-Repeating Character in a String](#q3-first-non-repeating-character-in-a-string)
   - [Q4. Longest Substring Without Repeating Characters](#q4-longest-substring-without-repeating-characters)
   - [Q5. Longest Common Prefix in an Array of Strings](#q5-longest-common-prefix-in-an-array-of-strings)
2. **Array & Data Structure Manipulations**
   - [Q6. Remove Duplicates from an Array in a Single Iteration](#q6-remove-duplicates-from-an-array-in-a-single-iteration)
   - [Q7. Rearrange Integer Array: Even Before Odd](#q7-rearrange-integer-array-even-before-odd)
   - [Q8. Print Prime Numbers Between 1 and 200 (Minimal Complexity)](#q8-print-prime-numbers-between-1-and-200-minimal-complexity)
   - [Q9. Reverse a Singly Linked List (Iterative & Recursive)](#q9-reverse-a-singly-linked-list-iterative--recursive)
   - [Q10. Path Sum Verification in a Binary Tree (Root to Leaf)](#q10-path-sum-verification-in-a-binary-tree-root-to-leaf)
3. **Core Language Semantics & Keywords**
   - [Q11 & Q25. `==` vs `equals()` and the String Pool Mechanism](#q11--q25--vs-equals-and-the-string-pool-mechanism)
   - [Q12. Short-Circuiting with Logical Operators (`&&`, `||`)](#q12-short-circuiting-with-logical-operators)
   - [Q13 & Q14. Constructors and Invoking Static Methods Inside Constructors](#q13--q14-constructors-and-invoking-static-methods-inside-constructors)
   - [Q26 & Q27. `final`, `finally`, and `finalize()`](#q26--q27-final-finally-and-finalize)
   - [Q28. `ClassCastException`: Root Causes and Prevention](#q28-classcastexception-root-causes-and-prevention)
4. **Object-Oriented Design & Polymorphism**
   - [Q15. Composition Over Inheritance](#q15-composition-over-inheritance)
   - [Q16 & Q17. Interface vs Abstract Class & Static vs Default Methods](#q16--q17-interface-vs-abstract-class--static-vs-default-methods)
   - [Q18. Marker Interfaces and Modern Alternatives](#q18-marker-interfaces-and-modern-alternatives)
   - [Q19 & Q20. Overriding Static Methods & Method Hiding](#q19--q20-overriding-static-methods--method-hiding)
   - [Q21. Anonymous Inner Classes & Scoping](#q21-anonymous-inner-classes--scoping)
   - [Q22. Designing Completely Immutable Classes](#q22-designing-completely-immutable-classes)
   - [Q23. Shallow Copy vs Deep Copy](#q23-shallow-copy-vs-deep-copy)
   - [Q24. `StringBuffer` vs `StringBuilder`](#q24-stringbuffer-vs-stringbuilder)
5. **JVM Internals, Memory Management & Runtime**
   - [Q29 & Q30. JVM Memory Architecture & Handling `OutOfMemoryError`](#q29--q30-jvm-memory-architecture--handling-outofmemoryerror)
   - [Q31. ClassLoader Architecture and Delegation Model](#q31-classloader-architecture-and-delegation-model)
   - [Q32 & Q33. Java Reflection API & Dynamic Proxies](#q32--q33-java-reflection-api--dynamic-proxies)
   - [Q34. Java NIO `Path` vs Classic IO `File`](#q34-java-nio-path-vs-classic-io-file)
   - [Q35. Tail Call Optimization in Java](#q35-tail-call-optimization-in-java)
6. **Modern Java Features (Java 17 to Java 21+)**
   - [Q36. Text Blocks and Multiline Strings](#q36-text-blocks-and-multiline-strings)
   - [Q37. Pattern Matching for `switch`](#q37-pattern-matching-for-switch)
   - [Q38 & Q39. Records, Standard Classes & Sealed Classes in Production](#q38--q39-records-standard-classes--sealed-classes-in-production)
   - [Q40. Virtual Threads (Project Loom) vs Platform Threads](#q40-virtual-threads-project-loom-vs-platform-threads)

---

# 1. Algorithmic Foundations & String Manipulation

---

### Q1. Reverse a String Without Built-ins
**Topic Covered:** String Manipulation & In-Place Algorithms  
**Definition:** Reversing a string requires rearranging its characters so that the first character becomes the last and vice versa, without invoking helper methods like `StringBuilder.reverse()`.

```java
public class StringReversal {
    public static String reverse(String input) {
        if (input == null || input.length() <= 1) {
            return input;
        }
        char[] chars = input.toCharArray();
        int left = 0;
        int right = chars.length - 1;
        while (left < right) {
            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;
            left++;
            right--;
        }
        return new String(chars);
    }
}
```
- **Time Complexity:** $O(N)$ — touches $N/2$ elements.
- **Space Complexity:** $O(N)$ — strictly for the output character array due to Java's string immutability.

---

### Q2. Convert First Half to Lowercase & Second Half to Uppercase
**Topic Covered:** String Slicing & ASCII Transformation  
**Definition:** Given a string of length $N$, divide it at index $\lfloor N / 2 \rfloor$. Characters in the first segment are mapped to lowercase; characters in the second segment are mapped to uppercase.

```java
public class HalfCaseTransformer {
    public static String transformHalves(String str) {
        if (str == null || str.isEmpty()) {
            return str;
        }
        int mid = str.length() / 2;
        char[] chars = str.toCharArray();

        for (int i = 0; i < mid; i++) {
            if (chars[i] >= 'A' && chars[i] <= 'Z') {
                chars[i] = (char) (chars[i] + 32); // Convert to lowercase
            }
        }
        for (int i = mid; i < chars.length; i++) {
            if (chars[i] >= 'a' && chars[i] <= 'z') {
                chars[i] = (char) (chars[i] - 32); // Convert to uppercase
            }
        }
        return new String(chars);
    }
}
```

---

### Q3. First Non-Repeating Character in a String
**Topic Covered:** Frequency Hashing & Stream Processing  
**Definition:** Identify the earliest character index in a string whose frequency throughout the string equals exactly 1.

```java
public class FirstUniqueChar {
    public static char findFirstNonRepeating(String str) {
        if (str == null || str.isEmpty()) {
            throw new IllegalArgumentException("String cannot be null or empty");
        }
        int[] freq = new int[256]; // Assuming standard ASCII
        for (int i = 0; i < str.length(); i++) {
            freq[str.charAt(i)]++;
        }
        for (int i = 0; i < str.length(); i++) {
            if (freq[str.charAt(i)] == 1) {
                return str.charAt(i);
            }
        }
        throw new NoSuchElementException("No non-repeating character found");
    }
}
```
- **Time Complexity:** $O(N)$ where $N$ is string length.
- **Space Complexity:** $O(1)$ fixed 256-integer array.

---

### Q4. Longest Substring Without Repeating Characters
**Topic Covered:** Sliding Window & Dynamic Frequency Tracking  
**Definition:** Find the maximum length of a contiguous substring where every character is unique.

```mermaid
flowchart LR
    Start["Left Pointer (L=0)"] --> Window["Sliding Window [L ... R]"]
    Window --> SeenMap["Map&lt;Char, LastSeenIndex&gt;"]
    SeenMap -->|"Duplicate found at pos >= L"| Advance["Shift L to lastSeen + 1"]
    SeenMap -->|"No duplicate"| Expand["Expand R, Update MaxLen"]

    style Start fill:#0f172a,stroke:#3b82f6,stroke-width:1px,color:#93c5fd
    style Window fill:#1e293b,stroke:#06b6d4,stroke-width:1px,color:#e2e8f0
    style SeenMap fill:#1f2937,stroke:#8b5cf6,stroke-width:1px,color:#fff
    style Advance fill:#3b0764,stroke:#ec4899,stroke-width:1px,color:#fbcfe8
    style Expand fill:#064e3b,stroke:#10b981,stroke-width:1px,color:#a7f3d0
```

```java
import java.util.HashMap;
import java.util.Map;

public class LongestUniqueSubstring {
    public static int lengthOfLongestSubstring(String s) {
        if (s == null || s.isEmpty()) return 0;

        Map<Character, Integer> lastSeen = new HashMap<>();
        int maxLength = 0;
        int left = 0;

        for (int right = 0; right < s.length(); right++) {
            char current = s.charAt(right);
            if (lastSeen.containsKey(current)) {
                left = Math.max(left, lastSeen.get(current) + 1);
            }
            lastSeen.put(current, right);
            maxLength = Math.max(maxLength, right - left + 1);
        }
        return maxLength;
    }
}
```
- **Time Complexity:** $O(N)$ single pass across the string.
- **Space Complexity:** $O(\min(N, \Sigma))$ where $\Sigma$ is the alphabet size.

---

### Q5. Longest Common Prefix in an Array of Strings
**Topic Covered:** Horizontal & Vertical String Scanning  
**Definition:** Identify the longest string prefix shared across all strings in an array.

```java
public class LongestCommonPrefix {
    public static String getLongestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) return "";
        String prefix = strs[0];

        for (int i = 1; i < strs.length; i++) {
            while (!strs[i].startsWith(prefix)) {
                prefix = prefix.substring(0, prefix.length() - 1);
                if (prefix.isEmpty()) return "";
            }
        }
        return prefix;
    }
}
```

---

# 2. Array & Data Structure Manipulations

---

### Q6. Remove Duplicates from an Array in a Single Iteration
**Topic Covered:** In-Place Two-Pointer Deduplication  
**Definition:** Given a sorted array, remove duplicates in-place such that each unique element appears once, returning the length of the deduplicated sub-array in $O(N)$ time and $O(1)$ memory.

```java
public class ArrayDeduplication {
    public static int removeDuplicatesSorted(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int writeIndex = 1;

        for (int readIndex = 1; readIndex < nums.length; readIndex++) {
            if (nums[readIndex] != nums[readIndex - 1]) {
                nums[writeIndex++] = nums[readIndex];
            }
        }
        return writeIndex;
    }
}
```

---

### Q7. Rearrange Integer Array: Even Before Odd
**Topic Covered:** Partitioning / Two-Pointer Technique (Hoare Variant)  
**Definition:** Segregate an array such that all even integers precede odd integers.

```java
public class EvenOddPartition {
    public static void rearrangeEvenOdd(int[] nums) {
        if (nums == null || nums.length <= 1) return;
        int left = 0;
        int right = nums.length - 1;

        while (left < right) {
            while (left < right && nums[left] % 2 == 0) left++;
            while (left < right && nums[right] % 2 != 0) right--;
            if (left < right) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
                left++;
                right--;
            }
        }
    }
}
```
- **Time Complexity:** $O(N)$ single iteration.
- **Space Complexity:** $O(1)$ strictly in-place.

---

### Q8. Print Prime Numbers Between 1 and 200 (Minimal Complexity)
**Topic Covered:** Number Theory & Sieve of Eratosthenes  
**Definition:** Identifying primes up to $M = 200$ using the Sieve of Eratosthenes in $O(M \log(\log M))$.

```java
import java.util.Arrays;

public class PrimeSieve {
    public static void printPrimesUpTo(int limit) {
        boolean[] isPrime = new boolean[limit + 1];
        Arrays.fill(isPrime, true);
        isPrime[0] = false;
        isPrime[1] = false;

        for (int p = 2; p * p <= limit; p++) {
            if (isPrime[p]) {
                for (int i = p * p; i <= limit; i += p) {
                    isPrime[i] = false;
                }
            }
        }

        for (int i = 2; i <= limit; i++) {
            if (isPrime[i]) {
                System.out.print(i + " ");
            }
        }
        System.out.println();
    }
}
```

---

### Q9. Reverse a Singly Linked List (Iterative & Recursive)
**Topic Covered:** Linked List Pointer Manipulation  
**Definition:** Given the head of a singly linked list, invert pointer directions such that the tail becomes the new head.

```java
public class LinkedListReversal {
    public static class ListNode {
        int val;
        ListNode next;
        ListNode(int val) { this.val = val; }
    }

    // Iterative: O(N) time, O(1) space
    public static ListNode reverseIterative(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode nextTemp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextTemp;
        }
        return prev;
    }

    // Recursive: O(N) time, O(N) call-stack space
    public static ListNode reverseRecursive(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode newHead = reverseRecursive(head.next);
        head.next.next = head;
        head.next = null;
        return newHead;
    }
}
```

---

### Q10. Path Sum Verification in a Binary Tree (Root to Leaf)
**Topic Covered:** Tree Traversal & Depth-First Search (DFS)  
**Definition:** Determine if there exists a root-to-leaf path where the sum of node values along the path equals a specified target sum.

```java
public class BinaryTreePathSum {
    public static class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode(int val) { this.val = val; }
    }

    public static boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) return false;

        if (root.left == null && root.right == null) {
            return targetSum == root.val;
        }

        int remaining = targetSum - root.val;
        return hasPathSum(root.left, remaining) || hasPathSum(root.right, remaining);
    }
}
```

---

# 3. Core Language Semantics & Keywords

---

### Q11 & Q25. `==` vs `equals()` and the String Pool Mechanism
*(Grouped together: Identity vs Equivalence and String Pool Allocation)*  
**Topic Covered:** Object Memory Identity, Value Equivalence, String Interning  
- `==`: Reference identity operator. Evaluates whether two reference variables point to the exact same physical memory address on the JVM Heap.
- `equals()`: Method on `Object`. Evaluates semantic value equivalence when overridden.
- **String Pool:** A specialized hash table (`StringTable`) allocated in the JVM Heap that stores a single canonical copy of each string literal.

```mermaid
flowchart TD
    subgraph Stack["Stack Variables"]
        s1["s1 = 'Java'"]
        s2["s2 = 'Java'"]
        s3["s3 = new String('Java')"]
    end

    subgraph Heap["JVM Heap"]
        subgraph StringPool["String Constant Pool (StringTable)"]
            Literal["'Java' (char[] hash cached)"]
        end
        HeapObj["String Object (Heap Instance)"]
    end

    s1 --> Literal
    s2 --> Literal
    s3 --> HeapObj
    HeapObj -.->|"Internal value ref"| Literal

    style Stack fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Heap fill:#0f172a,stroke:#06b6d4,stroke-width:1.5px,color:#67e8f9
    style StringPool fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#a7f3d0
    style Literal fill:#1f2937,stroke:#10b981,stroke-width:1px,color:#fff
    style HeapObj fill:#1f2937,stroke:#06b6d4,stroke-width:1px,color:#fff
```

```java
String s1 = "Hello";
String s2 = "Hello";
String s3 = new String("Hello");

System.out.println(s1 == s2);      // true  (Shared string pool reference)
System.out.println(s1 == s3);      // false (s3 is a distinct heap instance)
System.out.println(s1.equals(s3)); // true  (Equal character contents)
```

---

### Q12. Short-Circuiting with Logical Operators (`&&`, `||`)
**Topic Covered:** Boolean Evaluation & Control Flow  
- `&&` (Logical AND): Halts evaluation as `false` as soon as the first operand evaluates to `false`.
- `||` (Logical OR): Halts evaluation as `true` as soon as the first operand evaluates to `true`.

```java
// Prevents NullPointerException:
if (user != null && user.isActive()) {
    // user.isActive() is NEVER invoked if user is null
}
```

---

### Q13 & Q14. Constructors and Invoking Static Methods Inside Constructors
*(Grouped together: Constructor Mechanics and Static Invocations)*  
**Topic Covered:** Object Lifecycle & Class Loading  
**Can you invoke a static method inside a constructor?**
**Yes.** Static methods belong to class metadata in Metaspace and are initialized during class loading, well before constructor execution. They are commonly used for defensive argument checks and unique ID generation.

```java
public class Order {
    private final String orderId;

    public Order(double amount) {
        validateAmount(amount); // Invoking static method
        this.orderId = generateId();
    }

    private static void validateAmount(double amount) {
        if (amount <= 0) throw new IllegalArgumentException("Amount must be positive");
    }

    private static String generateId() {
        return "ORD-" + System.currentTimeMillis();
    }
}
```

---

### Q26 & Q27. `final`, `finally`, and `finalize()`
*(Grouped together: Keyword distinctions and Lifecycle handling)*  
**Topic Covered:** Java Core Keywords & Resource Management  

| Feature | Type | Purpose / Scope | Execution Guarantee |
| :--- | :--- | :--- | :--- |
| `final` | Modifier | Applied to variable (immutable ref), method (cannot override), class (cannot subclass). | Compile-time constraint. |
| `finally` | Block | Accompanying `try`/`catch`. Used for mandatory cleanup. | Guaranteed unless `System.exit()` or JVM crash. |
| `finalize()` | Method | Deprecated method on `Object` invoked by GC. | **No guarantee**. Deprecated Java 9, removed Java 18+. |

---

### Q28. `ClassCastException`: Root Causes and Prevention
**Topic Covered:** Type Safety & Pattern Matching  
- **Cause:** Attempting to cast an object reference to an incompatible type at runtime.
- **Prevention:** Use Java 16+ **Pattern Matching for `instanceof`** or enforce generic types at compile-time:
  ```java
  if (obj instanceof String s) {
      System.out.println("Length: " + s.length());
  }
  ```

---

# 4. Object-Oriented Design & Polymorphism

---

### Q15. Composition Over Inheritance
**Topic Covered:** Architectural Design Principles  
**Why Prefer Composition?**
1. **Protects Encapsulation:** Inheriting from a concrete superclass creates tight coupling to its internal implementation details (Fragile Base Class problem).
2. **Runtime Flexibility:** Behavior can be dynamically swapped by injecting different strategy implementations.
3. **Single Inheritance Limitation:** Java classes can extend only one superclass, but can compose multiple delegates.

---

### Q16 & Q17. Interface vs Abstract Class & Static vs Default Methods
*(Grouped together: Abstraction Mechanisms in Java)*  
**Topic Covered:** Interface Contracts & Multiple Inheritance of Behavior  

| Criterion | Abstract Class | Interface (Java 8+) |
| :--- | :--- | :--- |
| **State** | Holds mutable instance fields. | Only `public static final` constants. |
| **Constructors**| Has constructors. | No constructors. |
| **Inheritance** | Single inheritance (`extends`). | Multiple inheritance (`implements`). |
| **Methods** | Abstract, concrete, `protected`, `private`. | Abstract, `default`, `static`, and `private` (Java 9+). |

- **Default Methods (`default`):** Non-breaking method additions with default implementation that implementing classes can override.
- **Static Methods (`static`):** Namespace utility methods that cannot be overridden by implementing classes.

---

### Q18. Marker Interfaces and Modern Alternatives
**Topic Covered:** Type Tagging & Metadata  
A marker interface is an interface with **zero method declarations** (`Serializable`, `Cloneable`). Modern enterprise Java replaces marker interfaces with **Annotations** (`@Entity`, `@Component`, `@Audited`), which support typed metadata without polluting type hierarchies.

---

### Q19 & Q20. Overriding Static Methods & Method Hiding
*(Grouped together: Static Polymorphism Semantics)*  
**Topic Covered:** Compile-Time Resolution vs Dynamic Dispatch  
**Rule:** You **cannot** override a static method. If a subclass declares a static method with the same signature, it is **Method Hiding**, resolved at compile-time via `invokestatic` based on the reference type, not the runtime object instance.

---

### Q21. Anonymous Inner Classes & Scoping
**Topic Covered:** Local Classes & Closures  
An anonymous class is declared and instantiated in a single expression. Any local variable accessed inside it must be explicitly `final` or **effectively final** to ensure thread and stack lifetime safety.

---

### Q22. Designing Completely Immutable Classes
**Topic Covered:** Thread Safety & Invariant Protection  
1. Declare the class `final`.
2. Declare all fields `private final`.
3. Provide no setters.
4. Perform **defensive copies** in constructors for mutable fields (`Date`, `List`, `Map`).
5. Return **unmodifiable views** or defensive copies in getters.

---

### Q23. Shallow Copy vs Deep Copy
**Topic Covered:** Object Cloning & Memory References  
- **Shallow Copy:** Copies primitive fields and duplicates object references directly (both original and copy point to the same child objects on the heap).
- **Deep Copy:** Recursively clones the entire object graph, creating fully independent child copies.

---

### Q24. `StringBuffer` vs `StringBuilder`
**Topic Covered:** Mutability & Synchronization Overhead  
- **`StringBuffer`:** Thread-safe (methods use `synchronized`). Has lock acquisition overhead.
- **`StringBuilder` (Preferred):** Not thread-safe. Significantly faster for single-threaded string concatenation.

---

# 5. JVM Internals, Memory Management & Runtime

---

### Q29 & Q30. JVM Memory Architecture & Handling `OutOfMemoryError`
*(Grouped together: Memory layout and OOM diagnosis)*  
**Topic Covered:** JVM Memory Spaces, GC Generations, Diagnostic Procedures  

```mermaid
flowchart TD
    subgraph JVMProcess ["JVM Process Memory Space (Virtual Memory)"]
        subgraph Heap ["JVM Managed Heap Space (-Xms / -Xmx)"]
            direction TB
            subgraph YoungGen ["Young Generation (-Xmn)"]
                Eden["Eden Space<br/>(New Object Allocation)"]
                S0["Survivor From (S0)<br/>(Age Counter 1..14)"]
                S1["Survivor To (S1)<br/>(Compacted Survivors)"]
            end
            
            subgraph OldGen ["Tenured / Old Generation"]
                Old["Old Space<br/>(Long-Lived Objects & Huge Arrays)"]
            end
            
            Eden -->|"Minor GC: Surviving Objects"| S0
            S0 <-->|"Minor GC: Ping-Pong & Age Increment"| S1
            S0 -->|"Promotion: Age >= TenuringThreshold (15)"| Old
            S1 -->|"Promotion: Age >= TenuringThreshold (15)"| Old
            Old -.->|"Major / Full GC: Mark-Sweep-Compact"| Old
        end

        subgraph NonHeap ["Non-Heap Native Memory"]
            direction TB
            ThreadStacks["Thread Stacks (-Xss)<br/>Stack Frames, Primitive Locals, Object References"]
            Meta["Metaspace (-XX:MaxMetaspaceSize)<br/>Class Metadata, Method Bytecode, Constant Pool"]
            CodeCache["Code Cache<br/>JIT Compiled Native Assembly"]
        end
        
        ThreadStacks -->|"new MyObject() (Stack Pointer)"| Eden
        Eden -.->|"Klass Pointer Header"| Meta
        CodeCache -.->|"Executes Inlined JIT Code"| ThreadStacks
    end

    style JVMProcess fill:#0f172a,stroke:#3b82f6,stroke-width:2px,color:#93c5fd
    style Heap fill:#1e293b,stroke:#06b6d4,stroke-width:1.5px,color:#67e8f9
    style YoungGen fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#a7f3d0
    style OldGen fill:#0f172a,stroke:#f59e0b,stroke-width:1.5px,color:#fde68a
    style NonHeap fill:#1e293b,stroke:#8b5cf6,stroke-width:1.5px,color:#c4b5fd
    style Eden fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
    style S0 fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
    style S1 fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
    style Old fill:#1f2937,stroke:#f59e0b,stroke-width:1.5px,color:#fff
    style ThreadStacks fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style Meta fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style CodeCache fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
```

#### Diagnostic & Triage Matrix: `java.lang.OutOfMemoryError`

| Error Subtype | Affected Memory Region | Root Cause & Failure Mechanism | Production Diagnostic & Triage Playbook |
| :--- | :--- | :--- | :--- |
| **`Java heap space`** | JVM Heap (Old / Young Gen) | Live object leak (static collections, unclosed streams, cache retainers) or insufficient `-Xmx` under sudden traffic spikes. | Enable `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/dumps/heap.hprof`. Analyze dominant dominator tree paths via Eclipse MAT or JProfiler. |
| **`Metaspace`** | Native Metaspace | Unbounded dynamic class creation (Spring CGLIB, ByteBuddy, reflection mocks, hot class reloading). | Tune `-XX:MaxMetaspaceSize=512m`. Inspect classloader retention with `jcmd <pid> VM.classloader_stats` and detect dynamic proxy leaks. |
| **`Unable to create new native thread`** | OS Thread Table / Virtual Memory | Thread leaks (unbounded `Executors.newCachedThreadPool()`) or OS thread allocation limit exhaustion (`ulimit -u`). | Cap pool sizes via bounded `ThreadPoolExecutor`. Check OS limits: `cat /proc/sys/kernel/threads-max` and reduce stack size (`-Xss256k`). |
| **`Direct buffer memory`** | Off-Heap Direct Memory | Netty / NIO `ByteBuffer.allocateDirect()` allocation exceeds `-XX:MaxDirectMemorySize` without GC reclaiming phantom references. | Verify off-heap buffer pooling and release semantics (`ReferenceCountUtil.release()`). Set `-XX:MaxDirectMemorySize` explicitly. |
| **`GC overhead limit exceeded`** | Full Heap Space | Over 98% of total CPU time is spent in GC collecting less than 2% of heap space. | Critical throughput collapse. Profile memory allocation rate with async-profiler; scale heap or prune in-memory processing payloads. |

---

### Q31. ClassLoader Architecture and Delegation Model
**Topic Covered:** JVM Class Loading & Parent Delegation  
1. **Bootstrap ClassLoader:** Loads core runtime classes (`java.base`).
2. **Platform ClassLoader:** Loads modular platform libraries.
3. **Application ClassLoader:** Loads classpath JARs.
- **Parent Delegation Principle:** Child classloaders always delegate class lookup to their parent first, preventing core classes like `java.lang.String` from being overridden.

---

### Q32 & Q33. Java Reflection API & Dynamic Proxies
*(Grouped together: Introspection and Dynamic Interception)*  
**Topic Covered:** Runtime Bytecode Introspection & Dynamic Invocation  
`java.lang.reflect.Proxy` generates dynamic proxy classes at runtime that intercept interface method calls via an `InvocationHandler`. Used heavily by Spring AOP, `@Transactional`, and Mockito.

---

### Q34. Java NIO `Path` vs Classic IO `File`
**Topic Covered:** File System Abstraction & Modern I/O  
`Path` (Java 7+) throws descriptive `IOException` subclasses instead of returning `false`, supports symbolic links, native file attributes, memory-mapped buffers, and asynchronous channels.

---

### Q35. Tail Call Optimization in Java
**Topic Covered:** Call Stack Mechanics  
The standard HotSpot JVM does **not** implement Tail Call Optimization (TCO) to preserve complete stack traces for debugging and security permission stack-walks. Deep recursion results in `StackOverflowError`.

---

# 6. Modern Java Features (Java 17 to Java 21+)

---

### Q36. Text Blocks and Multiline Strings
**Topic Covered:** Modern Syntax (Java 15+)  
Text blocks (`""" ... """`) simplify JSON, SQL, and HTML string definitions without escape characters.

---

### Q37. Pattern Matching for `switch`
**Topic Covered:** Pattern Matching & Type Narrowing (Java 17 / 21)  
Enables pattern matching over types with `when` guard clauses:
```java
return switch (payment) {
    case CreditCard c && c.limit() > 10000 -> "VIP Credit Card";
    case CreditCard c                      -> "Standard Credit Card";
    case BankTransfer b                    -> "Bank transfer IBAN: " + b.iban();
    case null                              -> "Null payment";
    default                                -> "Unknown: " + payment;
};
```

---

### Q38 & Q39. Records, Standard Classes & Sealed Classes in Production
*(Grouped together: Modern Type Hierarchies and Domain Modeling)*  
**Topic Covered:** Data-Oriented Programming (Java 16/17+)  
- **`record`:** Compact, shallowly immutable data carriers with auto-generated getters, `equals()`, `hashCode()`, and `toString()`.
- **`sealed` class/interface:** Restricts which implementations are allowed (`permits`). Guarantees compile-time exhaustiveness in `switch` statements without requiring a `default` case.

---

### Q40. Virtual Threads (Project Loom) vs Platform Threads
**Topic Covered:** High-Throughput Lightweight Concurrency (Java 21)  
- **Platform Thread:** 1:1 mapped to OS kernel thread with ~1 MB memory footprint.
- **Virtual Thread:** User-mode thread managed directly by the JVM with a few KB footprint. When blocking on network I/O, the virtual thread unmounts from its carrier thread, allowing millions of concurrent connections on standard server hardware.

---

# 7. Advanced Algorithmic Patterns & Real-World OOP Scenarios

---

### Q41. Detect a Cycle in a Singly Linked List Without Using Extra Space
**Topic Covered:** Pointer Topologies & Floyd's Cycle-Finding Algorithm  
**Difficulty:** `[Medium]`

#### Architectural Overview
When traversing linked records in memory (e.g., undo stacks, workflow transition states, or custom pointer structures), circular references cause infinite traversal loops and stack overflows. Floyd's Algorithm utilizes two pointers—a slow pointer advancing 1 node per iteration and a fast pointer advancing 2 nodes per iteration. If a cycle exists, the fast pointer will lap and intersect the slow pointer inside the cycle loop without allocating heap memory.

```mermaid
flowchart LR
    Head["Head: Node 1"] --> Node2["Node 2"]
    Node2 --> Node3["Node 3 (Cycle Start)"]
    Node3 --> Node4["Node 4"]
    Node4 --> Node5["Node 5"]
    Node5 --> Node3

    subgraph Pointers ["Pointer Convergence"]
        direction TB
        Slow["Slow Pointer (1 step/iter)"]
        Fast["Fast Pointer (2 steps/iter)"]
        Collision["Collision inside loop guarantees cycle!"]
        Slow --> Collision
        Fast --> Collision
    end

    style Head fill:#1e293b,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Node2 fill:#1e293b,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Node3 fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#6ee7b7
    style Node4 fill:#1e293b,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Node5 fill:#1e293b,stroke:#ef4444,stroke-width:1.5px,color:#fca5a5
    style Pointers fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c4b5fd
    style Slow fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
    style Fast fill:#1f2937,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style Collision fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
```

#### Production Code
```java
package com.enterprise.algorithms;

public final class LinkedListCycleChecker {

    public static final class ListNode<T> {
        private final T data;
        private ListNode<T> next;

        public ListNode(T data) {
            this.data = data;
        }

        public T getData() { return data; }
        public ListNode<T> getNext() { return next; }
        public void setNext(ListNode<T> next) { this.next = next; }
    }

    /**
     * Determines whether a linked list contains a cycle using Floyd's Tortoise and Hare.
     * Time Complexity: O(N) | Space Complexity: O(1)
     */
    public static <T> boolean hasCycle(ListNode<T> head) {
        if (head == null || head.getNext() == null) {
            return false;
        }

        ListNode<T> slow = head;
        ListNode<T> fast = head;

        while (fast != null && fast.getNext() != null) {
            slow = slow.getNext();
            fast = fast.getNext().getNext();

            if (slow == fast) {
                return true;
            }
        }
        return false;
    }
}
```

* **Complexity**: Time $O(N)$ where $N$ is the number of nodes; Auxiliary Space $O(1)$ strictly stack pointers.
* **Production Trap**: Storing visited nodes in a `HashSet<ListNode>` consumes $O(N)$ heap memory and can trigger `OutOfMemoryError` on high-throughput streaming node processors.

---

### Q42. Static Method Hiding vs Dynamic Method Overriding in Java Inheritance
**Topic Covered:** Polymorphism, Static Binding & Bytecode Dispatch  
**Difficulty:** `[Easy]`

#### Architectural Overview
In Java, instance methods use **dynamic method dispatch** resolved at runtime using the object's actual class `vtable` in Metaspace (`invokevirtual`). However, `static` methods belong to the class, not the instance. When a subclass declares a static method with the exact same signature as a superclass static method, it is **method hiding**, not overriding. The JVM binds static calls at compile time (`invokestatic`) based strictly on the reference type declared.

```mermaid
flowchart TD
    subgraph References ["Compile-Time Reference vs Runtime Instance"]
        Ref["Parent p = new Child();"]
        Ref --> StaticCall["p.staticPrint() -> Resolved at COMPILE TIME to Parent.staticPrint()"]
        Ref --> VirtualCall["p.instancePrint() -> Resolved at RUNTIME via vtable to Child.instancePrint()"]
    end

    style References fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Ref fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style StaticCall fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style VirtualCall fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### Production Code & Prediction
```java
class Parent {
    public static void display() {
        System.out.println("Parent static display");
    }
    public void print() {
        System.out.println("Parent instance print");
    }
}

class Child extends Parent {
    public static void display() {
        System.out.println("Child static display");
    }
    @Override
    public void print() {
        System.out.println("Child instance print");
    }
}

public class InheritanceDispatchDemo {
    public static void main(String[] args) {
        Parent p = new Child();
        p.display(); // Prints: "Parent static display" (invokestatic based on Parent type)
        p.print();   // Prints: "Child instance print"  (invokevirtual dispatched to Child instance)
    }
}
```

#### Method Overloading vs Method Overriding Comparison Matrix

| Dimension | Method Overloading (Compile-Time Polymorphism) | Method Overriding (Runtime Polymorphism) |
| :--- | :--- | :--- |
| **Resolution Mechanism** | Resolved at **compile time** via static type signatures (`invokevirtual` with fixed descriptor or `invokestatic`). | Resolved at **runtime** using the object's dynamic dispatch table (`vtable`). |
| **Scope / Location** | Within the **same class** (or inherited from superclass). | Between a **superclass and a subclass**. |
| **Method Name** | Must be **identical**. | Must be **identical**. |
| **Argument List** | Must **differ** (number, types, or order of parameters). | Must be **strictly identical** (same types and count). |
| **Return Type** | Can be **different** (return type alone does not differentiate overloaded methods). | Must be **identical or covariant** (sub-type of superclass return type). |
| **Access Modifiers** | Can be modified arbitrarily (`public`, `protected`, `private`). | Cannot be **more restrictive** than the superclass method. |
| **Checked Exceptions** | Can throw any checked exception independently. | Cannot throw **broader or new checked exceptions**; can throw fewer, narrower, or unchecked exceptions. |
| **Private / Static Methods** | Private and static methods **can be overloaded**. | Private methods **cannot be overridden**; static methods **cannot be overridden** (they are hidden). |

* **Complexity**: Compile-time direct binding $O(1)$; virtual table (`vtable`) pointer lookup $O(1)$.
* **Production Trap**: Invoking static methods on instance references (e.g., `p.display()`) causes deceptive code reading; always invoke static methods through their class name directly (`Parent.display()`). Avoid overloading methods with ambiguous varargs or auto-boxing conversions (e.g., `doWork(int)` vs `doWork(Integer)`) as it leads to fragile overload resolution.

---

### Q43. Character Parity / Zero vs One Frequency Comparison Without Using a Third Variable
**Topic Covered:** In-Place Bitwise & Arithmetic Balance  
**Difficulty:** `[Medium]`

#### Architectural Overview
When comparing whether character `'0'` or `'1'` occurs more frequently in a binary string under strict memory constraints (zero heap allocations, zero additional scalar variables), we can repurpose a single running accumulator as a signed balance offset: increment by $+1$ for `'1'` and decrement by $-1$ for `'0'`. If final balance $>0$, `'1'` occurs more; if $<0$, `'0'` occurs more; if $0$, they are equal.

```java
package com.enterprise.algorithms;

public final class BitBalanceComparator {

    /**
     * Checks whether '0' or '1' is more frequent without extra variables.
     * Uses the method argument itself or returns integer balance (+1 for 1, -1 for 0).
     */
    public static String compareFrequencies(String s) {
        if (s == null || s.isEmpty()) return "Equal";

        int balance = 0; // single tracking variable
        for (int i = 0; i < s.length(); i++) {
            balance += (s.charAt(i) == '1') ? 1 : -1;
        }

        if (balance > 0) return "Ones occur more frequently";
        if (balance < 0) return "Zeros occur more frequently";
        return "Both occur with equal frequency";
    }
}
```

* **Complexity**: Time $O(N)$ single linear scan; Space $O(1)$ zero heap allocation.

---

### Q44. Highest Average Student Score (Rounded Down) from Tabular Data
**Topic Covered:** In-Memory Aggregations & Streaming Maps  
**Difficulty:** `[Medium]`

#### Architectural Overview
Given a tabular dataset of student names and exam scores, determine the student with the highest average score (rounded down to the nearest integer). We aggregate total scores and count per student using a `HashMap<String, int[]>`, compute `sum / count`, and track the maximum average.

```java
package com.enterprise.analytics;

import java.util.*;

public final class StudentScoreAnalytics {

    public record StudentRecord(String name, int score) {}

    /**
     * Computes the maximum average score across all students.
     * Time Complexity: O(N) | Space Complexity: O(U) where U is unique students.
     */
    public static int getHighestAverageScore(StudentRecord[] records) {
        if (records == null || records.length == 0) return 0;

        // Map: Student Name -> int[]{totalScore, examCount}
        Map<String, int[]> scoreMap = new HashMap<>();

        for (StudentRecord r : records) {
            scoreMap.computeIfAbsent(r.name(), k -> new int[2]);
            scoreMap.get(r.name())[0] += r.score();
            scoreMap.get(r.name())[1] += 1;
        }

        int maxAverage = Integer.MIN_VALUE;

        for (int[] stats : scoreMap.values()) {
            int average = stats[0] / stats[1]; // Integer division rounds down
            if (average > maxAverage) {
                maxAverage = average;
            }
        }

        return maxAverage;
    }
}
```

* **Complexity**: Time $O(N)$; Space $O(U)$ where $U \le N$ is unique student count.

---

### Q45. Checked vs Unchecked Exceptions, Performance Overhead & `@ControllerAdvice` Exception Handling
**Topic Covered:** Exception Hierarchy & Global Fault Handling  
**Difficulty:** `[Medium]`

#### Architectural Overview
Java categorizes throwables into:
1. **Checked Exceptions (`Exception` subclasses excluding `RuntimeException`)**: Enforced by the compiler, representing recoverable external conditions (e.g., `IOException`, `SQLException`).
2. **Unchecked Exceptions (`RuntimeException` subclasses)**: Represent programming errors or unrecoverable bugs (e.g., `NullPointerException`, `IllegalArgumentException`).
3. **Errors (`Error` subclasses)**: Fatal JVM failures (e.g., `OutOfMemoryError`, `StackOverflowError`).

```mermaid
flowchart TD
    Throwable["java.lang.Throwable"]
    Throwable --> Error["java.lang.Error (Fatal JVM: OOM, StackOverflow)"]
    Throwable --> Exception["java.lang.Exception"]
    Exception --> Checked["Checked Exceptions (IOException, SQLException)<br/>Compile-time enforced"]
    Exception --> RuntimeException["java.lang.RuntimeException (Unchecked)<br/>NullPointerException, BusinessException"]

    style Throwable fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Error fill:#1e293b,stroke:#ef4444,stroke-width:2px,color:#fca5a5
    style Exception fill:#1e293b,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Checked fill:#1e293b,stroke:#f59e0b,stroke-width:1.5px,color:#fde68a
    style RuntimeException fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#6ee7b7
```

#### Production Best Practice:
Never use exceptions for business control flow due to the heavy performance penalty of `Throwable.fillInStackTrace()`. Handle enterprise web errors globally via `@RestControllerAdvice` emitting RFC 7807 `ProblemDetail`.
