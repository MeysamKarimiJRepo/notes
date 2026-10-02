# Java Data Structures & Algorithms — Interview Prep Guide

> Converted from *Java Data Structures & Algorithms + LEETCODE Exercises* (course notes), then reviewed, corrected, de-duplicated and extended for Java interview preparation.
> Every topic is taught with **text-based figures** so you can redraw them on a whiteboard during an interview.

**How to use this guide**

1. Read the concept + figure first, then try to write the code yourself before looking at the solution.
2. For every problem, be able to say out loud: *idea → edge cases → time/space complexity*.
3. Use the cheat sheets (section 0) the night before the interview.
4. Sections marked **★ Interview** are the classic LeetCode-style questions.
5. Sections marked **➕ Added** did not exist (or were empty) in the original notes and were filled in during the review.

---

## Table of Contents

0. [Cheat Sheets (read first)](#0-cheat-sheets-read-first)
1. [Big O Notation](#1-big-o-notation)
2. [Classes and References ("Pointers")](#2-classes-and-references-pointers)
3. [Singly Linked List](#3-singly-linked-list)
4. [★ Linked List Interview Problems](#4--linked-list-interview-problems)
5. [Doubly Linked List](#5-doubly-linked-list)
6. [★ Doubly Linked List Interview Problems](#6--doubly-linked-list-interview-problems)
7. [Stacks and Queues](#7-stacks-and-queues)
8. [★ Stack & Queue Interview Problems](#8--stack--queue-interview-problems)
9. [Trees and Binary Search Trees](#9-trees-and-binary-search-trees)
10. [Hash Tables](#10-hash-tables)
11. [★ Hash Table & Set Interview Problems](#11--hash-table--set-interview-problems)
12. [Graphs](#12-graphs)
13. [Heaps](#13-heaps)
14. [Recursion and Recursive BST](#14-recursion-and-recursive-bst)
15. [★ Recursive BST Interview Problems](#15--recursive-bst-interview-problems)
16. [Tree Traversal (BFS & DFS)](#16-tree-traversal-bfs--dfs)
17. [★ BST Traversal Interview Problems](#17--bst-traversal-interview-problems)
18. [Basic Sorts (Bubble, Selection, Insertion)](#18-basic-sorts-bubble-selection-insertion)
19. [Merge Sort](#19-merge-sort)
20. [Quick Sort](#20-quick-sort)
21. [Dynamic Programming](#21-dynamic-programming)
22. [★ Array Interview Problems](#22--array-interview-problems)
23. [Interview Patterns, Java Gotchas & Final Checklist](#23-interview-patterns-java-gotchas--final-checklist)
24. [Review Log — What Was Corrected or Added](#24-review-log--what-was-corrected-or-added)

---

## 0. Cheat Sheets (read first)

### 0.1 Growth of common complexities

Operations needed when **n = 16** (each █ ≈ 4 operations):

```
O(1)       │▏ 1
O(log n)   │█ 4
O(n)       │████ 16
O(n log n) │████████████████ 64
O(n²)      │████████████████████████████████████████████████████████████████ 256
```

Double n to 32 and O(n) doubles (32) but O(n²) quadruples (1,024) — that is why the shape matters more than the constant.

| Big O      | Name         | n = 10 | n = 1,000 | n = 1,000,000 | Typical example                      |
|------------|--------------|-------:|----------:|--------------:|--------------------------------------|
| O(1)       | constant     | 1      | 1         | 1             | `array[i]`, `HashMap.get`            |
| O(log n)   | logarithmic  | ~3     | ~10       | ~20           | binary search, balanced BST lookup   |
| O(n)       | linear       | 10     | 1,000     | 1,000,000     | one loop over the input              |
| O(n log n) | linearithmic | ~33    | ~10,000   | ~20,000,000   | merge sort, `Arrays.sort` (objects)  |
| O(n²)      | quadratic    | 100    | 1,000,000 | 10¹²          | nested loops, bubble sort            |
| O(2ⁿ)      | exponential  | 1,024  | ≈ ∞       | ≈ ∞           | naive recursive Fibonacci            |

### 0.2 Data structure operations

| Structure             | Access  | Search   | Insert                  | Delete                   | Space    |
|-----------------------|---------|----------|-------------------------|--------------------------|----------|
| Array / `ArrayList`   | O(1)    | O(n)     | O(1)* end / O(n) middle | O(1) end / O(n) middle   | O(n)     |
| Singly Linked List    | O(n)    | O(n)     | O(1) head/tail          | O(1) head / **O(n) tail**| O(n)     |
| Doubly Linked List    | O(n)    | O(n)     | O(1) head/tail          | O(1) head/tail           | O(n)     |
| Stack (push/pop/peek) | –       | O(n)     | O(1)                    | O(1)                     | O(n)     |
| Queue (enqueue/deq.)  | –       | O(n)     | O(1)                    | O(1)                     | O(n)     |
| Hash Table            | –       | O(1) avg | O(1) avg                | O(1) avg                 | O(n)     |
| BST (balanced)        | –       | O(log n) | O(log n)                | O(log n)                 | O(n)     |
| BST (degenerate)      | –       | O(n)     | O(n)                    | O(n)                     | O(n)     |
| Heap                  | O(1) top| O(n)     | O(log n)                | O(log n) (remove top)    | O(n)     |
| Graph (adj. list)     | –       | –        | vertex O(1), edge O(1)  | edge O(E), vertex O(V+E) | O(V + E) |
| Graph (adj. matrix)   | –       | –        | vertex O(V²), edge O(1) | edge O(1), vertex O(V²)  | O(V²)    |

`*` amortized — occasionally the backing array is resized (copy = O(n)).
Hash table worst case is O(n) (all keys collide); Java 8+ `HashMap` turns long bucket chains into red-black trees, giving O(log n) worst case per bucket.

### 0.3 Sorting algorithms

| Algorithm      | Best       | Average    | Worst      | Space    | Stable? | Notes                                         |
|----------------|------------|------------|------------|----------|---------|-----------------------------------------------|
| Bubble sort    | O(n)*      | O(n²)      | O(n²)      | O(1)     | Yes     | *only with the "no swaps → stop" optimization |
| Selection sort | O(n²)      | O(n²)      | O(n²)      | O(1)     | No      | fewest swaps (≤ n)                            |
| Insertion sort | O(n)       | O(n²)      | O(n²)      | O(1)     | Yes     | great for small / nearly sorted data          |
| Merge sort     | O(n log n) | O(n log n) | O(n log n) | O(n)     | Yes     | predictable; good for linked lists            |
| Quick sort     | O(n log n) | O(n log n) | O(n²)      | O(log n) | No      | fast in practice; worst case with bad pivots  |
| Heap sort      | O(n log n) | O(n log n) | O(n log n) | O(1)     | No      | ➕ for completeness                            |

> **Java fact:** `Arrays.sort(int[])` uses Dual-Pivot Quicksort; `Arrays.sort(Object[])` and `Collections.sort` use TimSort (stable merge/insertion hybrid).

### 0.4 Course structure → Java Collections Framework ➕

| Built in this course     | Use in real Java code                       | Remember                                               |
|--------------------------|---------------------------------------------|--------------------------------------------------------|
| `ArrayList`-style array  | `ArrayList<E>`                              | random access O(1), growth ~1.5×                       |
| Singly linked list       | (none) — `LinkedList<E>` is **doubly** linked| `LinkedList.removeLast()` is O(1)                      |
| Doubly linked list       | `LinkedList<E>`                             | also implements `Deque`                                |
| Stack                    | `ArrayDeque<E>` (`push/pop/peek`)           | avoid legacy `java.util.Stack` (synchronized, Vector)  |
| Queue                    | `ArrayDeque<E>` / `LinkedList<E>` (`offer/poll/peek`) | `add/remove/element` throw, `offer/poll/peek` don't |
| Hash table               | `HashMap<K,V>`, `HashSet<E>`                | needs correct `equals` + `hashCode`                    |
| Ordered map/set (BST)    | `TreeMap<K,V>`, `TreeSet<E>`                | red-black tree → O(log n), sorted iteration            |
| Heap                     | `PriorityQueue<E>`                          | **min-heap** by default; max-heap: `Collections.reverseOrder()` |
| Graph                    | `Map<V, List<V>>`                           | adjacency list                                         |

---

## 1. Big O Notation

### 1.1 What is Big O?

Big O is a way to compare two pieces of code **mathematically** by how their cost grows as the input grows.

- Measuring with a stopwatch is unreliable: a computer twice as fast finishes twice as fast, but the *code* is not better.
- So we count **operations** as a function of the input size `n`.
- **Time complexity** = how the number of operations grows.
- **Space complexity** = how the extra memory grows.

Interviewers usually ask for time complexity first, then: *"What if memory is the priority?"* — be ready to discuss both and the trade-off.

```
          same task, two implementations
   Code 1: fast, but uses a big HashMap    → better TIME, worse SPACE
   Code 2: slower, but only a few variables → worse TIME, better SPACE
```

> ➕ **Benchmarking note (performance engineering):** naive `System.currentTimeMillis()` timing in Java is misleading — the JIT warms up, may remove dead code (e.g. a loop whose result is never used), and GC adds noise. For real measurements use **JMH**. Big O is about growth, not wall-clock time.

### 1.2 Best, average and worst case (Ω, Θ, O)

The course uses this simplified mapping:

| Greek letter | Course meaning | Example: linear search in `[1,2,3,4,5,6,7]` |
|--------------|----------------|----------------------------------------------|
| Ω (Omega)    | best case      | target = 1 → found in 1 step → Ω(1)          |
| Θ (Theta)    | average case   | target = 4 → about n/2 steps → Θ(n)          |
| O (Big O)    | worst case     | target = 7 (or missing) → n steps → O(n)     |

```
array:  [ 1 | 2 | 3 | 4 | 5 | 6 | 7 ]
          ^           ^           ^
        best        average     worst
       (1 step)    (~n/2)      (n steps)
```

> ➕ **Precise version (good to know if an interviewer probes):** Ω, Θ and O are really *bounds* — lower, tight, and upper — and each can be applied to any case. "Best / average / worst case" describes *which input* you analyze. Saying "the worst-case running time is O(n)" is the most common and always safe phrasing. In interviews, "Big O" normally means worst case.

```java
int[] array = {1, 2, 3, 4, 5, 6, 7};
int target = 4;                       // change to 1 (best) or 7 (worst)
for (int value : array) {
    if (value == target) break;       // stops early in the best case
}
```

### 1.3 O(n) — linear time

The number of operations grows proportionally with `n`.

```java
public static void printItems(int n) {
    for (int i = 0; i < n; i++) {
        System.out.println(i);        // runs n times
    }
}
```

```
n = 4:   i=0 → print   i=1 → print   i=2 → print   i=3 → print     (4 operations)

  n    | operations
 ------+-----------
   1   |     1
  10   |    10
  100  |   100
 1000  |  1000          → a straight line through the origin
```

### 1.4 Rule 1 — Drop constants

```java
public static void printItems(int n) {
    for (int i = 0; i < n; i++) System.out.println(i);   // n
    for (int j = 0; j < n; j++) System.out.println(j);   // n
}                                                         // total 2n → O(n)
```

We only care about the **growth rate**, so constant multipliers disappear:

| Raw count  | Simplified |
|------------|------------|
| O(2n)      | O(n)       |
| O(5n + 20) | O(n)       |
| O(n/2)     | O(n)       |
| O(1000)    | O(1)       |

### 1.5 O(n²) — quadratic time

Typically a loop nested inside a loop over the same input.

```java
public static void printItems(int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            System.out.println(i + " " + j);
        }
    }
}
```

```
n = 3                  j=0   j=1   j=2
               i=0   [0 0] [0 1] [0 2]
               i=1   [1 0] [1 1] [1 2]       3 × 3 = 9 operations
               i=2   [2 0] [2 1] [2 2]

   n     O(n)      O(n²)
  10      10         100
  100     100     10,000
 1000    1000  1,000,000     ← refactoring O(n²) → O(n) is a huge win
```

> Note: three nested loops would be O(n³) — but by the "drop non-dominant terms" logic, interviewers mostly care whether you can bring O(n²) down to O(n log n) or O(n).

### 1.6 Rule 2 — Drop non-dominant terms

```java
public static void printItems(int n) {
    for (int i = 0; i < n; i++)            // ┐
        for (int j = 0; j < n; j++)        // ├ O(n²)
            System.out.println(i + " " + j); // ┘
    for (int k = 0; k < n; k++)            // ─ O(n)
        System.out.println(k);
}
// O(n² + n) → O(n²)
```

```
   n      n²        n      n² + n
  10     100       10        110     (n is 9% of total)
  1000   1,000,000 1000  1,001,000   (n is 0.1% of total)  → keep only n²
```

### 1.7 O(1) — constant time

```java
public static int addItems(int n) {
    return n + n;          // 1 operation whether n = 10 or n = 1,000,000
}
// n + n + n is 2 operations → O(2) → still O(1) (drop constants)
```

O(1) is a flat line along the bottom of the chart — the best possible complexity.

### 1.8 O(log n) — logarithmic time

Each step **halves** the remaining problem. The classic example is binary search on a **sorted** array.

```
Find 1 in a sorted array of 8 items:

step 1:  [ 1  2  3  4 | 5  6  7  8 ]   1 < 5 → keep left half
step 2:  [ 1  2 | 3  4 ]               1 < 3 → keep left half
step 3:  [ 1 | 2 ]                     1 < 2 → keep left half
         [ 1 ]  found                  3 steps  (2³ = 8 → log₂ 8 = 3)

n = 1,000,000,000  →  log₂ n ≈ 30 steps
```

➕ Binary search in Java (a must-know):

```java
public static int binarySearch(int[] sorted, int target) {
    int left = 0, right = sorted.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;   // avoids int overflow of (left + right)
        if (sorted[mid] == target) return mid;
        if (sorted[mid] < target) left = mid + 1;
        else                      right = mid - 1;
    }
    return -1;                                 // not found
}
```

Where O(log n) appears: binary search, balanced BST operations (`TreeMap`), heap insert/remove, "divide in half" recursion.

> Correction from the original: O(log n) does not require sorted data in general — **binary search** does. O(log n) arises whenever the problem size is divided by a constant each step (e.g. heap operations work on unsorted data).

### 1.9 Rule 3 — Different inputs → different variables

A very common interview trap.

```java
public static void printItems(int a, int b) {
    for (int i = 0; i < a; i++) System.out.println(i);   // O(a)
    for (int j = 0; j < b; j++) System.out.println(j);   // O(b)
}                                                         // O(a + b)  — NOT O(n)

public static void printPairs(int a, int b) {
    for (int i = 0; i < a; i++)
        for (int j = 0; j < b; j++)
            System.out.println(i + ", " + j);            // O(a × b)  — NOT O(n²)
}
```

```
a = 1, b = 1,000,000,000 → calling both "n" hides that one input dominates.

 sequential loops  →  add       →  O(a + b)
 nested loops      →  multiply  →  O(a × b)
```

Tip: whenever there are two collections (two arrays, two strings, two trees), use two variables.

### 1.10 Big O of `ArrayList`

```
index:   0    1    2    3    4
       [ 11 | 3  | 23 | 7  |    ]       contiguous memory
```

| Operation                         | Big O       | Why                                         |
|-----------------------------------|-------------|---------------------------------------------|
| `add(value)` at the end           | O(1)*       | write to the next free slot                 |
| `remove(size - 1)` at the end     | O(1)        | nothing to shift                            |
| `add(0, value)` / middle          | O(n)        | every following element shifts right        |
| `remove(0)` / middle              | O(n)        | every following element shifts left         |
| `get(index)` / `set(index, v)`    | O(1)        | address = base + index × elementSize        |
| `contains(v)` / `indexOf(v)`      | O(n)        | linear scan (not sorted)                    |

```
add(0, 99):
before:  [ 11 | 3  | 23 | 7  |    ]
            \    \    \    \
after:   [ 99 | 11 | 3  | 23 | 7  ]     every element moved → O(n)
```

`*` amortized: when the internal array is full, Java allocates a bigger one (about 1.5×) and copies → that single add is O(n), but on average adds are O(1).

### 1.11 Wrap-up

| Big O    | n = 100 | n = 1000  | Meaning                 |
|----------|---------|-----------|-------------------------|
| O(1)     | 1       | 1         | constant                |
| O(log n) | ~7      | ~10       | divide and conquer      |
| O(n)     | 100     | 1,000     | proportional            |
| O(n²)    | 10,000  | 1,000,000 | nested loops — avoid    |

| Rule                        | Example                                        |
|-----------------------------|------------------------------------------------|
| Drop constants              | O(2n) → O(n)                                   |
| Drop non-dominant terms     | O(n² + n) → O(n²)                              |
| Different inputs, different terms | O(a + b), O(a × b) — not O(n), O(n²)     |

Reference: [Big-O Cheat Sheet](https://www.bigocheatsheet.com/)

### 1.12 Quiz 1 — Big O (with answers) ➕

1. Two sequential loops over `n` → **O(n)** (O(2n), drop constant).
2. A loop over `n` containing a loop over `n` → **O(n²)**.
3. A loop over array `a` followed by a loop over array `b` → **O(a + b)**.
4. `ArrayList.add(0, x)` → **O(n)** (shifts every element).
5. Binary search in a sorted array → **O(log n)**.
6. `return n * n + 7;` → **O(1)**.

---

## 2. Classes and References ("Pointers")

### 2.1 Classes — the cookie cutter

A **class** is a blueprint (cookie cutter); an **object** is an instance (cookie) created from it.

```
        class Cookie  (blueprint)
        ┌─────────────────────┐
        │ - color : String    │
        │ + Cookie(color)     │
        │ + getColor()        │
        │ + setColor(color)   │
        └─────────┬───────────┘
          new     │      new
     ┌────────────┴────────────┐
     ▼                         ▼
 cookie1 {color="green"}   cookie2 {color="blue"}
```

```java
public class Cookie {
    private String color;

    public Cookie(String color) { this.color = color; }   // constructor
    public String getColor()     { return color; }
    public void setColor(String color) { this.color = color; }
}

Cookie cookie1 = new Cookie("green");
Cookie cookie2 = new Cookie("blue");
cookie1.setColor("yellow");
System.out.println(cookie1.getColor());   // yellow
System.out.println(cookie2.getColor());   // blue  (independent object)
```

- `this` refers to the current object's fields.
- `private` fields + getters/setters = encapsulation.
- Every data structure in this guide is a class (`LinkedList`, `Node`, `Stack`, `BinarySearchTree`, …).

### 2.2 Primitives vs. references

Java has no explicit pointers, but every object variable holds a **reference** (an address) to an object on the heap.

**Primitives are copied by value:**

```java
int num1 = 11;
int num2 = num1;    // copy the value 11
num1 = 22;
System.out.println(num2);   // 11
```

```
STACK
num1 [ 22 ]
num2 [ 11 ]      two independent boxes
```

**Object variables copy the reference:**

```java
HashMap<String, Integer> map1 = new HashMap<>();
map1.put("value", 11);
HashMap<String, Integer> map2 = map1;   // copy the REFERENCE
map1.put("value", 22);
System.out.println(map2.get("value"));  // 22
```

```
STACK              HEAP
map1 [ ●──────┐
              ├──►  { "value" : 22 }     one shared object
map2 [ ●──────┘
```

**Garbage collection:**

```java
map1 = null;
map2 = null;
```

```
map1 [ null ]
map2 [ null ]          { "value" : 22 }   ← unreachable → eligible for GC
```

| Topic            | Primitive (`int`, `char`, …) | Object (reference)              |
|------------------|------------------------------|---------------------------------|
| What is copied   | the value                    | the address                     |
| Shared changes?  | no                           | yes — all references see them   |
| Can be `null`?   | no                           | yes                             |
| Memory           | stack / inline               | heap, managed by GC             |

> ➕ **Interview classic — "Is Java pass-by-reference?"** No. Java is **always pass-by-value**. For objects, the *value passed is the reference*. So a method can mutate the object you pass in, but reassigning the parameter inside the method does not affect the caller's variable.

```java
static void mutate(List<Integer> list) { list.add(1); }        // caller sees the change
static void reassign(List<Integer> list) { list = new ArrayList<>(); } // caller does NOT
```

These references are exactly what `head`, `tail`, `next`, `prev`, `left` and `right` are in the coming data structures.

---

## 3. Singly Linked List

### 3.1 Linked list vs. ArrayList

```
ArrayList — contiguous memory, indexed
  index:  0     1     2     3
        [ 11 ][ 3  ][ 23 ][ 7  ]

LinkedList — nodes scattered in memory, connected by references
   head                              tail
    │                                 │
    ▼                                 ▼
 ┌────┬───┐   ┌────┬───┐   ┌────┬───┐   ┌────┬──────┐
 │ 11 │ ●─┼──►│ 3  │ ●─┼──►│ 23 │ ●─┼──►│ 7  │ null │
 └────┴───┘   └────┴───┘   └────┴───┘   └────┴──────┘
  value next
```

- **No index access** — to reach node *i* you walk from `head`.
- Each **node** = `value` + `next` reference.
- `head` → first node, `tail` → last node, last node's `next` = `null`.

### 3.2 Under the hood — a node is a nested object

```
head ─► { value: 11,
          next: { value: 3,
                  next: { value: 23,
                          next: { value: 7, next: null } } } }
                                          ▲
                                         tail
```

### 3.3 Big O of linked list operations

| Operation             | LinkedList | ArrayList | Why (linked list)                                 |
|-----------------------|------------|-----------|---------------------------------------------------|
| append (add to end)   | O(1)       | O(1)*     | `tail` gives direct access                        |
| removeLast            | **O(n)**   | O(1)      | must walk to the node *before* `tail`             |
| prepend (add to start)| O(1)       | O(n)      | just re-point `head`                              |
| removeFirst           | O(1)       | O(n)      | just re-point `head`                              |
| insert at index       | O(n)       | O(n)      | walk to `index - 1`                               |
| remove at index       | O(n)       | O(n)      | walk to `index - 1`                               |
| get / set by index    | O(n)       | O(1)      | no random access                                  |
| search by value       | O(n)       | O(n)      | linear scan                                       |

> **Interview tip:** choose a linked list when you add/remove at the **front** frequently; choose an ArrayList when you need **random access**. In practice `ArrayList` wins most of the time because of CPU cache locality.

### 3.4 Full implementation

```java
public class LinkedList {

    private Node head;
    private Node tail;
    private int length;

    class Node {
        int value;
        Node next;

        Node(int value) {
            this.value = value;
        }
    }

    public LinkedList(int value) {
        Node newNode = new Node(value);
        head = newNode;
        tail = newNode;
        length = 1;
    }

    public void printList() {
        Node temp = head;
        while (temp != null) {
            System.out.print(temp.value + (temp.next != null ? " -> " : ""));
            temp = temp.next;
        }
        System.out.println();
    }

    public Node getHead()  { return head; }
    public Node getTail()  { return tail; }
    public int getLength() { return length; }

    public void append(int value) { /* 3.6 */ }
    public Node removeLast()      { /* 3.7 */ return null; }
    public void prepend(int value){ /* 3.8 */ }
    public Node removeFirst()     { /* 3.9 */ return null; }
    public Node get(int index)    { /* 3.10 */ return null; }
    public boolean set(int index, int value)    { /* 3.11 */ return false; }
    public boolean insert(int index, int value) { /* 3.12 */ return false; }
    public Node remove(int index) { /* 3.13 */ return null; }
    public void reverse()         { /* 3.14 */ }
}
```

The `Node` class is defined first because the constructor, `append`, `prepend` and `insert` all create nodes.

### 3.5 Constructor

```java
LinkedList myLinkedList = new LinkedList(4);
```

```
head ─┐
      ▼
   ┌───┬──────┐
   │ 4 │ null │        length = 1
   └───┴──────┘
      ▲
tail ─┘
```

### 3.6 append — O(1)

Two cases: empty list, or non-empty list.

```
append(5)  on  4 -> 7

before:  head                tail
          ▼                   ▼
        [ 4 ] ──► [ 7 ] ──► null          newNode [ 5 ]

step 1:  tail.next = newNode
        [ 4 ] ──► [ 7 ] ──► [ 5 ] ──► null
                   ▲
                  tail

step 2:  tail = newNode
        [ 4 ] ──► [ 7 ] ──► [ 5 ] ──► null
                             ▲
                            tail
```

```java
public void append(int value) {
    Node newNode = new Node(value);
    if (length == 0) {
        head = newNode;
        tail = newNode;
    } else {
        tail.next = newNode;   // 1. link the old tail to the new node
        tail = newNode;        // 2. move tail
    }
    length++;
}
```

Order matters: if you moved `tail` first, you would lose the reference to the old last node.

### 3.7 removeLast — O(n)

We must find the node **before** `tail`, and a singly linked list has no backward pointer → walk from `head` with two pointers.

```
remove last of  11 -> 3 -> 23 -> 7

         pre/temp
           ▼
start:   [11] -> [3] -> [23] -> [7] -> null

loop (while temp.next != null):  pre = temp; temp = temp.next
         pre    temp
          ▼      ▼
         [11] -> [3] -> [23] -> [7]
                 pre    temp
                  ▼      ▼
         [11] -> [3] -> [23] -> [7]
                        pre     temp
                         ▼       ▼
         [11] -> [3] -> [23] -> [7]      temp.next == null → stop

tail = pre;  tail.next = null;
         [11] -> [3] -> [23] -> null        return [7]
                         ▲
                        tail
```

```java
public Node removeLast() {
    if (length == 0) return null;

    Node temp = head;
    Node pre = head;
    while (temp.next != null) {
        pre = temp;
        temp = temp.next;
    }
    tail = pre;
    tail.next = null;
    length--;

    if (length == 0) {        // list had exactly one node
        head = null;
        tail = null;
    }
    return temp;
}
```

Edge cases: empty list → `null`; one node → `head` and `tail` both become `null`.
Pitfall: `list.removeLast().value` throws `NullPointerException` when the list is empty — check for `null` first.

### 3.8 prepend — O(1)

```
prepend(1)  on  2 -> 3

newNode [1]       head
                   ▼
                  [2] -> [3] -> null

newNode.next = head:
   [1] -> [2] -> [3] -> null
           ▲
          head

head = newNode:
   [1] -> [2] -> [3] -> null
    ▲
   head
```

```java
public void prepend(int value) {
    Node newNode = new Node(value);
    if (length == 0) {
        head = newNode;
        tail = newNode;
    } else {
        newNode.next = head;
        head = newNode;
    }
    length++;
}
```

This is the main advantage over `ArrayList`, where adding at index 0 is O(n).

### 3.9 removeFirst — O(1)

```
temp = head;  head = head.next;  temp.next = null;

before:  head                      after:          head
          ▼                                         ▼
         [2] -> [1] -> null          [2]   [1] -> null
                                      ▲
                                    temp (detached, returned)
```

```java
public Node removeFirst() {
    if (length == 0) return null;
    Node temp = head;
    head = head.next;
    temp.next = null;          // fully detach the removed node
    length--;
    if (length == 0) {
        tail = null;           // head is already null at this point
    }
    return temp;
}
```

| List state | Result                                     |
|------------|--------------------------------------------|
| several    | first node returned, `head` moves forward  |
| one node   | `head` and `tail` both become `null`       |
| empty      | `null`                                     |

### 3.10 get(index) — O(n)

```
get(2) on  0 -> 1 -> 2 -> 3

i=0  temp ─► [0]
i=1  temp ─►       [1]
     stop  ─►             [2]   ← returned (not removed)
```

```java
public Node get(int index) {
    if (index < 0 || index >= length) return null;
    Node temp = head;
    for (int i = 0; i < index; i++) {
        temp = temp.next;
    }
    return temp;
}
```

### 3.11 set(index, value) — O(n)

Reuse `get` (code reuse is appreciated in interviews).

```java
public boolean set(int index, int value) {
    Node temp = get(index);
    if (temp != null) {
        temp.value = value;
        return true;
    }
    return false;
}
```

```
set(1, 4):   11 -> 3 -> 23 -> 7   ⇒   11 -> 4 -> 23 -> 7
```

### 3.12 insert(index, value) — O(n)

```
insert(2, 99) on  0 -> 1 -> 2 -> 3

temp = get(index - 1) = node [1]

          temp
           ▼
  [0] -> [1] -> [2] -> [3]
                 ▲
       [99] ─────┘     1) newNode.next = temp.next

  [0] -> [1]    [2] -> [3]
          │      ▲
          ▼      │
         [99] ───┘     2) temp.next = newNode

result: 0 -> 1 -> 99 -> 2 -> 3
```

```java
public boolean insert(int index, int value) {
    if (index < 0 || index > length) return false;   // note: index == length is allowed
    if (index == 0) {
        prepend(value);
        return true;
    }
    if (index == length) {
        append(value);
        return true;
    }
    Node newNode = new Node(value);
    Node temp = get(index - 1);
    newNode.next = temp.next;    // 1. new node points to the rest of the list
    temp.next = newNode;         // 2. previous node points to the new node
    length++;
    return true;
}
```

Pitfall: doing step 2 before step 1 loses the rest of the list.

### 3.13 remove(index) — O(n)

```
remove(2) on  11 -> 3 -> 23 -> 7

prev = get(1) = [3]       temp = prev.next = [23]

          prev   temp
           ▼      ▼
  [11] -> [3] -> [23] -> [7]

prev.next = temp.next:
  [11] -> [3] ─────────► [7]
                 [23]
temp.next = null  → return [23]
```

```java
public Node remove(int index) {
    if (index < 0 || index >= length) return null;
    if (index == 0) return removeFirst();
    if (index == length - 1) return removeLast();

    Node prev = get(index - 1);
    Node temp = prev.next;
    prev.next = temp.next;
    temp.next = null;
    length--;
    return temp;
}
```

### 3.14 reverse — O(n), in place ★ (LeetCode 206)

Three pointers: `before`, `temp`, `after`.

```
start:   head                 tail
          ▼                    ▼
         [1] -> [2] -> [3] -> [4] -> null

swap head and tail first, then flip every arrow:

iteration 1:   before=null   temp=[1]   after=[2]
               null <- [1]    [2] -> [3] -> [4]

iteration 2:   before=[1]    temp=[2]   after=[3]
               null <- [1] <- [2]    [3] -> [4]

iteration 3:   before=[2]    temp=[3]   after=[4]
               null <- [1] <- [2] <- [3]    [4]

iteration 4:   before=[3]    temp=[4]   after=null
               null <- [1] <- [2] <- [3] <- [4]
                        ▲                     ▲
                       tail                  head
```

```java
public void reverse() {
    Node temp = head;
    head = tail;
    tail = temp;

    Node after;
    Node before = null;
    for (int i = 0; i < length; i++) {
        after = temp.next;     // 1. remember the rest of the list
        temp.next = before;    // 2. flip the arrow
        before = temp;         // 3. advance before
        temp = after;          // 4. advance temp
    }
}
```

Memorize the 4-line loop body — the order is essential. A version that does not rely on `length` (needed on LeetCode) uses `while (temp != null)`.

### 3.15 Quiz 2 — Linked List Big O (with answers) ➕

| Question                                   | Answer |
|--------------------------------------------|--------|
| Append to a linked list with a tail pointer| O(1)   |
| Remove last from a singly linked list      | O(n)   |
| Prepend / remove first                     | O(1)   |
| Look up by index                           | O(n)   |
| Look up by value                           | O(n)   |
| Insert in the middle                       | O(n) (walk) — the pointer change itself is O(1) |

> ➕ **Java note:** `java.util.LinkedList` is a **doubly** linked list, so its `removeLast()` is O(1). The O(n) `removeLast` applies to a *singly* linked list like the one built here.

---

## 4. ★ Linked List Interview Problems

Common techniques you will reuse:

```
1. Fast & slow pointers   slow moves 1, fast moves 2  → middle, cycle detection
2. Two pointers k apart   fast starts k steps ahead   → k-th from end
3. Dummy node             fake node before head       → no special case for head
4. Previous pointer       keep "the node before"      → deletions / rewiring
```

> Several of these exercises use a `LinkedList` **without `length` or `tail`** — that is intentional: the interviewer wants you to solve them in a single pass.

### 4.1 Find Middle Node (fast & slow pointers)

**Task:** return the middle node; for an even count, return the **second** middle. No `length` available.

```
Odd: 1 -> 2 -> 3 -> 4 -> 5
       start:   S,F at 1
       iter 1:  S=2, F=3
       iter 2:  S=3, F=5      F.next == null → stop → return 3

Even: 1 -> 2 -> 3 -> 4 -> null
       start:   S,F at 1
       iter 1:  S=2, F=3
       iter 2:  S=3, F=null   F == null → stop → return 3 (second middle of 2,3)

Even (6): 1 -> 2 -> 3 -> 4 -> 5 -> 6
       S: 1 → 2 → 3 → 4
       F: 1 → 3 → 5 → null    → return 4
```

When `fast` covers the full list, `slow` has covered half of it.

```java
public Node findMiddleNode() {
    Node slow = head;
    Node fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow;
}
```

Time O(n), space O(1). To return the **first** middle for even sizes, loop while `fast.next != null && fast.next.next != null`.

### 4.2 Has Loop (Floyd's cycle detection — tortoise & hare)

**Task:** return `true` if the list has a cycle. No extra data structures.

```
        head
         ▼
        [1] -> [2] -> [3] -> [4]
                       ▲      │
                       │      ▼
                      [6] <- [5]          (5 → 6 → 3 forms a loop)

step  slow  fast
 0     1     1
 1     2     3
 2     3     5
 3     4     3
 4     5     5     ← slow == fast → loop!
```

Inside a loop, the fast pointer gains one node per step on the slow pointer, so they must meet.

```java
public boolean hasLoop() {
    Node slow = head;
    Node fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) return true;    // compare references, not values!
    }
    return false;
}
```

**Why both `fast != null` and `fast.next != null`?** `fast.next.next` would throw `NullPointerException` if `fast` is the last node (even length) or `fast` is already `null` (odd length).

Time O(n), space O(1). The alternative (store visited nodes in a `HashSet<Node>`) is O(n) space.

> ➕ Follow-up (LeetCode 142): to find **where** the cycle starts, after they meet move one pointer back to `head`, then move both one step at a time — they meet at the cycle's start.

### 4.3 Find Kth Node From End (two pointers, k apart)

```
k = 2, list 1 -> 2 -> 3 -> 4 -> 5

1) move fast k = 2 steps:
          S       F
         [1] [2] [3] [4] [5]  null

2) move both one step until fast == null:
              S       F
         [1] [2] [3] [4] [5]  null
                  S       F
         [1] [2] [3] [4] [5]  null
                      S         F
         [1] [2] [3] [4] [5]  null     → fast is null → return 4

The gap between S and F is always k, so when F falls off the end, S is k from the end.
```

```java
public Node findKthFromEnd(int k) {
    Node slow = head;
    Node fast = head;
    for (int i = 0; i < k; i++) {
        if (fast == null) return null;   // fewer than k nodes
        fast = fast.next;
    }
    while (fast != null) {
        slow = slow.next;
        fast = fast.next;
    }
    return slow;
}
```

`findKthFromEnd(5)` → node 1, `findKthFromEnd(6)` → `null`. Time O(n), space O(1).

### 4.4 Remove Duplicates (keep first occurrence, preserve order)

```
input:   1 -> 2 -> 3 -> 1 -> 4 -> 2 -> 5
output:  1 -> 2 -> 3 -> 4 -> 5
```

**Approach A — HashSet, O(n) time, O(n) space (optimal):**

```
seen = {}
prev  curr
null  [1]   1 not seen → add, prev = 1
      [2]   add                         seen = {1,2}
      [3]   add                         seen = {1,2,3}
      [1]   seen! prev.next = curr.next → 3 -> 4
      [4]   add
      [2]   seen! skip
      [5]   add
```

```java
public void removeDuplicates() {
    Set<Integer> values = new HashSet<>();
    Node previous = null;
    Node current = head;
    while (current != null) {
        if (values.contains(current.value)) {
            previous.next = current.next;   // unlink current
            length--;
        } else {
            values.add(current.value);
            previous = current;             // only advance previous when we keep a node
        }
        current = current.next;
    }
}
```

`previous` is never `null` when a duplicate is found, because the first node can never be a duplicate.

**Approach B — no extra memory, O(n²) time, O(1) space (runner technique):**

```java
public void removeDuplicates() {
    Node current = head;
    while (current != null) {
        Node runner = current;
        while (runner.next != null) {
            if (runner.next.value == current.value) {
                runner.next = runner.next.next;   // skip the duplicate
                length--;
            } else {
                runner = runner.next;
            }
        }
        current = current.next;
    }
}
```

> If the list has a `tail`, remember to update it when the last node is removed.

### 4.5 Binary to Decimal

Each node holds a bit, head = most significant bit.

```
1 -> 0 -> 1         (binary 101)

num = 0
[1]  num = 0*2 + 1 = 1
[0]  num = 1*2 + 0 = 2
[1]  num = 2*2 + 1 = 5      → 5

Same idea in base 10: "123" → ((0*10+1)*10+2)*10+3 = 123
```

```java
public int binaryToDecimal() {
    int num = 0;
    Node current = head;
    while (current != null) {
        num = num * 2 + current.value;   // shift left, add new bit
        current = current.next;
    }
    return num;
}
```

`num * 2` is a left shift; you can also write `num = (num << 1) | current.value;`. Time O(n), space O(1).

### 4.6 Reverse Between (LeetCode 92 style, 0-indexed) — advanced

**Task:** reverse the nodes between `startIndex` and `endIndex` (inclusive) by rewiring nodes, not values.

```
reverseBetween(1, 3) on 1 -> 2 -> 3 -> 4 -> 5   ⇒   1 -> 4 -> 3 -> 2 -> 5
```

Idea: a dummy node, `previousNode` stays just before the sublist, `currentNode` is the first node of the sublist and moves backwards as we repeatedly pull the node after it to the front.

```
dummy -> 1 -> 2 -> 3 -> 4 -> 5
         ▲    ▲
       prev  curr

iteration 1: nodeToMove = 3
   curr.next = nodeToMove.next      2 -> 4
   nodeToMove.next = prev.next      3 -> 2
   prev.next = nodeToMove           1 -> 3
dummy -> 1 -> 3 -> 2 -> 4 -> 5
         ▲         ▲
       prev       curr

iteration 2: nodeToMove = 4
   2 -> 5 ;  4 -> 3 ;  1 -> 4
dummy -> 1 -> 4 -> 3 -> 2 -> 5      done (endIndex - startIndex = 2 iterations)
```

```java
public void reverseBetween(int startIndex, int endIndex) {
    if (head == null) return;

    Node dummyNode = new Node(0);
    dummyNode.next = head;
    Node previousNode = dummyNode;

    for (int i = 0; i < startIndex; i++) {
        previousNode = previousNode.next;
    }

    Node currentNode = previousNode.next;
    for (int i = 0; i < endIndex - startIndex; i++) {
        Node nodeToMove = currentNode.next;
        currentNode.next = nodeToMove.next;
        nodeToMove.next = previousNode.next;
        previousNode.next = nodeToMove;
    }
    head = dummyNode.next;     // head changes if startIndex == 0
}
```

Time O(n), space O(1). Draw it on paper — interviewers expect you to.

### 4.7 Swap Nodes in Pairs (LeetCode 24)

```
1 -> 2 -> 3 -> 4 -> 5   ⇒   2 -> 1 -> 4 -> 3 -> 5

dummy -> [1] -> [2] -> [3] -> ...
  prev   first second

previous.next = second;      dummy -> 2
first.next    = second.next; 1 -> 3
second.next   = first;       2 -> 1
                             dummy -> 2 -> 1 -> 3 -> ...
previous = first (node 1);  first = first.next (node 3)
```

```java
public void swapPairs() {
    Node dummy = new Node(0);
    dummy.next = head;
    Node previous = dummy;
    Node first = head;

    while (first != null && first.next != null) {
        Node second = first.next;

        previous.next = second;
        first.next = second.next;
        second.next = first;

        previous = first;
        first = first.next;
    }
    head = dummy.next;
}
```

Works for empty, single-node, even and odd lists. Time O(n), space O(1).

---

## 5. Doubly Linked List

### 5.1 Structure

Each node has **two** references: `next` and `prev`.

```
            head                                       tail
             ▼                                          ▼
          ┌──────┬────┬──────┐   ┌──────┬────┬──────┐   ┌──────┬────┬──────┐
 null ◄───┤ prev │ 1  │ next ├──►│ prev │ 2  │ next ├──►│ prev │ 3  │ next ├───► null
          │      │    │      │◄──┤      │    │      │◄──┤      │    │      │
          └──────┴────┴──────┘   └──────┴────┴──────┘   └──────┴────┴──────┘

short form:   null <- 1 <-> 2 <-> 3 -> null
```

| Feature                 | Singly linked list | Doubly linked list           |
|-------------------------|--------------------|------------------------------|
| Pointer to previous     | no                 | yes (`prev`)                 |
| Traverse backwards      | no                 | yes                          |
| removeLast              | O(n)               | **O(1)**                     |
| get(index)              | walk from head     | walk from the closer end     |
| Memory per node         | less               | more (extra reference)       |

### 5.2 Node, constructor and helpers

```java
public class DoublyLinkedList {

    private Node head;
    private Node tail;
    private int length;

    class Node {
        int value;
        Node next;
        Node prev;

        Node(int value) {
            this.value = value;
        }
    }

    public DoublyLinkedList(int value) {
        Node newNode = new Node(value);
        head = newNode;
        tail = newNode;
        length = 1;
    }

    public void printList() {
        Node temp = head;
        while (temp != null) {
            System.out.print(temp.value + (temp.next != null ? " <-> " : ""));
            temp = temp.next;
        }
        System.out.println();
    }

    public Node getHead()  { return head; }
    public Node getTail()  { return tail; }
    public int getLength() { return length; }
}
```

### 5.3 append — O(1)

```
append(3) on 1 <-> 2

tail.next = newNode;      1 <-> 2 -> 3
newNode.prev = tail;      1 <-> 2 <-> 3
tail = newNode;                       ▲ tail
```

```java
public void append(int value) {
    Node newNode = new Node(value);
    if (length == 0) {
        head = newNode;
        tail = newNode;
    } else {
        tail.next = newNode;
        newNode.prev = tail;
        tail = newNode;
    }
    length++;
}
```

### 5.4 removeLast — O(1) (the big win over a singly list)

```
before:  1 <-> 2 <-> 3          temp = tail (3)
                     ▲ tail

tail = tail.prev;        tail → 2
tail.next = null;        1 <-> 2 -> null
temp.prev = null;        3 is fully detached → return it
```

```java
public Node removeLast() {
    if (length == 0) return null;
    Node temp = tail;
    if (length == 1) {
        head = null;
        tail = null;
    } else {
        tail = tail.prev;
        tail.next = null;
        temp.prev = null;
    }
    length--;
    return temp;
}
```

### 5.5 prepend — O(1)

```
prepend(1) on 2 <-> 3

newNode.next = head;   1 -> 2 <-> 3
head.prev = newNode;   1 <-> 2 <-> 3
head = newNode;        ▲ head
```

```java
public void prepend(int value) {
    Node newNode = new Node(value);
    if (length == 0) {
        head = newNode;
        tail = newNode;
    } else {
        newNode.next = head;
        head.prev = newNode;
        head = newNode;
    }
    length++;
}
```

### 5.6 removeFirst — O(1)

```java
public Node removeFirst() {
    if (length == 0) return null;
    Node temp = head;
    if (length == 1) {
        head = null;
        tail = null;
    } else {
        head = head.next;
        head.prev = null;   // new head has no previous
        temp.next = null;   // detach removed node
    }
    length--;
    return temp;
}
```

### 5.7 get(index) — O(n), but walks from the closer end

```
length = 8, get(6)

index:   0    1    2    3  | 4    5    6    7
         ▲ head            |                ▲ tail
                    first half → from head
                    second half → from tail:  7 → 6  (1 step instead of 6)
```

```java
public Node get(int index) {
    if (index < 0 || index >= length) return null;
    Node temp;
    if (index < length / 2) {
        temp = head;
        for (int i = 0; i < index; i++) {
            temp = temp.next;
        }
    } else {
        temp = tail;
        for (int i = length - 1; i > index; i--) {
            temp = temp.prev;
        }
    }
    return temp;
}
```

Still O(n) (n/2 is O(n)), but about twice as fast in practice.

### 5.8 set(index, value) — same as singly list (reuses `get`)

```java
public boolean set(int index, int value) {
    Node temp = get(index);
    if (temp != null) {
        temp.value = value;
        return true;
    }
    return false;
}
```

### 5.9 insert(index, value) — O(n) — four pointer updates

```
insert(1, 2) on 1 <-> 3

 before = get(index - 1) = [1]      after = before.next = [3]

        before          after
          ▼               ▼
         [1] <────────► [3]
                [2]                 newNode

 newNode.prev = before;      [1] <- [2]
 newNode.next = after;              [2] -> [3]
 before.next  = newNode;     [1] -> [2]
 after.prev   = newNode;            [2] <- [3]

 result:  [1] <-> [2] <-> [3]
```

```java
public boolean insert(int index, int value) {
    if (index < 0 || index > length) return false;
    if (index == 0) {
        prepend(value);
        return true;
    }
    if (index == length) {
        append(value);
        return true;
    }
    Node newNode = new Node(value);
    Node before = get(index - 1);
    Node after = before.next;

    newNode.prev = before;
    newNode.next = after;
    before.next = newNode;
    after.prev = newNode;

    length++;
    return true;
}
```

### 5.10 remove(index) — O(n) — no "previous" lookup needed

```
remove(1) on 0 <-> 1 <-> 2          temp = get(1)

        temp.prev   temp   temp.next
            ▼        ▼        ▼
           [0] <->  [1]  <-> [2]

 temp.next.prev = temp.prev;    [0] <------- [2]
 temp.prev.next = temp.next;    [0] -------> [2]
 temp.next = null; temp.prev = null;   → return [1]
```

```java
public Node remove(int index) {
    if (index < 0 || index >= length) return null;
    if (index == 0) return removeFirst();
    if (index == length - 1) return removeLast();

    Node temp = get(index);
    temp.next.prev = temp.prev;
    temp.prev.next = temp.next;
    temp.next = null;
    temp.prev = null;

    length--;
    return temp;
}
```

---

## 6. ★ Doubly Linked List Interview Problems

### 6.1 Swap First and Last (values)

```
1 <-> 2 <-> 3 <-> 4 <-> 5   ⇒   5 <-> 2 <-> 3 <-> 4 <-> 1
(nodes stay in place; only values change)
```

```java
public void swapFirstLast() {
    if (length < 2) return;
    int temp = head.value;
    head.value = tail.value;
    tail.value = temp;
}
```

O(1) time, O(1) space.

### 6.2 Reverse a DLL (swap `next` and `prev` in every node)

```
before:  null <- 1 <-> 2 <-> 3 -> null        head=1, tail=3

for each node: swap(prev, next)
  node 1: prev=2, next=null
  node 2: prev=3, next=1
  node 3: prev=null, next=2

finally swap head and tail:
after:   null <- 3 <-> 2 <-> 1 -> null        head=3, tail=1
```

```java
public void reverse() {
    Node current = head;
    Node temp = null;
    while (current != null) {
        temp = current.prev;
        current.prev = current.next;
        current.next = temp;
        current = current.prev;   // prev now holds the ORIGINAL next
    }
    temp = head;
    head = tail;
    tail = temp;
}
```

O(n) time, O(1) space.

### 6.3 Palindrome Checker

```
[1, 2, 3, 2, 1]

 forward ─►                    ◄─ backward
    1      2      3      2      1
    ▲                           ▲      1 == 1 ✓
           ▲             ▲             2 == 2 ✓
                  (middle, stop after length/2 comparisons) → true
```

```java
public boolean isPalindrome() {
    if (length <= 1) return true;
    Node forwardNode = head;
    Node backwardNode = tail;
    for (int i = 0; i < length / 2; i++) {
        if (forwardNode.value != backwardNode.value) return false;
        forwardNode = forwardNode.next;
        backwardNode = backwardNode.prev;
    }
    return true;
}
```

O(n) time, O(1) space. ➕ For a **singly** linked list (LeetCode 234): find the middle (fast/slow), reverse the second half, compare, (optionally restore).

### 6.4 Swap Nodes in Pairs (DLL, no tail) — advanced

**Task:** `1 <-> 2 <-> 3 <-> 4` ⇒ `2 <-> 1 <-> 4 <-> 3`, by changing pointers only.

```
dummy <-> 1 <-> 2 <-> 3 <-> 4
 prev   first second

previousNode.next = secondNode;    dummy -> 2
firstNode.next = secondNode.next;  1 -> 3
secondNode.next = firstNode;       2 -> 1
secondNode.prev = previousNode;    dummy <- 2
firstNode.prev = secondNode;       2 <- 1
if (firstNode.next != null)
    firstNode.next.prev = firstNode;   1 <- 3

dummy <-> 2 <-> 1 <-> 3 <-> 4
                ▲     ▲
              prev   head (next pair)
```

```java
public void swapPairs() {
    Node dummyNode = new Node(0);
    dummyNode.next = head;
    Node previousNode = dummyNode;

    while (head != null && head.next != null) {
        Node firstNode = head;
        Node secondNode = head.next;

        // forward links
        previousNode.next = secondNode;
        firstNode.next = secondNode.next;
        secondNode.next = firstNode;

        // backward links
        secondNode.prev = previousNode;
        firstNode.prev = secondNode;
        if (firstNode.next != null) {
            firstNode.next.prev = firstNode;
        }

        head = firstNode.next;      // move to the next pair
        previousNode = firstNode;
    }
    head = dummyNode.next;
    if (head != null) head.prev = null;   // remove the link to dummy
}
```

O(n) time, O(1) space. Using `head` as the moving pointer is fine because it is reset from `dummyNode.next` at the end.

---

## 7. Stacks and Queues

### 7.1 Stack — LIFO (Last In, First Out)

Think of a can of tennis balls: you can only take the ball you put in last.

```
    push(4)        push(7)        pop() → 7       peek() → 4
                   ┌─────┐
                   │  7  │ ◄ top
    ┌─────┐        ├─────┤        ┌─────┐         ┌─────┐
    │  4  │ ◄ top  │  4  │        │  4  │ ◄ top   │  4  │ ◄ top
    └─────┘        └─────┘        └─────┘         └─────┘
```

Real-life uses: browser **Back** button, **undo** in editors, the **call stack** in recursion, matching brackets, DFS.

```
Browser history:  Facebook → YouTube → Instagram → Email
                    ┌───────────┐
                    │  Email    │ ◄ top   (Back = pop)
                    │  Instagram│
                    │  YouTube  │
                    │  Facebook │
                    └───────────┘
```

**Which end should be the top?**

```
ArrayList:   use the END            [a][b][c][d] ◄── push/pop here: O(1)
             (index 0 would be O(n) — re-indexing)

LinkedList:  use the HEAD           top=head ─► [d] -> [c] -> [b] -> [a] -> null
             push = prepend O(1), pop = removeFirst O(1)
             (using the tail would make pop = removeLast = O(n))
```

| LinkedList name | Stack name |
|-----------------|------------|
| head            | top        |
| tail            | (not used) |
| length          | height     |
| prepend()       | push()     |
| removeFirst()   | pop()      |

### 7.2 Stack implemented with a linked list

```java
public class Stack {

    private Node top;
    private int height;

    class Node {
        int value;
        Node next;
        Node(int value) { this.value = value; }
    }

    public Stack(int value) {
        Node newNode = new Node(value);
        top = newNode;
        height = 1;
    }

    public Node getTop()   { return top; }
    public int getHeight() { return height; }

    public void printStack() {        // prints from top to bottom
        Node temp = top;
        while (temp != null) {
            System.out.println(temp.value);
            temp = temp.next;
        }
    }

    // push = prepend — O(1)
    public void push(int value) {
        Node newNode = new Node(value);
        if (height == 0) {
            top = newNode;
        } else {
            newNode.next = top;
            top = newNode;
        }
        height++;
    }

    // pop = removeFirst — O(1)
    public Node pop() {
        if (height == 0) return null;
        Node temp = top;
        top = top.next;
        temp.next = null;
        height--;
        return temp;
    }
}
```

```
push(1) on [2]:            pop() on 7 → 23 → 3 → 11:

  top ─► [1]               temp=top=[7]; top=top.next
          │                  top ─► [23] → [3] → [11]
          ▼                  [7] detached and returned
         [2]
```

### 7.3 Queue — FIFO (First In, First Out)

Like a line at a shop: join at the back, leave from the front.

```
                 dequeue ◄──  [ A | B | C | D ]  ◄── enqueue
                            first           last
```

**Which end should be which?**

| Implementation | enqueue at end | dequeue at front | Result |
|----------------|----------------|------------------|--------|
| ArrayList      | O(1)           | O(n) (shift)     | one side is always O(n) |
| LinkedList     | O(1) (`append`)| O(1) (`removeFirst`) | ✅ both O(1) |

With a linked list: **enqueue at the tail (`last`), dequeue at the head (`first`)**. Dequeuing from the tail would need `removeLast` = O(n).

```
 first                         last
   ▼                            ▼
  [1] -> [2] -> [3] -> [4] -> null
   ▲                            ▲
 dequeue here                enqueue here
```

### 7.4 Queue implemented with a linked list

```java
public class Queue {

    private Node first;
    private Node last;
    private int length;

    class Node {
        int value;
        Node next;
        Node(int value) { this.value = value; }
    }

    public Queue(int value) {
        Node newNode = new Node(value);
        first = newNode;
        last = newNode;
        length = 1;
    }

    public void printQueue() {
        Node temp = first;
        while (temp != null) {
            System.out.print(temp.value + " -> ");
            temp = temp.next;
        }
        System.out.println("null");
    }

    // enqueue = append — O(1)
    public void enqueue(int value) {
        Node newNode = new Node(value);
        if (length == 0) {
            first = newNode;
            last = newNode;
        } else {
            last.next = newNode;
            last = newNode;
        }
        length++;
    }

    // dequeue = removeFirst — O(1)
    public Node dequeue() {
        if (length == 0) return null;
        Node temp = first;
        if (length == 1) {
            first = null;
            last = null;
        } else {
            first = first.next;
            temp.next = null;
        }
        length--;
        return temp;
    }
}
```

```
Queue q = new Queue(2); q.enqueue(1);   →  2 -> 1 -> null
q.dequeue() → 2,  q.dequeue() → 1,  q.dequeue() → null
```

> The original notes used a generic `Queue<T>` whose public `dequeue()` returned a `private` nested `Node<T>` type — that leaks an inaccessible type through a public API. The version above keeps `int` values to match the rest of the course. In production you would return `T` (the value), not the node.

### 7.5 Stacks & Queues Big O ➕

| Operation            | Stack (LL head) | Stack (ArrayList end) | Queue (LL)  |
|----------------------|-----------------|-----------------------|-------------|
| push / enqueue       | O(1)            | O(1) amortized        | O(1)        |
| pop / dequeue        | O(1)            | O(1)                  | O(1)        |
| peek                 | O(1)            | O(1)                  | O(1)        |
| search               | O(n)            | O(n)                  | O(n)        |

### 7.6 In real Java code ➕

```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1); stack.push(2);
stack.peek();   // 2
stack.pop();    // 2

Queue<Integer> queue = new ArrayDeque<>();
queue.offer(1); queue.offer(2);
queue.peek();   // 1
queue.poll();   // 1  (returns null when empty; remove() throws)
```

`java.util.Stack` still works but is legacy (extends `Vector`, every method synchronized). `ArrayDeque` is the recommended stack *and* queue. Note: `ArrayDeque` does not accept `null` elements.

---

## 8. ★ Stack & Queue Interview Problems

These exercises use a generic **ArrayList-backed stack**:

```java
import java.util.ArrayList;

public class Stack<T> {
    private ArrayList<T> stackList = new ArrayList<>();

    public ArrayList<T> getStackList() { return stackList; }
    public boolean isEmpty()           { return stackList.size() == 0; }
    public int size()                  { return stackList.size(); }

    public T peek() {
        if (isEmpty()) return null;
        return stackList.get(stackList.size() - 1);
    }

    public void printStack() {               // top first
        for (int i = stackList.size() - 1; i >= 0; i--) {
            System.out.println(stackList.get(i));
        }
    }

    public void push(T value) { /* 8.1 */ }
    public T pop()            { /* 8.2 */ return null; }
}
```

The generic parameter `<T>` lets the same class hold `Integer`, `Character`, `String`, …

### 8.1 Push (ArrayList stack)

```java
public void push(T value) {
    stackList.add(value);          // the end of the list is the top — O(1) amortized
}
```

### 8.2 Pop (ArrayList stack)

```java
public T pop() {
    if (isEmpty()) return null;
    return stackList.remove(stackList.size() - 1);   // remove from the end — O(1)
}
```

```
stackList: [ 3 | 8 | 5 ]        pop() → 5        [ 3 | 8 ]
                     ▲ top                              ▲ top
```

### 8.3 Reverse a String with a stack

```
"hello"
push each char:   ┌───┐
                  │ o │ ◄ top
                  │ l │
                  │ l │
                  │ e │
                  │ h │
                  └───┘
pop all: o, l, l, e, h  → "olleh"
```

```java
public static String reverseString(String string) {
    Stack<Character> stack = new Stack<>();
    for (char c : string.toCharArray()) {
        stack.push(c);
    }
    StringBuilder reversed = new StringBuilder();     // ➕ improvement
    while (!stack.isEmpty()) {
        reversed.append(stack.pop());
    }
    return reversed.toString();
}
```

> ➕ Improvement over the original: the original used `reversedString += stack.pop()` in a loop. Strings are immutable, so each `+=` creates a new String → O(n²) total. `StringBuilder` makes it O(n). (In real code: `new StringBuilder(s).reverse().toString()`.)

The method is `static` because it is called directly from `main` without creating an object of the enclosing class.

### 8.4 Balanced Parentheses

**Task:** `"((()))"` → true, `"(()))"` → false, `")("` → false.

```
input: ( ( ) ( ) )

char   action             stack (top on right)
 (     push               (
 (     push               ( (
 )     pop matches '('    (
 (     push               ( (
 )     pop                (
 )     pop                (empty)
end:   stack empty → BALANCED

input: ( ) )
 (     push               (
 )     pop                (empty)
 )     stack empty → return false immediately
```

```java
public static boolean isBalancedParentheses(String parentheses) {
    Stack<Character> stack = new Stack<>();
    for (char p : parentheses.toCharArray()) {
        if (p == '(') {
            stack.push(p);
        } else if (p == ')') {
            if (stack.isEmpty() || stack.pop() != '(') {
                return false;
            }
        }
    }
    return stack.isEmpty();     // leftover '(' means unbalanced
}
```

➕ **Common follow-up (LeetCode 20 — Valid Parentheses):** support `()[]{}`.

```java
public static boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    for (char c : s.toCharArray()) {
        switch (c) {
            case '(' -> stack.push(')');      // push the EXPECTED closer
            case '[' -> stack.push(']');
            case '{' -> stack.push('}');
            default  -> {
                if (stack.isEmpty() || stack.pop() != c) return false;
            }
        }
    }
    return stack.isEmpty();
}
```

Time O(n), space O(n).

### 8.5 Sort a Stack (using one extra stack)

**Task:** sort so the **smallest value is on top**, using only `push`, `pop`, `peek`, `isEmpty` and one additional stack.

Idea: keep `additionalStack` sorted (largest on top). For each popped `temp`, move larger elements back to the original stack until `temp` fits.

```
stack (bottom→top): [3, 1, 4, 2]          additional: []

pop 2 → additional empty            → push 2        stack [3,1,4]   add [2]
pop 4 → peek 2 > 4? no              → push 4        stack [3,1]     add [2,4]
pop 1 → peek 4 > 1 → move 4 back    stack [3,4]
        peek 2 > 1 → move 2 back    stack [3,4,2]
        push 1                                      stack [3,4,2]   add [1]
pop 2 → peek 1 > 2? no              → push 2        stack [3,4]     add [1,2]
pop 4 → push                                        stack [3]       add [1,2,4]
pop 3 → peek 4 > 3 → move 4 back    stack [4]
        peek 2 > 3? no → push 3                     stack [4]       add [1,2,3]
pop 4 → peek 3 > 4? no → push 4                     stack []        add [1,2,3,4]

move everything back:  pop 4,3,2,1 from add → push onto stack
stack (bottom→top): [4, 3, 2, 1]   → smallest (1) on top ✓
```

```java
public static void sortStack(Stack<Integer> stack) {
    Stack<Integer> additionalStack = new Stack<>();

    while (!stack.isEmpty()) {
        int temp = stack.pop();
        while (!additionalStack.isEmpty() && additionalStack.peek() > temp) {
            stack.push(additionalStack.pop());
        }
        additionalStack.push(temp);
    }

    while (!additionalStack.isEmpty()) {
        stack.push(additionalStack.pop());
    }
}
```

Time O(n²), space O(n) for the extra stack.

> Correction: the original walkthrough stopped with `4` still in the main stack. In reality the outer `while` keeps running until the main stack is empty (see the last `pop 4` line above).

### 8.6 Queue Using Two Stacks — enqueue (O(n)) / dequeue (O(1))

Course approach: keep `stack1` ordered so its **top is the front of the queue**.

```
enqueue(3) when queue = [1, 2]   (stack1 top = 1)

stack1        stack2                 stack1        stack2       stack1
 ┌───┐                                                            ┌───┐
 │ 1 │ top   move all ─►   ┌───┐     push 3 ─►      ┌───┐  back ─► │ 1 │ top
 │ 2 │                     │ 2 │ top   ┌───┐        │ 2 │          │ 2 │
 └───┘                     │ 1 │       │ 3 │        │ 1 │          │ 3 │
                           └───┘       └───┘        └───┘          └───┘
                                                                  front = top = 1 ✓
```

```java
public class MyQueue {
    private Stack<Integer> stack1 = new Stack<>();
    private Stack<Integer> stack2 = new Stack<>();

    public void enqueue(int value) {            // O(n)
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
        stack1.push(value);
        while (!stack2.isEmpty()) {
            stack1.push(stack2.pop());
        }
    }

    public Integer dequeue() {                  // O(1)
        if (isEmpty()) return null;
        return stack1.pop();
    }

    public Integer peek()   { return stack1.peek(); }
    public boolean isEmpty() { return stack1.isEmpty(); }
}
```

➕ **Better answer (LeetCode 232): amortized O(1) for both operations.** Push onto an `in` stack; pop from an `out` stack, refilling `out` from `in` only when `out` is empty. Each element is moved at most once.

```
enqueue 1,2,3:   in: [1,2,3]   out: []
dequeue:         out empty → move all → in: []  out: [3,2,1] (top=1) → pop 1
enqueue 4:       in: [4]       out: [3,2]
dequeue:         out not empty → pop 2
```

```java
public class MyQueueAmortized {
    private final Deque<Integer> in = new ArrayDeque<>();
    private final Deque<Integer> out = new ArrayDeque<>();

    public void enqueue(int value) { in.push(value); }          // O(1)

    public Integer dequeue() {                                   // amortized O(1)
        if (out.isEmpty()) {
            while (!in.isEmpty()) out.push(in.pop());
        }
        return out.poll();                                       // null if empty
    }
}
```

---

## 9. Trees and Binary Search Trees

### 9.1 Terminology

```
                 ┌──────┐
     root ──►    │  47  │                ◄ level 0
                 └──┬───┘
            ┌───────┴────────┐
         ┌──┴───┐         ┌──┴───┐
         │  21  │         │  76  │       ◄ level 1   (21 and 76 are siblings)
         └──┬───┘         └──┬───┘
        ┌───┴───┐        ┌───┴───┐
      ┌─┴──┐  ┌─┴──┐   ┌─┴──┐  ┌─┴──┐
      │ 18 │  │ 27 │   │ 52 │  │ 82 │    ◄ level 2   (leaves: no children)
      └────┘  └────┘   └────┘  └────┘

  parent of 18 = 21        children of 76 = 52, 82
  height of tree = 2 (edges on the longest root→leaf path)
```

- **Binary tree:** each node has at most two children: `left` and `right`.
- **Every node has exactly one parent** (except the root). If a node has two parents, it is a graph, not a tree.
- A **linked list** is a tree that never forks.

```
Linked List  ⊂  Tree  ⊂  Graph
```

**Full, Perfect, Complete:**

```
FULL                     PERFECT                  COMPLETE
(0 or 2 children each)   (all levels full)        (all levels full except maybe the
                                                   last, filled LEFT to RIGHT)
       1                        1                        1
      / \                     /   \                    /   \
     2   3                   2     3                  2     3
        / \                 / \   / \                / \   /
       4   5               4   5 6   7              4   5 6

 full: yes                full + complete           complete: yes
 perfect: no              + perfect                 full: no (3 has 1 child)
```

> A **heap** must be a *complete* tree — that is what allows it to be stored in an array (section 13).

### 9.2 Binary Search Tree (BST) rule

For **every** node: everything in the left subtree is **smaller**, everything in the right subtree is **larger**.

Insert `47, 76, 52, 21, 82, 18, 27`:

```
47                 → root
76 > 47            → right of 47
52 > 47, 52 < 76   → left of 76
21 < 47            → left of 47
82 > 47, 82 > 76   → right of 76
18 < 47, 18 < 21   → left of 21
27 < 47, 27 > 21   → right of 21

             47
           /    \
         21      76
        /  \    /  \
      18   27  52   82
```

### 9.3 BST Big O

```
BALANCED (n = 7, height ≈ log₂ n)        DEGENERATE (insert sorted data 1..5)
             47                           1
           /    \                          \
         21      76                         2
        /  \    /  \                         \
      18   27  52   82                        3
                                               \
 each step discards half → O(log n)             4
                                                 \
                                                  5      ← it's a linked list → O(n)
```

| Operation | BST (balanced) | BST (worst) | Linked List |
|-----------|----------------|-------------|-------------|
| lookup    | O(log n)       | O(n)        | O(n)        |
| insert    | O(log n)       | O(n)        | O(1)        |
| remove    | O(log n)       | O(n)        | O(n)        |

> Strictly, the BST worst case is O(n). Self-balancing trees (AVL, **red-black** — used by `TreeMap`/`TreeSet`) guarantee O(log n).

### 9.4 Constructor

A BST can start empty — no constructor needed; `root` defaults to `null`.

```java
public class BinarySearchTree {

    Node root;            // null → empty tree

    class Node {
        int value;
        Node left;
        Node right;

        Node(int value) {
            this.value = value;
        }
    }
}
```

### 9.5 Insert (iterative)

```
insert(27) into:      47                 compare 27 with 47 → smaller → go left
                    /    \               compare 27 with 21 → larger  → go right
                  21      76             right of 21 is null → insert here
                 /          \
               18            82                     47
                                                  /    \
                                                21      76
                                               /  \       \
                                             18   [27]     82
```

```java
public boolean insert(int value) {
    Node newNode = new Node(value);
    if (root == null) {
        root = newNode;
        return true;
    }
    Node temp = root;
    while (true) {
        if (newNode.value == temp.value) return false;   // no duplicates
        if (newNode.value < temp.value) {
            if (temp.left == null) {
                temp.left = newNode;
                return true;
            }
            temp = temp.left;
        } else {
            if (temp.right == null) {
                temp.right = newNode;
                return true;
            }
            temp = temp.right;
        }
    }
}
```

### 9.6 Contains (iterative)

```
contains(27):  47 → (27<47) left → 21 → (27>21) right → 27 ✓ true
contains(17):  47 → left → 21 → left → 18 → left → null ✗ false
```

```java
public boolean contains(int value) {
    Node temp = root;
    while (temp != null) {
        if (value < temp.value) {
            temp = temp.left;
        } else if (value > temp.value) {
            temp = temp.right;
        } else {
            return true;
        }
    }
    return false;
}
```

> Almost every BST interview question (delete, validate, invert, convert sorted array, k-th smallest) uses **recursion** or **traversal** — see sections 14–17.

---

## 10. Hash Tables

### 10.1 Concept

A hash table stores **key → value** pairs in an array. A **hash function** converts the key into an array index.

```
          hash("nails")  = 2
          hash("screws") = 6
          hash("nuts")   = 2   ← collision with "nails"

 index
   0  │  null
   1  │  null
   2  │  ["nails", 1000] ──► ["nuts", 200] ──► null     (separate chaining)
   3  │  null
   4  │  ["bolts", 750]
   5  │  null
   6  │  ["screws", 500]
```

Properties of a hash function:
- **One-way:** key → index, but you cannot get the key back from the index.
- **Deterministic:** the same key always produces the same index.
- **Fast** and spreads keys evenly.

### 10.2 Collisions

**Separate chaining (used in this course and in Java's `HashMap`):** each slot holds a linked list.

```
2 │ [nails, 1000] → [nuts, 200] → [hooks, 150] → null
```
+ simple, handles many collisions  − extra memory for nodes

**Linear probing (open addressing):** if the slot is taken, try the next one.

```
insert nails → 2 ✔
insert nuts  → 2 ✗ → 3 ✔
insert hooks → 2 ✗ → 3 ✗ → 4 ✔

 2 │ nails   3 │ nuts   4 │ hooks
```
+ everything stays in the array  − clustering slows access

> Using a **prime number** for the array size (and in the hash formula) spreads keys more evenly and reduces collisions.

### 10.3 Implementation (course style) ➕ *(sections 69–73 were empty in the original)*

```java
import java.util.ArrayList;

public class HashTable {

    private int size = 7;           // prime size
    private Node[] dataMap;

    class Node {
        String key;
        int value;
        Node next;

        Node(String key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    public HashTable() {
        dataMap = new Node[size];
    }

    // 70. hash — sum of (ASCII value × prime), kept inside the array bounds
    private int hash(String key) {
        int hash = 0;
        char[] keyChars = key.toCharArray();
        for (int i = 0; i < keyChars.length; i++) {
            int asciiValue = keyChars[i];
            hash = (hash + asciiValue * 23) % dataMap.length;
        }
        return hash;                 // always 0 .. dataMap.length - 1
    }

    // 71. set — append to the chain (➕ updates the value if the key already exists)
    public void set(String key, int value) {
        int index = hash(key);
        Node newNode = new Node(key, value);
        if (dataMap[index] == null) {
            dataMap[index] = newNode;
            return;
        }
        Node temp = dataMap[index];
        while (true) {
            if (temp.key.equals(key)) {    // compare Strings with equals(), never ==
                temp.value = value;
                return;
            }
            if (temp.next == null) break;
            temp = temp.next;
        }
        temp.next = newNode;
    }

    // 72. get — walk the chain at the hashed index
    public Integer get(String key) {
        int index = hash(key);
        Node temp = dataMap[index];
        while (temp != null) {
            if (temp.key.equals(key)) return temp.value;
            temp = temp.next;
        }
        return null;                 // course version returns 0
    }

    // 73. keys — collect every key from every chain
    public ArrayList<String> keys() {
        ArrayList<String> allKeys = new ArrayList<>();
        for (Node head : dataMap) {
            Node temp = head;
            while (temp != null) {
                allKeys.add(temp.key);
                temp = temp.next;
            }
        }
        return allKeys;
    }

    public void printTable() {
        for (int i = 0; i < dataMap.length; i++) {
            System.out.print(i + ":");
            Node temp = dataMap[i];
            while (temp != null) {
                System.out.print("  {" + temp.key + "=" + temp.value + "}");
                temp = temp.next;
            }
            System.out.println();
        }
    }
}
```

```
set("nails", 100); set("tile", 50); set("lumber", 80); set("bolts", 200); set("screws", 140);

hash("nails") = 6   hash("tile") = 6   hash("lumber") = 6   hash("bolts") = 4   hash("screws") = 3

printTable():
 0:
 1:
 2:
 3:  {screws=140}
 4:  {bolts=200}
 5:
 6:  {nails=100}  {tile=50}  {lumber=80}      ← three keys collide → one chain

get("lumber") → index 6 → walk nails → tile → lumber → 80
keys()        → [screws, bolts, nails, tile, lumber]
```

### 10.4 Hash table Big O (74)

| Operation          | Average | Worst (all keys collide) |
|--------------------|---------|--------------------------|
| set / put          | O(1)    | O(n)                     |
| get                | O(1)    | O(n)                     |
| remove             | O(1)    | O(n)                     |
| keys()             | O(n)    | O(n)                     |

The hash function itself is O(1) with respect to n (it depends on key length, not on how many entries exist). Hash tables are **not ordered** — for sorted keys use `TreeMap` (O(log n)).

### 10.5 How Java's `HashMap` really works ➕ (frequent interview question)

```
HashMap<String,Integer>  (default capacity 16, load factor 0.75)

key ──► key.hashCode() ──► spread (h ^ (h >>> 16)) ──► index = hash & (capacity - 1)

 bucket[ 0] → null
 bucket[ 5] → Node(k1) → Node(k2) → null            linked list
 bucket[ 9] → TreeNode (red-black tree)             when a chain grows beyond 8
 ...                                                 (and capacity ≥ 64)
 resize: when size > capacity × 0.75 → capacity doubles, entries rehashed
```

- Keys must implement **`equals()` and `hashCode()` consistently**: equal objects ⇒ same hash code. Break this and `get` won't find your key.
- Mutable keys are dangerous — changing a key after insertion changes its hash.
- `HashMap` allows one `null` key; `Hashtable` (legacy, synchronized) allows none. For concurrency use `ConcurrentHashMap`.
- `LinkedHashMap` keeps insertion order (useful for an LRU cache).

### 10.6 75. HT interview question — Item in Common: O(n²) vs O(n) ➕

```
array1 = [1, 3, 5]      array2 = [2, 4, 5]

Naive: compare every pair (nested loops) → O(n²)
   1-2 1-4 1-5 3-2 3-4 3-5 5-2 5-4 5-5 ✓

Hash: put array1 into a set   {1, 3, 5}
      check each of array2:    2? no   4? no   5? yes ✓  → O(n + m)
```

This trade-off (extra O(n) memory for a big time win) is the core idea behind most hash-table interview questions.

---

## 11. ★ Hash Table & Set Interview Problems

### 11.1 Item In Common

```java
public static boolean itemInCommon(int[] array1, int[] array2) {
    Set<Integer> seen = new HashSet<>();      // original used HashMap<Integer, Boolean>
    for (int i : array1) seen.add(i);
    for (int j : array2) {
        if (seen.contains(j)) return true;
    }
    return false;
}
```

Time O(n + m), space O(n).

### 11.2 Find Duplicates

```
nums = [4, 3, 2, 7, 8, 2, 3, 1]

counts: {4:1, 3:2, 2:2, 7:1, 8:1, 1:1}  → keys with count > 1 → [2, 3]
```

```java
public static List<Integer> findDuplicates(int[] nums) {
    Map<Integer, Integer> numCounts = new HashMap<>();
    for (int num : nums) {
        numCounts.put(num, numCounts.getOrDefault(num, 0) + 1);
        // ➕ modern alternative: numCounts.merge(num, 1, Integer::sum);
    }
    List<Integer> duplicates = new ArrayList<>();
    for (Map.Entry<Integer, Integer> entry : numCounts.entrySet()) {
        if (entry.getValue() > 1) duplicates.add(entry.getKey());
    }
    return duplicates;
}
```

Time O(n), space O(n). Note: `HashMap` iteration order is not guaranteed, so `[3, 2]` is also a valid output.

### 11.3 First Non-Repeating Character

```
"leetcode"
pass 1 (count):  l:1 e:3 t:1 c:1 o:1 d:1
pass 2 (scan string in order):  'l' count 1 → return 'l'

"hello" → 'h'        "aabb" → null
```

```java
public static Character firstNonRepeatingChar(String string) {
    Map<Character, Integer> charCounts = new HashMap<>();
    for (int i = 0; i < string.length(); i++) {
        char c = string.charAt(i);
        charCounts.put(c, charCounts.getOrDefault(c, 0) + 1);
    }
    for (int i = 0; i < string.length(); i++) {     // second pass over the STRING keeps order
        char c = string.charAt(i);
        if (charCounts.get(c) == 1) return c;
    }
    return null;
}
```

Time O(n), space O(1) for a fixed alphabet (at most 26 keys), O(k) in general. ➕ For lowercase letters only, an `int[26]` array is faster than a map.

### 11.4 Group Anagrams

```
["eat","tea","tan","ate","nat","bat"]

 word   sorted (canonical key)
 eat →  aet ┐
 tea →  aet ├─► "aet": [eat, tea, ate]
 ate →  aet ┘
 tan →  ant ┐
 nat →  ant ┴─► "ant": [tan, nat]
 bat →  abt ──► "abt": [bat]
```

```java
public static List<List<String>> groupAnagrams(String[] strings) {
    Map<String, List<String>> anagramGroups = new HashMap<>();
    for (String string : strings) {
        char[] chars = string.toCharArray();
        Arrays.sort(chars);
        String canonical = new String(chars);
        anagramGroups.computeIfAbsent(canonical, k -> new ArrayList<>()).add(string);
    }
    return new ArrayList<>(anagramGroups.values());
}
```

Time O(n · k log k) where k = max word length; space O(n · k). `computeIfAbsent` replaces the original `containsKey / get / put` block.

### 11.5 Two Sum (LeetCode 1)

```
nums = [2, 7, 11, 15], target = 9

 i   num   complement = 9 - num   map before         action
 0    2         7                 {}                 store 2→0
 1    7         2                 {2:0}              2 found! → [0, 1]
```

```java
public static int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> numMap = new HashMap<>();   // value → index
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (numMap.containsKey(complement)) {
            return new int[]{numMap.get(complement), i};
        }
        numMap.put(nums[i], i);     // put AFTER checking → never pairs a number with itself
    }
    return new int[]{};
}
```

Time O(n), space O(n). `[3, 3]`, target 6 → `[0, 1]` works because we check before inserting.

### 11.6 Subarray Sum (prefix sums + hash map)

**Task:** return `[start, end]` of a contiguous subarray whose sum is `target`. Works with negative numbers.

Key insight: if `prefix[j] - prefix[i] == target`, then the elements **after i up to j** sum to target.

```
nums   =   [ 1,  2,  3,  4,  5 ]     target = 9
index  :     0   1   2   3   4

running sum (currentSum) and what we look for (currentSum - target):

 i  num  currentSum  need = currentSum - 9   map (sum → index)          found?
 -   -       0              -                {0:-1}                      (seed)
 0   1       1             -8                {0:-1, 1:0}                 no
 1   2       3             -6                {.., 3:1}                   no
 2   3       6             -3                {.., 6:2}                   no
 3   4      10              1                1 is in map at index 0  ✓

 start = 0 + 1 = 1, end = 3   →  nums[1..3] = 2 + 3 + 4 = 9
```

```java
public static int[] subarraySum(int[] nums, int target) {
    Map<Integer, Integer> sumIndex = new HashMap<>();
    sumIndex.put(0, -1);                // empty prefix: lets a subarray start at index 0
    int currentSum = 0;
    for (int i = 0; i < nums.length; i++) {
        currentSum += nums[i];
        if (sumIndex.containsKey(currentSum - target)) {
            return new int[]{sumIndex.get(currentSum - target) + 1, i};
        }
        sumIndex.put(currentSum, i);
    }
    return new int[]{};
}
```

Time O(n), space O(n). ➕ Related: LeetCode 560 (count subarrays with sum k) uses the same idea with a `sum → count` map.

### 11.7 Sets — quick reference

A `Set` stores **unique** elements (keys only, no values).

```java
Set<Integer> mySet = new HashSet<>(List.of(1, 2, 3, 4, 5));
Set<Integer> other = new HashSet<>(List.of(3, 4, 5, 6));

mySet.add(6);         // ignored if already present (returns false)
mySet.remove(3);
mySet.contains(4);    // O(1) average

Set<Integer> union = new HashSet<>(mySet);        union.addAll(other);
Set<Integer> intersection = new HashSet<>(mySet); intersection.retainAll(other);
Set<Integer> difference = new HashSet<>(mySet);   difference.removeAll(other);
```

```
   mySet          other
  ┌─────────┐   ┌─────────┐
  │ 1 2  ┌──┼───┼──┐      │
  │      │ 4│ 5 │6 │      │     intersection = overlap
  └──────┼──┘   └──┼──────┘
         └─────────┘
```

| Set type        | Order              | Operations |
|-----------------|--------------------|------------|
| `HashSet`       | none               | O(1) avg   |
| `LinkedHashSet` | insertion order    | O(1) avg   |
| `TreeSet`       | sorted             | O(log n)   |

> Correction: the original example called `mySet.contains("hello")` on a `Set<Integer>` — it compiles (the parameter is `Object`) but is always `false`. A classic bug that IDE warnings catch.

### 11.8 Set: Remove Duplicates

```java
public static List<Integer> removeDuplicates(List<Integer> myList) {
    Set<Integer> uniqueSet = new HashSet<>(myList);
    return new ArrayList<>(uniqueSet);
    // ➕ to KEEP the original order: new ArrayList<>(new LinkedHashSet<>(myList));
    // ➕ streams: myList.stream().distinct().toList();
}
```

Time O(n), space O(n).

### 11.9 Set: Has Unique Chars

```java
public static boolean hasUniqueChars(String string) {
    Set<Character> charSet = new HashSet<>();
    for (char ch : string.toCharArray()) {
        if (!charSet.add(ch)) return false;   // add() returns false if already present
    }
    return true;
}
```

`"abcdefg"` → true, `"hello"` → false, `""` → true. Time O(n), space O(n).

### 11.10 Set: Find Pairs (one from each array, sum = target)

```
arr1 = {1, 2, 3, 4, 5}    arr2 = {2, 4, 6, 8, 10}    target = 7
set(arr1) = {1,2,3,4,5}

 num (arr2)  complement   in set?   pair
     2          5           ✓       [5, 2]
     4          3           ✓       [3, 4]
     6          1           ✓       [1, 6]
     8         -1           ✗
    10         -3           ✗
```

```java
public static List<int[]> findPairs(int[] arr1, int[] arr2, int target) {
    Set<Integer> mySet = new HashSet<>();
    List<int[]> pairs = new ArrayList<>();
    for (int num : arr1) mySet.add(num);
    for (int num : arr2) {
        int complement = target - num;
        if (mySet.contains(complement)) {
            pairs.add(new int[]{complement, num});
        }
    }
    return pairs;
}
```

Time O(n + m), space O(n).

### 11.11 Set: Longest Consecutive Sequence (LeetCode 128)

```
nums = [100, 4, 200, 1, 3, 2]     set = {1, 2, 3, 4, 100, 200}

num   num-1 in set?   start of a run?   walk forward          length
 1        0  ✗            yes            1 → 2 → 3 → 4          4
 2        1  ✓            no (skip)
 3        2  ✓            no
 4        3  ✓            no
100      99  ✗            yes            100                    1
200     199  ✗            yes            200                    1
                                                         longest = 4
```

```java
public static int longestConsecutiveSequence(int[] nums) {
    Set<Integer> numSet = new HashSet<>();
    for (int num : nums) numSet.add(num);

    int longestStreak = 0;
    for (int num : numSet) {
        if (!numSet.contains(num - 1)) {          // only start counting at the beginning of a run
            int currentNum = num;
            int currentStreak = 1;
            while (numSet.contains(currentNum + 1)) {
                currentNum++;
                currentStreak++;
            }
            longestStreak = Math.max(longestStreak, currentStreak);
        }
    }
    return longestStreak;
}
```

Time **O(n)** — each number is visited by the inner `while` at most once overall, because walking only starts at run beginnings. Sorting would be O(n log n).

---

## 12. Graphs

### 12.1 Terminology

```
        A ─────── B                 vertex (node): A, B, C, D, E
        │         │                 edge: a connection, e.g. A─B
        │         │                 a vertex can have any number of edges
        E ─── D ─ C                 hop: moving along one edge
```

**Weighted vs. unweighted** — edges can carry a cost (distance, time, price). GPS picks the *cheapest* route, not necessarily the one with the fewest hops.

```
      A ──(2)── B
      │          \
     (10)        (3)
      │            \
      D ────(1)──── C        A→B→C→D costs 2+3+1 = 6  <  A→D costs 10
```

**Undirected vs. directed:**

```
Undirected (Facebook friends)        Directed (Instagram follows)
    A ─── B                              A ──► B
  (both ways)                          (A follows B; not necessarily back)
```

Trees and linked lists are restricted graphs: `Linked List ⊂ Tree ⊂ Graph`.

### 12.2 Adjacency matrix

For the graph `A─B, B─C, C─D, D─E, E─A`:

```
        A   B   C   D   E
   A  [ 0   1   0   0   1 ]
   B  [ 1   0   1   0   0 ]
   C  [ 0   1   0   1   0 ]
   D  [ 0   0   1   0   1 ]
   E  [ 1   0   0   1   0 ]
```

- The diagonal is always 0 (no vertex connects to itself, in simple graphs).
- **Undirected** → the matrix is symmetric: `matrix[A][B] == matrix[B][A]`.
- **Directed** → not symmetric.
- **Weighted** → store the weight instead of 1, e.g. `matrix[A][B] = 5`.

### 12.3 Adjacency list

```
{
  "A": ["B", "E"],
  "B": ["A", "C"],
  "C": ["B", "D"],
  "D": ["C", "E"],
  "E": ["A", "D"]
}
```

In Java: `HashMap<String, ArrayList<String>>`.

### 12.4 Graph Big O

| Operation       | Adjacency List | Adjacency Matrix | Why                                                         |
|-----------------|----------------|------------------|-------------------------------------------------------------|
| Space           | O(V + E)       | O(V²)            | matrix stores every *possible* edge, including the zeros    |
| Add vertex      | O(1)           | O(V²)            | matrix: build a new (V+1)×(V+1) grid                        |
| Add edge        | O(1)           | O(1)             | append to two lists / set two cells                         |
| Remove edge     | O(E)           | O(1)             | list: search inside the vertex's list                       |
| Remove vertex   | O(V + E)       | O(V²)            | list: remove it from every neighbor's list                  |

**Choose:** adjacency list for sparse graphs (most real graphs — social networks, roads); matrix for dense graphs or when you need O(1) "is there an edge?" checks.

### 12.5 Implementation (adjacency list, undirected)

```java
import java.util.ArrayList;
import java.util.HashMap;

public class Graph {

    private HashMap<String, ArrayList<String>> adjList = new HashMap<>();

    public void printGraph() {
        System.out.println(adjList);
    }

    public boolean addVertex(String vertex) {
        if (adjList.get(vertex) == null) {
            adjList.put(vertex, new ArrayList<>());
            return true;
        }
        return false;                         // already exists
    }

    public boolean addEdge(String vertex1, String vertex2) {
        if (adjList.get(vertex1) != null && adjList.get(vertex2) != null) {
            adjList.get(vertex1).add(vertex2);
            adjList.get(vertex2).add(vertex1);   // undirected: both directions
            return true;
        }
        return false;
    }

    public boolean removeEdge(String vertex1, String vertex2) {
        if (adjList.get(vertex1) != null && adjList.get(vertex2) != null) {
            adjList.get(vertex1).remove(vertex2);   // remove(Object), not remove(int index)
            adjList.get(vertex2).remove(vertex1);
            return true;
        }
        return false;
    }

    public boolean removeVertex(String vertex) {
        if (adjList.get(vertex) == null) return false;
        for (String otherVertex : adjList.get(vertex)) {
            adjList.get(otherVertex).remove(vertex);   // remove all edges pointing to it
        }
        adjList.remove(vertex);
        return true;
    }
}
```

```
addVertex A, B, C, D;  addEdge A-B, A-D, B-D, C-D

        A ─── B           A --> [B, D]
        │   ╱             B --> [A, D]
        │  ╱              C --> [D]
        D ─── C           D --> [A, B, C]

removeVertex("D"):  visit D's list [A, B, C] and remove D from each, then drop D

        A ─── B           A --> [B]
                          B --> [A]
              C           C --> []
```

> Why does `removeVertex` only need to visit D's own list? In an **undirected** graph, if X is in D's list then D is in X's list. For a **directed** graph you would need to scan every vertex. A directed `removeEdge(from, to)` only removes `to` from `from`'s list.

### 12.6 Quiz 6 — Graph Big O (answers)

1. Adding a vertex to an adjacency list is O(1) → **True**.
2. Graphs are the go-to structure for entities and the relationships between them → **True**.

### 12.7 Graph traversal — BFS & DFS ➕ (not in the course, very common in interviews)

```
        A ─── B ─── E
        │     │
        C ─── D

BFS from A (queue, level by level):  A, B, C, E, D
DFS from A (stack/recursion, go deep): A, B, E, D, C   (depends on neighbor order)
```

```java
public List<String> bfs(String start) {
    List<String> order = new ArrayList<>();
    Set<String> visited = new HashSet<>();
    Queue<String> queue = new ArrayDeque<>();
    queue.offer(start);
    visited.add(start);                       // mark when ENQUEUED, not when dequeued
    while (!queue.isEmpty()) {
        String v = queue.poll();
        order.add(v);
        for (String n : adjList.get(v)) {
            if (visited.add(n)) queue.offer(n);
        }
    }
    return order;
}

public List<String> dfs(String start) {
    List<String> order = new ArrayList<>();
    dfsHelper(start, new HashSet<>(), order);
    return order;
}

private void dfsHelper(String v, Set<String> visited, List<String> order) {
    if (!visited.add(v)) return;              // already visited → stop (prevents infinite loops)
    order.add(v);
    for (String n : adjList.get(v)) dfsHelper(n, visited, order);
}
```

Both are O(V + E). BFS finds the **shortest path in an unweighted graph**; for weighted graphs use Dijkstra (BFS with a `PriorityQueue`). Unlike trees, graphs can have cycles — the `visited` set is mandatory.

---

## 13. Heaps

### 13.1 What is a heap?

A **complete binary tree** where every parent is ≥ its children (**max heap**) or ≤ its children (**min heap**).

```
MAX HEAP                               NOT a BST! Siblings are unordered:
              99                       72 and 61 have no left/right rule.
            /    \
          72      61                   Guarantee: the maximum is at the top.
         /  \    /  \                  Searching for an arbitrary value is O(n).
       58   55  27   18
```

Duplicates are allowed. Because it is complete (filled left to right), it fits perfectly into an array:

```
index:    0    1    2    3    4    5    6
        [ 99 | 72 | 61 | 58 | 55 | 27 | 18 ]

for a node at index i (root at index 0):
   left child  = 2i + 1
   right child = 2i + 2
   parent      = (i - 1) / 2      (integer division)

  e.g. i = 1 (72):  left = 3 (58), right = 4 (55), parent = 0 (99)
```

> Some textbooks leave index 0 empty and store the root at 1 (then left = 2i, right = 2i + 1, parent = i/2). This guide uses the 0-based version, matching the Java code below.

### 13.2 Max heap class

```java
import java.util.ArrayList;
import java.util.List;

public class Heap {

    private List<Integer> heap = new ArrayList<>();

    public List<Integer> getHeap() { return new ArrayList<>(heap); }

    private int leftChild(int index)  { return 2 * index + 1; }
    private int rightChild(int index) { return 2 * index + 2; }
    private int parent(int index)     { return (index - 1) / 2; }

    private void swap(int index1, int index2) {
        int temp = heap.get(index1);
        heap.set(index1, heap.get(index2));
        heap.set(index2, temp);
    }

    public void insert(int value)  { /* 13.3 */ }
    public Integer remove()        { /* 13.4 */ return null; }
    private void sinkDown(int index) { /* 13.5 */ }
}
```

### 13.3 insert — add at the end, then bubble up — O(log n)

```
insert(100) into [99, 72, 61, 58, 55, 27, 18]

1) append at index 7:                      2) 100 > parent 58 (index 3) → swap
              99                                        99
            /    \                                    /    \
          72      61                                72      61
         /  \    /  \                              /  \    /  \
       58   55  27   18                         [100] 55  27   18
       /                                          /
    [100]                                        58

3) 100 > parent 72 (index 1) → swap        4) 100 > parent 99 (index 0) → swap → root, stop
              99                                       [100]
            /    \                                    /    \
        [100]     61                                99      61
         /  \    /  \                              /  \    /  \
       72   55  27   18                          72   55  27   18
       /                                         /
      58                                        58

array: [100, 99, 61, 72, 55, 27, 18, 58]
```

```java
public void insert(int value) {
    heap.add(value);
    int current = heap.size() - 1;
    while (current > 0 && heap.get(current) > heap.get(parent(current))) {
        swap(current, parent(current));
        current = parent(current);
    }
}
```

At most one swap per level → O(log n). Also called *heapify-up*, *sift-up* or *bubble-up*.

### 13.4 remove — take the root, move the last element up, sink down — O(log n)

```
remove() on [95, 75, 80, 55, 60, 50, 65]

          95  ◄ remove (max)           move last (65) to root       sink: 65 < max child 80 → swap
        /    \                               65                             80
      75      80                           /    \                         /    \
     /  \    /  \                        75      80                     75      65
   55   60  50   65                     /  \    /                      /  \    /
                                      55   60  50                    55   60  50
                                                                 65 > 50 → stop
returns 95; array: [80, 75, 65, 55, 60, 50]
```

```java
public Integer remove() {
    if (heap.size() == 0) return null;
    if (heap.size() == 1) return heap.remove(0);   // List.remove(int), not recursion

    int maxValue = heap.get(0);
    heap.set(0, heap.remove(heap.size() - 1));     // move last element to the root
    sinkDown(0);
    return maxValue;
}
```

### 13.5 sinkDown — swap with the LARGER child until in place

```java
private void sinkDown(int index) {
    int maxIndex = index;
    while (true) {
        int leftIndex = leftChild(index);
        int rightIndex = rightChild(index);

        if (leftIndex < heap.size() && heap.get(leftIndex) > heap.get(maxIndex)) {
            maxIndex = leftIndex;
        }
        if (rightIndex < heap.size() && heap.get(rightIndex) > heap.get(maxIndex)) {
            maxIndex = rightIndex;
        }
        if (maxIndex != index) {
            swap(index, maxIndex);
            index = maxIndex;
        } else {
            return;            // both children smaller (or none) → heap property restored
        }
    }
}
```

Why the **larger** child? Swapping with the smaller one would put a smaller value above the larger sibling and break the heap.

### 13.6 Min heap — flip the comparisons

```java
// insert: bubble up while SMALLER than parent
while (current > 0 && heap.get(current) < heap.get(parent(current))) { ... }

// sinkDown: swap with the SMALLER child
if (leftIndex < heap.size() && heap.get(leftIndex) < heap.get(minIndex)) minIndex = leftIndex;
if (rightIndex < heap.size() && heap.get(rightIndex) < heap.get(minIndex)) minIndex = rightIndex;

// remove returns the minimum (root)
```

```
MIN HEAP            [2, 5, 3, 9, 7, 8]
          2
        /   \
       5     3
      / \   /
     9   7 8
```

### 13.7 Heap Big O and Java's `PriorityQueue` ➕

| Operation               | Big O    |
|-------------------------|----------|
| peek (max/min)          | O(1)     |
| insert                  | O(log n) |
| remove top              | O(log n) |
| search arbitrary value  | O(n)     |
| build heap from array   | O(n)     |
| heap sort               | O(n log n) |

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();                          // min-heap (default)
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder()); // max-heap
minHeap.offer(5); minHeap.offer(1); minHeap.offer(3);
minHeap.peek();  // 1
minHeap.poll();  // 1
```

**Classic use — K-th largest element (LeetCode 215):** keep a min-heap of size k; the root is the answer. O(n log k).

```java
public static int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> minHeap = new PriorityQueue<>();
    for (int num : nums) {
        minHeap.offer(num);
        if (minHeap.size() > k) minHeap.poll();   // drop the smallest
    }
    return minHeap.peek();
}
```

Other heap use cases: priority scheduling, Dijkstra, merge k sorted lists, running median (two heaps).

---

## 14. Recursion and Recursive BST

### 14.1 Recursion in one picture ➕

A recursive method calls itself on a **smaller** problem until it reaches a **base case**. Every unfinished call waits on the **call stack**.

```java
public static int factorial(int n) {
    if (n == 1) return 1;            // base case — without it: StackOverflowError
    return n * factorial(n - 1);     // recursive case — problem gets smaller
}
```

```
factorial(4)

 call stack (grows down)                    returns (unwinds up)
 ┌──────────────────────────┐
 │ factorial(4) = 4 * f(3)  │  ◄─────────── 4 * 6  = 24  ✓
 ├──────────────────────────┤
 │ factorial(3) = 3 * f(2)  │  ◄─────────── 3 * 2  = 6
 ├──────────────────────────┤
 │ factorial(2) = 2 * f(1)  │  ◄─────────── 2 * 1  = 2
 ├──────────────────────────┤
 │ factorial(1) = 1         │  base case ── 1
 └──────────────────────────┘
```

Rules: (1) a base case that stops, (2) every call moves toward the base case, (3) recursion depth costs **O(depth) stack space**.

### 14.2 rContains — recursive search

```
rContains(root, 27)
   47: 27 < 47 → rContains(left)
   21: 27 > 21 → rContains(right)
   27: equal → true  (returned all the way up)
```

```java
public boolean rContains(int value) {
    return rContains(root, value);
}

private boolean rContains(Node currentNode, int value) {
    if (currentNode == null) return false;            // base case 1: fell off the tree
    if (currentNode.value == value) return true;      // base case 2: found
    if (value < currentNode.value) {
        return rContains(currentNode.left, value);
    } else {
        return rContains(currentNode.right, value);
    }
}
```

The public method has the friendly signature; the private overload carries the extra `Node` parameter — a standard pattern.

### 14.3 rInsert — recursive insert

Base case: reaching `null` means "this is the spot" → return a new node; the caller attaches it.

```
rInsert(27) into   47
                  /
                21
  47: 27 < 47 → left = rInsert(21-subtree)
     21: 27 > 21 → right = rInsert(null) → returns new Node(27)
     21.right = [27]; return 21
  47.left = 21 (unchanged); return 47
```

```java
public void rInsert(int value) {
    root = rInsert(root, value);     // handles the empty tree too
}

private Node rInsert(Node currentNode, int value) {
    if (currentNode == null) return new Node(value);
    if (value < currentNode.value) {
        currentNode.left = rInsert(currentNode.left, value);
    } else if (value > currentNode.value) {
        currentNode.right = rInsert(currentNode.right, value);
    }
    return currentNode;          // duplicates: nothing changes
}
```

> Correction: in the original notes the base case `return new Node(value);` and the final `return currentNode;` were missing from one listing (they were lost with the comments) — without them the method does not compile.

### 14.4 Delete — the three cases

```
CASE 1: leaf                 CASE 2: one child              CASE 3: two children
delete 18                    delete 27                      delete 25

     21                         21                              25  ◄ delete
    /                             \                            /  \
  [18]   → return null            [27]   → return its child  20    30
                                    \                              /
                                     30                          28

                                result:  21                 1) find MIN of right subtree = 28
                                           \                2) copy 28 into the node
                                            30              3) delete 28 from the right subtree

                                                                28
                                                               /  \
                                                             20    30
```

Why the minimum of the right subtree? It is the smallest value larger than everything on the left, so the BST rule still holds. (The maximum of the left subtree also works.)

### 14.5 minValue helper

```
minValue(47-subtree):  47 → 21 → 18 → left is null → 18
minValue(76-subtree):  76 → 52 → left is null → 52
"keep going left"
```

```java
public int minValue(Node currentNode) {
    while (currentNode.left != null) {
        currentNode = currentNode.left;
    }
    return currentNode.value;
}
```

### 14.6 deleteNode — full code

```java
public void deleteNode(int value) {
    root = deleteNode(root, value);
}

private Node deleteNode(Node currentNode, int value) {
    if (currentNode == null) return null;                      // value not found

    if (value < currentNode.value) {
        currentNode.left = deleteNode(currentNode.left, value);
    } else if (value > currentNode.value) {
        currentNode.right = deleteNode(currentNode.right, value);
    } else {                                                   // found the node
        if (currentNode.left == null && currentNode.right == null) {
            return null;                                       // case 1: leaf
        } else if (currentNode.left == null) {
            currentNode = currentNode.right;                   // case 2: only right child
        } else if (currentNode.right == null) {
            currentNode = currentNode.left;                    // case 2: only left child
        } else {                                               // case 3: two children
            int subTreeMin = minValue(currentNode.right);
            currentNode.value = subTreeMin;
            currentNode.right = deleteNode(currentNode.right, subTreeMin);
        }
    }
    return currentNode;
}
```

```
Example:      2                delete(2): two children → min of right subtree = 3
             / \               copy 3 into root, delete 3 from the right subtree
            1   3
                               result:    3
                                         /
                                        1
```

> Correction: the original listings left the two-children `else { }` branch empty. The code above is the complete version.

Time O(h) for all recursive BST operations (h = height): O(log n) balanced, O(n) degenerate. Space O(h) for the call stack.

---

## 15. ★ Recursive BST Interview Problems

### 15.1 Convert Sorted Array to Balanced BST (LeetCode 108)

**Idea:** the middle element becomes the root; recurse on the left half and the right half (divide and conquer — same shape as merge sort).

```
nums = [-10, -3, 0, 5, 9]
index:   0    1  2  3  4

build(0,4): mid=2 → 0
 ├─ build(0,1): mid=0 → -10
 │   ├─ build(0,-1) → null
 │   └─ build(1,1): mid=1 → -3
 └─ build(3,4): mid=3 → 5
     ├─ build(3,2) → null
     └─ build(4,4): mid=4 → 9

            0
          /   \
       -10     5
          \     \
          -3     9          height-balanced: subtree heights differ by ≤ 1
```

```java
public void sortedArrayToBST(int[] nums) {
    this.root = sortedArrayToBST(nums, 0, nums.length - 1);
}

private Node sortedArrayToBST(int[] nums, int left, int right) {
    if (left > right) return null;               // empty range
    int mid = left + (right - left) / 2;         // overflow-safe middle
    Node node = new Node(nums[mid]);
    node.left = sortedArrayToBST(nums, left, mid - 1);
    node.right = sortedArrayToBST(nums, mid + 1, right);
    return node;
}
```

Time O(n) (each element becomes one node), space O(log n) recursion depth.

### 15.2 Invert (Mirror) a Binary Tree (LeetCode 226)

```
before:          47                    after:          47
               /    \                                /    \
             21      76                            76      21
            /  \    /  \                          /  \    /  \
          18   27  52   82                      82   52  27   18
```

```java
private Node invertTree(Node node) {
    if (node == null) return null;
    Node temp = node.left;
    node.left = invertTree(node.right);
    node.right = invertTree(temp);
    return node;
}
```

Every node's children are swapped; recursion handles the subtrees. Time O(n), space O(h). The `temp` variable is required — after `node.left = ...` the original left subtree would otherwise be lost.

---

## 16. Tree Traversal (BFS & DFS)

All examples use:

```
             47
           /    \
         21      76
        /  \    /  \
      18   27  52   82
```

| Traversal          | Order               | Result                          | Typical use                       |
|--------------------|---------------------|---------------------------------|-----------------------------------|
| BFS (level order)  | level by level      | 47, 21, 76, 18, 27, 52, 82      | shortest path, print by levels    |
| DFS pre-order      | **node**, left, right | 47, 21, 18, 27, 76, 52, 82    | copy / serialize a tree           |
| DFS in-order       | left, **node**, right | 18, 21, 27, 47, 52, 76, 82    | **sorted output of a BST**        |
| DFS post-order     | left, right, **node** | 18, 27, 21, 52, 82, 76, 47    | delete a tree, compute sizes/heights |

Memory trick: the name says **where the node goes** — *pre* = before children, *in* = in between, *post* = after.

### 16.1 BFS — Breadth First Search (uses a queue)

```
queue (front → back)           results
[47]                           []
[21, 76]                       [47]
[76, 18, 27]                   [47, 21]
[18, 27, 52, 82]               [47, 21, 76]
[27, 52, 82]                   [47, 21, 76, 18]
[52, 82]                       [47, 21, 76, 18, 27]
[82]                           [47, 21, 76, 18, 27, 52]
[]                             [47, 21, 76, 18, 27, 52, 82]
```

```java
public ArrayList<Integer> BFS() {
    ArrayList<Integer> results = new ArrayList<>();
    if (root == null) return results;

    Queue<Node> queue = new LinkedList<>();
    queue.add(root);

    while (!queue.isEmpty()) {
        Node currentNode = queue.remove();
        results.add(currentNode.value);
        if (currentNode.left != null)  queue.add(currentNode.left);
        if (currentNode.right != null) queue.add(currentNode.right);
    }
    return results;
}
```

Time O(n), space O(w) where w = maximum width of the tree (up to n/2).

### 16.2 DFS Pre-order (node → left → right)

```
visit 47 → go left: visit 21 → go left: visit 18 → back → visit 27 → back to 47
→ go right: visit 76 → visit 52 → visit 82
           47(1)
          /     \
      21(2)     76(5)
      /   \     /   \
  18(3) 27(4) 52(6) 82(7)
```

```java
public ArrayList<Integer> DFSPreOrder() {
    ArrayList<Integer> results = new ArrayList<>();
    preOrder(root, results);
    return results;
}

private void preOrder(Node node, ArrayList<Integer> results) {
    if (node == null) return;
    results.add(node.value);          // NODE
    preOrder(node.left, results);     // LEFT
    preOrder(node.right, results);    // RIGHT
}
```

### 16.3 DFS Post-order (left → right → node)

```
           47(7)
          /     \
      21(3)     76(6)
      /   \     /   \
  18(1) 27(2) 52(4) 82(5)
```

```java
private void postOrder(Node node, ArrayList<Integer> results) {
    if (node == null) return;
    postOrder(node.left, results);    // LEFT
    postOrder(node.right, results);   // RIGHT
    results.add(node.value);          // NODE — only after both subtrees are done
}
```

### 16.4 DFS In-order (left → node → right)

```
           47(4)
          /     \
      21(2)     76(6)
      /   \     /   \
  18(1) 27(3) 52(5) 82(7)

result: 18, 21, 27, 47, 52, 76, 82  ← sorted, because of the BST rule
```

```java
private void inOrder(Node node, ArrayList<Integer> results) {
    if (node == null) return;
    inOrder(node.left, results);      // LEFT
    results.add(node.value);          // NODE
    inOrder(node.right, results);     // RIGHT
}
```

All DFS variants: time O(n), space O(h) for the recursion stack.

> Correction: the original listings mixed `node.val` and `node.value` and used a local `class Traverse` whose constructor did the recursion. That works, but a private helper method (above) is the idiomatic version interviewers expect.

---

## 17. ★ BST Traversal Interview Problems

### 17.1 Validate BST (LeetCode 98)

**Approach 1 (course): in-order traversal must be strictly increasing.**

```java
public boolean isValidBST() {
    ArrayList<Integer> nodeValues = DFSInOrder();
    for (int i = 1; i < nodeValues.size(); i++) {
        if (nodeValues.get(i) <= nodeValues.get(i - 1)) return false;
    }
    return true;
}
```

Time O(n), space O(n) for the list. Note: `nodeValues.get(i) <= nodeValues.get(i - 1)` compares two `Integer` objects with `<=`, which unboxes them — correct. (`==` on two `Integer`s would be a bug, see section 23.)

**➕ Approach 2: pass down a valid (min, max) range — O(h) space, no list.**

The classic mistake is checking only parent vs. child:

```
         10
        /  \
       5    15
           /  \
          6    20        6 < 15 ✓ locally, but 6 is in 10's RIGHT subtree → must be > 10 ✗
```

```
range checks:
  10 in (-∞, +∞) ✓
   5 in (-∞, 10) ✓
  15 in (10, +∞) ✓
   6 in (10, 15) ✗  → invalid
```

```java
public boolean isValidBSTRange() {
    return isValid(root, Long.MIN_VALUE, Long.MAX_VALUE);   // long avoids edge cases at Integer.MIN/MAX
}

private boolean isValid(Node node, long min, long max) {
    if (node == null) return true;
    if (node.value <= min || node.value >= max) return false;
    return isValid(node.left, min, node.value)
        && isValid(node.right, node.value, max);
}
```

### 17.2 Kth Smallest Node (LeetCode 230)

In-order visits a BST in ascending order → stop at the k-th visited node. Iterative version with an explicit stack:

```
tree:        5          k = 3
           /   \
          3     7
         / \   / \
        2   4 6   8

push 5, 3, 2 (go left)       stack [5,3,2]
pop 2  → k=2                 go right: null
pop 3  → k=1                 go right: push 4
pop 4  → k=0 → return 4 ✓
```

```java
public Integer kthSmallest(int k) {
    Stack<Node> stack = new Stack<>();         // java.util.Stack (or ArrayDeque)
    Node node = this.root;

    while (!stack.isEmpty() || node != null) {
        while (node != null) {                 // go as far left as possible
            stack.push(node);
            node = node.left;
        }
        node = stack.pop();                    // smallest unvisited node
        k -= 1;
        if (k == 0) return node.value;
        node = node.right;                     // then explore its right subtree
    }
    return null;                               // fewer than k nodes
}
```

Time O(h + k), space O(h). Examples: k=1 → 2, k=3 → 4, k=6 → 7.

---

## 18. Basic Sorts (Bubble, Selection, Insertion)

### 18.1 Bubble sort

Repeatedly compare **adjacent** pairs and swap if out of order. After each pass the largest unsorted value has "bubbled" to the end.

```
[5, 1, 4, 2, 8, 6]

pass 1:  (5 1) 4 2 8 6 → swap → 1 5 4 2 8 6
         1 (5 4) 2 8 6 → swap → 1 4 5 2 8 6
         1 4 (5 2) 8 6 → swap → 1 4 2 5 8 6
         1 4 2 (5 8) 6 → ok
         1 4 2 5 (8 6) → swap → 1 4 2 5 6 | 8     ← 8 is final
pass 2:  1 4 2 5 6 | 8 → (4 2) swap           → 1 2 4 5 | 6 8
pass 3:  no swaps → array is sorted → stop early (optimized version)
```

```java
public static void bubbleSort(int[] array) {
    for (int i = array.length - 1; i > 0; i--) {
        boolean swapped = false;                       // ➕ optimization
        for (int j = 0; j < i; j++) {
            if (array[j] > array[j + 1]) {
                int temp = array[j];
                array[j] = array[j + 1];
                array[j + 1] = temp;
                swapped = true;
            }
        }
        if (!swapped) return;                          // already sorted → O(n) best case
    }
}
```

> Correction: the original claimed a best case of O(n) but its code had no `swapped` flag, so it was always O(n²). The flag above makes the claim true.

### 18.2 Selection sort

Find the **minimum** of the unsorted part and swap it to the front. Track the **index** of the minimum (`minIndex`), not the value.

```
[4, 2, 6, 5, 1, 3]

i=0: min of [4 2 6 5 1 3] is 1 (idx 4) → swap with 4 → [1 | 2 6 5 4 3]
i=1: min of [2 6 5 4 3]   is 2         → no swap     → [1 2 | 6 5 4 3]
i=2: min of [6 5 4 3]     is 3         → swap with 6 → [1 2 3 | 5 4 6]
i=3: min of [5 4 6]       is 4         → swap with 5 → [1 2 3 4 | 5 6]
i=4: min of [5 6]         is 5         → no swap     → [1 2 3 4 5 | 6]
```

```java
public static void selectionSort(int[] array) {
    for (int i = 0; i < array.length - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < array.length; j++) {
            if (array[j] < array[minIndex]) minIndex = j;
        }
        if (i != minIndex) {
            int temp = array[i];
            array[i] = array[minIndex];
            array[minIndex] = temp;
        }
    }
}
```

Always O(n²) — even on sorted input it scans for the minimum. Advantage: at most n−1 swaps. Not stable.

### 18.3 Insertion sort

Like sorting playing cards in your hand: take the next card and slide it left into the sorted part.

```
[4, 2, 6, 5, 1, 3]        sorted | unsorted

i=1: take 2  → 4 shifts right → [2 4 | 6 5 1 3]
i=2: take 6  → no shift       → [2 4 6 | 5 1 3]
i=3: take 5  → 6 shifts       → [2 4 5 6 | 1 3]
i=4: take 1  → 6,5,4,2 shift  → [1 2 4 5 6 | 3]
i=5: take 3  → 6,5,4 shift    → [1 2 3 4 5 6]
```

```java
public static void insertionSort(int[] array) {
    for (int i = 1; i < array.length; i++) {
        int temp = array[i];
        int j = i - 1;
        while (j >= 0 && array[j] > temp) {   // check j >= 0 FIRST (short-circuit)
            array[j + 1] = array[j];          // shift right
            j--;
        }
        array[j + 1] = temp;                  // drop the card into the gap
    }
}
```

Pitfall: writing `array[j] > temp && j >= 0` throws `ArrayIndexOutOfBoundsException` when `j` reaches -1.

| Case    | Insertion sort | Why                                         |
|---------|----------------|---------------------------------------------|
| Best    | O(n)           | sorted / nearly sorted — inner loop rarely runs |
| Average | O(n²)          | inner loop runs about halfway               |
| Worst   | O(n²)          | reverse sorted — every element shifts to the front |
| Space   | O(1)           | in place                                    |

### 18.4 Quiz 7 — Basic sorts (answers)

1. Bubble, selection and insertion sort all have O(n) time complexity → **False** (O(n²)).
2. They all have O(1) space complexity → **True**.
3. They are all O(n) on a sorted / almost sorted array → **False** — insertion sort (and optimized bubble sort) yes; selection sort is still O(n²).

### 18.5 ★ Bubble sort of a linked list (swap values)

```
sortedUntil marks where the sorted tail begins (initially null = past the end)

start:          4 → 2 → 6 → 1
after pass 1:   2 → 4 → 1 | 6        (6 bubbled to the end)
after pass 2:   2 → 1 | 4 → 6
after pass 3:   1 | 2 → 4 → 6        sortedUntil == head.next → stop
```

```java
public void bubbleSort() {
    if (length < 2) return;
    Node sortedUntil = null;
    while (sortedUntil != head.next) {
        Node current = head;
        while (current.next != sortedUntil) {
            Node nextNode = current.next;
            if (current.value > nextNode.value) {
                int temp = current.value;
                current.value = nextNode.value;
                nextNode.value = temp;
            }
            current = current.next;
        }
        sortedUntil = current;     // last node examined is now in final position
    }
}
```

### 18.6 ★ Selection sort of a linked list (swap values)

```java
public void selectionSort() {
    if (length < 2) return;
    Node current = head;
    while (current.next != null) {
        Node smallest = current;
        Node innerCurrent = current.next;
        while (innerCurrent != null) {
            if (innerCurrent.value < smallest.value) smallest = innerCurrent;
            innerCurrent = innerCurrent.next;
        }
        if (smallest != current) {
            int temp = current.value;
            current.value = smallest.value;
            smallest.value = temp;
        }
        current = current.next;
    }
}
```

(`head` and `tail` nodes do not change because only values are swapped.)

### 18.7 ★ Insertion sort of a linked list (relink nodes)

```
original: 3 → 1 → 2

sorted: [3]          unsorted: 1 → 2
take 1: 1 < 3 → new head          sorted: 1 → 3
take 2: walk: 1 → (2 > 1) → (2 < 3) insert after 1      sorted: 1 → 2 → 3
```

```java
public void insertionSort() {
    if (length < 2) return;

    Node sortedListHead = head;
    Node unsortedListHead = head.next;
    sortedListHead.next = null;                 // sorted part = first node only

    while (unsortedListHead != null) {
        Node current = unsortedListHead;
        unsortedListHead = unsortedListHead.next;

        if (current.value < sortedListHead.value) {      // new smallest → becomes head
            current.next = sortedListHead;
            sortedListHead = current;
        } else {
            Node searchPointer = sortedListHead;
            while (searchPointer.next != null && current.value > searchPointer.next.value) {
                searchPointer = searchPointer.next;
            }
            current.next = searchPointer.next;
            searchPointer.next = current;
        }
    }

    head = sortedListHead;
    Node temp = head;                     // tail may have changed → find it again
    while (temp.next != null) temp = temp.next;
    tail = temp;
}
```

All three: O(n²) time, O(1) space.

---

## 19. Merge Sort

### 19.1 Overview — divide, then merge

```
                    [6, 3, 1, 7, 5, 2, 8, 4]
                     /                    \
            [6, 3, 1, 7]                [5, 2, 8, 4]          DIVIDE
             /        \                  /        \           (log n levels)
         [6, 3]     [1, 7]           [5, 2]     [8, 4]
         /   \      /   \            /   \      /   \
       [6]  [3]   [1]  [7]         [5]  [2]   [8]  [4]       base case: size 1
         \   /      \   /            \   /      \   /
         [3, 6]     [1, 7]           [2, 5]     [4, 8]        MERGE
             \        /                  \        /           (O(n) work per level)
            [1, 3, 6, 7]                [2, 4, 5, 8]
                     \                    /
                    [1, 2, 3, 4, 5, 6, 7, 8]
```

log₂ n levels × O(n) merging per level = **O(n log n)** in every case.

### 19.2 merge — combine two sorted arrays

```
array1 = [1, 3, 7, 8]        array2 = [2, 4, 5, 6]
          i                            j

compare 1 vs 2 → take 1     combined: [1]
compare 3 vs 2 → take 2               [1, 2]
compare 3 vs 4 → take 3               [1, 2, 3]
compare 7 vs 4 → take 4               [1, 2, 3, 4]
compare 7 vs 5 → take 5               [1, 2, 3, 4, 5]
compare 7 vs 6 → take 6               [1, 2, 3, 4, 5, 6]
array2 exhausted → copy rest of array1 [1, 2, 3, 4, 5, 6, 7, 8]
```

```java
public static int[] merge(int[] array1, int[] array2) {
    int[] combined = new int[array1.length + array2.length];
    int index = 0, i = 0, j = 0;

    while (i < array1.length && j < array2.length) {
        if (array1[i] <= array2[j]) {          // <= keeps the sort STABLE
            combined[index++] = array1[i++];
        } else {
            combined[index++] = array2[j++];
        }
    }
    while (i < array1.length) combined[index++] = array1[i++];   // leftovers
    while (j < array2.length) combined[index++] = array2[j++];
    return combined;
}
```

Merge assumes both inputs are already sorted. Time O(n + m), space O(n + m).

> Small correction: the original used `<` — with equal values that takes from `array2` first, which breaks stability (it does not matter for plain `int`s, but it does for objects).

### 19.3 mergeSort — recursion

```java
public static int[] mergeSort(int[] array) {
    if (array.length <= 1) return array;                       // base case

    int midIndex = array.length / 2;
    int[] left  = mergeSort(Arrays.copyOfRange(array, 0, midIndex));
    int[] right = mergeSort(Arrays.copyOfRange(array, midIndex, array.length));

    return merge(left, right);
}
```

```java
int[] original = {6, 3, 8, 2};
int[] sorted = mergeSort(original);
// original is unchanged: [6, 3, 8, 2]   sorted: [2, 3, 6, 8]
```

Note: this version returns a **new** array; the input is not modified.

### 19.4 Merge sort Big O

| Aspect | Value      | Why                                                  |
|--------|------------|------------------------------------------------------|
| Time   | O(n log n) | log n levels of splitting × O(n) merge per level      |
| Space  | O(n)       | new arrays for the halves and merged results          |
| Stable | yes        | equal elements keep their order (with `<=`)          |

| Algorithm      | Time        | Space |
|----------------|-------------|-------|
| Bubble         | O(n²)       | O(1)  |
| Selection      | O(n²)       | O(1)  |
| Insertion      | O(n²)       | O(1)  |
| **Merge**      | **O(n log n)** | O(n) |

O(n log n) is the best possible worst-case time for a **comparison-based** sort.

### 19.5 ★ Merge Two Sorted Linked Lists (LeetCode 21)

**Task:** both lists are sorted ascending; merge `otherList` into the current list.

> Correction: the original description said "the input lists themselves do not need to be sorted" — they **must** be sorted, otherwise the result is not sorted.

```
this:   1 → 3 → 5 → 7          other:  2 → 4 → 6 → 8

dummy → ?
   compare 1 vs 2 → take 1     dummy → 1
   compare 3 vs 2 → take 2     dummy → 1 → 2
   compare 3 vs 4 → take 3     dummy → 1 → 2 → 3
   ...                          dummy → 1 → 2 → 3 → 4 → 5 → 6 → 7
   this is empty → attach the rest of other: → 8   (and tail = other's tail)
head = dummy.next
```

```java
public void merge(LinkedList otherList) {
    Node otherHead = otherList.getHead();
    Node dummy = new Node(0);
    Node current = dummy;

    while (head != null && otherHead != null) {
        if (head.value < otherHead.value) {
            current.next = head;
            head = head.next;
        } else {
            current.next = otherHead;
            otherHead = otherHead.next;
        }
        current = current.next;
    }

    if (head != null) {
        current.next = head;              // tail stays the same
    } else {
        current.next = otherHead;
        tail = otherList.getTail();       // the last node now comes from the other list
    }

    head = dummy.next;
    length += otherList.getLength();
}
```

Time O(n + m), space O(1) — nodes are relinked, not copied.

---

## 20. Quick Sort

### 20.1 Idea

1. Pick a **pivot** (the course uses the first element).
2. **Partition:** move smaller values to the pivot's left, larger to its right → the pivot lands in its **final** sorted position.
3. Recursively quick-sort the left part and the right part.

### 20.2 The pivot (partition) helper

`swapIndex` marks the last position holding a value smaller than the pivot.

```
pivot(array, 0, 6)     pivot value = 4

          0   1   2   3   4   5   6
start:  [ 4 , 6 , 1 , 7 , 3 , 2 , 5 ]     swapIndex = 0
          P
i=1  6 > 4   nothing
i=2  1 < 4   swapIndex=1, swap(1,2)  → [ 4 , 1 , 6 , 7 , 3 , 2 , 5 ]
i=3  7 > 4   nothing
i=4  3 < 4   swapIndex=2, swap(2,4)  → [ 4 , 1 , 3 , 7 , 6 , 2 , 5 ]
i=5  2 < 4   swapIndex=3, swap(3,5)  → [ 4 , 1 , 3 , 2 , 6 , 7 , 5 ]
i=6  5 > 4   nothing
final: swap(pivot 0, swapIndex 3)    → [ 2 , 1 , 3 , 4 , 6 , 7 , 5 ]
                                                    ▲
                                    returns 3: 4 is in its final place
        smaller than 4: [2, 1, 3]          larger than 4: [6, 7, 5]
```

> Correction: the original notes showed the result as `[3, 1, 2, 4, 7, 6, 5]`. Running the code gives `[2, 1, 3, 4, 6, 7, 5]` (traced above). Only the pivot's position (index 3) is guaranteed; the order inside each side depends on the algorithm.

```java
private static void swap(int[] array, int firstIndex, int secondIndex) {
    int temp = array[firstIndex];
    array[firstIndex] = array[secondIndex];
    array[secondIndex] = temp;
}

private static int pivot(int[] array, int pivotIndex, int endIndex) {
    int swapIndex = pivotIndex;
    for (int i = pivotIndex + 1; i <= endIndex; i++) {
        if (array[i] < array[pivotIndex]) {
            swapIndex++;
            swap(array, swapIndex, i);
        }
    }
    swap(array, pivotIndex, swapIndex);
    return swapIndex;
}
```

### 20.3 quickSort

```
quickSortHelper(0, 6)  → pivot at 3   [2 1 3] 4 [6 7 5]
├─ quickSortHelper(0, 2) → pivot 2 lands at 1   [1] 2 [3]
│   ├─ (0,0) single element → stop
│   └─ (2,2) stop
└─ quickSortHelper(4, 6) → pivot 6 lands at 5   [5] 6 [7]
    ├─ (4,4) stop
    └─ (6,6) stop
result: [1, 2, 3, 4, 5, 6, 7]
```

```java
public static void quickSort(int[] array) {
    quickSortHelper(array, 0, array.length - 1);
}

private static void quickSortHelper(int[] array, int left, int right) {
    if (left < right) {                                 // base case: 0 or 1 element
        int pivotIndex = pivot(array, left, right);
        quickSortHelper(array, left, pivotIndex - 1);
        quickSortHelper(array, pivotIndex + 1, right);
    }
}
```

Sorts **in place** (no new arrays, unlike merge sort).

### 20.4 Quick sort Big O

```
GOOD pivot (splits in half)            BAD pivot (already sorted input, first element as pivot)

        n                                n
      /   \                               \
   n/2     n/2       log n levels          n-1
   / \     / \       × O(n) each             \
 ...  ...  ... ...   = O(n log n)            n-2        n levels × O(n) = O(n²)
                                               \
                                               ...
```

| Case           | Time       | Space (call stack) |
|----------------|------------|--------------------|
| Best / average | O(n log n) | O(log n)           |
| Worst          | O(n²)      | O(n)               |

The worst case happens with **already sorted (or reverse sorted) data** when the first element is the pivot. Fixes: pick a **random** pivot or the **median of three** (first, middle, last).

**Merge sort vs. quick sort:**

| | Merge sort | Quick sort |
|---|---|---|
| Worst case | O(n log n) guaranteed | O(n²) |
| Extra memory | O(n) | O(log n) |
| Stable | yes | no |
| In practice | linked lists, external sorting, stability required | arrays of primitives (fast, cache friendly) |

---

## 21. Dynamic Programming

DP = solve each subproblem **once**, store the result, reuse it. It applies when a problem has both:

### 21.1 Requirement 1 — Overlapping subproblems

The same subproblem appears again and again (unlike merge sort, whose subproblems are all different → merge sort is divide and conquer, **not** DP).

### 21.2 Requirement 2 — Optimal substructure

The optimal solution can be built from optimal solutions of subproblems.

```
Lowest-cost path A → D:

     A ──10── B ──15── D           best(A→D) = best(A→B) + best(B→D) = 10 + 15 = 25 ✓
     │                 │           → optimal substructure holds
     └──20── C ──30────┘           (A→C→D = 50)

Highest-cost simple path (no repeated vertices) does NOT have it:
the longest A→C path and the longest C→D path may reuse the same vertices,
so combining them is not a valid (or optimal) answer.
```

### 21.3 Fibonacci — the classic example

```
0, 1, 1, 2, 3, 5, 8, 13, 21, ...      fib(n) = fib(n-1) + fib(n-2)
```

**Naive recursion — O(2ⁿ):**

```java
static int counter = 0;

public static int fib(int n) {
    counter++;
    if (n == 0 || n == 1) return n;
    return fib(n - 1) + fib(n - 2);
}
```

```
                         fib(5)
                    /              \
              fib(4)                fib(3)          ← fib(3) computed twice
             /      \              /      \
        fib(3)      fib(2)     fib(2)    fib(1)     ← fib(2) computed three times
        /    \      /    \     /    \
    fib(2) fib(1) fib(1) fib(0) fib(1) fib(0)
    /    \
 fib(1) fib(0)
```

| n  | naive calls     |
|----|-----------------|
| 7  | 41              |
| 20 | 21,891          |
| 40 | 331,160,281     |

### 21.4 Memoization (top-down DP) — O(n)

Store each result the first time; return it immediately next time.

```
fib(5) with memo
   fib(4) → fib(3) → fib(2) → fib(1), fib(0)      computed once each, stored
                   ↳ fib(1) (base)
          ↳ fib(2) → memo hit ✓
   fib(3) → memo hit ✓

memo: [0, 1, 1, 2, 3, 5]
```

```java
static Integer[] memo = new Integer[100];
static int counter = 0;

public static int fib(int n) {
    counter++;
    if (memo[n] != null) return memo[n];          // already solved → O(1)
    if (n == 0 || n == 1) return n;
    memo[n] = fib(n - 1) + fib(n - 2);            // solve once, store
    return memo[n];
}
```

> Correction: the original memoized listing was missing the line `memo[n] = fib(n - 1) + fib(n - 2);` (lost with a comment). Without it nothing is ever stored and `memo[n]` stays `null`.

| n  | naive calls  | memoized calls |
|----|--------------|----------------|
| 7  | 41           | 13             |
| 20 | 21,891       | 39             |
| 40 | 331,160,281  | 79             |

Time O(n), space O(n) (memo + call stack). Note: "memo**ization**", not "memorization".

### 21.5 Bottom-up (tabulation) — O(n), no recursion

```
i:        0   1   2   3   4   5   6   7
fibList: [0 | 1 | 1 | 2 | 3 | 5 | 8 | 13]
                  └─┬─┘
          each cell = previous two cells
```

```java
public static int fib(int n) {
    if (n < 2) return n;
    int[] fibList = new int[n + 1];
    fibList[0] = 0;
    fibList[1] = 1;
    for (int i = 2; i <= n; i++) {
        fibList[i] = fibList[i - 1] + fibList[i - 2];
    }
    return fibList[n];        // fib(40): 39 loop iterations
}
```

➕ **O(1) space** — only the last two values are needed:

```java
public static long fib(int n) {
    if (n < 2) return n;
    long prev = 0, curr = 1;
    for (int i = 2; i <= n; i++) {
        long next = prev + curr;
        prev = curr;
        curr = next;
    }
    return curr;
}
```

> `int` overflows after fib(46); use `long` (up to fib(92)) or `BigInteger`.

| Approach        | Time   | Space | Notes                         |
|-----------------|--------|-------|-------------------------------|
| Naive recursion | O(2ⁿ)  | O(n)  | exponential — never in prod   |
| Memoization     | O(n)   | O(n)  | top-down, natural to write    |
| Bottom-up       | O(n)   | O(n)  | no recursion, no stack overflow |
| Two variables   | O(n)   | O(1)  | best for Fibonacci            |

---

## 22. ★ Array Interview Problems

### 22.1 Remove Element (LeetCode 27) — two pointers

**Task:** remove all `val` in place, return the new length. Order may change; elements beyond the new length do not matter.

```
nums = [3, 2, 2, 3], val = 3
        i
        j

j=0: 3 == val → skip
j=1: 2 → nums[0] = 2, i=1     [2, 2, 2, 3]
j=2: 2 → nums[1] = 2, i=2     [2, 2, 2, 3]
j=3: 3 == val → skip
return 2  → first 2 elements: [2, 2]
```

```java
public static int removeElement(int[] nums, int val) {
    int i = 0;                                   // write pointer
    for (int j = 0; j < nums.length; j++) {      // read pointer
        if (nums[j] != val) {
            nums[i] = nums[j];
            i++;
        }
    }
    return i;
}
```

Time O(n), space O(1).

> Correction: the original said the array becomes `[2, 2, 3, 3]`; it actually becomes `[2, 2, 2, 3]` (positions after the new length keep their old values — which is allowed).

### 22.2 Find Max and Min

```java
public static int[] findMaxMin(int[] myList) {
    int maximum = myList[0];
    int minimum = myList[0];
    for (int num : myList) {
        if (num > maximum) maximum = num;
        else if (num < minimum) minimum = num;
    }
    return new int[]{maximum, minimum};
}
```

`[5, 3, 8, 1, 6, 9]` → `[9, 1]`. Time O(n), space O(1). Edge case: an empty array throws `ArrayIndexOutOfBoundsException` — mention it and ask the interviewer what to return.

### 22.3 Find Longest String

```java
public static String findLongestString(String[] stringList) {
    String longestString = "";
    for (String str : stringList) {
        if (str.length() > longestString.length()) {   // strict > keeps the FIRST longest
            longestString = str;
        }
    }
    return longestString;
}
```

`{"apple", "banana", "kiwi", "pear"}` → `"banana"`. Time O(n), space O(1).

### 22.4 Remove Duplicates from a Sorted Array (LeetCode 26)

```
nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
write = 1
read:  compare nums[read] with nums[read - 1]; if different → nums[write++] = nums[read]

result prefix: [0, 1, 2, 3, 4]  → return 5
```

```java
public static int removeDuplicates(int[] nums) {
    if (nums.length == 0) return 0;
    int writePointer = 1;
    for (int readPointer = 1; readPointer < nums.length; readPointer++) {
        if (nums[readPointer] != nums[readPointer - 1]) {
            nums[writePointer] = nums[readPointer];
            writePointer++;
        }
    }
    return writePointer;
}
```

Time O(n), space O(1). Works only because the array is **sorted** (duplicates are adjacent).

### 22.5 Max Profit — Best Time to Buy and Sell Stock (LeetCode 121)

```
prices:     7    1    5    3    6    4
minPrice:   7    1    1    1    1    1
profit:     0    0    4    2    5    3
maxProfit:  0    0    4    4    5    5      → 5 (buy at 1, sell at 6)
```

```java
public static int maxProfit(int[] prices) {
    int minPrice = Integer.MAX_VALUE;
    int maxProfit = 0;
    for (int price : prices) {
        minPrice = Math.min(minPrice, price);
        maxProfit = Math.max(maxProfit, price - minPrice);
    }
    return maxProfit;
}
```

Time O(n), space O(1). (The original notes contained this solution twice; duplicate removed.)

### 22.6 Rotate Array (LeetCode 189) ➕ *(empty in the original)*

**Task:** rotate right by `k` steps, in place.

```
nums = [1, 2, 3, 4, 5, 6, 7], k = 3     expected → [5, 6, 7, 1, 2, 3, 4]

reverse whole array:        [7, 6, 5, 4, 3, 2, 1]
reverse first k (0..2):     [5, 6, 7, 4, 3, 2, 1]
reverse the rest (3..6):    [5, 6, 7, 1, 2, 3, 4]  ✓
```

```java
public static void rotate(int[] nums, int k) {
    int n = nums.length;
    if (n == 0) return;
    k = k % n;                        // k may be larger than n
    reverse(nums, 0, n - 1);
    reverse(nums, 0, k - 1);
    reverse(nums, k, n - 1);
}

private static void reverse(int[] nums, int start, int end) {
    while (start < end) {
        int temp = nums[start];
        nums[start] = nums[end];
        nums[end] = temp;
        start++;
        end--;
    }
}
```

Time O(n), space O(1). (Copying into a new array is O(n) space.)

### 22.7 Maximum Subarray — Kadane's algorithm (LeetCode 53) ➕ *(empty in the original)*

**Task:** the largest sum of a contiguous subarray.

Idea: at each index decide — *extend* the previous subarray or *start fresh* here.

```
nums:        -2    1   -3    4   -1    2    1   -5    4
currentSum:  -2    1   -2    4    3    5    6    1    5
maxSum:      -2    1    1    4    4    5    6    6    6

currentSum = max(num, currentSum + num)      → 6  (subarray [4, -1, 2, 1])
```

```java
public static int maxSubarray(int[] nums) {
    if (nums.length == 0) return 0;
    int maxSum = nums[0];
    int currentSum = nums[0];
    for (int i = 1; i < nums.length; i++) {
        currentSum = Math.max(nums[i], currentSum + nums[i]);
        maxSum = Math.max(maxSum, currentSum);
    }
    return maxSum;
}
```

Time O(n), space O(1). Initializing with `nums[0]` (not 0) handles all-negative arrays: `[-3, -1, -2]` → `-1`.

---

## 23. Interview Patterns, Java Gotchas & Final Checklist

### 23.1 Pattern recognition table

| If the problem says…                                   | Think of…                               | Examples in this guide                |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------|
| linked list middle / cycle                             | fast & slow pointers                    | 4.1, 4.2                              |
| k-th from the end, single pass                         | two pointers k apart                    | 4.3                                   |
| head may change / rewiring nodes                       | dummy node                              | 4.6, 4.7, 6.4, 19.5                   |
| sorted array, pairs, in-place removal                  | two pointers (read/write or left/right) | 22.1, 22.4                            |
| "have I seen this before?", counting, complements      | `HashMap` / `HashSet`                   | 11.1 – 11.11                          |
| contiguous subarray with sum k                         | prefix sum + hash map                   | 11.6                                  |
| max/min running value                                  | single pass with a running variable     | 22.5, 22.7                            |
| matching brackets, undo, "most recent"                 | stack                                   | 8.4                                   |
| process in arrival order, level order                  | queue                                   | 16.1, 12.7                            |
| k largest / smallest, scheduling                       | heap (`PriorityQueue`)                  | 13.7                                  |
| sorted traversal of a BST                              | in-order DFS                            | 16.4, 17.1, 17.2                      |
| tree problem                                           | recursion: base case `null` + combine children | 14, 15                         |
| repeated subproblems                                   | memoization / bottom-up DP              | 21                                    |
| shortest path, unweighted                              | BFS                                     | 12.7                                  |

### 23.2 How to answer a coding question (out loud)

```
1. Clarify     → input size? duplicates? negatives? empty input? return type?
2. Example     → walk a small example by hand (draw the figure!)
3. Brute force → state it and its Big O, even if you won't code it
4. Optimize    → which pattern above applies? what's the new Big O?
5. Code        → clean names, helper methods, handle edge cases first
6. Test        → run your example through the code; then edge cases:
                 empty, one element, two elements, all equal, negative, max size
7. Complexity  → time AND space, including recursion stack
```

### 23.3 Java gotchas interviewers love ➕

**1. `==` on wrapper objects**

```java
Integer a = 127, b = 127;   a == b  → true   (cached -128..127)
Integer c = 128, d = 128;   c == d  → false  (different objects!)
c.equals(d)                         → true   ✓
```

Danger zone: `stack.peek() == otherStack.peek()` with `Stack<Integer>`. Comparing an `Integer` with an `int` (`stack.peek() > temp`) is fine — it unboxes.

**2. Strings:** compare with `.equals()`, never `==`. Build strings in loops with `StringBuilder`.

**3. `List.remove(int)` vs `List.remove(Object)`**

```java
List<Integer> list = new ArrayList<>(List.of(10, 20, 30));
list.remove(1);                    // removes INDEX 1 → [10, 30]
list.remove(Integer.valueOf(10));  // removes VALUE 10 → [30]
```

**4. Integer overflow:** `(left + right) / 2` can overflow → use `left + (right - left) / 2`. Sums of large ints → use `long`.

**5. `ConcurrentModificationException`:** do not remove from a collection while iterating it with for-each; use `Iterator.remove()` or `removeIf`.

**6. `hashCode`/`equals` contract:** override both or neither when objects are used as `HashMap` keys / `HashSet` elements.

**7. Recursion depth:** very deep recursion (e.g. a degenerate tree with 100,000 nodes) → `StackOverflowError`. Mention the iterative alternative.

**8. `Arrays.asList` / `List.of`:** `List.of(...)` is immutable; `Arrays.asList(...)` is fixed-size (no add/remove).

**9. `PriorityQueue` iteration** is NOT in sorted order — only `poll()` returns elements in order.

**10. `char` arithmetic:** `'c' - 'a'` = 2 → handy for `int[26]` frequency arrays.

### 23.4 Final checklist (night before)

- [ ] Recite the Big O of every operation in section 0.2.
- [ ] Write from memory: linked list `reverse`, `removeLast`, `insert`; DLL `remove`.
- [ ] Fast & slow pointers: middle + cycle.
- [ ] Two Sum, Group Anagrams, Longest Consecutive Sequence, Subarray Sum.
- [ ] Valid parentheses with a stack; queue with two stacks (amortized version).
- [ ] BST insert / contains / delete (3 cases) / validate (range method).
- [ ] BFS with a queue; pre/in/post-order recursively.
- [ ] Heap insert (bubble up) and remove (sink down) on an array.
- [ ] Merge sort and quick sort (pivot) + their Big O and when each is worse.
- [ ] Fibonacci: naive → memo → bottom-up → O(1) space.
- [ ] Kadane's algorithm, max profit, rotate array.
- [ ] Java: `ArrayDeque` over `Stack`, `HashMap` internals, `Integer ==` trap.

---

## 24. Review Log — What Was Corrected or Added

**Format & cleanup**

- Converted from `.docx` to Markdown; removed duplicated Persian/English paragraphs (kept one clean English version), chatbot leftovers ("Do you want me to…", "You said / ChatGPT said", `CopyEdit`), video-player text and broken image links.
- Every topic now has a text-based figure (memory layouts, pointer rewiring, tree shapes, traces, call stacks, tables).
- Empty headings filled in: Big O quiz, LL quiz, Stacks & Queues Big O, HT constructor/hash/set/get/keys/Big O, HT interview question, Rotate Array, Max Sub Array.

**Technical corrections**

| Section | Original | Corrected |
|---------|----------|-----------|
| 1.2 | Ω/Θ/O presented strictly as best/average/worst | added the precise "bounds vs. cases" explanation |
| 1.8 | "O(log n) only possible if data is sorted" | binary search needs sorted data; O(log n) comes from halving |
| 8.3 | `String +=` in a loop | `StringBuilder` (O(n) instead of O(n²)) |
| 8.5 | sort-stack trace stopped with 4 left in the stack | full correct trace |
| 7.4 | public `dequeue()` returned a private generic `Node<T>` | consistent `int` queue; note about returning values |
| 11.7 | `Set<Integer>.contains("hello")` | flagged as always-false bug |
| 14.3 / 14.6 | missing `return` statements and empty two-children branch | complete, compiling code |
| 16 | `node.val` vs `node.value`, `class Traverse` trick | idiomatic private recursive helpers |
| 18.1 | bubble sort "best case O(n)" without the early-exit flag | added `swapped` flag |
| 18.3 | insertion sort inner loop lines missing | complete shifting version |
| 19.2 | `<` in merge (unstable for equal keys) | `<=` keeps merge sort stable |
| 19.5 | "input lists do not need to be sorted" | inputs must be sorted |
| 20.2 | pivot result `[3,1,2,4,7,6,5]` | actual result `[2,1,3,4,6,7,5]` (verified by running) |
| 21.4 | memoized Fibonacci missing `memo[n] = …` | fixed; correct call counts table |
| 22.1 | `[3,2,2,3]` becomes `[2,2,3,3]` | becomes `[2,2,2,3]` |
| 22.5 | Max Profit solution duplicated | duplicate removed |

**Additions for interviews (marked ➕)**

Java Collections mapping, `HashMap` internals, pass-by-value explanation, binary search, `ArrayDeque`, amortized two-stack queue, valid parentheses (all bracket types), range-based BST validation, cycle start follow-up, graph BFS/DFS, `PriorityQueue` + k-th largest, heap sort row, O(1)-space Fibonacci, Kadane's algorithm, rotate array, pattern table, Java gotchas, final checklist.
