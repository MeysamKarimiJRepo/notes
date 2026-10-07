# Senior Java Developer Interview Guide — Java, Hibernate, Spring

> Read-and-memorize edition: every topic = **short answer → text figure → code snippet → trap to remember**.
> Built from my own notes (memory management, concurrency, collections, networking, unresolved questions) and extended with the remaining topics a senior Java / Hibernate / Spring interview usually covers.

**Legend**

```text
✅  correct / do this          ❌  wrong / avoid
⚠️  interview trap             💡  one-liner to memorize
-->  points to / references    ==>  results in / leads to
```

## Table of Contents

- [1. Core Java and OOP](#1-core-java-and-oop)
  - [1.1 JDK vs JRE vs JVM and how Java code runs](#11-jdk-vs-jre-vs-jvm-and-how-java-code-runs)
  - [1.2 The four OOP pillars](#12-the-four-oop-pillars)
  - [1.3 Violating encapsulation — common mistakes](#13-violating-encapsulation--common-mistakes)
  - [1.4 Overloading vs overriding (static vs dynamic dispatch)](#14-overloading-vs-overriding-static-vs-dynamic-dispatch)
  - [1.5 Abstract class vs interface](#15-abstract-class-vs-interface)
  - [1.6 equals() and hashCode() contract](#16-equals-and-hashcode-contract)
  - [1.7 Immutable classes](#17-immutable-classes)
  - [1.8 String, StringBuilder, StringBuffer](#18-string-stringbuilder-stringbuffer)
  - [1.9 final vs finally vs finalize](#19-final-vs-finally-vs-finalize)
  - [1.10 static and nested classes](#110-static-and-nested-classes)
  - [1.11 Exceptions hierarchy, checked vs unchecked](#111-exceptions-hierarchy-checked-vs-unchecked)
  - [1.12 Generics, type erasure and PECS](#112-generics-type-erasure-and-pecs)
  - [1.13 Annotations and when to write custom annotations](#113-annotations-and-when-to-write-custom-annotations)
  - [1.14 Reflection — what and the cost](#114-reflection--what-and-the-cost)
  - [1.15 Why do array indexes start at zero?](#115-why-do-array-indexes-start-at-zero)
  - [1.16 Serialization, serialVersionUID, transient](#116-serialization-serialversionuid-transient)
  - [1.17 Shallow copy vs deep copy](#117-shallow-copy-vs-deep-copy)
- [2. Modern Java 8 to 21](#2-modern-java-8-to-21)
  - [2.1 Version map — what came when](#21-version-map--what-came-when)
  - [2.2 Lambdas and functional interfaces](#22-lambdas-and-functional-interfaces)
  - [2.3 Streams — pipeline, laziness, common collectors](#23-streams--pipeline-laziness-common-collectors)
  - [2.4 Optional — right and wrong](#24-optional--right-and-wrong)
  - [2.5 Records, sealed classes and pattern matching](#25-records-sealed-classes-and-pattern-matching)
  - [2.6 Sequenced collections (Java 21)](#26-sequenced-collections-java-21)
- [3. JVM Memory Management and Garbage Collection](#3-jvm-memory-management-and-garbage-collection)
  - [3.1 JVM memory areas (modern JVM)](#31-jvm-memory-areas-modern-jvm)
  - [3.2 Stack vs heap allocation](#32-stack-vs-heap-allocation)
  - [3.3 Java is always pass-by-value](#33-java-is-always-pass-by-value)
  - [3.4 final reference vs final object](#34-final-reference-vs-final-object)
  - [3.5 When is an object eligible for GC? (reachability)](#35-when-is-an-object-eligible-for-gc-reachability)
  - [3.6 String pool: literal vs new String vs intern()](#36-string-pool-literal-vs-new-string-vs-intern)
  - [3.7 Autoboxing and the Integer cache](#37-autoboxing-and-the-integer-cache)
  - [3.8 Generational heap and how GC moves objects](#38-generational-heap-and-how-gc-moves-objects)
  - [3.9 Garbage collectors compared](#39-garbage-collectors-compared)
  - [3.10 Escape analysis](#310-escape-analysis)
  - [3.11 Reference types: strong, soft, weak, phantom](#311-reference-types-strong-soft-weak-phantom)
  - [3.12 finalize() vs Cleaner vs AutoCloseable](#312-finalize-vs-cleaner-vs-autocloseable)
  - [3.13 Memory leaks in Java](#313-memory-leaks-in-java)
  - [3.14 ThreadLocal memory leak](#314-threadlocal-memory-leak)
  - [3.15 Class loading and the delegation model](#315-class-loading-and-the-delegation-model)
- [4. Multithreading and Concurrency](#4-multithreading-and-concurrency)
  - [4.1 Thread lifecycle and states](#41-thread-lifecycle-and-states)
  - [4.2 Java Memory Model: visibility, ordering, atomicity](#42-java-memory-model-visibility-ordering-atomicity)
  - [4.3 synchronized, intrinsic locks, wait/notify](#43-synchronized-intrinsic-locks-waitnotify)
  - [4.4 volatile vs synchronized vs Atomic](#44-volatile-vs-synchronized-vs-atomic)
  - [4.5 Double-checked locking singleton](#45-double-checked-locking-singleton)
  - [4.6 Explicit locks: ReentrantLock, ReadWriteLock, StampedLock](#46-explicit-locks-reentrantlock-readwritelock-stampedlock)
  - [4.7 Deadlock — when it happens, detection, resolution](#47-deadlock--when-it-happens-detection-resolution)
  - [4.8 Livelock and starvation](#48-livelock-and-starvation)
  - [4.9 ThreadLocal](#49-threadlocal)
  - [4.10 Executors and ThreadPoolExecutor internals](#410-executors-and-threadpoolexecutor-internals)
  - [4.11 Future vs CompletableFuture](#411-future-vs-completablefuture)
  - [4.12 Synchronizers: CountDownLatch, CyclicBarrier, Semaphore, Phaser](#412-synchronizers-countdownlatch-cyclicbarrier-semaphore-phaser)
  - [4.13 Fork/Join framework and work stealing](#413-forkjoin-framework-and-work-stealing)
  - [4.14 Concurrent collections and ConcurrentHashMap internals](#414-concurrent-collections-and-concurrenthashmap-internals)
  - [4.15 Virtual threads (Java 21)](#415-virtual-threads-java-21)
  - [4.16 Concurrency best practices (senior summary)](#416-concurrency-best-practices-senior-summary)
- [5. Collections Framework](#5-collections-framework)
  - [5.1 Collections hierarchy](#51-collections-hierarchy)
  - [5.2 HashMap internals](#52-hashmap-internals)
  - [5.3 Mutable key trap](#53-mutable-key-trap)
  - [5.4 Performance characteristics: List, Set, Map](#54-performance-characteristics-list-set-map)
  - [5.5 Fail-fast vs fail-safe iterators](#55-fail-fast-vs-fail-safe-iterators)
  - [5.6 Comparable vs Comparator](#56-comparable-vs-comparator)
  - [5.7 LinkedHashMap as an LRU cache](#57-linkedhashmap-as-an-lru-cache)
  - [5.8 Special-purpose collections](#58-special-purpose-collections)
  - [5.9 Unmodifiable vs immutable collections](#59-unmodifiable-vs-immutable-collections)
  - [5.10 HashMap vs Hashtable vs synchronizedMap vs ConcurrentHashMap vs TreeMap](#510-hashmap-vs-hashtable-vs-synchronizedmap-vs-concurrenthashmap-vs-treemap)
- [6. Design Principles and Patterns](#6-design-principles-and-patterns)
  - [6.1 SOLID](#61-solid)
  - [6.2 Creational patterns: Singleton, Factory, Builder](#62-creational-patterns-singleton-factory-builder)
  - [6.3 Structural and behavioural patterns you meet in Spring](#63-structural-and-behavioural-patterns-you-meet-in-spring)
  - [6.4 Composition over inheritance](#64-composition-over-inheritance)
- [7. Hibernate and JPA](#7-hibernate-and-jpa)
  - [7.1 JPA vs Hibernate vs Spring Data JPA vs JDBC](#71-jpa-vs-hibernate-vs-spring-data-jpa-vs-jdbc)
  - [7.2 Persistence context (first-level cache)](#72-persistence-context-first-level-cache)
  - [7.3 Entity lifecycle states](#73-entity-lifecycle-states)
  - [7.4 persist vs merge vs save vs update](#74-persist-vs-merge-vs-save-vs-update)
  - [7.5 find vs getReference (get vs load)](#75-find-vs-getreference-get-vs-load)
  - [7.6 Dirty checking and flush modes](#76-dirty-checking-and-flush-modes)
  - [7.7 Fetching: lazy vs eager](#77-fetching-lazy-vs-eager)
  - [7.8 LazyInitializationException and Open Session In View](#78-lazyinitializationexception-and-open-session-in-view)
  - [7.9 The N+1 select problem](#79-the-n1-select-problem)
  - [7.10 Associations: owning side and mappedBy](#710-associations-owning-side-and-mappedby)
  - [7.11 Cascade types and orphanRemoval](#711-cascade-types-and-orphanremoval)
  - [7.12 Primary key generation strategies](#712-primary-key-generation-strategies)
  - [7.13 Inheritance mapping strategies](#713-inheritance-mapping-strategies)
  - [7.14 equals and hashCode for entities](#714-equals-and-hashcode-for-entities)
  - [7.15 Hibernate proxies — why entities can't be final](#715-hibernate-proxies--why-entities-cant-be-final)
  - [7.16 Second-level cache and query cache](#716-second-level-cache-and-query-cache)
  - [7.17 Optimistic vs pessimistic locking](#717-optimistic-vs-pessimistic-locking)
  - [7.18 Transaction isolation levels and anomalies](#718-transaction-isolation-levels-and-anomalies)
  - [7.19 Batch processing with Hibernate](#719-batch-processing-with-hibernate)
  - [7.20 JPQL vs Criteria API vs native SQL](#720-jpql-vs-criteria-api-vs-native-sql)
  - [7.21 Entity callbacks and auditing](#721-entity-callbacks-and-auditing)
  - [7.22 Hibernate performance checklist](#722-hibernate-performance-checklist)
- [8. Spring Core: IoC, DI, Beans and AOP](#8-spring-core-ioc-di-beans-and-aop)
  - [8.1 IoC and dependency injection](#81-ioc-and-dependency-injection)
  - [8.2 BeanFactory vs ApplicationContext](#82-beanfactory-vs-applicationcontext)
  - [8.3 Bean lifecycle](#83-bean-lifecycle)
  - [8.4 Bean scopes](#84-bean-scopes)
  - [8.5 @Component vs @Bean and @Configuration proxying](#85-component-vs-bean-and-configuration-proxying)
  - [8.6 Resolving multiple beans: @Primary, @Qualifier, collections](#86-resolving-multiple-beans-primary-qualifier-collections)
  - [8.7 Circular dependencies](#87-circular-dependencies)
  - [8.8 AOP concepts and proxies](#88-aop-concepts-and-proxies)
  - [8.9 Self-invocation problem](#89-self-invocation-problem)
  - [8.10 Configuration: @Value, @ConfigurationProperties, profiles, conditions](#810-configuration-value-configurationproperties-profiles-conditions)
  - [8.11 Spring events](#811-spring-events)
- [9. Spring Transactions](#9-spring-transactions)
  - [9.1 How @Transactional works](#91-how-transactional-works)
  - [9.2 Propagation types](#92-propagation-types)
  - [9.3 Rollback rules](#93-rollback-rules)
  - [9.4 @Transactional pitfalls checklist](#94-transactional-pitfalls-checklist)
  - [9.5 Programmatic transactions](#95-programmatic-transactions)
  - [9.6 Distributed transactions: 2PC vs Saga vs Outbox](#96-distributed-transactions-2pc-vs-saga-vs-outbox)
- [10. Spring Boot](#10-spring-boot)
  - [10.1 What Spring Boot adds](#101-what-spring-boot-adds)
  - [10.2 How auto-configuration works](#102-how-auto-configuration-works)
  - [10.3 Startup sequence](#103-startup-sequence)
  - [10.4 Externalized configuration precedence (high → low, simplified)](#104-externalized-configuration-precedence-high--low-simplified)
  - [10.5 Actuator and observability](#105-actuator-and-observability)
  - [10.6 Spring Boot 3 key changes](#106-spring-boot-3-key-changes)
- [11. Spring MVC and REST](#11-spring-mvc-and-rest)
  - [11.1 DispatcherServlet request flow](#111-dispatcherservlet-request-flow)
  - [11.2 Filter vs Interceptor vs AOP](#112-filter-vs-interceptor-vs-aop)
  - [11.3 REST controller, validation, error handling](#113-rest-controller-validation-error-handling)
  - [11.4 HTTP methods, idempotency and status codes](#114-http-methods-idempotency-and-status-codes)
  - [11.5 HTTP clients: RestTemplate vs WebClient vs RestClient](#115-http-clients-resttemplate-vs-webclient-vs-restclient)
  - [11.6 Spring MVC vs Spring WebFlux](#116-spring-mvc-vs-spring-webflux)
- [12. Spring Data JPA](#12-spring-data-jpa)
  - [12.1 Repository hierarchy and query methods](#121-repository-hierarchy-and-query-methods)
  - [12.2 Projections](#122-projections)
  - [12.3 Specifications for dynamic filters](#123-specifications-for-dynamic-filters)
- [13. Spring Security](#13-spring-security)
  - [13.1 Filter chain architecture](#131-filter-chain-architecture)
  - [13.2 Authentication flow](#132-authentication-flow)
  - [13.3 Security interview quick answers](#133-security-interview-quick-answers)
- [14. Testing Spring Applications](#14-testing-spring-applications)
  - [14.1 Test pyramid and Spring test slices](#141-test-pyramid-and-spring-test-slices)
- [15. Microservices with Spring — quick senior topics](#15-microservices-with-spring--quick-senior-topics)
- [16. Networking Basics for Backend Interviews](#16-networking-basics-for-backend-interviews)
  - [16.1 Network layers (TCP/IP vs OSI)](#161-network-layers-tcpip-vs-osi)
  - [16.2 TCP vs TLS vs UDP](#162-tcp-vs-tls-vs-udp)
  - [16.3 HTTP vs HTTPS vs WebSocket](#163-http-vs-https-vs-websocket)
- [17. Last-Minute Review and Senior Answer Tips](#17-last-minute-review-and-senior-answer-tips)
  - [17.1 Rapid-fire one-liners](#171-rapid-fire-one-liners)
  - [17.2 How to answer like a senior](#172-how-to-answer-like-a-senior)


---

## 1. Core Java and OOP

### 1.1 JDK vs JRE vs JVM and how Java code runs

💡 **JDK** = tools to build (javac, jar, jshell, jcmd…) + JRE. **JRE** = JVM + core libraries. **JVM** = the engine that loads, verifies and executes bytecode. (Since Java 11 there is no separate JRE download; you build a custom runtime with `jlink`.)

```text
  Hello.java ──javac──► Hello.class (bytecode) ──► JVM
                                                   │
          ┌────────────────────────────────────────┤
          ▼                                        ▼
   ClassLoader subsystem                   Execution engine
   (load → link → init)              ┌─ Interpreter (runs bytecode first)
          │                          ├─ JIT compiler C1/C2 (hot code → native)
          ▼                          └─ Garbage Collector
   Runtime data areas
   (Heap, Metaspace, Stacks, PC, Native stacks)
```

⚠️ "Java is interpreted" is half true: code starts interpreted, then **hot methods are JIT-compiled** to native code (tiered compilation: C1 fast, C2 optimized).

---

### 1.2 The four OOP pillars

```text
┌───────────────┬──────────────────────────────────────────────────────┐
│ Encapsulation │ hide state, expose behaviour (private fields + API)  │
│ Abstraction   │ show WHAT, hide HOW (interfaces, abstract classes)   │
│ Inheritance   │ IS-A reuse (class Dog extends Animal)                │
│ Polymorphism  │ one interface, many forms (overload / override)      │
└───────────────┴──────────────────────────────────────────────────────┘
```

```java
interface Payment { void pay(BigDecimal amount); }          // abstraction

class CardPayment implements Payment {                      // inheritance (of type)
    private final String cardNumber;                        // encapsulation
    CardPayment(String cardNumber) { this.cardNumber = cardNumber; }
    @Override public void pay(BigDecimal amount) { /* ... */ }
}

Payment p = new CardPayment("4111...");                     // polymorphism
p.pay(BigDecimal.TEN);   // runtime decides which pay() → dynamic dispatch
```

---

### 1.3 Violating encapsulation — common mistakes

💡 Encapsulation holds when: fields are private, access is validated, internal state is never leaked, invariants are protected.

```text
  Outside world                 Team object
  ─────────────                ┌────────────────────────┐
  caller ── getMembers() ────► │ private List members ──┼──► [ "A", "B" ]
     │                         └────────────────────────┘        ▲
     └──── list.clear() ─────────────────────────────────────────┘
           ❌ caller mutated internal state without asking the object
```

```java
// ❌ 1. public field — no validation possible
public class Person { public String name; }

// ❌ 2. leaking a mutable internal collection
public class Team {
    private final List<String> members = new ArrayList<>();
    public List<String> getMembers() { return members; }            // leak
}
// ✅ fix: unmodifiable view or defensive copy
public List<String> getMembers() { return Collections.unmodifiableList(members); }
public List<String> getMembersCopy() { return List.copyOf(members); }

// ❌ 3. setter without validation
public void setAge(int age) { this.age = age; }                     // -5 allowed
// ✅
public void setAge(int age) {
    if (age < 0) throw new IllegalArgumentException("age < 0");
    this.age = age;
}

// ❌ 4. constructor storing caller's mutable object
public Period(Date start) { this.start = start; }                   // caller can change it later
// ✅ defensive copy IN and OUT
public Period(Date start) { this.start = new Date(start.getTime()); }
```

Other violations: overusing `protected`/package-private, exposing JPA entities directly from REST APIs, global `static` mutable state, depending on concrete classes instead of interfaces.

---

### 1.4 Overloading vs overriding (static vs dynamic dispatch)

```text
             Overloading                    Overriding
  ─────────────────────────────  ──────────────────────────────────
  same class, same name          subclass, same signature
  different parameter list       same (or covariant) return type
  resolved at COMPILE time       resolved at RUNTIME (vtable)
  static polymorphism            dynamic polymorphism
  can't overload by return type  can't reduce visibility / widen checked exceptions
```

```java
class Printer {
    void print(Object o) { System.out.println("Object"); }
    void print(String s) { System.out.println("String"); }
}
Object x = "hello";
new Printer().print(x);   // ⚠️ prints "Object" — overload chosen by STATIC type

class Animal { Animal get() { return this; } void sound() { System.out.println("..."); } }
class Dog extends Animal {
    @Override Dog get() { return this; }          // covariant return ✅
    @Override void sound() { System.out.println("Woof"); }
}
Animal a = new Dog();
a.sound();                // "Woof" — override chosen by RUNTIME type
```

⚠️ `static` methods are **hidden**, not overridden. `private` and `final` methods can't be overridden.

---

### 1.5 Abstract class vs interface

```text
┌──────────────────────┬──────────────────────────┬───────────────────────────┐
│                      │ abstract class           │ interface                 │
├──────────────────────┼──────────────────────────┼───────────────────────────┤
│ state (fields)       │ yes, any                 │ only public static final  │
│ constructors         │ yes                      │ no                        │
│ multiple inheritance │ no (extends one)         │ yes (implements many)     │
│ methods              │ any                      │ abstract, default (8),    │
│                      │                          │ static (8), private (9)   │
│ use when             │ shared state + template  │ capability / contract     │
│                      │ "IS-A"                   │ "CAN-DO"                  │
└──────────────────────┴──────────────────────────┴───────────────────────────┘
```

```java
interface Auditable {
    String user();
    default String auditLine() { return stamp() + " by " + user(); }   // default (Java 8)
    private String stamp() { return Instant.now().toString(); }        // private (Java 9)
    static Auditable system() { return () -> "SYSTEM"; }               // static
}

abstract class BaseJob {                         // template method pattern
    public final void run() { before(); execute(); after(); }
    protected abstract void execute();
    private void before() { /* log start */ }
    private void after()  { /* log end */ }
}
```

⚠️ Diamond problem with defaults: if two interfaces give the same default method, the class **must override** it and can pick one with `A.super.method()`.

---

### 1.6 equals() and hashCode() contract

💡 **Equal objects MUST have equal hash codes.** Equal hash codes do NOT imply equal objects (collisions).

```text
 put(key)                     HashMap buckets
   │ hashCode() ──► index 3 ──►  [3] ─► (k1,v1) ─► (k2,v2)
   │                                     │
   └───────── equals() scans the chain ──┘  to find the exact key

 If you override equals but NOT hashCode:
   a.equals(b) == true, but a.hashCode() != b.hashCode()
   ==> they land in DIFFERENT buckets ==> map.get(b) returns null ❌
```

```java
public final class Money {
    private final BigDecimal amount;
    private final String currency;

    @Override public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Money m)) return false;          // pattern matching (16+)
        return amount.compareTo(m.amount) == 0 && currency.equals(m.currency);
    }
    @Override public int hashCode() {
        return Objects.hash(amount.stripTrailingZeros(), currency);
    }
}
// Or simply: public record Money(BigDecimal amount, String currency) {}
```

Contract for `equals`: reflexive, symmetric, transitive, consistent, `x.equals(null) == false`.

⚠️ Mutable keys: if a field used in `hashCode` changes after `put`, the entry is "lost" in the wrong bucket.

---

### 1.7 Immutable classes

```text
  Recipe for immutability
  ① class is final (or private constructor + factories)
  ② all fields private final
  ③ no setters
  ④ defensive copies of mutable inputs (constructor) and outputs (getters)
  ⑤ "withX()" methods return NEW instances
```

```java
public final class Order {
    private final String id;
    private final List<String> items;

    public Order(String id, List<String> items) {
        this.id = id;
        this.items = List.copyOf(items);     // defensive copy + unmodifiable
    }
    public List<String> items() { return items; }               // already unmodifiable
    public Order withItem(String item) {
        var copy = new ArrayList<>(items); copy.add(item);
        return new Order(id, copy);                              // new object
    }
}

// Java 16+: records are shallowly immutable
public record Customer(String id, List<String> tags) {
    public Customer { tags = List.copyOf(tags); }               // compact constructor
}
```

💡 Why immutability: thread-safe for free, safe HashMap keys, cacheable, no defensive copies by callers. Examples: `String`, `Integer`, `LocalDate`, `BigDecimal`.

---

### 1.8 String, StringBuilder, StringBuffer

```text
┌───────────────┬────────────┬─────────────┬──────────────────────────┐
│               │ mutable?   │ thread-safe │ use                      │
├───────────────┼────────────┼─────────────┼──────────────────────────┤
│ String        │ no         │ yes         │ values, keys             │
│ StringBuilder │ yes        │ no          │ building strings (local) │
│ StringBuffer  │ yes        │ yes (sync)  │ legacy                   │
└───────────────┴────────────┴─────────────┴──────────────────────────┘
```

Why `String` is immutable: string pool sharing, security (class names, URLs, passwords can't change after check), cached `hashCode`, thread-safety.

```java
String s = "";
for (int i = 0; i < 10_000; i++) s += i;          // ❌ creates ~10k temporary Strings in a loop
var sb = new StringBuilder();
for (int i = 0; i < 10_000; i++) sb.append(i);    // ✅ one growing buffer
```

⚠️ `char[]` is preferred over `String` for passwords — you can zero the array; a String stays in memory until GC.

(Pool, `intern()` and `==` vs `equals` → see section 3.6.)

---

### 1.9 final vs finally vs finalize

```java
final int x = 1;                 // variable: assign once
final class Util {}              // class: cannot be extended
final void run() {}              // method: cannot be overridden

try { ... } finally { close(); } // finally: always runs (except System.exit / JVM crash)

@Deprecated(since="9", forRemoval=true)
protected void finalize() {}     // ❌ GC hook, not guaranteed — use AutoCloseable / Cleaner
```

⚠️ Trap: `return` in `finally` overrides the `try` return and **swallows exceptions**.

```java
int f() { try { return 1; } finally { return 2; } }   // returns 2 ❌ never do this
```

---

### 1.10 static and nested classes

```text
  Outer class
  ├── static nested class   → no reference to Outer instance  (✅ preferred)
  ├── inner class           → holds hidden Outer.this         (⚠️ memory leak risk)
  ├── local class           → declared inside a method
  └── anonymous class       → one-off implementation (pre-lambda style)
```

```java
class Outer {
    private int value = 42;
    static class Nested { }                         // new Outer.Nested()
    class Inner { int read() { return value; } }    // outer.new Inner()
}
```

⚠️ An anonymous/inner class handed to a long-living object (listener, thread, cache) keeps the **whole outer object** alive → leak.

---

### 1.11 Exceptions hierarchy, checked vs unchecked

```text
                     Throwable
               ┌─────────┴─────────┐
             Error             Exception
       (OutOfMemoryError,   ┌──────┴────────────────┐
        StackOverflowError) │                       │
        don't catch      IOException,        RuntimeException
                         SQLException        (NullPointer, IllegalArgument,
                         = CHECKED            IllegalState, ArithmeticException)
                         must declare/catch   = UNCHECKED
```

```java
// try-with-resources: closes in REVERSE order, even on exception
try (var conn = dataSource.getConnection();
     var ps = conn.prepareStatement("select 1")) {
    ps.execute();
} catch (SQLException e) {
    for (Throwable s : e.getSuppressed()) log.warn("close failed", s);  // close() errors
    throw new DataAccessFailure("query failed", e);                     // ✅ keep the cause
}

// multi-catch
catch (IOException | TimeoutException e) { ... }
```

💡 Senior answer: use checked exceptions for **recoverable** conditions the caller must handle; unchecked for **programming errors**. Spring/Hibernate translate checked `SQLException` into unchecked `DataAccessException`. Never swallow exceptions; always keep the cause.

---

### 1.12 Generics, type erasure and PECS

```text
  Compile time:  List<String>  List<Integer>
                       │            │
                  type erasure ─────┘
                       ▼
  Runtime:        List  (raw)      ==> no new T(), no T.class, no instanceof List<String>

  PECS = Producer Extends, Consumer Super
   List<? extends Number>  → you READ Numbers from it   (producer)
   List<? super Integer>   → you WRITE Integers into it (consumer)
```

```java
static double sum(List<? extends Number> nums) {          // producer → extends
    return nums.stream().mapToDouble(Number::doubleValue).sum();
}
static void fill(List<? super Integer> sink) {            // consumer → super
    sink.add(1); sink.add(2);
}
// Collections.copy(List<? super T> dest, List<? extends T> src)  ← textbook PECS

// Bounded type parameter
static <T extends Comparable<T>> T max(List<T> list) { ... }

// ⚠️ generics are invariant:
List<Object> objs = new ArrayList<String>();   // ❌ compile error
Object[] arr = new String[1]; arr[0] = 1;      // compiles, ArrayStoreException at runtime
```

---

### 1.13 Annotations and when to write custom annotations

💡 Use a custom annotation when you need **declarative metadata** that a framework, aspect, or processor will read: cross-cutting behaviour (audit, rate-limit, retry), validation rules, marking things for scanning, code generation.

```text
  @Target    → WHERE it can be placed   (TYPE, METHOD, FIELD, PARAMETER…)
  @Retention → HOW LONG it lives
       SOURCE  ──► discarded by compiler        (@Override, Lombok)
       CLASS   ──► in .class, not at runtime    (default)
       RUNTIME ──► readable via reflection      (Spring, JPA, custom aspects)

  @Audited on method ──► Spring AOP proxy sees it ──► @Around advice runs audit logic
```

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Audited {
    String action();
    boolean logArgs() default false;
}

@Aspect @Component
class AuditAspect {
    @Around("@annotation(audited)")
    Object audit(ProceedingJoinPoint pjp, Audited audited) throws Throwable {
        log.info("AUDIT {} by {}", audited.action(), currentUser());
        return pjp.proceed();
    }
}

@Service
class CardService {
    @Audited(action = "BLOCK_CARD")
    public void block(String cardId) { ... }
}

// Custom Bean Validation constraint
@Constraint(validatedBy = IbanValidator.class)
@Target(ElementType.FIELD) @Retention(RetentionPolicy.RUNTIME)
public @interface ValidIban { String message() default "invalid IBAN";
    Class<?>[] groups() default {}; Class<? extends Payload>[] payload() default {}; }
```

---

### 1.14 Reflection — what and the cost

```java
Class<?> c = Class.forName("com.acme.Person");
Object p = c.getDeclaredConstructor().newInstance();
Field f = c.getDeclaredField("name");
f.setAccessible(true);           // breaks encapsulation; blocked by modules unless "opens"
f.set(p, "Ali");
```

💡 Frameworks (Spring DI, Hibernate, Jackson) are built on reflection + proxies. Costs: slower than direct calls, no compile-time safety, breaks encapsulation. Java 9 modules restrict deep reflection (`--add-opens`).

---

### 1.15 Why do array indexes start at zero?

```text
  int[] a = {10, 20, 30, 40};     base address = 1000, int = 4 bytes

  address(a[i]) = base + i * elementSize

  index:     0      1      2      3
  address: 1000   1004   1008   1012
            ▲
            first element is at offset 0 from the base  ==> no "-1" arithmetic
```

💡 The index is an **offset** from the start of a contiguous block. Zero-based indexing makes `base + i*size` direct (one less subtraction), matches C's `a[i] == *(a + i)` pointer arithmetic, and Java inherited the convention from C/C++. Also simplifies algorithms: half-open ranges `[0, n)`, `mid = (lo + hi) >>> 1`, `i % n` for circular buffers.

---

### 1.16 Serialization, serialVersionUID, transient

```java
public class Session implements Serializable {
    private static final long serialVersionUID = 1L;  // version of the class format
    private String user;
    private transient String password;                 // NOT serialized
}
```

```text
 object ──ObjectOutputStream──► bytes ──► file/network ──ObjectInputStream──► object
                                                     ⚠️ if serialVersionUID differs
                                                        ==> InvalidClassException
```

💡 Senior answer: Java native serialization is a **security risk** (deserialization gadgets) and brittle. Prefer JSON/Avro/Protobuf. Static fields are never serialized.

---

### 1.17 Shallow copy vs deep copy

```text
 Shallow copy                         Deep copy
 orig ─► [name, address ─┐]           orig ─► [name, address ─► Addr#1]
 copy ─► [name, address ─┴─► Addr#1]  copy ─► [name, address ─► Addr#2]
         shared inner object ⚠️               independent ✅
```

```java
// prefer copy constructors / static factories over clone()
public Person(Person other) {
    this.name = other.name;
    this.address = new Address(other.address);   // deep
}
```

⚠️ `Cloneable` is a broken design (no `clone()` in the interface, shallow by default, bypasses constructors). Mention this in interviews.

---

[⬆ Back to top](#table-of-contents)

## 2. Modern Java 8 to 21

### 2.1 Version map — what came when

```text
 Java 8  (LTS) : lambdas, streams, Optional, default methods, java.time, CompletableFuture
 Java 9        : modules (JPMS), List.of/Set.of/Map.of, private interface methods, jshell
 Java 10       : var (local type inference)
 Java 11 (LTS) : HttpClient, String.isBlank/strip/lines, run .java directly
 Java 14       : switch expressions, helpful NullPointerExceptions
 Java 15       : text blocks
 Java 16       : records, pattern matching for instanceof
 Java 17 (LTS) : sealed classes
 Java 21 (LTS) : virtual threads, pattern matching for switch, record patterns,
                 sequenced collections, generational ZGC
 Java 25 (LTS) : scoped values, compact source files / instance main, module imports,
                 flexible constructor bodies
```

---

### 2.2 Lambdas and functional interfaces

```text
  Functional interface = exactly ONE abstract method

  Supplier<T>        ()   → T        () -> new ArrayList<>()
  Consumer<T>        T    → void     s -> log.info(s)
  Function<T,R>      T    → R        s -> s.length()
  Predicate<T>       T    → boolean  s -> s.isEmpty()
  BiFunction<T,U,R>  T,U  → R        (a, b) -> a + b
  UnaryOperator<T>   T    → T        s -> s.trim()
```

```java
Function<String, Integer> len = String::length;            // method reference
Predicate<String> notEmpty = Predicate.not(String::isEmpty);
Function<Integer, Integer> plus1 = x -> x + 1, times2 = x -> x * 2;
plus1.andThen(times2).apply(3);   // (3+1)*2 = 8
plus1.compose(times2).apply(3);   // (3*2)+1 = 7

int base = 10;
// base++;                         // ❌ captured locals must be effectively final
Function<Integer, Integer> add = x -> x + base;
```

---

### 2.3 Streams — pipeline, laziness, common collectors

```text
 source ──► filter ──► map ──► sorted ──► collect
           └──── intermediate (LAZY) ───┘  └ terminal (TRIGGERS execution)

 Elements flow ONE BY ONE through the chain (vertical), not stage by stage:
   "a" → filter ✓ → map → collect
   "b" → filter ✗
   "c" → filter ✓ → map → collect
 (sorted/distinct are STATEFUL and must buffer everything)
```

```java
record Tx(String account, String type, BigDecimal amount) {}

Map<String, BigDecimal> totalPerAccount = txs.stream()
    .filter(t -> t.type().equals("DEBIT"))
    .collect(Collectors.groupingBy(Tx::account,
             Collectors.reducing(BigDecimal.ZERO, Tx::amount, BigDecimal::add)));

Map<Boolean, List<Tx>> bigOrSmall = txs.stream()
    .collect(Collectors.partitioningBy(t -> t.amount().compareTo(BigDecimal.valueOf(1000)) > 0));

// map vs flatMap
List<List<String>> nested = List.of(List.of("a","b"), List.of("c"));
nested.stream().map(List::size).toList();             // [2, 1]
nested.stream().flatMap(List::stream).toList();       // [a, b, c]

// toMap with duplicate keys → IllegalStateException unless merge function given
Map<String, Tx> byAcc = txs.stream()
    .collect(Collectors.toMap(Tx::account, t -> t, (a, b) -> b));
```

⚠️ Traps:
- A stream can be consumed **only once**.
- `peek` is for debugging; it may not run if the terminal op short-circuits.
- **Parallel streams** use the common `ForkJoinPool` — avoid for blocking I/O, small data, or ordered/stateful operations; they share threads with the whole JVM.
- `Stream.toList()` (16+) returns an unmodifiable list; `Collectors.toList()` doesn't guarantee mutability.

---

### 2.4 Optional — right and wrong

```java
// ✅ return type for "may be absent"
Optional<User> findByEmail(String email);

String city = userRepo.findByEmail(e)
        .map(User::address)
        .map(Address::city)
        .orElse("UNKNOWN");

User u = repo.findById(id).orElseThrow(() -> new NotFound(id));

// ❌ anti-patterns
Optional<User> field;                  // not for fields (not Serializable)
void save(Optional<User> u);           // not for parameters
if (opt.isPresent()) opt.get();        // just use map/orElse/ifPresent
opt.orElse(expensiveCall());           // ⚠️ ALWAYS evaluated → use orElseGet(() -> ...)
```

---

### 2.5 Records, sealed classes and pattern matching

```text
  sealed interface Shape permits Circle, Square, Rect
                │
     ┌──────────┼───────────┐
   Circle    Square       Rect      (records = final, immutable data carriers)

  switch over a sealed type ==> compiler checks EXHAUSTIVENESS (no default needed)
```

```java
sealed interface Shape permits Circle, Square, Rect {}
record Circle(double r) implements Shape {}
record Square(double side) implements Shape {}
record Rect(double w, double h) implements Shape {}

static double area(Shape s) {
    return switch (s) {                                   // Java 21
        case Circle c            -> Math.PI * c.r() * c.r();
        case Square(double side) -> side * side;          // record pattern
        case Rect(var w, var h) when w == h -> w * w;     // guard
        case Rect(var w, var h)  -> w * h;
    };
}

// instanceof pattern (16)
if (obj instanceof String str && !str.isBlank()) System.out.println(str.length());

// switch expression (14)
int days = switch (month) {
    case FEB -> 28;
    case APR, JUN, SEP, NOV -> 30;
    default -> { log.debug("31"); yield 31; }
};

// text block (15)
String sql = """
    SELECT id, name
    FROM customer
    WHERE status = 'ACTIVE'
    """;
```

💡 Records give you: private final fields, canonical constructor, accessors (`name()`), `equals/hashCode/toString`. They **can't** extend classes (implicitly extend `Record`), but can implement interfaces. ⚠️ Records are **not suitable as JPA entities** (need no-arg constructor, mutability, proxies) — use them as DTOs/projections.

---

### 2.6 Sequenced collections (Java 21)

```java
List<String> l = new ArrayList<>(List.of("a","b","c"));
l.getFirst(); l.getLast(); l.reversed();        // new uniform API
LinkedHashMap<String,Integer> m = new LinkedHashMap<>();
m.putFirst("x", 1); m.firstEntry(); m.pollLastEntry();
```

[⬆ Back to top](#table-of-contents)

## 3. JVM Memory Management and Garbage Collection

### 3.1 JVM memory areas (modern JVM)

```text
 ┌──────────────────────────── JVM process ─────────────────────────────┐
 │  SHARED by all threads                 PER THREAD                    │
 │  ┌──────────────────────────────┐      ┌──────────┐ ┌──────────┐     │
 │  │ HEAP  (-Xms / -Xmx)          │      │ Stack T1 │ │ Stack T2 │ ... │
 │  │  ┌─────────── Young ───────┐ │      │ frame()  │ │ frame()  │     │
 │  │  │ Eden │ S0 │ S1         │ │      │ locals   │ │ locals   │     │
 │  │  └─────────────────────────┘ │      │ operand  │ │ operand  │     │
 │  │  ┌─────────── Old ─────────┐ │      └──────────┘ └──────────┘     │
 │  │  │ long-lived objects      │ │      PC register, native stack     │
 │  │  └─────────────────────────┘ │                                    │
 │  │  String pool (Java 7+)       │                                    │
 │  └──────────────────────────────┘                                    │
 │  METASPACE (native memory, Java 8+; replaced PermGen)                │
 │    class metadata, method bytecode, constant pools                   │
 │  Code cache (JIT-compiled native code)   Direct buffers (NIO)        │
 └──────────────────────────────────────────────────────────────────────┘
```

| Item | Where |
|---|---|
| Object instances, arrays | Heap |
| Class metadata | Metaspace |
| Method frames, local primitives, references | Thread stack |
| String pool | Heap (since Java 7) |
| Static fields | Heap (inside the `Class` mirror object, since Java 8) |

Errors: `OutOfMemoryError: Java heap space` / `Metaspace` / `GC overhead limit exceeded` / `Direct buffer memory` / `unable to create native thread`; `StackOverflowError` (deep recursion).

---

### 3.2 Stack vs heap allocation

```java
void test() {
    int x = 10;
    Person p = new Person();
}
```

```text
   STACK (frame of test())          HEAP
   ┌───────────────────┐
   │ x = 10            │
   │ p = 0x100  ───────┼──────►  0x100: Person { name=null }
   └───────────────────┘
   frame popped when test() returns ==> Person has no references ==> GC-eligible
```

💡 Local primitives → stack. References → stack. Actual objects → heap. Interviewers want you to separate the **reference** from the **object**.

---

### 3.3 Java is always pass-by-value

💡 Java copies **whatever is in the variable**: for primitives → the number, for objects → the reference (address). You can change what's inside the object; you can't change which object the caller's variable points to.

**Case A — reassigning the parameter (no effect on caller)**

```java
void change(Person p) { p = new Person(); }
Person p1 = new Person();
change(p1);          // p1 unchanged
```

```text
 ① before call            ② during call (copy)        ③ after p = new Person()
 p1 ──► 0x100 Person@A    p1 ──► 0x100 Person@A       p1 ──► 0x100 Person@A
                          p  ──► 0x100 Person@A       p  ──► 0x200 Person@B (lost after return)
```

**Case B — primitive**

```java
void change(int x) { x = 10; }
int a = 5;
change(a);
System.out.println(a);   // 5
```

```text
 caller: a = 5      callee: x = 5 (copy) ──► x = 10      caller still: a = 5
```

**Case C — mutating the object (visible to caller)**

```java
void change(Person p) { p.name = "Ali"; }
Person p1 = new Person(); p1.name = "Bob";
change(p1);
System.out.println(p1.name);   // "Ali"
```

```text
 STACK                         HEAP
 caller: p1 = 0x100 ──┐
                      ├────►  0x100: Person { name = "Bob" → "Ali" }
 change: p  = 0x100 ──┘       both variables point to ONE shared object
```

**Bonus trick question**

```java
void tricky(Person p) {
    p.name = "Ali";       // mutates shared object ✅ visible
    p = new Person();     // p now points elsewhere
    p.name = "Zara";      // changes the NEW object only ❌ invisible
}
Person p1 = new Person(); p1.name = "Bob";
tricky(p1);
System.out.println(p1.name);   // "Ali"
```

**Array version**

```java
void m(int[] arr) { arr[0] = 99; arr = new int[]{7}; arr[0] = 1; }
int[] a = {1, 2};
m(a);
System.out.println(a[0]);      // 99
```

---

### 3.4 final reference vs final object

```java
final Person p = new Person();
p.name = "Sara";        // ✅ allowed — object state can change
p = new Person();       // ❌ compile error — reference is locked

final List<String> list = new ArrayList<>();
list.add("x");          // ✅ still allowed
```

```text
  final p ══(locked arrow)══► Person { name = mutable }
```

💡 `final` locks the **reference**, not the **object**. For an unchangeable object, make the object immutable.

---

### 3.5 When is an object eligible for GC? (reachability)

💡 GC is about **reachability from GC roots**, not variable names, not reference counting.

```text
  GC ROOTS: local vars on thread stacks, static fields, active threads,
            JNI refs, monitors held by synchronized
       │
       ▼
   [A] ──► [B] ──► [C]        reachable  → kept
   [X] ◄──► [Y]               unreachable island → collected (even with a cycle)
```

```java
// Q: what is collectable?
Person p1 = new Person();
Person p2 = p1;
p1 = null;            // nothing — object still reachable via p2

Person a = new Person();      // object #1
Person b = new Person();      // object #2
a = b;                        // object #1 now unreachable → eligible ✅

class A { B b; }  class B { A a; }
A x = new A(); B y = new B();
x.b = y; y.a = x;
x = null; y = null;           // both eligible ✅ — cycle doesn't matter (not ref-counting)
```

⚠️ `System.gc()` is only a **hint**; there is no guarantee GC runs.

---

### 3.6 String pool: literal vs new String vs intern()

```java
String a = "java";
String b = "java";
String c = new String("java");
String d = c.intern();

a == b       // true  — same pooled literal
a == c       // false — new always creates a heap object
a == d       // true  — intern() returns the pooled reference
a.equals(c)  // true  — compare content with equals ✅

String s1 = "ja" + "va";           // compile-time constant → pooled
s1 == a                            // true
String part = "ja";
String s2 = part + "va";           // runtime concatenation → new object
s2 == a                            // false
```

```text
        HEAP
  ┌──────────────── String pool ──────────────┐
  │   "java" ◄──── a, b, d, s1                │
  └───────────────────────────────────────────┘
      String("java") ◄──── c     (separate object)
      String("java") ◄──── s2    (separate object)
```

⚠️ `new String("java")` may create **two** objects: the literal in the pool (if not already there) plus the new heap object.

---

### 3.7 Autoboxing and the Integer cache

```java
Integer a = 127, b = 127;
a == b          // true  — Integer.valueOf caches -128..127
Integer c = 128, d = 128;
c == d          // false — outside cache → different objects
c.equals(d)     // true  ✅

Integer e = 100; int f = 100;
e == f          // true  — e is UNBOXED, primitive comparison

Integer n = null;
int x = n;      // ❌ NullPointerException on unboxing

Long sum = 0L;
for (long i = 0; i < 1_000_000; i++) sum += i;   // ❌ creates ~1M Long objects
long fast = 0;                                   // ✅ use primitives in hot loops
```

```text
 Integer cache:  [-128 ... 0 ... 127]  pre-created, shared
 valueOf(128)  ──► new Integer(128) every time
 (upper bound configurable: -XX:AutoBoxCacheMax=<n>)
```

---

### 3.8 Generational heap and how GC moves objects

```text
  new objects
      │
      ▼
 ┌──────── YOUNG GEN ─────────┐        ┌────────── OLD GEN ──────────┐
 │  Eden        │ S0  │ S1    │        │                             │
 │ ●●●●●●●●●    │ ●●  │       │ ─────► │  objects that survived N    │
 └──────────────┴─────┴───────┘ promote│  minor GCs (tenuring        │
  Minor GC (frequent, fast):           │  threshold, max 15)         │
   live Eden + S0 ──copy──► S1         │  Major/Full GC (rare, slow) │
   Eden cleared, S0/S1 swap roles      └─────────────────────────────┘

 Weak generational hypothesis: MOST objects die young
   ==> collecting young gen often is cheap (copy only the few survivors)
```

Phases of a typical collector: **mark** (find live objects from roots) → **sweep** (free dead) → **compact** (defragment). **Stop-The-World (STW)** = all application threads paused.

Large objects (`new byte[10_000_000]`) may be allocated **directly in Old Gen**; in G1 they become **humongous** objects (≥ 50% of a region) to avoid expensive copying.

---

### 3.9 Garbage collectors compared

| Collector | Flag | Idea | Pauses | Use for |
|---|---|---|---|---|
| Serial | `-XX:+UseSerialGC` | single thread, STW | long | tiny heaps, containers with 1 CPU |
| Parallel | `-XX:+UseParallelGC` | multi-thread STW, max throughput | medium | batch jobs |
| CMS | (removed in Java 14) | concurrent mark-sweep, no compaction → fragmentation | short | legacy |
| **G1** | `-XX:+UseG1GC` (default since 9) | heap split in regions, collects "garbage-first" regions, pause target | predictable (~`MaxGCPauseMillis=200`) | general server apps |
| **ZGC** | `-XX:+UseZGC` (generational in 21, default mode from 23) | concurrent, colored pointers + load barriers | < 1 ms, independent of heap size | low latency, huge heaps |
| Shenandoah | `-XX:+UseShenandoahGC` | concurrent compaction | very low | low latency |
| Epsilon | `-XX:+UseEpsilonGC` | no-op GC | none | performance testing |

```text
  G1 heap = many equal regions (1–32 MB)
  ┌──┬──┬──┬──┬──┬──┬──┬──┐
  │E │O │E │S │O │H │H │  │   E=Eden S=Survivor O=Old H=Humongous
  ├──┼──┼──┼──┼──┼──┼──┼──┤
  │O │  │E │O │  │O │E │S │   G1 collects regions with MOST garbage first
  └──┴──┴──┴──┴──┴──┴──┴──┘
```

**Typical tuning flags**

```bash
java -Xms2g -Xmx2g                         # same min/max → no resize pauses
     -XX:+UseG1GC -XX:MaxGCPauseMillis=200
     -XX:MaxRAMPercentage=75                # in containers instead of fixed -Xmx
     -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/dumps
     -Xlog:gc*:file=gc.log:time,uptime      # unified GC logging (Java 9+)
     -Xss512k                               # thread stack size
     -XX:MaxMetaspaceSize=256m
     -jar app.jar
```

💡 Senior answer: "First measure (GC logs, JFR, p99 latency), then choose: throughput → Parallel; balanced → G1; strict latency → ZGC/Shenandoah. Tune heap size before exotic flags."

---

### 3.10 Escape analysis

```java
public Person create() {                 // object ESCAPES (returned) → must live on heap
    Person p = new Person();
    return p;
}

public int localOnly() {                 // object does NOT escape
    Point pt = new Point(1, 2);          // JIT may apply SCALAR REPLACEMENT:
    return pt.x() + pt.y();              // fields become locals, no heap allocation at all
}
```

```text
  escapes?  ── yes (returned / stored in field / passed to other thread) ──► heap
            └─ no  ──► JIT (C2) may: scalar-replace, eliminate locks (lock elision)
```

⚠️ Precise wording: HotSpot doesn't literally put the object "on the stack"; it **removes the allocation** via scalar replacement.

---

### 3.11 Reference types: strong, soft, weak, phantom

```text
 Strength   Collected when…                         Typical use
 ────────   ─────────────────────────────────────   ────────────────────────────
 Strong     never while reachable                   normal references
 Soft       only under MEMORY PRESSURE (before OOM) memory-sensitive caches
 Weak       at the NEXT GC if only weakly reachable WeakHashMap, canonical maps
 Phantom    after finalization, get() always null   cleanup hooks (Cleaner)
```

```java
WeakReference<Person> weak = new WeakReference<>(new Person());
System.gc();
weak.get();                  // likely null — weak refs don't prevent GC

SoftReference<byte[]> img = new SoftReference<>(loadImage());
byte[] data = img.get();     // may be null if memory got tight → reload

Map<Key, Meta> meta = new WeakHashMap<>();   // entry vanishes when key unreachable
```

---

### 3.12 finalize() vs Cleaner vs AutoCloseable

```java
// ❌ finalize: not guaranteed to run, slows GC, can resurrect objects; deprecated (Java 9, for removal 18)
// ✅ deterministic: AutoCloseable + try-with-resources
// ✅ safety net: Cleaner
class NativeBuffer implements AutoCloseable {
    private static final Cleaner CLEANER = Cleaner.create();
    private final Cleaner.Cleanable cleanable;
    NativeBuffer() { long addr = allocate(); cleanable = CLEANER.register(this, () -> free(addr)); }
    @Override public void close() { cleanable.clean(); }   // runs at most once
}
```

---

### 3.13 Memory leaks in Java

💡 A Java memory leak = objects that are **still reachable but no longer needed** (unintended retention). It is not about `static` only.

```text
 Common leak sources
 ├─ ever-growing collections (static or long-lived singleton caches without eviction)
 ├─ ThreadLocal in thread pools without remove()
 ├─ listeners/callbacks registered but never unregistered
 ├─ inner/anonymous classes holding the outer instance
 ├─ unclosed resources (streams, connections, ResultSets)
 ├─ mutable HashMap keys (entry unreachable by get, but still stored)
 ├─ ClassLoader leaks on redeploy (static refs to app classes from server classes)
 └─ Hibernate session holding thousands of managed entities (no clear() in batch)
```

```java
// leak without static: a long-lived singleton bean
@Service class AuditBuffer {
    private final List<Event> events = new ArrayList<>();
    void add(Event e) { events.add(e); }   // ❌ never drained → grows forever
}

// static field lives as long as its ClassLoader
class Cache { static final List<String> DATA = new ArrayList<>(); }
```

**How to investigate (senior answer)**

```text
 1. Observe: heap after each full GC keeps rising (sawtooth with rising floor)
       ▲ heap
       │   /|  /|  /|  /|
       │  / | / | / |/ |    ← floor goes up ==> leak
       │ /  |/  |/       
       └──────────────────► time
 2. Capture:  jcmd <pid> GC.heap_dump /tmp/heap.hprof
              (or -XX:+HeapDumpOnOutOfMemoryError)
              jcmd <pid> GC.class_histogram  (quick: which classes grow)
 3. Analyze:  Eclipse MAT → "Leak Suspects", dominator tree, path to GC roots
              VisualVM / JProfiler / YourKit / JFR + JDK Mission Control
 4. Fix:      bounded caches (Caffeine with maximumSize/expireAfter), remove(), close()
```

---

### 3.14 ThreadLocal memory leak

```text
 Thread (pooled, lives forever)
   └─ threadLocals: ThreadLocalMap
         Entry[ key = WeakReference<ThreadLocal> , value = STRONG ref ──► BigObject ]
                     │
   static ThreadLocal set to null / class unloaded ==> key cleared (weak)
   BUT value is still strongly held by the thread ==> leak until thread dies
   In a pool, the thread never dies ==> leak + data from previous request leaks to next request!
```

```java
private static final ThreadLocal<UserContext> CTX = new ThreadLocal<>();

void handle(Request r) {
    CTX.set(new UserContext(r.user()));
    try {
        process(r);
    } finally {
        CTX.remove();          // ✅ ALWAYS remove in finally when using pools
    }
}
```

---

### 3.15 Class loading and the delegation model

```text
            Bootstrap ClassLoader   (java.base: java.lang.*, in native code)
                     ▲
            Platform ClassLoader    (other JDK modules; "Extension" before Java 9)
                     ▲
            Application ClassLoader (your classpath / module path)
                     ▲
            Custom loaders (Tomcat webapp, Spring Boot LaunchedClassLoader)

 loadClass("X"): ask PARENT first ──► only if parent can't find it, load yourself
 ==> core classes can't be replaced by your own java.lang.String ✅

 Lifecycle: Loading → Linking (verify, prepare, resolve) → Initialization (static blocks)
```

⚠️ Same class name loaded by two loaders = **two different classes** → `ClassCastException: X cannot be cast to X`.

[⬆ Back to top](#table-of-contents)

## 4. Multithreading and Concurrency

### 4.1 Thread lifecycle and states

```text
                 start()
   ┌─────┐  ───────────────►  ┌──────────────────────────┐
   │ NEW │                    │ RUNNABLE                 │
   └─────┘                    │ (ready ⇄ running on CPU, │
                              │  scheduler decides)      │
                              └──┬───────┬────────┬──────┘
          waiting for monitor    │       │        │  run() ends / exception
          (synchronized)         ▼       │        ▼
                         ┌─────────┐     │   ┌────────────┐
                         │ BLOCKED │     │   │ TERMINATED │
                         └─────────┘     │   └────────────┘
                   wait(), join(),       │   sleep(ms), wait(ms),
                   LockSupport.park()    │   join(ms), parkNanos()
                         ┌─────────┐     │     ┌───────────────┐
                         │ WAITING │◄────┴────►│ TIMED_WAITING │
                         └─────────┘           └───────────────┘
```

💡 Java's `Thread.State` has **no RUNNING** state — "running" is inside RUNNABLE. BLOCKED = waiting for a `synchronized` monitor; WAITING = waiting for another thread's action.

```java
Thread t = Thread.ofPlatform().name("worker").start(() -> doWork());   // Java 21 builder
Runnable r = () -> doWork();                    // no result, no checked exception
Callable<Integer> c = () -> compute();          // returns value, can throw
```

⚠️ `t.run()` executes on the **current** thread; only `t.start()` creates a new thread. Calling `start()` twice → `IllegalThreadStateException`.

---

### 4.2 Java Memory Model: visibility, ordering, atomicity

```text
     CPU core 1                       CPU core 2
   ┌─────────────┐                  ┌─────────────┐
   │ Thread A    │                  │ Thread B    │
   │ cache: flag=true               │ cache: flag=false  ◄── stale value!
   └──────┬──────┘                  └──────┬──────┘
          └────────────► Main memory ◄─────┘
                          flag = ?

 Three problems the JMM defines:
   VISIBILITY : a write by A may never be seen by B
   ORDERING   : compiler/CPU may reorder instructions
   ATOMICITY  : count++ is read-modify-write (3 steps), not one
```

**Happens-before rules (memorize these)**

```text
 1. Program order      : each action in a thread HB later actions in that thread
 2. Monitor lock       : unlock(m)  HB  every later lock(m)
 3. Volatile           : write(v)   HB  every later read(v)
 4. Thread start       : t.start()  HB  any action in t
 5. Thread join        : all actions in t  HB  t.join() returning
 6. Transitivity       : A HB B and B HB C  ==>  A HB C
 (+ final fields: correctly constructed object's finals are visible to all threads)
```

```java
class Worker {
    private volatile boolean running = true;   // without volatile, loop may never stop
    void stop() { running = false; }
    void loop() { while (running) { /* work */ } }
}

// volatile piggy-backing: everything written BEFORE the volatile write is visible
int data; volatile boolean ready;
// Thread A:            data = 42; ready = true;
// Thread B:            if (ready) assert data == 42;   ✅ guaranteed by HB
```

---

### 4.3 synchronized, intrinsic locks, wait/notify

```text
  Each object has a MONITOR:
  ┌───────────────── monitor of obj ──────────────────┐
  │ owner: Thread A                                   │
  │ entry set  : [B, C]  (BLOCKED, want the lock)     │
  │ wait set   : [D]     (WAITING, called wait())     │
  └───────────────────────────────────────────────────┘
  notify()    → moves ONE thread from wait set to entry set
  notifyAll() → moves ALL
```

```java
public synchronized void inc() { count++; }           // locks "this"
public static synchronized void sInc() { }            // locks Counter.class
public void inc2() { synchronized (lock) { count++; } } // private final Object lock ✅

// Classic guarded wait — ALWAYS in a while loop (spurious wakeups)
synchronized (queue) {
    while (queue.isEmpty()) queue.wait();             // releases the monitor while waiting
    item = queue.poll();
}
synchronized (queue) { queue.add(x); queue.notifyAll(); }
```

💡 `synchronized` gives **mutual exclusion + visibility** and is **reentrant** (same thread can re-acquire). `wait()` releases the lock; `sleep()` does **not**.

⚠️ Don't lock on String literals, boxed values, or `this` of public objects — anyone can lock on them too.

---

### 4.4 volatile vs synchronized vs Atomic

| | visibility | atomic compound ops (`i++`) | blocking | use for |
|---|---|---|---|---|
| `volatile` | ✅ | ❌ | no | flags, publish-once references |
| `synchronized` / `Lock` | ✅ | ✅ | yes | multi-variable invariants |
| `AtomicInteger` etc. (CAS) | ✅ | ✅ (single variable) | no (lock-free) | counters, sequences |
| `LongAdder` | ✅ | ✅ | no | very high-contention counters |

```text
 CAS (compare-and-swap) loop inside AtomicInteger.incrementAndGet():
   do {
     old = value;              read 5
     new = old + 1;            compute 6
   } while (!CAS(value, old, new));   if value is still 5 → set 6, else retry
```

```java
AtomicInteger counter = new AtomicInteger();
counter.incrementAndGet();
counter.updateAndGet(x -> Math.max(x, 10));
AtomicReference<Config> cfg = new AtomicReference<>(initial);
cfg.compareAndSet(old, updated);

LongAdder hits = new LongAdder();   // spreads updates over cells, sum() at read time
hits.increment();
```

⚠️ ABA problem with CAS: value changes A→B→A and CAS succeeds incorrectly → use `AtomicStampedReference`.

---

### 4.5 Double-checked locking singleton

```java
public final class Registry {
    private static volatile Registry instance;          // volatile is REQUIRED
    private Registry() {}
    public static Registry get() {
        Registry r = instance;
        if (r == null) {                                // 1st check (no lock)
            synchronized (Registry.class) {
                r = instance;
                if (r == null) instance = r = new Registry();   // 2nd check
            }
        }
        return r;
    }
}
```

```text
 Why volatile? "instance = new Registry()" is 3 steps:
   1. allocate memory   2. run constructor   3. assign reference
 CPU may reorder to 1 → 3 → 2 ==> other thread sees non-null, HALF-BUILT object ❌
```

✅ Simpler alternatives: `enum Registry { INSTANCE; }` or the holder idiom:

```java
class Registry { private static class Holder { static final Registry I = new Registry(); }
                 static Registry get() { return Holder.I; } }   // lazy + thread-safe via class init
```

---

### 4.6 Explicit locks: ReentrantLock, ReadWriteLock, StampedLock

| Feature | `synchronized` | `ReentrantLock` |
|---|---|---|
| try without blocking | ❌ | `tryLock()`, `tryLock(timeout)` |
| interruptible wait | ❌ | `lockInterruptibly()` |
| fairness | ❌ | `new ReentrantLock(true)` |
| multiple conditions | one wait set | `newCondition()` many |
| auto release | ✅ (block end) | ❌ must `unlock()` in `finally` |

```java
private final ReentrantLock lock = new ReentrantLock();
void transfer() {
    lock.lock();
    try { /* critical section */ }
    finally { lock.unlock(); }          // ✅ always in finally
}

// Many readers, few writers
private final ReadWriteLock rw = new ReentrantReadWriteLock();
V get(K k) { rw.readLock().lock();  try { return map.get(k); } finally { rw.readLock().unlock(); } }
void put(K k, V v) { rw.writeLock().lock(); try { map.put(k, v); } finally { rw.writeLock().unlock(); } }

// StampedLock optimistic read (not reentrant!)
long stamp = sl.tryOptimisticRead();
double x = this.x, y = this.y;
if (!sl.validate(stamp)) { stamp = sl.readLock(); try { x = this.x; y = this.y; } finally { sl.unlockRead(stamp); } }
```

```text
 ReadWriteLock:   R R R R  (parallel)  |  W (exclusive)  |  R R ...
```

---

### 4.7 Deadlock — when it happens, detection, resolution

💡 Deadlock = two or more threads each hold a lock the other needs, waiting forever. **Four Coffman conditions** must ALL hold:

```text
 1. Mutual exclusion   – resource held by one thread at a time
 2. Hold and wait      – holding one lock while waiting for another
 3. No preemption      – locks can't be forcibly taken
 4. Circular wait      – T1 → waits for T2 → waits for T1
 Break ANY one ==> no deadlock (usually #4 via lock ordering, or #3 via tryLock)

     Thread 1                    Thread 2
   holds R1 ─── wants R2 ──┐  ┌── wants R1 ─── holds R2
                           ▼  ▼
                     ⛔ circular wait ⛔
```

```java
// ❌ deadlock: opposite lock order
Thread t1 = new Thread(() -> { synchronized (r1) { sleep(100); synchronized (r2) { } } });
Thread t2 = new Thread(() -> { synchronized (r2) { sleep(100); synchronized (r1) { } } });

// ✅ Fix A: global lock ordering (e.g., by account id)
void transfer(Account a, Account b, BigDecimal amt) {
    Account first  = a.id() < b.id() ? a : b;
    Account second = a.id() < b.id() ? b : a;
    synchronized (first) { synchronized (second) { a.debit(amt); b.credit(amt); } }
}

// ✅ Fix B: tryLock with timeout, back off and retry
if (l1.tryLock(50, MILLISECONDS)) {
    try {
        if (l2.tryLock(50, MILLISECONDS)) {
            try { /* work */ } finally { l2.unlock(); }
        } else { /* back off, retry later */ }
    } finally { l1.unlock(); }
}

// Detection at runtime
ThreadMXBean mx = ManagementFactory.getThreadMXBean();
long[] ids = mx.findDeadlockedThreads();          // null if none
```

```text
 Detect in production:
   jstack <pid>   or   jcmd <pid> Thread.print
   ──► "Found one Java-level deadlock:" + both stack traces
   Also: VisualVM / JMC thread view, thread dumps 3x a few seconds apart
```

Prevention checklist: consistent lock order · `tryLock` with timeout · short critical sections · no calls to foreign/unknown code while holding a lock · prefer `java.util.concurrent` structures · dedicated `private final` lock objects. **Database deadlocks** are similar — the DB detects them and aborts one transaction (retry it).

---

### 4.8 Livelock and starvation

```text
 Deadlock   : threads BLOCKED forever, no progress        (two people frozen)
 Livelock   : threads ACTIVE, keep reacting to each other, no progress
              (two people in a corridor both step aside the same way forever)
              fix: random back-off / jitter
 Starvation : a thread never gets CPU/lock because others always win
              (greedy threads, unfair locks, priority)
              fix: fair locks, bounded work, avoid long lock holding
```

---

### 4.9 ThreadLocal

💡 Each thread has its **own independent copy** of the variable — no sharing, no synchronization. Used for per-request context: user/security context (Spring's `SecurityContextHolder`), transaction/session binding (Spring `TransactionSynchronizationManager`), MDC logging, non-thread-safe objects like `SimpleDateFormat`.

```text
  ThreadLocal<Integer> tl
   Thread-1.threadLocals: { tl → 42 }
   Thread-2.threadLocals: { tl → 7  }     same tl object, different values per thread
```

```java
private static final ThreadLocal<Integer> VALUE = ThreadLocal.withInitial(() -> 0);

Runnable task = () -> {
    System.out.println(Thread.currentThread().getName() + " initial: " + VALUE.get()); // 0
    VALUE.set(ThreadLocalRandom.current().nextInt(100));
    System.out.println(Thread.currentThread().getName() + " new: " + VALUE.get());
    VALUE.remove();                                    // ✅ clean up
};
new Thread(task).start(); new Thread(task).start();
```

⚠️ Pitfalls: leaks in pools (see 3.14), context not propagated to `@Async`/`CompletableFuture` threads, and with **virtual threads** (millions of threads → millions of copies). Java 25: **ScopedValue** is the modern immutable alternative.

---

### 4.10 Executors and ThreadPoolExecutor internals

```text
 submit(task)
     │
     ▼
 running threads < corePoolSize ? ── yes ──► create new worker thread
     │ no
     ▼
 queue.offer(task) succeeds ? ── yes ──► task waits in queue
     │ no (queue full)
     ▼
 running threads < maximumPoolSize ? ── yes ──► create extra thread
     │ no
     ▼
 RejectedExecutionHandler:
   AbortPolicy (default, throws) | CallerRunsPolicy (back-pressure) |
   DiscardPolicy | DiscardOldestPolicy
```

```java
ExecutorService pool = new ThreadPoolExecutor(
        4, 16,                               // core, max
        60, TimeUnit.SECONDS,                // idle keep-alive for extra threads
        new ArrayBlockingQueue<>(1_000),     // ✅ BOUNDED queue
        Thread.ofPlatform().name("tx-", 0).factory(),
        new ThreadPoolExecutor.CallerRunsPolicy());

Future<Integer> f = pool.submit(() -> compute());
Integer result = f.get(2, TimeUnit.SECONDS);   // blocks; TimeoutException

pool.shutdown();                                // stop accepting, finish queued
if (!pool.awaitTermination(30, SECONDS)) pool.shutdownNow();   // interrupt running
```

| Factory | Hidden risk |
|---|---|
| `newFixedThreadPool(n)` | **unbounded** `LinkedBlockingQueue` → OOM under load |
| `newCachedThreadPool()` | **unbounded threads** → thread explosion |
| `newSingleThreadExecutor()` | unbounded queue |
| `newScheduledThreadPool(n)` | periodic tasks; exception silently cancels the schedule |
| `newVirtualThreadPerTaskExecutor()` | Java 21, one virtual thread per task |

💡 Pool sizing: CPU-bound ≈ `cores (+1)`; I/O-bound ≈ `cores × (1 + wait/compute)`. ⚠️ Exceptions in `submit()` tasks are captured in the `Future` — if you never call `get()`, they disappear silently.

---

### 4.11 Future vs CompletableFuture

```text
 Future            : get() blocks, no chaining, no combine, no callback
 CompletableFuture : non-blocking pipeline

   supplyAsync(fetchUser) ──thenApply(toDto)──┐
                                              ├─ thenCombine ─► thenAccept(send)
   supplyAsync(fetchCards) ───────────────────┘        │
                                         exceptionally / handle (fallback)
```

```java
ExecutorService io = Executors.newFixedThreadPool(20);   // ✅ own pool for blocking I/O

CompletableFuture<User>  user  = CompletableFuture.supplyAsync(() -> userClient.get(id), io);
CompletableFuture<Cards> cards = CompletableFuture.supplyAsync(() -> cardClient.get(id), io);

CompletableFuture<Profile> profile = user
        .thenCombine(cards, Profile::new)                   // both results
        .orTimeout(2, TimeUnit.SECONDS)                     // Java 9
        .exceptionally(ex -> Profile.empty());              // fallback

// thenApply   : sync transform  T → R
// thenCompose : async flatMap   T → CompletableFuture<R>  (avoid CF<CF<R>>)
// thenAccept  : consume, no result
// allOf / anyOf : wait for many / first
CompletableFuture.allOf(f1, f2, f3).join();
```

⚠️ Without an executor argument, async stages run on `ForkJoinPool.commonPool()` (size = cores-1) — blocking I/O there starves the whole JVM (and parallel streams).

---

### 4.12 Synchronizers: CountDownLatch, CyclicBarrier, Semaphore, Phaser

| Tool | Picture | Reusable | Typical use |
|---|---|---|---|
| `CountDownLatch(n)` | gate opens when count hits 0 | ❌ | wait for N services to start |
| `CyclicBarrier(n)` | N threads meet at a point, then all continue | ✅ | parallel simulation steps |
| `Semaphore(n)` | n permits | ✅ | limit concurrent DB/API calls |
| `Phaser` | dynamic barrier with phases | ✅ | variable number of parties |
| `Exchanger` | two threads swap objects | ✅ | pipeline buffers |

```text
 CountDownLatch(3):  W1 ─countDown─┐
                     W2 ─countDown─┼─► 0 ─► main.await() returns
                     W3 ─countDown─┘
 Semaphore(2):  [permit][permit]  T1 ✓ T2 ✓ T3 waits… T1 releases → T3 ✓
```

```java
CountDownLatch ready = new CountDownLatch(3);
for (int i = 0; i < 3; i++) pool.submit(() -> { init(); ready.countDown(); });
ready.await(10, TimeUnit.SECONDS);

Semaphore limit = new Semaphore(10);
limit.acquire();
try { callExternalApi(); } finally { limit.release(); }
```

---

### 4.13 Fork/Join framework and work stealing

```text
             sum(0..1M)
           /            \           fork(): split until small enough (threshold)
     sum(0..500k)    sum(500k..1M)  compute(): solve directly
       /     \          /     \     join(): combine results
     ...     ...      ...     ...

 Work stealing: each worker has a DEQUE; idle workers steal from the TAIL of busy ones
   W1: [t1 t2 t3 t4] ◄── W2 (idle) steals t4
```

```java
class SumTask extends RecursiveTask<Long> {
    private final long[] a; private final int lo, hi;
    SumTask(long[] a, int lo, int hi) { this.a = a; this.lo = lo; this.hi = hi; }
    @Override protected Long compute() {
        if (hi - lo <= 10_000) { long s = 0; for (int i = lo; i < hi; i++) s += a[i]; return s; }
        int mid = (lo + hi) >>> 1;
        SumTask left = new SumTask(a, lo, mid);
        left.fork();                                        // async
        long right = new SumTask(a, mid, hi).compute();     // current thread
        return right + left.join();
    }
}
long total = ForkJoinPool.commonPool().invoke(new SumTask(arr, 0, arr.length));
```

---

### 4.14 Concurrent collections and ConcurrentHashMap internals

```text
 Collections.synchronizedMap / Hashtable : ONE lock for the whole map  ──► contention
 ConcurrentHashMap (Java 8+):
   table[]  [0]  [1]  [2]  [3] ...
             │    │
             │    └─ empty bin: insert with CAS (no lock)
             └─ non-empty bin: synchronized on FIRST NODE of that bin only
   reads: no locking (volatile reads)          size(): summed counter cells
   (Java 7 used 16 Segments — old interview answer)
```

```java
ConcurrentHashMap<String, LongAdder> counts = new ConcurrentHashMap<>();
counts.computeIfAbsent(word, k -> new LongAdder()).increment();   // ✅ atomic per key

// ❌ check-then-act race, even on a concurrent map
if (!map.containsKey(k)) map.put(k, v);
// ✅
map.putIfAbsent(k, v);
map.merge(k, 1, Integer::sum);
```

| Collection | Notes |
|---|---|
| `ConcurrentHashMap` | no `null` keys/values; weakly consistent iterators (no CME) |
| `CopyOnWriteArrayList` | copies array on every write → many reads, rare writes (listeners) |
| `ConcurrentLinkedQueue` | lock-free non-blocking queue |
| `ConcurrentSkipListMap` | sorted concurrent map |
| `ArrayBlockingQueue` | bounded, one lock |
| `LinkedBlockingQueue` | optionally bounded, two locks (put/take) |
| `PriorityBlockingQueue` | unbounded, ordered |
| `DelayQueue` / `SynchronousQueue` | delayed elements / direct hand-off (no capacity) |

**Producer–consumer with BlockingQueue**

```java
BlockingQueue<Order> q = new ArrayBlockingQueue<>(100);
// producer
q.put(order);              // blocks when full  (back-pressure)
// consumer
Order o = q.take();        // blocks when empty
```

---

### 4.15 Virtual threads (Java 21)

```text
 Platform threads: 1 Java thread = 1 OS thread (~1 MB stack, expensive, thousands max)

 Virtual threads:  millions of cheap Java threads multiplexed on few CARRIER threads
   VT1 VT2 VT3 VT4 VT5 ... VT1_000_000
     \   |   |   /
   [carrier-1] [carrier-2] ... (ForkJoinPool, ≈ #cores)
   On blocking I/O the VT is UNMOUNTED (stack saved to heap) → carrier runs another VT
```

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    IntStream.range(0, 10_000).forEach(i ->
        executor.submit(() -> { Thread.sleep(Duration.ofSeconds(1)); return i; }));
}   // 10k concurrent blocking tasks, done in ~1 s

Thread.startVirtualThread(() -> handle(request));
// Spring Boot 3.2+: spring.threads.virtual.enabled=true
```

💡 Good for **I/O-bound, thread-per-request** code; no benefit for CPU-bound work. Don't pool virtual threads. Limit concurrency with a `Semaphore`, not pool size.

⚠️ **Pinning**: in Java 21–23, blocking inside `synchronized` pins the VT to its carrier (use `ReentrantLock` instead); fixed in Java 24 (JEP 491). Native calls still pin. Big `ThreadLocal` values × millions of threads = memory pressure.

---

### 4.16 Concurrency best practices (senior summary)

```text
 ✅ Prefer immutability and confinement (no sharing = no problem)
 ✅ Use high-level tools: executors, concurrent collections, CompletableFuture
 ✅ Bounded queues + rejection policy = back-pressure
 ✅ Always: unlock in finally, ThreadLocal.remove() in finally, shutdown executors
 ✅ Name your threads; handle InterruptedException properly:
       catch (InterruptedException e) { Thread.currentThread().interrupt(); return; }
 ✅ Measure: thread dumps, JFR, contention profiling
 ❌ Don't call alien methods while holding a lock
 ❌ Don't block on commonPool
 ❌ Don't rely on Thread priorities or sleep() for coordination
```

[⬆ Back to top](#table-of-contents)

## 5. Collections Framework

### 5.1 Collections hierarchy

```text
                         Iterable
                            │
                        Collection                         Map (separate tree)
          ┌─────────────────┼──────────────────┐            ├─ HashMap
         List              Set               Queue          │    └─ LinkedHashMap
          ├─ ArrayList      ├─ HashSet         ├─ PriorityQueue ├─ TreeMap (SortedMap/NavigableMap)
          ├─ LinkedList ◄───┼──────────────────┤ Deque       ├─ Hashtable (legacy)
          ├─ Vector(legacy) ├─ LinkedHashSet   │  ├─ ArrayDeque├─ ConcurrentHashMap
          └─ CopyOnWrite... ├─ TreeSet         │  └─ LinkedList├─ WeakHashMap
                            └─ EnumSet         └─ BlockingQueue ├─ IdentityHashMap
                                                                └─ EnumMap
 Java 21: SequencedCollection / SequencedMap added above List, Deque, LinkedHashSet, LinkedHashMap
```

💡 `Map` does **not** extend `Collection`. `LinkedList` is both a `List` and a `Deque`.

---

### 5.2 HashMap internals

```text
 put("card", v)
   1. h = key.hashCode()
   2. spread: hash = h ^ (h >>> 16)           (mix high bits into low bits)
   3. index = (n - 1) & hash                  (n = table length, power of 2)
   4. bin empty → place node
      bin used  → walk chain: equals()? replace value : append node

 table (n = 16)
  [0] → null
  [1] → (k1,v1) → (k9,v9) → (k17,v17)         linked list
  [2] → null
  [5] → ⟨red-black tree⟩                      when a bin has ≥ 8 nodes AND n ≥ 64
  ...                                          (back to list when ≤ 6)

 Defaults: capacity 16, load factor 0.75 → threshold 12
 size > capacity × loadFactor ==> RESIZE (double), nodes split into "lo" and "hi" buckets
```

| Operation | Average | Worst (Java 8+) |
|---|---|---|
| get / put / remove | O(1) | O(log n) with treeified bins (O(n) before Java 8) |

```java
Map<String, Integer> m = new HashMap<>(expectedSize * 4 / 3 + 1);   // avoid resizes
// Java 19+: HashMap.newHashMap(expectedSize)

m.getOrDefault("x", 0);
m.computeIfAbsent("list", k -> new ArrayList<>());
m.merge("count", 1, Integer::sum);
```

Facts: one `null` key allowed (always bucket 0); not thread-safe (concurrent resize in Java 7 could create an infinite loop; in Java 8+ you "only" lose data).

---

### 5.3 Mutable key trap

```java
List<String> key = new ArrayList<>(List.of("a"));
Map<List<String>, String> map = new HashMap<>();
map.put(key, "value");
key.add("b");                    // hashCode changes!
map.get(key);                    // null ❌ — looks in the wrong bucket
map.size();                      // 1 — entry still there (leak)
```

```text
 put:  hash(["a"])     → bucket 3   (entry stored here)
 get:  hash(["a","b"]) → bucket 7   (empty) ==> null
```

💡 Use immutable keys (`String`, `Integer`, records with immutable fields, enums).

---

### 5.4 Performance characteristics: List, Set, Map

| List | get(i) | add at end | add/remove in middle | contains |
|---|---|---|---|---|
| `ArrayList` | O(1) | O(1) amortized (grows 1.5x) | O(n) shift | O(n) |
| `LinkedList` | O(n) | O(1) | O(1) only if you hold the node/iterator, else O(n) | O(n) |
| `ArrayDeque` | — | O(1) both ends | — | O(n) |

| Set / Map | get / put / contains | order | requires |
|---|---|---|---|
| `HashSet` / `HashMap` | O(1) avg | none | `equals` + `hashCode` |
| `LinkedHashSet` / `LinkedHashMap` | O(1) | insertion (or access) order | `equals` + `hashCode` |
| `TreeSet` / `TreeMap` | O(log n) | sorted (red-black tree) | `Comparable` or `Comparator` |
| `EnumSet` / `EnumMap` | O(1), very fast | enum ordinal | enum keys |

```text
 ArrayList                     LinkedList
 [a][b][c][d][ ][ ]            null ◄─[a]⇄[b]⇄[c]⇄[d]─► null
 contiguous, CPU-cache         each node = object + 2 pointers
 friendly ✅                   poor locality, more memory ❌
```

💡 Senior answer: "In practice `ArrayList` wins almost always, even for many inserts, because of CPU cache locality. Use `ArrayDeque` for stacks/queues instead of `Stack`/`LinkedList`."

---

### 5.5 Fail-fast vs fail-safe iterators

```text
 Fail-fast (ArrayList, HashMap):
   iterator remembers expectedModCount; list.modCount changes ==> ConcurrentModificationException
 Fail-safe / weakly consistent (CopyOnWriteArrayList, ConcurrentHashMap):
   iterates a snapshot / tolerates changes, never throws CME
```

```java
List<Integer> list = new ArrayList<>(List.of(1, 2, 3, 4));
for (Integer i : list) if (i % 2 == 0) list.remove(i);   // ❌ CME

Iterator<Integer> it = list.iterator();
while (it.hasNext()) if (it.next() % 2 == 0) it.remove();   // ✅
list.removeIf(i -> i % 2 == 0);                              // ✅ best
```

⚠️ CME can happen in a **single thread** — it's about structural modification during iteration, not only concurrency.

---

### 5.6 Comparable vs Comparator

```java
record Employee(String name, int age, BigDecimal salary) implements Comparable<Employee> {
    @Override public int compareTo(Employee o) { return name.compareTo(o.name); }  // natural order
}

list.sort(Comparator.comparing(Employee::salary).reversed()
                    .thenComparing(Employee::name)
                    .thenComparingInt(Employee::age));

Comparator<Employee> nullsSafe = Comparator.comparing(Employee::name,
                                     Comparator.nullsLast(Comparator.naturalOrder()));
```

⚠️ Never compare with `a - b` (integer overflow) → use `Integer.compare(a, b)`. `TreeSet` uses `compareTo`, **not** `equals`, for uniqueness — inconsistent `compareTo` loses elements.

---

### 5.7 LinkedHashMap as an LRU cache

```java
class LruCache<K, V> extends LinkedHashMap<K, V> {
    private final int max;
    LruCache(int max) { super(16, 0.75f, true); this.max = max; }   // accessOrder = true
    @Override protected boolean removeEldestEntry(Map.Entry<K, V> e) { return size() > max; }
}
```

```text
 access order list:  eldest ◄── [A] ⇄ [B] ⇄ [C] ──► newest
 get(A)           :  [B] ⇄ [C] ⇄ [A]
 put(D), max = 3  :  [C] ⇄ [A] ⇄ [D]     (B evicted)
```

(Not thread-safe; in production use Caffeine.)

---

### 5.8 Special-purpose collections

| Class | What's special | Use |
|---|---|---|
| `WeakHashMap` | keys held weakly; entry removed when key unreachable | metadata caches keyed by objects |
| `IdentityHashMap` | uses `==` and `System.identityHashCode` | graph traversal, serialization |
| `EnumMap` / `EnumSet` | array / bit vector backing | state machines, flags |
| `PriorityQueue` | binary heap, O(log n) offer/poll, head = smallest | schedulers, top-K |
| `ArrayDeque` | circular array | stack & queue |
| `BitSet` | compact bits | flags, bloom-like sets |

```java
PriorityQueue<Integer> topK = new PriorityQueue<>();           // min-heap keeps K largest
for (int x : nums) { topK.offer(x); if (topK.size() > k) topK.poll(); }
```

---

### 5.9 Unmodifiable vs immutable collections

```java
List<String> a = Arrays.asList("x", "y");        // fixed-size view of array: set ✅ add ❌; allows null
List<String> b = Collections.unmodifiableList(src); // read-only VIEW: changes to src are visible!
List<String> c = List.of("x", "y");              // truly immutable; null ❌ (NPE)
List<String> d = List.copyOf(src);               // immutable snapshot
```

```text
 src ──► [x, y] ◄── b (view)        src.add("z")  ==> b shows [x, y, z] ⚠️
 c, d ──► own immutable copy        unaffected ✅
```

---

### 5.10 HashMap vs Hashtable vs synchronizedMap vs ConcurrentHashMap vs TreeMap

| | thread-safe | null key/value | ordering | locking |
|---|---|---|---|---|
| `HashMap` | ❌ | 1 null key, null values | none | — |
| `Hashtable` | ✅ (legacy) | ❌ | none | whole table |
| `Collections.synchronizedMap` | ✅ | as wrapped map | as wrapped | whole map (iteration needs manual sync) |
| `ConcurrentHashMap` | ✅ | ❌ | none | per bin + CAS |
| `TreeMap` | ❌ | null key ❌ (natural order) | sorted | — |
| `ConcurrentSkipListMap` | ✅ | ❌ | sorted | lock-free |

---

[⬆ Back to top](#table-of-contents)

## 6. Design Principles and Patterns

### 6.1 SOLID

```text
 S  Single Responsibility  – one reason to change
 O  Open/Closed            – open for extension, closed for modification
 L  Liskov Substitution    – subtypes must be usable wherever the base type is
 I  Interface Segregation  – many small interfaces > one fat interface
 D  Dependency Inversion   – depend on abstractions; high-level code doesn't know low-level details
```

```java
// O + D: add a new fee type without touching the calculator
interface FeeRule { boolean applies(Tx tx); BigDecimal fee(Tx tx); }

@Service
class FeeCalculator {
    private final List<FeeRule> rules;                 // Spring injects all implementations
    FeeCalculator(List<FeeRule> rules) { this.rules = rules; }
    BigDecimal total(Tx tx) {
        return rules.stream().filter(r -> r.applies(tx)).map(r -> r.fee(tx))
                    .reduce(BigDecimal.ZERO, BigDecimal::add);
    }
}

// L violation classic: Square extends Rectangle
class Rectangle { void setWidth(int w); void setHeight(int h); }
class Square extends Rectangle { /* setWidth also changes height */ }   // breaks callers' expectations ❌
```

---

### 6.2 Creational patterns: Singleton, Factory, Builder

```java
// Singleton — best: enum (serialization & reflection safe)
public enum IdGenerator { INSTANCE; private final AtomicLong seq = new AtomicLong();
    public long next() { return seq.incrementAndGet(); } }

// Factory method
static Notifier of(Channel ch) {
    return switch (ch) { case SMS -> new SmsNotifier(); case EMAIL -> new EmailNotifier(); };
}

// Builder — many optional params, readable, immutable result
Card card = Card.builder().pan("4111...").holder("ALI").expiry(YearMonth.of(2030, 1)).build();
```

💡 Spring beans are singletons **per container** — not the GoF JVM-wide singleton.

---

### 6.3 Structural and behavioural patterns you meet in Spring

```text
┌─────────────────┬──────────────────────────────────────────────────────────┐
│ Pattern         │ Where in Spring / Java                                   │
├─────────────────┼──────────────────────────────────────────────────────────┤
│ Proxy           │ AOP, @Transactional, @Cacheable, Hibernate lazy proxies  │
│ Decorator       │ BufferedInputStream(InputStream), HttpServletRequestWrapper│
│ Adapter         │ HandlerAdapter in Spring MVC                             │
│ Template Method │ JdbcTemplate, RestTemplate, AbstractController           │
│ Strategy        │ PlatformTransactionManager impls, PasswordEncoder        │
│ Observer        │ ApplicationEvent / @EventListener                        │
│ Factory         │ BeanFactory, FactoryBean                                 │
│ Chain of Resp.  │ Servlet filters, Spring Security filter chain            │
│ Front Controller│ DispatcherServlet                                        │
│ Builder         │ UriComponentsBuilder, WebClient.builder()                │
└─────────────────┴──────────────────────────────────────────────────────────┘
```

```java
// Strategy chosen at runtime by key (Spring injects Map<beanName, bean>)
@Service
class PaymentRouter {
    private final Map<String, PaymentStrategy> strategies;
    PaymentRouter(Map<String, PaymentStrategy> strategies) { this.strategies = strategies; }
    void pay(String type, Order o) { strategies.get(type + "Strategy").pay(o); }
}
```

---

### 6.4 Composition over inheritance

```text
 Inheritance (white-box, tight)      Composition (black-box, loose)
   Base ◄── Child                     Service ──has-a──► Logger
   change Base ==> Child may break    swap implementation at runtime/tests
```

```java
// ❌ InstrumentedHashSet extends HashSet → addAll() calls add() → double counting
// ✅ wrap instead
class CountingSet<E> implements Set<E> {
    private final Set<E> delegate; private int added;
    public boolean add(E e) { added++; return delegate.add(e); }
    // ... delegate the rest
}
```

[⬆ Back to top](#table-of-contents)

## 7. Hibernate and JPA

### 7.1 JPA vs Hibernate vs Spring Data JPA vs JDBC

```text
  Your code
     │  userRepository.findByEmail(...)
     ▼
 ┌──────────────────────┐  Spring Data JPA : generates repository implementations
 │ Spring Data JPA      │                    (derived queries, paging, auditing)
 └─────────┬────────────┘
           ▼
 ┌──────────────────────┐  JPA (Jakarta Persistence) : the SPECIFICATION
 │ JPA API              │    EntityManager, @Entity, JPQL
 └─────────┬────────────┘
           ▼
 ┌──────────────────────┐  Hibernate ORM : an IMPLEMENTATION (provider) of JPA
 │ Hibernate            │    + extras: Session, @BatchSize, @Formula, filters
 └─────────┬────────────┘
           ▼
 ┌──────────────────────┐  JDBC : low-level SQL API
 │ JDBC + HikariCP pool │
 └─────────┬────────────┘
           ▼
        Database
```

| JPA | Hibernate native |
|---|---|
| `EntityManagerFactory` | `SessionFactory` (heavy, thread-safe, one per DB) |
| `EntityManager` | `Session` (light, NOT thread-safe, one per transaction/request) |
| `EntityTransaction` | `Transaction` |
| JPQL | HQL (superset) |

```java
Session session = entityManager.unwrap(Session.class);   // reach Hibernate API from JPA
```

---

### 7.2 Persistence context (first-level cache)

```text
  EntityManager / Session
  ┌──────────────────── Persistence Context ─────────────────────┐
  │  identity map:  (Customer, 1) ──► Customer@a1  + snapshot    │
  │                 (Card, 7)     ──► Card@b2      + snapshot    │
  │  action queue:  INSERT card#8, UPDATE customer#1 (at flush)  │
  └──────────────────────────────────────────────────────────────┘

 em.find(Customer.class, 1)  → SQL select (first time)
 em.find(Customer.class, 1)  → returned from context, NO SQL, SAME instance (==)
```

💡 Guarantees: **one Java object per DB row per persistence context** (repeatable reads at app level), automatic **dirty checking**, **write-behind** (SQL is delayed until flush). Scope = one transaction (default in Spring).

⚠️ Thousands of loaded entities in one context = memory + slow flush (dirty checking compares every snapshot) → in batches use `flush()` + `clear()`.

---

### 7.3 Entity lifecycle states

```text
                 new Customer()
                       │
                       ▼
                 ┌───────────┐  persist()
                 │ TRANSIENT │ ───────────────┐
                 └───────────┘                ▼
                                       ┌───────────┐  remove()   ┌─────────┐
   find(), query, merge() returns ───► │  MANAGED  │ ──────────► │ REMOVED │
                                       └───────────┘ ◄────────── └─────────┘
                                         │      ▲     persist()       │
               detach(), clear(),        │      │ merge() (returns    │ flush/commit
               close(), tx end,          ▼      │  a MANAGED copy)    ▼
               serialization        ┌───────────┐                 DELETE SQL
                                    │ DETACHED  │
                                    └───────────┘
```

| State | In persistence context? | Has DB row? | Changes auto-saved? |
|---|---|---|---|
| Transient | ❌ | ❌ | ❌ |
| Managed | ✅ | ✅ (or pending insert) | ✅ dirty checking |
| Detached | ❌ | ✅ | ❌ (needs `merge`) |
| Removed | ✅ (scheduled) | until flush | DELETE at flush |

---

### 7.4 persist vs merge vs save vs update

```java
// persist: transient → managed (void). Detached arg → EntityExistsException / PersistentObjectException
Customer c = new Customer("Ali");
em.persist(c);                       // c itself becomes managed

// merge: copies state of the given object ONTO a managed instance and RETURNS it
Customer detached = ...;             // e.g. from a previous transaction / REST DTO
Customer managed = em.merge(detached);
detached.setName("X");               // ❌ not tracked
managed.setName("X");                // ✅ tracked
```

```text
 merge(detached)
   1. look for (Customer, id) in context → else SELECT from DB
   2. copy detached's fields onto the managed instance
   3. return managed instance  (the argument stays DETACHED)
```

| Method | API | Returns | Notes |
|---|---|---|---|
| `persist` | JPA | void | only for new entities |
| `merge` | JPA | managed copy | new or detached; may SELECT first |
| `save` | Hibernate (deprecated in 6) | id | immediate id generation |
| `update` | Hibernate (deprecated in 6) | void | reattaches the same instance; `NonUniqueObjectException` if another copy is loaded |
| `saveOrUpdate` | Hibernate (deprecated in 6) | void | — |

💡 Spring Data `repository.save(e)`: `isNew(e)` ? `persist` : `merge` — `isNew` = id is `null` (or `@Version` is null). ⚠️ With **manually assigned ids**, `save` thinks the entity is not new → does `merge` → extra SELECT. Fix: `@Version` field or implement `Persistable<ID>`.

---

### 7.5 find vs getReference (get vs load)

```java
Customer c1 = em.find(Customer.class, 1L);          // hits DB now (or context); null if missing
Customer c2 = em.getReference(Customer.class, 1L);  // returns PROXY, no SQL yet
c2.getId();      // no SQL (id is known)
c2.getName();    // SQL now; EntityNotFoundException if row missing
```

```text
 getReference ──► Customer$HibernateProxy { id=1, target=null }
                    │ first access to non-id field
                    ▼
                  SELECT ... → target = Customer@real
```

💡 Use `getReference` to set a foreign key without loading the parent:

```java
Order o = new Order();
o.setCustomer(em.getReference(Customer.class, customerId));   // no SELECT for customer
em.persist(o);
```

Hibernate native: `get()` ≈ `find`, `load()` ≈ `getReference` (`load` deprecated in 6).

---

### 7.6 Dirty checking and flush modes

```java
@Transactional
public void rename(Long id, String name) {
    Customer c = repo.findById(id).orElseThrow();   // managed, snapshot taken
    c.setName(name);                                // no save() needed!
}                                                   // commit → flush → UPDATE customer ...
```

```text
 flush():  for each managed entity: compare current state vs snapshot
           changed? ==> UPDATE   (all columns by default; @DynamicUpdate = changed only)

 When does Hibernate flush (FlushMode.AUTO)?
   ① before transaction commit
   ② before a JPQL/HQL query that touches affected tables (so query sees your changes)
   ③ when you call em.flush() explicitly
   (native SQL queries: Hibernate flushes the whole context in JPA mode)
 FlushMode.COMMIT : only at commit
 FlushMode.MANUAL : only explicit flush (read-only transactions)
```

⚠️ `flush` ≠ `commit`: flush sends SQL inside the transaction; it can still roll back. `@Transactional(readOnly = true)` sets flush mode MANUAL in Spring + Hibernate → skips dirty checking (faster, less memory).

---

### 7.7 Fetching: lazy vs eager

| Association | JPA default fetch |
|---|---|
| `@ManyToOne` | **EAGER** ⚠️ |
| `@OneToOne` | **EAGER** ⚠️ |
| `@OneToMany` | LAZY |
| `@ManyToMany` | LAZY |
| `@ElementCollection` | LAZY |
| basic fields | EAGER (lazy only with bytecode enhancement) |

💡 Senior rule: **make every association LAZY** and fetch what each use-case needs per query (JOIN FETCH / entity graph / DTO).

```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "customer_id")
private Customer customer;
```

⚠️ Bidirectional `@OneToOne` on the non-owning side (`mappedBy`) can't be lazy without bytecode enhancement — Hibernate must query to know whether it's null. Use `@MapsId` (shared primary key) or make it unidirectional.

---

### 7.8 LazyInitializationException and Open Session In View

```text
 Controller ──► Service @Transactional ──► repo.findById(1) ─► Order (orderLines = PROXY)
                         │ transaction ends, EntityManager closed
                         ▼
 Controller: order.getOrderLines().size()
                         ▼
   ❌ LazyInitializationException: could not initialize proxy – no Session
```

Fixes (best first):

```java
// 1. Fetch what you need in the query
@Query("select o from Order o join fetch o.lines where o.id = :id")
Optional<Order> findWithLines(Long id);

// 2. Entity graph
@EntityGraph(attributePaths = {"lines", "customer"})
Optional<Order> findById(Long id);

// 3. Map to a DTO inside the transaction (best for APIs)
@Transactional(readOnly = true)
public OrderDto get(Long id) { return mapper.toDto(repo.findWithLines(id).orElseThrow()); }

// 4. Hibernate.initialize(order.getLines());   inside the transaction
```

❌ Anti-patterns: switching to EAGER, `hibernate.enable_lazy_load_no_trans=true` (new session per lazy load → N+1 out of transaction).

**Open Session In View (OSIV)**: Spring Boot enables `spring.jpa.open-in-view=true` by default (with a startup warning). It keeps the EntityManager open until the view/JSON is rendered → no LazyInitializationException, **but**: DB connection held for the whole request, lazy loading in the web layer → hidden N+1 queries. 💡 Senior answer: **disable it** (`spring.jpa.open-in-view=false`) and fetch explicitly.

---

### 7.9 The N+1 select problem

```java
List<Order> orders = em.createQuery("select o from Order o", Order.class).getResultList(); // 1 query
for (Order o : orders) {
    o.getCustomer().getName();     // + 1 query PER order (lazy proxy init)  ==> N+1
}
```

```text
 SELECT * FROM orders;                       ← 1
 SELECT * FROM customer WHERE id = 10;       ← +1
 SELECT * FROM customer WHERE id = 11;       ← +1
 ... (N times)                               100 orders = 101 round trips ❌
```

**Solutions**

```java
// A. JOIN FETCH                                               ==> 1 query
@Query("select o from Order o join fetch o.customer")
List<Order> findAllWithCustomer();

// B. Entity graph                                             ==> 1 query
@EntityGraph(attributePaths = "customer")
List<Order> findByStatus(Status s);

// C. Batch fetching: load proxies in IN (...) groups          ==> 1 + N/size queries
@BatchSize(size = 50)                     // on the entity class or the collection
// or globally: spring.jpa.properties.hibernate.default_batch_fetch_size=50
//   SELECT * FROM customer WHERE id IN (10, 11, 12, ... 59)

// D. DTO projection — only the columns you need               ==> 1 query, no entities
@Query("select new com.acme.OrderView(o.id, c.name) from Order o join o.customer c")
List<OrderView> findViews();

// E. @Fetch(FetchMode.SUBSELECT) for collections: second query uses the first as subselect
```

How to detect: `spring.jpa.show-sql`, `hibernate.generate_statistics=true`, datasource-proxy / p6spy, Hypersistence Utils `SQLStatementCountValidator` in tests.

⚠️ `JOIN FETCH` of a collection + pagination → Hibernate warns **HHH90003004 / HHH000104 "firstResult/maxResults specified with collection fetch; applying in memory"** — it loads ALL rows and pages in memory. Fix: page the parent ids first, then fetch with `where id in :ids`. Also: fetching **two `List` collections** at once → `MultipleBagFetchException` (use `Set` or separate queries).

---

### 7.10 Associations: owning side and mappedBy

```text
   Order (1) ─────────────< (N) OrderLine
   @OneToMany(mappedBy="order")    @ManyToOne @JoinColumn(name="order_id")
   INVERSE side (read-only)        OWNING side ← holds the FK, Hibernate writes from HERE

 If you only do order.getLines().add(line) without line.setOrder(order)
   ==> order_id stays NULL ❌
```

```java
@Entity
class Order {
    @Id @GeneratedValue Long id;

    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<OrderLine> lines = new ArrayList<>();

    // ✅ helper methods keep BOTH sides in sync
    public void addLine(OrderLine l)    { lines.add(l); l.setOrder(this); }
    public void removeLine(OrderLine l) { lines.remove(l); l.setOrder(null); }
}

@Entity
class OrderLine {
    @Id @GeneratedValue Long id;
    @ManyToOne(fetch = FetchType.LAZY) @JoinColumn(name = "order_id")
    private Order order;
}
```

⚠️ Unidirectional `@OneToMany` without `@JoinColumn` creates a **join table** and inefficient SQL (insert + extra updates). Prefer bidirectional or just the `@ManyToOne` side.

⚠️ `@ManyToMany` with `List` → removing one element deletes ALL join rows and re-inserts the rest. Use `Set`. For extra columns on the link, map the join table as its own entity (`@ManyToOne` ×2).

---

### 7.11 Cascade types and orphanRemoval

```text
 CascadeType: PERSIST, MERGE, REMOVE, REFRESH, DETACH, ALL
   operation on PARENT ──► propagated to CHILDREN

 orphanRemoval = true : child removed from the collection ==> DELETE child row
 CascadeType.REMOVE   : parent deleted                   ==> children deleted
```

```java
order.getLines().remove(line);   // orphanRemoval=true → DELETE order_line
                                 // without it → line's FK set to null (or nothing if not owning)
```

⚠️ Never cascade from child to parent (`@ManyToOne(cascade = REMOVE)`) — deleting one line would delete the order (and its other lines). ⚠️ `CascadeType.REMOVE` on `@ManyToMany` deletes the shared entities themselves, not only the links.

---

### 7.12 Primary key generation strategies

| Strategy | How | JDBC batch inserts | Notes |
|---|---|---|---|
| `IDENTITY` | DB auto-increment column | ❌ disabled | INSERT must run immediately to get the id |
| `SEQUENCE` | DB sequence | ✅ | best for PostgreSQL/Oracle; `allocationSize` = pooled optimizer |
| `TABLE` | emulates sequence with a table + locks | ✅ | slow, avoid |
| `AUTO` | provider chooses | depends | Hibernate 6 → SEQUENCE when supported |
| `UUID` | `@GeneratedValue(strategy = UUID)` / `@UuidGenerator` | ✅ | no DB round trip; random UUIDv4 = index fragmentation → prefer time-ordered (v7) |

```java
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "card_seq")
@SequenceGenerator(name = "card_seq", sequenceName = "card_seq", allocationSize = 50)
private Long id;
```

```text
 Pooled optimizer, allocationSize = 50
   call nextval → 1     Hibernate hands out ids 1..50 from memory
   call nextval → 51    next 50 ids          ==> 1 DB call per 50 inserts ✅
   ⚠️ DB sequence INCREMENT BY must match allocationSize
```

---

### 7.13 Inheritance mapping strategies

```text
 class Payment { id, amount }  ◄── CardPayment { cardNumber }  ◄── BankTransfer { iban }

 SINGLE_TABLE (default)          JOINED                        TABLE_PER_CLASS
 ┌─────────────────────────┐    ┌──────────┐                  ┌──────────────────┐
 │ payment                 │    │ payment  │ id, amount       │ card_payment     │ id, amount, card_no
 │ id, dtype, amount,      │    └────┬─────┘                  ├──────────────────┤
 │ card_number, iban       │   ┌─────┴───────┐                │ bank_transfer    │ id, amount, iban
 └─────────────────────────┘   card_payment  bank_transfer    └──────────────────┘
 + fastest, no joins           + normalized, NOT NULL OK      + no joins for concrete
 – nullable subclass columns   – joins on every polymorphic   – polymorphic query = UNION
                                 query                           , no IDENTITY
```

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "type")
abstract class Payment { @Id @GeneratedValue Long id; BigDecimal amount; }

@Entity @DiscriminatorValue("CARD")
class CardPayment extends Payment { String cardNumber; }

// @MappedSuperclass: share fields (id, audit columns) — NOT an entity, no polymorphic queries
@MappedSuperclass
abstract class BaseEntity { @Id @GeneratedValue Long id; @Version Long version; Instant createdAt; }
```

---

### 7.14 equals and hashCode for entities

```text
 Problem: id is NULL until persist/flush
   set.add(newEntity)          → hashCode based on id=null
   persist → id = 42           → hashCode changes → entity "lost" in the HashSet ❌
```

```java
// ✅ Option 1: business/natural key (immutable, unique) e.g. IBAN, email
@NaturalId private String iban;
@Override public boolean equals(Object o) { return o instanceof Account a && iban.equals(a.getIban()); }
@Override public int hashCode() { return Objects.hash(iban); }

// ✅ Option 2: id-based but with CONSTANT hashCode
@Override public boolean equals(Object o) {
    if (this == o) return true;
    if (!(o instanceof Card other)) return false;    // instanceof (not getClass) → works with proxies
    return id != null && id.equals(other.getId());   // getter → initializes proxy safely
}
@Override public int hashCode() { return getClass().hashCode(); }   // stable across lifecycle
```

❌ Don't use Lombok `@Data` / `@EqualsAndHashCode` on entities (includes lazy collections → triggers loading, infinite recursion in bidirectional relations; `toString` too).

---

### 7.15 Hibernate proxies — why entities can't be final

```text
  em.getReference(Card.class, 1) / LAZY @ManyToOne
         │
         ▼
  Card$HibernateProxy$x9k  extends Card   (generated subclass via ByteBuddy)
     intercepts getters → loads the real Card on first access

  ==> entity class and its methods must NOT be final
  ==> needs a no-arg constructor (protected is fine)
  ==> proxy.getClass() != Card.class  (use Hibernate.getClass(obj) / instanceof)
```

(Kotlin: classes are final by default → use the `kotlin-jpa`/`allopen` plugins.)

---

### 7.16 Second-level cache and query cache

```text
                  ┌──────── Session 1 ────────┐   ┌──────── Session 2 ────────┐
 L1 (per session) │ persistence context       │   │ persistence context       │
                  └────────────┬──────────────┘   └────────────┬──────────────┘
                               └───────────────┬───────────────┘
 L2 (per SessionFactory)        ┌──────────────▼───────────────┐
                                │ second-level cache           │  Ehcache / Caffeine (JCache),
                                │  entity data (dehydrated)    │  Hazelcast, Infinispan
                                │  collection cache (ids)      │
                                │  query cache (ids per query) │
                                └──────────────┬───────────────┘
                                               ▼
                                            Database
 lookup order: L1 → L2 → DB
```

```java
@Entity
@Cacheable
@org.hibernate.annotations.Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
class Currency { @Id String code; String name; }
```

```properties
spring.jpa.properties.hibernate.cache.use_second_level_cache=true
spring.jpa.properties.hibernate.cache.region.factory_class=jcache
spring.jpa.properties.hibernate.cache.use_query_cache=true
```

| Strategy | For |
|---|---|
| `READ_ONLY` | reference data never updated (fastest) |
| `NONSTRICT_READ_WRITE` | rare updates, small staleness OK |
| `READ_WRITE` | soft locks, strong-ish consistency |
| `TRANSACTIONAL` | JTA / XA caches |

💡 Good for **read-mostly reference data** (currencies, country codes, product types). Bad for frequently updated or huge tables, and in multi-node setups without a distributed cache (stale data). Query cache is invalidated whenever any involved table changes → often useless on write-heavy tables. Bulk JPQL `UPDATE/DELETE` and native SQL bypass/evict caches.

---

### 7.17 Optimistic vs pessimistic locking

```text
 Lost update problem:
  T1 reads balance=100           T2 reads balance=100
  T1 writes 100-30=70            T2 writes 100+50=150   ==> T1's update lost ❌

 OPTIMISTIC (@Version): no DB lock, detect conflict at write
  UPDATE account SET balance=70, version=6 WHERE id=1 AND version=5   → 1 row ✅
  UPDATE account SET balance=150, version=6 WHERE id=1 AND version=5  → 0 rows
     ==> OptimisticLockException / ObjectOptimisticLockingFailureException → retry

 PESSIMISTIC: lock the row in the DB while reading
  SELECT ... FROM account WHERE id=1 FOR UPDATE      (others WAIT)
```

```java
@Entity class Account {
    @Id Long id;
    BigDecimal balance;
    @Version Long version;          // Hibernate increments + checks automatically
}

// pessimistic
@Lock(LockModeType.PESSIMISTIC_WRITE)
@QueryHints(@QueryHint(name = "jakarta.persistence.lock.timeout", value = "3000"))
@Query("select a from Account a where a.id = :id")
Optional<Account> findForUpdate(Long id);

// retry optimistic conflicts (spring-retry)
@Retryable(retryFor = ObjectOptimisticLockingFailureException.class, maxAttempts = 3)
@Transactional
public void debit(Long id, BigDecimal amt) { ... }
```

| | Optimistic | Pessimistic |
|---|---|---|
| Conflict rate | low | high |
| Cost | none until conflict; retry | DB locks, waiting, deadlock risk |
| Long user "think time" | ✅ works across requests (version in DTO) | ❌ can't hold DB lock across requests |
| Example | editing a customer profile | money transfer hot rows, seat booking |

---

### 7.18 Transaction isolation levels and anomalies

```text
 Dirty read        : read another tx's UNCOMMITTED data
 Non-repeatable    : same row read twice → different values (other tx committed UPDATE)
 Phantom read      : same query twice → different ROWS (other tx committed INSERT/DELETE)
 Lost update       : two read-modify-write cycles overwrite each other
```

| Isolation | Dirty | Non-repeatable | Phantom | Notes |
|---|---|---|---|---|
| READ_UNCOMMITTED | possible | possible | possible | PostgreSQL treats as READ COMMITTED |
| **READ_COMMITTED** | ❌ | possible | possible | default: PostgreSQL, Oracle, SQL Server |
| REPEATABLE_READ | ❌ | ❌ | possible (std) | default: MySQL InnoDB; PostgreSQL RR (snapshot) also blocks phantoms |
| SERIALIZABLE | ❌ | ❌ | ❌ | slowest; may abort with serialization failure → retry |

```java
@Transactional(isolation = Isolation.REPEATABLE_READ)
public Report buildReport() { ... }
```

💡 Most banking apps stay on READ COMMITTED + `@Version` (optimistic) + `SELECT FOR UPDATE` on hot rows.

---

### 7.19 Batch processing with Hibernate

```properties
spring.jpa.properties.hibernate.jdbc.batch_size=50
spring.jpa.properties.hibernate.order_inserts=true
spring.jpa.properties.hibernate.order_updates=true
spring.jpa.properties.hibernate.jdbc.batch_versioned_data=true
# PostgreSQL driver: reWriteBatchedInserts=true in the JDBC URL
```

```java
@Transactional
public void importCards(List<CardDto> dtos) {
    for (int i = 0; i < dtos.size(); i++) {
        em.persist(toEntity(dtos.get(i)));
        if (i > 0 && i % 50 == 0) {
            em.flush();      // send batch of INSERTs
            em.clear();      // free the persistence context (memory + dirty-check cost)
        }
    }
}
```

```text
 without batching:  INSERT ; INSERT ; INSERT ...      1 round trip per row
 with batching   :  [INSERT x50] → 1 round trip       ⚠️ IDENTITY ids disable this
```

For millions of rows: `StatelessSession` (no context, no cascades, no L1/L2 cache), JDBC `batchUpdate`, or DB-native bulk load (COPY / SQL*Loader). Bulk JPQL:

```java
@Modifying(clearAutomatically = true, flushAutomatically = true)
@Query("update Card c set c.status = 'EXPIRED' where c.expiry < :today")
int expireCards(LocalDate today);     // ⚠️ bypasses persistence context, @Version, callbacks
```

---

### 7.20 JPQL vs Criteria API vs native SQL

```java
// JPQL — entity names & fields, portable
List<Card> cards = em.createQuery(
        "select c from Card c where c.customer.id = :cid and c.status = :st", Card.class)
    .setParameter("cid", cid).setParameter("st", Status.ACTIVE)
    .setFirstResult(0).setMaxResults(20)
    .getResultList();

// Criteria — type-safe, dynamic filters
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Card> q = cb.createQuery(Card.class);
Root<Card> root = q.from(Card.class);
List<Predicate> ps = new ArrayList<>();
if (status != null) ps.add(cb.equal(root.get("status"), status));
q.where(ps.toArray(Predicate[]::new));

// Native — DB-specific features (window functions, hints, CTE)
em.createNativeQuery("select * from card where pan_hash = ?1", Card.class)
  .setParameter(1, hash).getResultList();
```

⚠️ Always use **bind parameters** — never concatenate input (SQL/JPQL injection, and kills statement cache).

---

### 7.21 Entity callbacks and auditing

```java
@Entity
@EntityListeners(AuditingEntityListener.class)    // Spring Data auditing
class Card {
    @CreatedDate Instant createdAt;
    @LastModifiedDate Instant updatedAt;
    @CreatedBy String createdBy;

    @PrePersist void beforeInsert() { ... }        // also @PreUpdate, @PostLoad, @PreRemove...
}
// @EnableJpaAuditing + AuditorAware<String> bean
```

Hibernate Envers (`@Audited`) keeps full revision history tables (`card_aud`) — common in banking.

---

### 7.22 Hibernate performance checklist

```text
 ✅ All associations LAZY; fetch per use case (JOIN FETCH / EntityGraph / DTO)
 ✅ open-in-view = false
 ✅ SEQUENCE ids + jdbc.batch_size + order_inserts
 ✅ readOnly = true for queries; DTO projections for read APIs
 ✅ default_batch_fetch_size to soften N+1
 ✅ Pagination on parents, not on fetched collections
 ✅ flush/clear in batches; StatelessSession for bulk
 ✅ Proper indexes on FK and filter columns (Hibernate doesn't create them for you in prod)
 ✅ Monitor: hibernate.generate_statistics, slow query log, HikariCP metrics
 ✅ Connection pool sizing (HikariCP maximumPoolSize ≈ small; more ≠ faster)
 ❌ ddl-auto=update in production → use Flyway / Liquibase
```

[⬆ Back to top](#table-of-contents)

## 8. Spring Core: IoC, DI, Beans and AOP

### 8.1 IoC and dependency injection

```text
 Without IoC:  OrderService ──new──► JdbcOrderRepository     (hard-wired, untestable)

 With IoC:     Spring container creates both and INJECTS the dependency
               ┌──────────── ApplicationContext ─────────────┐
               │  orderRepository (JpaOrderRepository)       │
               │        │ injected into                      │
               │        ▼                                    │
               │  orderService (OrderService)                │
               └─────────────────────────────────────────────┘
 "Inversion of Control" = the framework controls object creation & wiring, not your code
```

```java
@Service
public class OrderService {
    private final OrderRepository repo;          // final ✅
    private final PaymentClient payments;

    public OrderService(OrderRepository repo, PaymentClient payments) {   // single constructor
        this.repo = repo;                                                // → @Autowired optional
        this.payments = payments;
    }
}
```

| Injection type | Pros | Cons |
|---|---|---|
| **Constructor** ✅ | immutable `final` fields, mandatory deps explicit, easy unit tests (`new`), fails fast | long constructors (= SRP smell) |
| Setter | optional deps, reconfigurable | object can be half-initialized |
| Field (`@Autowired` on field) | short | hides deps, no `final`, needs reflection/Spring in tests ❌ |

---

### 8.2 BeanFactory vs ApplicationContext

```text
 BeanFactory          basic DI container, LAZY bean creation
      ▲
 ApplicationContext   + eager singleton creation at startup (fail fast)
                      + events, i18n (MessageSource), Environment/profiles,
                        resource loading, BeanPostProcessor auto-registration, AOP
 Implementations: AnnotationConfigApplicationContext,
                  AnnotationConfigServletWebServerApplicationContext (Boot web)
```

---

### 8.3 Bean lifecycle

```text
  ① Instantiate         (constructor; constructor injection happens here)
  ② Populate properties (setter / field injection)
  ③ Aware callbacks     BeanNameAware → BeanFactoryAware → ApplicationContextAware
  ④ BeanPostProcessor.postProcessBeforeInitialization
         └─ @PostConstruct runs here (CommonAnnotationBeanPostProcessor)
  ⑤ InitializingBean.afterPropertiesSet()
  ⑥ custom init-method  (@Bean(initMethod = "init"))
  ⑦ BeanPostProcessor.postProcessAfterInitialization
         └─ AOP PROXY is created here (@Transactional, @Async, @Cacheable…)
  ⑧ ── bean READY, in use ──
  ⑨ context close: @PreDestroy → DisposableBean.destroy() → destroy-method
     (⚠️ NOT called for prototype beans)

  Before all of this: BeanFactoryPostProcessor modifies bean DEFINITIONS
  (e.g. PropertySourcesPlaceholderConfigurer resolves ${...})
```

```java
@Component
class CacheWarmer implements InitializingBean, DisposableBean {
    CacheWarmer(Repo r) { /* ① deps available */ }
    @PostConstruct void postConstruct() { /* ④ */ }
    @Override public void afterPropertiesSet() { /* ⑤ */ }
    @PreDestroy void preDestroy() { /* ⑨ */ }
    @Override public void destroy() { /* ⑨ */ }
}
```

⚠️ Don't call `@Transactional` methods of yourself from `@PostConstruct` — the proxy isn't in front of `this` and transactions may not be ready. Use `ApplicationReadyEvent` or `SmartInitializingSingleton` for start-up work.

---

### 8.4 Bean scopes

| Scope | Instances | Notes |
|---|---|---|
| `singleton` (default) | one per container | must be **thread-safe / stateless** |
| `prototype` | new one per injection/lookup | Spring doesn't manage destruction |
| `request` | one per HTTP request | web only |
| `session` | one per HTTP session | web only |
| `application` | one per `ServletContext` | |
| `websocket` | one per WebSocket session | |

**Prototype injected into a singleton problem**

```text
  Singleton OrderService (created once)
       └── PrototypeBean  injected ONCE at creation ==> effectively a singleton ❌
```

```java
@Service
class OrderService {
    private final ObjectProvider<ReportBuilder> builders;     // ✅ fresh instance each call
    OrderService(ObjectProvider<ReportBuilder> builders) { this.builders = builders; }
    Report build() { return builders.getObject().build(); }
}
// alternatives: @Lookup method, scoped proxy:
@Component @Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
class RequestContext { }
```

---

### 8.5 @Component vs @Bean and @Configuration proxying

```text
 @Component (+ @Service, @Repository, @Controller)   → class-level, found by component scan
                                                        for YOUR classes
 @Bean (method inside @Configuration)                 → you write the factory method
                                                        for 3rd-party classes / conditional setup
 Stereotypes: @Repository also translates persistence exceptions → DataAccessException
```

```java
@Configuration                          // proxyBeanMethods = true (CGLIB subclass)
class AppConfig {
    @Bean DataSource dataSource() { return new HikariDataSource(); }
    @Bean JdbcTemplate jdbc() { return new JdbcTemplate(dataSource()); }   // ← returns the SAME singleton
    @Bean TxManager tx()      { return new TxManager(dataSource()); }      //   (intercepted by CGLIB)
}

@Configuration(proxyBeanMethods = false)   // "lite" mode: faster startup, inter-bean calls create NEW objects
```

---

### 8.6 Resolving multiple beans: @Primary, @Qualifier, collections

```java
interface Notifier {}
@Component @Primary class EmailNotifier implements Notifier {}
@Component("sms") class SmsNotifier implements Notifier {}

class A { A(Notifier n) {} }                          // → EmailNotifier (@Primary)
class B { B(@Qualifier("sms") Notifier n) {} }        // → SmsNotifier
class C { C(List<Notifier> all) {} }                  // → both (ordered by @Order)
class D { D(Map<String, Notifier> byName) {} }        // → {"emailNotifier"=..., "sms"=...}
class E { E(Optional<Notifier> maybe) {} }            // optional dependency
```

```text
 Autowiring resolution:  by TYPE  → several? → @Qualifier → @Primary → parameter NAME
                         → still ambiguous ==> NoUniqueBeanDefinitionException
                         none found        ==> NoSuchBeanDefinitionException
```

---

### 8.7 Circular dependencies

```text
   A ──needs──► B ──needs──► A
   constructor injection: Spring can't create either ==> BeanCurrentlyInCreationException
   Spring Boot 2.6+: circular references PROHIBITED by default even for field/setter injection
```

```java
// Fixes (best first):
// 1. Redesign: extract the shared logic into a third bean C used by both
// 2. Use events instead of direct calls
// 3. @Lazy on one constructor parameter → injects a lazy proxy
public A(@Lazy B b) { this.b = b; }
// 4. (last resort) spring.main.allow-circular-references=true
```

💡 A circular dependency is usually a **design smell** (two classes with mixed responsibilities).

---

### 8.8 AOP concepts and proxies

```text
 Aspect      : module of cross-cutting logic (logging, tx, security, metrics)
 Join point  : a point in execution (in Spring AOP: a METHOD call only)
 Pointcut    : expression selecting join points  execution(* com.acme..*Service.*(..))
 Advice      : what to run: @Before, @After, @AfterReturning, @AfterThrowing, @Around
 Weaving     : linking aspects to targets — Spring = runtime proxies; AspectJ = compile/load time

 Caller ──► [ PROXY ] ──► advice before ──► target.method() ──► advice after ──► Caller
```

```text
 JDK dynamic proxy                       CGLIB proxy
 ─────────────────                       ───────────
 implements the bean's INTERFACES        SUBCLASSES the bean's class
 only interface methods proxied          can't proxy final classes / final methods
 inject by interface type only           Spring Boot default: proxyTargetClass = true (CGLIB)
```

```java
@Aspect @Component
class TimingAspect {
    @Pointcut("within(@org.springframework.stereotype.Service *)")
    void services() {}

    @Around("services()")
    Object time(ProceedingJoinPoint pjp) throws Throwable {
        long t = System.nanoTime();
        try { return pjp.proceed(); }
        finally { log.info("{} took {} µs", pjp.getSignature(), (System.nanoTime() - t) / 1000); }
    }
}
```

---

### 8.9 Self-invocation problem

```text
            ┌────────── PROXY (adds tx) ──────────┐
 Controller │  service.outer() ──► target.outer() │
            └─────────────────────────┬───────────┘
                                      │ this.inner()   ← plain Java call on TARGET,
                                      ▼                  proxy is BYPASSED
                         @Transactional(REQUIRES_NEW) inner()  ==> annotation IGNORED ❌
```

```java
@Service
class ReportService {
    public void outer() { inner(); }                      // ❌ no new transaction
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void inner() { ... }
}
```

Fixes: ① move `inner()` to another bean (✅ cleanest) ② inject self lazily (`@Lazy ReportService self; self.inner()`) ③ `TransactionTemplate` programmatically ④ AspectJ weaving mode.

Same problem for `@Async`, `@Cacheable`, `@Retryable`, `@PreAuthorize`.

---

### 8.10 Configuration: @Value, @ConfigurationProperties, profiles, conditions

```java
@Value("${payment.timeout:5s}") Duration timeout;          // single value, default after ':'

@ConfigurationProperties(prefix = "payment")               // ✅ typed, grouped, validated
@Validated
public record PaymentProps(@NotNull URI url, Duration timeout, int retries) {}
// @EnableConfigurationProperties(PaymentProps.class) or @ConfigurationPropertiesScan
```

```yaml
payment:
  url: https://pay.example.com
  timeout: 3s
  retries: 2
---
spring:
  config:
    activate:
      on-profile: prod
payment:
  retries: 5
```

```java
@Profile("!prod") @Bean DataSeeder seeder() { ... }
@ConditionalOnProperty(name = "feature.x.enabled", havingValue = "true") @Bean FeatureX x() { ... }
```

Activate: `--spring.profiles.active=prod` or `SPRING_PROFILES_ACTIVE=prod`.

---

### 8.11 Spring events

```java
public record CardBlocked(String cardId) {}

@Service class CardService {
    private final ApplicationEventPublisher events;
    @Transactional public void block(String id) { /* ... */ events.publishEvent(new CardBlocked(id)); }
}

@Component class Notifications {
    @EventListener void on(CardBlocked e) { }                 // sync, same thread & transaction

    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    void afterCommit(CardBlocked e) { sendSms(e); }           // ✅ only if tx committed

    @Async @EventListener void async(CardBlocked e) { }       // other thread (@EnableAsync)
}
```

```text
 block() ─tx─► publish ─► @EventListener (inside tx)
                 commit ─► @TransactionalEventListener(AFTER_COMMIT)
                 rollback ─► AFTER_COMMIT listener NOT called ✅ no SMS for a failed block
```

---

[⬆ Back to top](#table-of-contents)

## 9. Spring Transactions

### 9.1 How @Transactional works

```text
 caller ──► CGLIB proxy ──► TransactionInterceptor
                               │ 1. read @Transactional attributes
                               │ 2. PlatformTransactionManager.getTransaction()
                               │      (JpaTransactionManager / DataSourceTransactionManager)
                               │      → binds Connection/EntityManager to ThreadLocal
                               ▼
                           target.method()
                               │
                 success ──────┼────── RuntimeException / Error
                    ▼                        ▼
                 commit()                 rollback()
```

💡 The transaction is bound to the **current thread** (ThreadLocal) → work done in `@Async` methods, `CompletableFuture`, or new threads runs **outside** the transaction.

---

### 9.2 Propagation types

| Propagation | Existing tx present | No tx present |
|---|---|---|
| **REQUIRED** (default) | join it | create new |
| **REQUIRES_NEW** | suspend it, create new (independent commit) | create new |
| NESTED | savepoint inside it (partial rollback) | create new |
| SUPPORTS | join | run without tx |
| NOT_SUPPORTED | suspend, run without tx | run without tx |
| MANDATORY | join | ❌ exception |
| NEVER | ❌ exception | run without tx |

```text
 REQUIRED                         REQUIRES_NEW
 ┌──── tx1 ──────────────┐        ┌──── tx1 ─────┐ (suspended) ┌── tx1 cont. ──┐
 │ outer()  → inner()    │        │ outer()      │ ┌── tx2 ──┐ │               │
 │ one tx; inner fails   │        │              │ │ inner() │ │               │
 │ ==> everything rolls  │        └──────────────┘ └─commit──┘ └───────────────┘
 │     back              │        inner commits even if outer later rolls back
 └───────────────────────┘        (e.g., audit log of a failed attempt)

 NESTED: outer tx ── SAVEPOINT ── inner work ── inner fails → rollback to savepoint,
         outer can continue (needs JDBC savepoint support: DataSourceTransactionManager)
```

```java
@Service class TransferService {
    private final AuditService audit;
    @Transactional
    public void transfer(...) {
        try { debitAndCredit(); }
        catch (InsufficientFunds e) { audit.logFailure(e); throw e; }
    }
}
@Service class AuditService {
    @Transactional(propagation = Propagation.REQUIRES_NEW)   // survives outer rollback
    public void logFailure(Exception e) { auditRepo.save(...); }
}
```

⚠️ `REQUIRES_NEW` needs a **second DB connection** while the first is suspended → pool exhaustion / deadlock under load if pool is small.

---

### 9.3 Rollback rules

```text
 Default: rollback on RuntimeException and Error
          COMMIT on checked exceptions ⚠️ (EJB heritage)
```

```java
@Transactional(rollbackFor = Exception.class)                 // also roll back on checked
@Transactional(noRollbackFor = BusinessWarning.class)
```

**UnexpectedRollbackException trap**

```java
@Transactional public void outer() {
    try { inner.process(); }                 // inner: @Transactional (REQUIRED) throws RuntimeException
    catch (RuntimeException e) { log.warn("ignored"); }   // you "handled" it...
}   // commit → ❌ UnexpectedRollbackException: Transaction silently rolled back
    //            because it has been marked as rollback-only
```

```text
 inner throws through ITS proxy ──► shared tx marked ROLLBACK-ONLY
 outer catches and tries to commit ──► Spring refuses ==> UnexpectedRollbackException
 Fix: let it propagate, use REQUIRES_NEW / NESTED for inner, or don't make inner transactional
```

---

### 9.4 @Transactional pitfalls checklist

```text
 ❌ self-invocation (this.method()) → no proxy → no tx                  (see 8.9)
 ❌ private methods → never proxied
     (Spring 6: protected / package-private OK with CGLIB class proxies)
 ❌ checked exception thrown → COMMIT by default
 ❌ catching the exception inside the method → proxy sees success → commit
 ❌ final class/method with CGLIB → not intercepted
 ❌ bean created with `new` → not a Spring bean → no proxy
 ❌ @Transactional on interface methods + class proxies (works since 5/6 mostly, but put it on classes)
 ❌ long transactions containing remote HTTP calls → locks + connections held
 ❌ @Async + @Transactional on caller → async work runs in another thread, outside the tx
 ✅ readOnly = true on queries (Hibernate skips dirty check, flush MANUAL; may route to replica)
 ✅ timeout = 5 (seconds)
 ✅ Put @Transactional on SERVICE layer (use-case boundary), not controller/repository
 ✅ In tests: @Transactional on a test method rolls back after the test by default
```

---

### 9.5 Programmatic transactions

```java
@Service
class BatchJob {
    private final TransactionTemplate tx;
    BatchJob(PlatformTransactionManager tm) {
        this.tx = new TransactionTemplate(tm);
        this.tx.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRES_NEW);
    }
    void run(List<Chunk> chunks) {
        for (Chunk c : chunks) {
            tx.executeWithoutResult(status -> {        // each chunk in its own tx
                process(c);
                if (c.invalid()) status.setRollbackOnly();
            });
        }
    }
}
```

💡 Use when you need fine-grained boundaries (per chunk), or to avoid self-invocation issues.

---

### 9.6 Distributed transactions: 2PC vs Saga vs Outbox

```text
 2PC (XA/JTA): coordinator → prepare all → commit all
   + atomic   – blocking, slow, coordinator SPOF, poor support in Kafka/REST  ==> avoid in microservices

 Saga: sequence of LOCAL transactions + COMPENSATING actions
   Order ✔ ──► Payment ✔ ──► Stock ✘
                 ▲ refund      │
                 └──── compensate ┘
   choreography (events) or orchestration (central coordinator)

 Transactional Outbox: avoid "DB commit OK but Kafka send failed"
   ┌──── one local DB tx ────────┐
   │ UPDATE account ...          │
   │ INSERT INTO outbox(event)   │
   └─────────────────────────────┘
        relay / Debezium CDC ──► Kafka ──► consumers (must be IDEMPOTENT)
```

[⬆ Back to top](#table-of-contents)

## 10. Spring Boot

### 10.1 What Spring Boot adds

```text
 Spring Framework  +  Boot
                      ├─ Auto-configuration   (beans configured from classpath + properties)
                      ├─ Starters             (curated dependency sets: spring-boot-starter-web)
                      ├─ Embedded server      (Tomcat / Jetty / Undertow / Netty) → java -jar
                      ├─ Externalized config  (application.yml, env vars, profiles)
                      ├─ Actuator             (health, metrics, info, env, threaddump)
                      └─ Opinionated defaults (HikariCP, Jackson, Logback)
```

---

### 10.2 How auto-configuration works

```text
 @SpringBootApplication
   = @SpringBootConfiguration (@Configuration)
   + @EnableAutoConfiguration
   + @ComponentScan (package of the main class and below)

 @EnableAutoConfiguration
   └─ reads META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
        (Boot 2.7+; before: META-INF/spring.factories)
        └─ DataSourceAutoConfiguration, JpaRepositoriesAutoConfiguration, ...
              each guarded by CONDITIONS:
                @ConditionalOnClass(DataSource.class)      class on classpath?
                @ConditionalOnMissingBean(DataSource.class) user didn't define one?  ← back-off
                @ConditionalOnProperty("spring.datasource.url")
 ==> your own @Bean always wins over auto-config (back-off)
```

```bash
java -jar app.jar --debug          # prints CONDITIONS EVALUATION REPORT (matched / not matched)
# or actuator: /actuator/conditions
```

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)   // switch one off
```

**Writing your own starter**: `@AutoConfiguration` class + conditions + `@ConfigurationProperties`, registered in `AutoConfiguration.imports`.

---

### 10.3 Startup sequence

```text
 main() → SpringApplication.run()
   1. create Environment (properties, profiles)
   2. print banner
   3. create ApplicationContext (servlet / reactive / none)
   4. load bean definitions (component scan + auto-config)
   5. refresh(): BeanFactoryPostProcessors → create singletons → BeanPostProcessors
   6. start embedded web server
   7. ApplicationStartedEvent → CommandLineRunner / ApplicationRunner
   8. ApplicationReadyEvent  → app ready for traffic
```

---

### 10.4 Externalized configuration precedence (high → low, simplified)

```text
 1. Command-line args            --server.port=9090
 2. SPRING_APPLICATION_JSON
 3. OS environment variables     SERVER_PORT=9090 (relaxed binding)
 4. Java system properties       -Dserver.port=9090
 5. application-{profile}.yml    outside jar  > inside jar
 6. application.yml              outside jar  > inside jar
 7. @PropertySource, defaults
```

💡 Secrets: never in the jar — env vars, Vault, Kubernetes secrets (`spring.config.import=optional:configtree:/etc/secrets/`).

---

### 10.5 Actuator and observability

```properties
management.endpoints.web.exposure.include=health,info,metrics,prometheus
management.endpoint.health.probes.enabled=true      # /actuator/health/liveness & /readiness for K8s
management.endpoint.health.show-details=when_authorized
```

```text
 Micrometer (metrics facade) ──► Prometheus ──► Grafana
 Micrometer Tracing (Boot 3; replaced Spring Cloud Sleuth) ──► OpenTelemetry/Zipkin
 traceId/spanId in MDC → correlated logs
```

⚠️ Don't expose `env`, `heapdump`, `threaddump`, `shutdown` publicly — secure the actuator.

---

### 10.6 Spring Boot 3 key changes

```text
 • Java 17 baseline (Spring Framework 6)
 • javax.* → jakarta.*  (jakarta.persistence, jakarta.servlet, jakarta.validation)
 • Hibernate 6
 • GraalVM native images + AOT processing
 • Micrometer Observation / Tracing built-in
 • ProblemDetail (RFC 7807/9457) error responses
 • HTTP interface clients (@HttpExchange), RestClient (6.1), virtual threads (3.2)
 • Spring Security 6: WebSecurityConfigurerAdapter removed → SecurityFilterChain beans
```

---

[⬆ Back to top](#table-of-contents)

## 11. Spring MVC and REST

### 11.1 DispatcherServlet request flow

```text
 HTTP request
     │
     ▼
 Servlet Filters (security, CORS, logging)  ← Servlet container level
     │
     ▼
 DispatcherServlet  (Front Controller)
     │ 1. HandlerMapping      → which controller method?  (RequestMappingHandlerMapping)
     │ 2. HandlerInterceptor.preHandle()
     │ 3. HandlerAdapter      → resolve arguments (@PathVariable, @RequestBody via
     │                          HttpMessageConverter/Jackson, @Valid validation)
     │ 4. Controller method executes
     │ 5. return value:
     │      @ResponseBody / @RestController → HttpMessageConverter → JSON
     │      view name → ViewResolver → template (Thymeleaf)
     │ 6. HandlerInterceptor.postHandle()
     │ 7. exceptions → HandlerExceptionResolver (@ControllerAdvice)
     │ 8. HandlerInterceptor.afterCompletion()
     ▼
 HTTP response
```

---

### 11.2 Filter vs Interceptor vs AOP

```text
 ┌───────────── Servlet container ─────────────────────────────────────┐
 │ Filter ─► Filter ─► DispatcherServlet ─► Interceptor ─► Controller ─► AOP ─► Service
 │ (all requests, raw          (Spring MVC, knows the       (any Spring bean
 │  request/response, even      handler method)              method, no HTTP)
 │  static resources)
 └─────────────────────────────────────────────────────────────────────┘
 Filter      : auth, CORS, compression, request logging, MDC correlation id
 Interceptor : per-handler checks, locale, timing per endpoint
 AOP         : business-level cross-cutting (audit, tx, retries)
```

```java
@Component
class CorrelationIdFilter extends OncePerRequestFilter {
    @Override protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res,
                                              FilterChain chain) throws ServletException, IOException {
        String id = Optional.ofNullable(req.getHeader("X-Correlation-Id")).orElse(UUID.randomUUID().toString());
        MDC.put("correlationId", id);
        try { chain.doFilter(req, res); } finally { MDC.remove("correlationId"); }   // ThreadLocal!
    }
}
```

---

### 11.3 REST controller, validation, error handling

```java
@RestController
@RequestMapping("/api/v1/cards")
@RequiredArgsConstructor
class CardController {
    private final CardService service;

    @GetMapping("/{id}")
    CardDto get(@PathVariable Long id) { return service.get(id); }

    @PostMapping
    ResponseEntity<CardDto> create(@Valid @RequestBody CreateCardRequest req, UriComponentsBuilder uri) {
        CardDto dto = service.create(req);
        return ResponseEntity.created(uri.path("/api/v1/cards/{id}").build(dto.id())).body(dto);  // 201 + Location
    }

    @GetMapping
    Page<CardDto> list(@RequestParam(defaultValue = "ACTIVE") Status status, Pageable pageable) {
        return service.list(status, pageable);           // ?page=0&size=20&sort=createdAt,desc
    }
}

record CreateCardRequest(@NotBlank String holder, @Pattern(regexp = "\\d{16}") String pan,
                         @Future YearMonth expiry) {}

@RestControllerAdvice
class ApiErrors {
    @ExceptionHandler(CardNotFound.class)
    ProblemDetail notFound(CardNotFound e) {
        ProblemDetail pd = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, e.getMessage());
        pd.setTitle("Card not found");
        return pd;                                        // application/problem+json
    }
    @ExceptionHandler(MethodArgumentNotValidException.class)
    ProblemDetail invalid(MethodArgumentNotValidException e) {
        ProblemDetail pd = ProblemDetail.forStatus(HttpStatus.BAD_REQUEST);
        pd.setProperty("errors", e.getFieldErrors().stream()
                .map(f -> f.getField() + ": " + f.getDefaultMessage()).toList());
        return pd;
    }
}
```

---

### 11.4 HTTP methods, idempotency and status codes

| Method | Safe | Idempotent | Typical response |
|---|---|---|---|
| GET | ✅ | ✅ | 200 |
| POST | ❌ | ❌ | 201 + Location |
| PUT (replace) | ❌ | ✅ | 200 / 204 |
| PATCH (partial) | ❌ | ❌ (not guaranteed) | 200 |
| DELETE | ❌ | ✅ | 204 |

```text
 2xx  200 OK · 201 Created · 202 Accepted (async) · 204 No Content
 4xx  400 Bad Request · 401 Unauthenticated · 403 Forbidden · 404 Not Found
      409 Conflict (version clash) · 412 Precondition Failed (ETag) · 422 Unprocessable · 429 Too Many Requests
 5xx  500 Internal · 502 Bad Gateway · 503 Unavailable · 504 Gateway Timeout
```

💡 Make POST payments idempotent with an **Idempotency-Key** header stored with a unique constraint → retried request returns the original result instead of charging twice.

---

### 11.5 HTTP clients: RestTemplate vs WebClient vs RestClient

```text
 RestTemplate (blocking, maintenance mode)
 WebClient    (reactive, non-blocking, needs WebFlux dependency)
 RestClient   (Spring 6.1, blocking, fluent API — modern choice for MVC apps)
 @HttpExchange interfaces (declarative, like Feign)
```

```java
RestClient client = RestClient.builder().baseUrl("https://api.bank.com")
        .requestFactory(factoryWithTimeouts())          // ⚠️ ALWAYS set connect/read timeouts
        .build();
AccountDto acc = client.get().uri("/accounts/{id}", id).retrieve().body(AccountDto.class);
```

---

### 11.6 Spring MVC vs Spring WebFlux

```text
 MVC (Servlet)                               WebFlux (Reactive, Netty)
 thread-per-request, blocking                event loop, few threads, non-blocking
 JDBC/JPA fine                               needs reactive drivers (R2DBC) end-to-end
 simple debugging                            Mono<T> / Flux<T>, back-pressure
 + virtual threads (Boot 3.2) ≈ scalability  best for streaming, high-concurrency I/O gateways
```

💡 Senior answer: "Don't mix blocking calls into WebFlux. With Java 21 virtual threads, MVC covers most high-concurrency I/O needs with simpler code."

---

[⬆ Back to top](#table-of-contents)

## 12. Spring Data JPA

### 12.1 Repository hierarchy and query methods

```text
 Repository (marker)
   └─ CrudRepository        save, findById, findAll, delete, count
        └─ ListCrudRepository (List instead of Iterable, Boot 3)
   └─ PagingAndSortingRepository
        └─ JpaRepository    flush, saveAndFlush, deleteAllInBatch, getReferenceById
 Implementation at runtime: SimpleJpaRepository behind a JDK proxy
 SimpleJpaRepository is @Transactional(readOnly = true) by default; write methods @Transactional
```

```java
interface CardRepository extends JpaRepository<Card, Long>, JpaSpecificationExecutor<Card> {

    List<Card> findByStatusAndExpiryBefore(Status s, LocalDate d);          // derived query
    Optional<Card> findFirstByCustomerIdOrderByCreatedAtDesc(Long cid);
    boolean existsByPanHash(String hash);
    long countByStatus(Status s);

    @Query("select c from Card c join fetch c.customer where c.id = :id")
    Optional<Card> findWithCustomer(@Param("id") Long id);

    @Query(value = "select * from card where status = :s", nativeQuery = true)
    List<Card> nativeByStatus(@Param("s") String s);

    @Modifying @Query("update Card c set c.status = :s where c.id in :ids")
    int updateStatus(Status s, List<Long> ids);

    Page<Card> findByStatus(Status s, Pageable p);       // + count query
    Slice<Card> findSliceByStatus(Status s, Pageable p); // no count query (cheaper, "has next")
}
```

---

### 12.2 Projections

```java
// interface-based (closed) projection — selects only these columns
interface CardSummary { Long getId(); String getHolder(); }
List<CardSummary> findByCustomerId(Long id);

// class/record-based DTO projection
record CardView(Long id, String holder) {}
@Query("select new com.acme.CardView(c.id, c.holder) from Card c where c.status = :s")
List<CardView> views(Status s);

// dynamic projection
<T> List<T> findByStatus(Status s, Class<T> type);
```

💡 Projections avoid entity overhead: no dirty checking, no persistence context, fewer columns.

---

### 12.3 Specifications for dynamic filters

```java
static Specification<Card> hasStatus(Status s) { return (r, q, cb) -> s == null ? null : cb.equal(r.get("status"), s); }
static Specification<Card> holderLike(String h) { return (r, q, cb) -> h == null ? null : cb.like(r.get("holder"), "%" + h + "%"); }

Page<Card> page = repo.findAll(Specification.where(hasStatus(st)).and(holderLike(name)), pageable);
```

(Alternatives: Querydsl, jOOQ, Criteria API directly.)

---

[⬆ Back to top](#table-of-contents)

## 13. Spring Security

### 13.1 Filter chain architecture

```text
 Request
   │
   ▼
 DelegatingFilterProxy        (servlet filter registered in container, delegates to Spring bean)
   │
   ▼
 FilterChainProxy ("springSecurityFilterChain")
   │ picks the FIRST matching SecurityFilterChain (by request matcher)
   ▼
 SecurityFilterChain:
   SecurityContextHolderFilter
   → CorsFilter → CsrfFilter → LogoutFilter
   → UsernamePasswordAuthenticationFilter / BearerTokenAuthenticationFilter (JWT)
   → ... → ExceptionTranslationFilter (401 / 403)
   → AuthorizationFilter (checks access rules)
   │
   ▼
 DispatcherServlet → Controller (@PreAuthorize via method-security AOP proxy)
```

---

### 13.2 Authentication flow

```text
 Filter builds Authentication(token, unauthenticated)
     │
     ▼
 AuthenticationManager (ProviderManager)
     │ iterates providers
     ▼
 AuthenticationProvider (DaoAuthenticationProvider / JwtAuthenticationProvider)
     │  DaoAuthenticationProvider ─► UserDetailsService.loadUserByUsername()
     │                             ─► PasswordEncoder.matches(raw, hash)   (BCrypt/Argon2)
     ▼
 Authentication(authenticated, authorities)
     │
     ▼
 SecurityContextHolder.getContext().setAuthentication(...)
   (ThreadLocal by default → available everywhere in this request thread)
```

```java
@Configuration
@EnableMethodSecurity
class SecurityConfig {
    @Bean
    SecurityFilterChain api(HttpSecurity http) throws Exception {
        return http
            .csrf(csrf -> csrf.disable())                         // stateless REST + JWT only
            .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(a -> a
                .requestMatchers("/actuator/health/**").permitAll()
                .requestMatchers(HttpMethod.POST, "/api/v1/cards/**").hasRole("OPERATOR")
                .anyRequest().authenticated())
            .oauth2ResourceServer(o -> o.jwt(Customizer.withDefaults()))   // validates JWT
            .build();
    }
    @Bean PasswordEncoder passwordEncoder() { return PasswordEncoderFactories.createDelegatingPasswordEncoder(); }
}

@Service class CardService {
    @PreAuthorize("hasRole('OPERATOR') and #customerId == authentication.principal.claims['cid']")
    public void block(Long customerId, Long cardId) { ... }
}
```

---

### 13.3 Security interview quick answers

```text
 401 vs 403        : 401 = who are you? (not authenticated)   403 = I know you, not allowed
 Role vs Authority : hasRole('ADMIN') == hasAuthority('ROLE_ADMIN')
 CSRF              : needed for cookie/session auth (browser sends cookies automatically);
                     not needed for stateless APIs using Authorization: Bearer header
 CORS              : browser policy for cross-origin calls; configure allowed origins, NOT "*"
                     with credentials
 JWT               : signed (JWS), not encrypted by default; validate signature, exp, iss, aud;
                     short-lived access token + refresh token; revocation is hard
 Passwords         : BCrypt/Argon2/PBKDF2 with salt — never MD5/SHA-1, never reversible
 OAuth2 roles      : Resource owner · Client · Authorization server · Resource server
 Flows             : Authorization Code + PKCE (users), Client Credentials (service-to-service)
 mTLS              : both sides present certificates — common between banking services
 SecurityContext in @Async : not propagated by default → DelegatingSecurityContextExecutor
                     or MODE_INHERITABLETHREADLOCAL
```

---

[⬆ Back to top](#table-of-contents)

## 14. Testing Spring Applications

### 14.1 Test pyramid and Spring test slices

```text
            ▲  few      E2E / contract tests (Pact, Spring Cloud Contract)
           ╱ ╲
          ╱   ╲         Integration: @SpringBootTest + Testcontainers (real Postgres/Kafka)
         ╱     ╲
        ╱       ╲       Slice tests: @WebMvcTest, @DataJpaTest, @JsonTest, @RestClientTest
       ╱_________╲ many Unit tests: plain JUnit 5 + Mockito, no Spring context
```

| Annotation | Loads | Use |
|---|---|---|
| `@SpringBootTest` | full context | integration tests (`webEnvironment = RANDOM_PORT`) |
| `@WebMvcTest(CardController.class)` | MVC layer only + `MockMvc` | controllers, validation, JSON, security |
| `@DataJpaTest` | JPA, repositories, embedded/test DB, tx rollback per test | queries, mappings |
| `@JsonTest` | Jackson | serialization |
| `@MockitoBean` (Boot 3.4+; was `@MockBean`) | replaces a bean with a Mockito mock | isolate dependencies |

```java
@WebMvcTest(CardController.class)
class CardControllerTest {
    @Autowired MockMvc mvc;
    @MockitoBean CardService service;

    @Test void returns404() throws Exception {
        when(service.get(1L)).thenThrow(new CardNotFound(1L));
        mvc.perform(get("/api/v1/cards/1")).andExpect(status().isNotFound());
    }
}

@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Testcontainers
class CardRepositoryTest {
    @Container @ServiceConnection                       // Boot 3.1+: auto-wires datasource
    static PostgreSQLContainer<?> pg = new PostgreSQLContainer<>("postgres:16");
    @Autowired CardRepository repo;
    @Test void findsExpired() { ... }
}
```

💡 Context caching: tests with the same configuration share the Spring context — too many different `@MockitoBean` combinations = many contexts = slow test suite.

---

[⬆ Back to top](#table-of-contents)

## 15. Microservices with Spring — quick senior topics

```text
 ┌──────────┐   ┌───────────────┐   ┌───────────┐
 │ Client   │──►│ API Gateway   │──►│ Service A │──► DB A   (database per service)
 └──────────┘   │ (Spring Cloud │   └─────┬─────┘
                │  Gateway)     │         │ async events
                └───────────────┘         ▼
                                      ┌────────┐      ┌───────────┐
                                      │ Kafka  │ ───► │ Service B │──► DB B
                                      └────────┘      └───────────┘
 Cross-cutting: config server/K8s ConfigMaps · discovery (Eureka / K8s DNS) ·
                tracing (Micrometer + OTel) · centralized logs · metrics
```

| Pattern | Tool | Why |
|---|---|---|
| Circuit breaker | Resilience4j `@CircuitBreaker` | stop calling a failing service, fail fast, fallback |
| Retry + backoff + jitter | Resilience4j `@Retry`, Spring Retry | transient failures (only for idempotent ops!) |
| Bulkhead | Resilience4j | isolate thread/connection pools per dependency |
| Rate limiter | Resilience4j, gateway | protect resources |
| Timeouts | client config | never wait forever |
| Saga / Outbox | see 9.6 | data consistency without 2PC |
| Idempotent consumer | processed-message table / unique key | at-least-once delivery from Kafka |
| API versioning | `/v1/` path or header | backward compatibility |

```text
 Circuit breaker states
   CLOSED ──(failure rate > threshold)──► OPEN ──(wait duration)──► HALF_OPEN
     ▲                                                               │
     └────────────(trial calls succeed)──────────────────────────────┘
                   (trial calls fail) ──► back to OPEN
```

```java
@CircuitBreaker(name = "cardApi", fallbackMethod = "cachedLimits")
@Retry(name = "cardApi")
public Limits limits(String cardId) { return client.limits(cardId); }
Limits cachedLimits(String cardId, Throwable t) { return cache.get(cardId); }
```

[⬆ Back to top](#table-of-contents)

## 16. Networking Basics for Backend Interviews

### 16.1 Network layers (TCP/IP vs OSI)

```text
  OSI (7)              TCP/IP (4)        Examples
  ─────────────────    ──────────────    ─────────────────────────────────
  7 Application  ┐
  6 Presentation ├──►  Application       HTTP, HTTPS, WebSocket, gRPC, DNS, SMTP
  5 Session      ┘                       (TLS sits between app and transport)
  4 Transport    ───►  Transport         TCP, UDP, (QUIC over UDP)
  3 Network      ───►  Internet          IP, ICMP
  2 Data link    ┐
  1 Physical     ┘──►  Link              Ethernet, Wi-Fi
```

---

### 16.2 TCP vs TLS vs UDP

| Protocol | What it does | Reliable? | Speed | Used for |
|---|---|---|---|---|
| TCP | connection-oriented, ordered, error-checked, retransmits, flow/congestion control | ✅ | medium | web, DB connections, email, file transfer |
| TLS | encrypts + authenticates on top of TCP (DTLS/QUIC for UDP) | n/a | adds handshake overhead | HTTPS, WSS, mTLS between services |
| UDP | connectionless datagrams, no ordering/retransmit | ❌ | fast | streaming, VoIP, gaming, DNS, HTTP/3 (QUIC) |

```text
 TCP 3-way handshake            TLS 1.3 handshake (1 round trip)
 Client        Server           Client                             Server
   │── SYN ──────►│               │── ClientHello (+key share) ─────►│
   │◄── SYN-ACK ──│               │◄─ ServerHello, cert, Finished ───│
   │── ACK ──────►│               │── Finished ─────────────────────►│
   connection open                encrypted application data ⇄
```

---

### 16.3 HTTP vs HTTPS vs WebSocket

| Protocol | Encrypted? | Communication | Connection | Best for |
|---|---|---|---|---|
| HTTP | ❌ | request → response | short-lived (keep-alive reuses TCP) | basic web, internal APIs |
| HTTPS | ✅ TLS | request → response | short-lived | secure browsing & APIs |
| WebSocket | optional (`wss://`) | full-duplex, server can push | persistent (starts as HTTP `Upgrade`) | chat, live dashboards, games |

```text
 HTTP/1.1 : one request at a time per connection (head-of-line blocking)
 HTTP/2   : binary, multiplexed streams over ONE TCP connection, header compression (gRPC uses it)
 HTTP/3   : over QUIC (UDP), no TCP head-of-line blocking

 WebSocket upgrade:
   GET /ws  Upgrade: websocket  Connection: Upgrade
   ◄── 101 Switching Protocols ──  then frames both ways ⇄
```

---

[⬆ Back to top](#table-of-contents)

## 17. Last-Minute Review and Senior Answer Tips

### 17.1 Rapid-fire one-liners

```text
 JAVA CORE
 • equals ⇒ same hashCode; not vice versa
 • Java is pass-by-value; for objects the VALUE is the reference
 • final locks the reference, not the object
 • String pool lives in heap (7+); Metaspace replaced PermGen (8)
 • Integer cache -128..127 → compare wrappers with equals()
 • Checked = recoverable, must handle; unchecked = programming bug
 • Generics erased at runtime; PECS: producer extends, consumer super

 JVM / GC
 • GC = reachability from roots, cycles are collected
 • Most objects die young → generational GC
 • G1 default; ZGC for sub-ms pauses; Parallel for throughput
 • Leak = reachable but unneeded (caches, ThreadLocal, listeners)
 • Heap dump + MAT → path to GC roots

 CONCURRENCY
 • volatile = visibility + ordering, NOT atomicity
 • synchronized = mutual exclusion + visibility, reentrant
 • Deadlock = 4 Coffman conditions; break circular wait by lock ordering
 • ThreadLocal.remove() in finally in pools
 • Bounded queues; unbounded Executors factories can OOM
 • CompletableFuture: give it your own executor for I/O
 • Virtual threads: I/O-bound, don't pool, avoid pinning

 COLLECTIONS
 • HashMap: 16 buckets, 0.75 LF, treeify at 8 (cap ≥ 64), resize ×2
 • ConcurrentHashMap: CAS + per-bin sync, no nulls
 • ArrayList > LinkedList almost always
 • Fail-fast iterators throw CME; use iterator.remove / removeIf

 HIBERNATE
 • States: transient, managed, detached, removed
 • L1 cache = persistence context; L2 = SessionFactory-wide, opt-in
 • Dirty checking at flush; flush ≠ commit
 • LAZY everything; fix N+1 with JOIN FETCH / EntityGraph / BatchSize / DTO
 • IDENTITY kills batch inserts → SEQUENCE with allocationSize
 • @Version for optimistic locking; FOR UPDATE for pessimistic
 • Owning side = side with the FK (@ManyToOne); keep both sides in sync

 SPRING
 • Constructor injection; singleton beans must be stateless
 • Proxy created in BPP after-initialization
 • Self-invocation bypasses proxy (@Transactional, @Async, @Cacheable)
 • Rollback only for unchecked by default
 • REQUIRES_NEW = separate tx & connection; NESTED = savepoint
 • Auto-config = conditions + back-off (@ConditionalOnMissingBean)
 • Disable OSIV; @TransactionalEventListener(AFTER_COMMIT) for side effects
```

---

### 17.2 How to answer like a senior

```text
 1. DEFINITION      one crisp sentence
 2. HOW IT WORKS    internals / a quick figure on the whiteboard
 3. TRADE-OFFS      when to use, when NOT to use, alternatives
 4. REAL EXPERIENCE "In our card-issuing platform we hit ... we measured ... we fixed by ..."
 5. RESULT          numbers: latency p99, throughput, memory, incidents avoided
```

Prepare a short story (STAR: Situation, Task, Action, Result) for each:

```text
 □ A deadlock / thread contention you debugged (thread dump, lock ordering)
 □ A memory leak or OOM you investigated (heap dump, MAT, fix)
 □ A slow endpoint you optimized (N+1, missing index, batch size, caching)
 □ A GC tuning or JVM sizing decision (G1 vs ZGC, container memory)
 □ A transaction / data-consistency bug (lost update → @Version, outbox)
 □ A production incident you led (detection, mitigation, root cause, prevention)
 □ A design decision you defended (trade-offs, what you would change today)
```


[⬆ Back to top](#table-of-contents)
