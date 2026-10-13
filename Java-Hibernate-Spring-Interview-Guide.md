# Senior Java Developer Interview Guide — Java, Hibernate, Spring

> Read-and-memorize edition: every topic = **short answer → text figure → code snippet → trap to remember**.
> Built from my own notes (memory management, concurrency, collections, networking, unresolved questions) and extended with the remaining topics a senior Java / Hibernate / Spring interview usually covers.

<div dir="rtl">

> **این راهنما دو صدا دارد:** متن، شکل‌ها و کدها به انگلیسی‌اند، همان زبانی که در مصاحبه حرف می‌زنی. کنار هر مبحث یک پاراگراف فارسی هست که مفهوم را «به زبان خودمان» توضیح می‌دهد، و یک **قلاب حافظه**: تشبیهی کوتاه که مفهوم را به تصویری آشنا گره می‌زند. روش پیشنهادی: اول پاراگراف فارسی، بعد شکل، بعد کد، و در آخر قلاب حافظه را با صدای بلند تکرار کن.

- 📜 **دروازه‌ی فصل:** مقدمه‌ی کوتاه هر فصل؛ تصویر کلی قبل از جزئیات
- 🪶 **به زبان ساده:** توضیح فارسی هر مبحث
- 🧠 **قلاب حافظه:** یک تصویر ذهنی برای به خاطر سپردن

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** جاوا را مثل یک شهر تصور کن: کلاس‌ها خانه‌ها هستند، آبجکت‌ها ساکنانشان، و قوانین OOP قانون اساسی این شهر. در این فصل پایه‌هایی را مرور می‌کنیم که مصاحبه‌گر سنیور از آن‌ها شروع می‌کند تا ببیند فقط «کد می‌نویسی» یا واقعاً «می‌فهمی چرا» این‌طور می‌نویسی.

</div>

### 1.1 JDK vs JRE vs JVM and how Java code runs

<div dir="rtl">

> 🪶 **به زبان ساده:** JDK جعبه‌ابزار کامل توسعه‌دهنده است (کامپایلر javac، ابزارهای jcmd و jshell و...) و JRE را هم در خودش دارد. JRE یعنی JVM به‌علاوه‌ی کتابخانه‌های استاندارد، و JVM موتوری است که bytecode را بارگذاری، بررسی و اجرا می‌کند. نکته‌ی سنیور این است که جاوا فقط «تفسیری» نیست: کد اول با Interpreter اجرا می‌شود و متدهای پرتکرار (hot) توسط JIT به کد native تبدیل می‌شوند؛ برای همین برنامه‌ی جاوا بعد از warm-up سریع‌تر می‌شود.
>
> 🧠 **قلاب حافظه:** JDK آشپزخانه است، JRE میز غذا، JVM همان آشپزی که اول از روی دستور پخت می‌خواند (Interpreter) و غذاهای پرسفارش را از حفظ و سریع می‌پزد (JIT).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** چهار ستون OOP: کپسوله‌سازی یعنی وضعیت داخلی را پنهان کن و فقط رفتار را نشان بده؛ انتزاع یعنی بگو «چه کاری» انجام می‌شود، نه «چطور»؛ وراثت یعنی رابطه‌ی IS-A و استفاده‌ی مجدد؛ و چندریختی یعنی یک رابط با چند پیاده‌سازی که در زمان اجرا انتخاب می‌شود. در مصاحبه هر کدام را با یک مثال واقعی (مثل Payment و CardPayment) توضیح بده، نه با تعریف کتابی.
>
> 🧠 **قلاب حافظه:** ماشین: داشبورد (انتزاع)، موتور زیر کاپوت (کپسوله‌سازی)، مدل اسپرت از مدل پایه (وراثت)، و پدال گاز که در هر ماشینی کار می‌کند ولی هر موتور به روش خودش جواب می‌دهد (چندریختی).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** کپسوله‌سازی وقتی می‌شکند که دنیای بیرون بتواند بدون اجازه‌ی آبجکت، وضعیت داخلی‌اش را عوض کند: فیلد public، setter بدون اعتبارسنجی، یا برگرداندن مستقیم لیست داخلی. راه درست: فیلدها private، اعتبارسنجی در setter، و کپی دفاعی (defensive copy) هم در ورودی سازنده و هم در خروجی getter. یک مثال سنیوری هم این است که Entity های JPA را مستقیم از REST API بیرون ندهی و DTO برگردانی.
>
> 🧠 **قلاب حافظه:** getter که لیست داخلی را برمی‌گرداند مثل این است که کلید خانه‌ات را به مهمان بدهی؛ کپی دفاعی یعنی فقط عکس اتاق را نشانش بدهی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Overloading یعنی چند متد هم‌نام با پارامترهای متفاوت در یک کلاس، که کامپایلر بر اساس نوع «ایستا»ی آرگومان در زمان کامپایل یکی را انتخاب می‌کند. Overriding یعنی زیرکلاس متد والد را با همان امضا بازنویسی کند و انتخاب در زمان اجرا بر اساس نوع واقعی آبجکت انجام شود. تله‌ی معروف: متدهای static override نمی‌شوند بلکه hide می‌شوند، و overload بر اساس نوع متغیر انتخاب می‌شود نه نوع آبجکت.
>
> 🧠 **قلاب حافظه:** Overload را کامپایلر از روی «برچسب جعبه» انتخاب می‌کند؛ Override را JVM از روی «محتوای واقعی جعبه».

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** کلاس abstract برای وقتی است که چند کلاس وضعیت (فیلد) و رفتار مشترک دارند و می‌خواهی یک اسکلت (Template Method) بسازی؛ فقط می‌شود از یکی ارث برد. Interface یک قرارداد یا «توانایی» است، می‌شود چندتا را پیاده کرد و از جاوا ۸ متد default و static و از جاوا ۹ متد private هم دارد. اگر دو interface متد default یکسان داشته باشند، کلاس باید خودش override کند و با `A.super.method()` یکی را انتخاب کند.
>
> 🧠 **قلاب حافظه:** Abstract class «شناسنامه» است (تو چه هستی)، Interface «گواهینامه» است (تو چه کاری بلدی) — یک شناسنامه داری ولی می‌توانی چند گواهینامه داشته باشی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** قرارداد طلایی: اگر دو آبجکت equals باشند، hashCode آن‌ها حتماً باید برابر باشد؛ ولی hashCode برابر به معنی equals بودن نیست (collision). اگر equals را override کنی و hashCode را نه، دو آبجکت «برابر» در دو bucket متفاوت HashMap می‌افتند و get نتیجه‌ی null می‌دهد. همچنین کلیدی که بعد از put تغییر کند، در bucket اشتباه گم می‌شود. ساده‌ترین راه امروز: record.
>
> 🧠 **قلاب حافظه:** hashCode شماره‌ی قفسه‌ی کتابخانه است و equals عنوان کتاب؛ اگر شماره‌ی قفسه غلط باشد، هرگز به عنوان کتاب نمی‌رسی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** کلاس immutable بعد از ساخته شدن هیچ‌وقت تغییر نمی‌کند: کلاس final، فیلدها private final، بدون setter، کپی دفاعی برای ورودی‌ها و خروجی‌های mutable، و متدهای withX که آبجکت جدید برمی‌گردانند. فایده‌اش thread-safety رایگان، کلید امن برای HashMap و قابلیت cache است. Record ها به‌صورت سطحی (shallow) immutable هستند، پس لیست داخلشان را هم باید با List.copyOf کپی کنی.
>
> 🧠 **قلاب حافظه:** آبجکت immutable مثل سنگ‌نوشته است: برای تغییر، سنگ جدید می‌تراشی، روی قبلی خط نمی‌کشی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** String تغییرناپذیر است تا بتواند در String Pool به اشتراک گذاشته شود، hashCode اش cache شود و از نظر امنیتی (مثل نام کلاس یا URL) بعد از بررسی عوض نشود. برای ساختن رشته در حلقه از StringBuilder استفاده کن چون += هر بار یک String جدید می‌سازد. StringBuffer همان StringBuilder است ولی synchronized و قدیمی. برای رمز عبور char[] بهتر است چون می‌توانی صفرش کنی.
>
> 🧠 **قلاب حافظه:** String مثل نامه‌ی مهر و موم شده است؛ StringBuilder دفترچه‌ی چرک‌نویس که رویش هرچقدر بخواهی می‌نویسی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** final سه کاربرد دارد: متغیر فقط یک بار مقداردهی می‌شود، متد override نمی‌شود، و کلاس ارث‌بری نمی‌شود. finally بلوکی است که تقریباً همیشه اجرا می‌شود (مگر System.exit یا crash). finalize یک هوک قدیمی GC است که تضمینی برای اجرایش نیست و deprecated شده. تله‌ی مصاحبه: return داخل finally مقدار return بلوک try را می‌بلعد و حتی exception را هم قورت می‌دهد.
>
> 🧠 **قلاب حافظه:** سه برادر با اسم شبیه: final «قفل» است، finally «نگهبان آخر شب»، finalize «پیرمرد بازنشسته‌ای که دیگر به او اعتماد نمی‌کنیم».

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** کلاس تودرتوی static هیچ ارجاعی به آبجکت بیرونی ندارد و انتخاب پیش‌فرض و امن است. Inner class (غیر static) یک ارجاع پنهان به Outer.this نگه می‌دارد؛ اگر آن را به یک آبجکت بلندعمر مثل listener یا thread بدهی، کل آبجکت بیرونی در حافظه می‌ماند و memory leak درست می‌شود. Local class داخل متد و anonymous class پیاده‌سازی یک‌باره است که قبل از lambda رایج بود.
>
> 🧠 **قلاب حافظه:** Inner class بچه‌ای است که همیشه دست مادرش (Outer) را گرفته؛ تا بچه زنده است، مادر هم نمی‌تواند برود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** ریشه‌ی همه Throwable است که به Error (مثل OutOfMemoryError که نباید catch شود) و Exception تقسیم می‌شود. Exception های checked باید declare یا catch شوند و برای شرایط قابل‌بازیابی‌اند؛ RuntimeException ها unchecked هستند و معمولاً باگ برنامه‌نویسی را نشان می‌دهند. try-with-resources منابع را به ترتیب معکوس می‌بندد و خطاهای close را به‌صورت suppressed نگه می‌دارد. جواب سنیور: هیچ‌وقت exception را قورت نده و همیشه cause را حفظ کن.
>
> 🧠 **قلاب حافظه:** Checked exception مثل چراغ زرد است که قانوناً باید به آن واکنش نشان دهی؛ unchecked مثل چاله‌ای است که نباید از اول آنجا می‌بود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Generics فقط در زمان کامپایل وجود دارند و در runtime با type erasure پاک می‌شوند؛ برای همین نمی‌توانی new T() یا instanceof List<String> بنویسی. قانون PECS: اگر از ساختار «می‌خوانی» (producer) از extends استفاده کن و اگر در آن «می‌نویسی» (consumer) از super. Generics برخلاف آرایه‌ها invariant هستند، یعنی List<String> زیرنوع List<Object> نیست.
>
> 🧠 **قلاب حافظه:** PECS: «تولیدکننده extends می‌دهد، مصرف‌کننده super می‌گیرد» — از سبد میوه (extends Fruit) سیب برمی‌داری، ولی در سبد super Apple سیب می‌گذاری.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Annotation یک برچسب (metadata) است که کسی باید آن را بخواند: کامپایلر، processor یا فریم‌ورک در زمان اجرا با reflection. annotation سفارشی را وقتی می‌سازی که بخواهی رفتاری را «اعلانی» (declarative) اضافه کنی: audit، rate limit، retry، قانون اعتبارسنجی یا علامت‌گذاری برای اسکن. Retention مشخص می‌کند برچسب تا کجا زنده بماند (SOURCE، CLASS، RUNTIME) و Target مشخص می‌کند کجا می‌شود آن را گذاشت. ترکیب annotation با Spring AOP قدرتمندترین الگوی واقعی آن است.
>
> 🧠 **قلاب حافظه:** Annotation برچسب «شکستنی» روی جعبه است؛ خودش کاری نمی‌کند، ولی باربر (Aspect) با دیدنش با احتیاط حمل می‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Reflection یعنی برنامه در زمان اجرا کلاس‌ها، فیلدها و متدهای خودش را ببیند و حتی صدا بزند. Spring، Hibernate و Jackson همه روی reflection و proxy ساخته شده‌اند. هزینه‌اش: کندتر از فراخوانی مستقیم، بدون بررسی نوع در کامپایل، و شکستن کپسوله‌سازی با setAccessible؛ از جاوا ۹ ماژول‌ها دسترسی عمیق را محدود کرده‌اند.
>
> 🧠 **قلاب حافظه:** Reflection آینه‌ای است که برنامه در آن خودش را می‌بیند — و گاهی با آن قفل درِ اتاق‌های private را هم باز می‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** اندیس آرایه در واقع «فاصله» (offset) از ابتدای بلوک پیوسته‌ی حافظه است: آدرس عنصر i برابر است با base + i * size. وقتی اولین عنصر در فاصله‌ی صفر باشد، دیگر نیازی به تفریق یک نیست و محاسبه مستقیم‌تر است. جاوا این قرارداد را از C به ارث برده که در آن `a[i]` همان `*(a + i)` است؛ بازه‌های نیمه‌باز `[0, n)` و فرمول‌هایی مثل `i % n` هم با شروع از صفر ساده‌تر می‌شوند.
>
> 🧠 **قلاب حافظه:** اندیس، شماره‌ی «قدم» از درِ ورودی است نه شماره‌ی اتاق: اتاق اول صفر قدم با در فاصله دارد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Serialization یعنی تبدیل آبجکت به بایت برای ذخیره یا ارسال، و serialVersionUID نسخه‌ی قالب کلاس است؛ اگر عوض شود، deserialize با InvalidClassException شکست می‌خورد. فیلدهای transient و static سریالایز نمی‌شوند. جواب سنیور: serialization بومی جاوا از نظر امنیتی خطرناک (deserialization gadget) و شکننده است و برای ارتباط بین سرویس‌ها JSON، Avro یا Protobuf بهتر است.
>
> 🧠 **قلاب حافظه:** serialVersionUID شماره‌ی ویرایش نقشه‌ی ساختمان است؛ با نقشه‌ی ویرایش دیگر نمی‌توانی همان ساختمان را دوباره بسازی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** کپی سطحی (shallow) فقط ارجاع‌ها را کپی می‌کند، پس آبجکت‌های داخلی بین نسخه‌ی اصلی و کپی مشترک می‌مانند؛ کپی عمیق (deep) آبجکت‌های داخلی را هم از نو می‌سازد. Cloneable یک طراحی خراب است: متد clone در خود interface نیست، پیش‌فرضش سطحی است و سازنده را دور می‌زند. راه بهتر copy constructor یا static factory است.
>
> 🧠 **قلاب حافظه:** کپی سطحی مثل دو کلید برای یک خانه است؛ کپی عمیق یعنی یک خانه‌ی دوم ساختی.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** جاوا دیگر آن زبان پرحرف دهه‌ی ۲۰۰۰ نیست. از جاوا ۸ تا ۲۱ زبان یاد گرفته کوتاه‌تر، تابعی‌تر و امن‌تر حرف بزند. مصاحبه‌گر انتظار دارد بدانی کدام قابلیت در کدام نسخه آمد و مهم‌تر از آن، چه مشکلی را حل کرد.

</div>

### 2.1 Version map — what came when

<div dir="rtl">

> 🪶 **به زبان ساده:** سه نسخه‌ی LTS مهم را حفظ کن: ۸ (lambda، stream، Optional، java.time)، ۱۷ (sealed class و پایه‌ی Spring Boot 3) و ۲۱ (virtual thread، pattern matching در switch، record pattern، sequenced collections). در میانه var در ۱۰، switch expression در ۱۴، text block در ۱۵ و record در ۱۶ آمدند. جاوا ۲۵ هم LTS بعدی است با ScopedValue و ساده‌سازی main.
>
> 🧠 **قلاب حافظه:** ۸ «انقلاب تابعی»، ۱۷ «نظم و قانون (sealed)»، ۲۱ «میلیون‌ها نخ سبک» — سه ایستگاه اصلی قطار جاوا.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Lambda یک تابع بی‌نام است که فقط جایی استفاده می‌شود که یک functional interface (دقیقاً یک متد abstract) انتظار می‌رود. چهار interface اصلی را حفظ کن: Supplier چیزی می‌دهد، Consumer چیزی می‌گیرد، Function تبدیل می‌کند و Predicate قضاوت می‌کند. متغیرهای محلی که lambda استفاده می‌کند باید effectively final باشند، و andThen و compose ترتیب ترکیب توابع را برعکس هم انجام می‌دهند.
>
> 🧠 **قلاب حافظه:** Supplier نانوا است (می‌دهد)، Consumer مشتری (می‌گیرد)، Function آسیاب (گندم را آرد می‌کند)، Predicate نگهبان در (بله یا نه).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Stream یک خط تولید است: منبع، عملیات میانی تنبل (filter، map) و یک عملیات پایانی که همه‌چیز را راه می‌اندازد. عناصر یکی‌یکی از کل زنجیره عبور می‌کنند، مگر عملیات stateful مثل sorted که باید همه را جمع کند. هر stream فقط یک بار مصرف می‌شود، و parallel stream از commonPool مشترک استفاده می‌کند، پس برای I/O و داده‌ی کم مناسب نیست. groupingBy، partitioningBy و toMap با تابع merge کالکتورهای پرتکرار مصاحبه‌اند.
>
> 🧠 **قلاب حافظه:** Stream نوار نقاله‌ی کارخانه است: تا دکمه‌ی پایانی (terminal) را نزنی، هیچ قطعه‌ای حرکت نمی‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Optional برای «نوع برگشتی متدی که ممکن است چیزی پیدا نکند» طراحی شده، نه برای فیلد یا پارامتر. به‌جای isPresent و get از map، orElse، orElseThrow و ifPresent استفاده کن. تله‌ی مهم: orElse(expensiveCall()) همیشه اجرا می‌شود، حتی اگر مقدار وجود داشته باشد؛ برای محاسبات سنگین از orElseGet استفاده کن.
>
> 🧠 **قلاب حافظه:** Optional جعبه‌ی کادویی است که شاید خالی باشد؛ مؤدبانه بازش کن (map/orElse)، با چکش نشکنش (get).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Record یک حامل داده‌ی immutable است که سازنده، accessor، equals، hashCode و toString را خودکار می‌سازد. Sealed مشخص می‌کند دقیقاً چه کلاس‌هایی اجازه‌ی پیاده‌سازی دارند، پس switch روی آن می‌تواند کامل بودن (exhaustiveness) را در کامپایل بررسی کند و default لازم ندارد. Pattern matching در instanceof و switch و record pattern ها، cast و استخراج دستی فیلدها را حذف می‌کنند. Record برای Entity در JPA مناسب نیست ولی برای DTO و projection عالی است.
>
> 🧠 **قلاب حافظه:** sealed فهرست مهمانان یک جشن خصوصی است؛ switch دم در می‌داند دقیقاً چه کسانی می‌آیند و کسی جا نمی‌ماند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** جاوا ۲۱ برای مجموعه‌هایی که ترتیب مشخص دارند (List، Deque، LinkedHashSet، LinkedHashMap) یک API یکسان اضافه کرد: getFirst، getLast، addFirst، reversed و برای Map ها firstEntry و pollLastEntry. قبلاً برای گرفتن آخرین عنصر هر ساختار راه متفاوتی داشتی.
>
> 🧠 **قلاب حافظه:** هر صفی بالاخره یک «اول» و یک «آخر» دارد؛ جاوا ۲۱ بالاخره برای همه یک زبان مشترک ساخت.

</div>

```java
List<String> l = new ArrayList<>(List.of("a","b","c"));
l.getFirst(); l.getLast(); l.reversed();        // new uniform API
LinkedHashMap<String,Integer> m = new LinkedHashMap<>();
m.putFirst("x", 1); m.firstEntry(); m.pollLastEntry();
```

[⬆ Back to top](#table-of-contents)

## 3. JVM Memory Management and Garbage Collection

<div dir="rtl">

📜 **دروازه‌ی فصل:** اگر JVM یک شهر باشد، Heap انبار بزرگ شهر است، Stack میز کار هر کارگر، Metaspace کتابخانه‌ی نقشه‌ها، و GC رفتگری که شب‌ها آنچه دیگر کسی به آن دسترسی ندارد را جمع می‌کند. این فصل قلب مصاحبه‌های سنیور است: هم «حافظه کجا می‌رود» و هم «چرا نشت می‌کند».

</div>

### 3.1 JVM memory areas (modern JVM)

<div dir="rtl">

> 🪶 **به زبان ساده:** Heap بین همه‌ی thread ها مشترک است و آبجکت‌ها، آرایه‌ها، String Pool (از جاوا ۷) و فیلدهای static (از جاوا ۸) در آن هستند. هر thread یک Stack اختصاصی دارد که frame متدها، متغیرهای محلی و ارجاع‌ها را نگه می‌دارد. Metaspace (جایگزین PermGen از جاوا ۸) در حافظه‌ی native است و متادیتای کلاس‌ها را نگه می‌دارد. انواع OutOfMemoryError را هم بشناس، چون هر کدام به یک ناحیه اشاره می‌کند.
>
> 🧠 **قلاب حافظه:** Heap انبار مشترک کارخانه، Stack میز شخصی هر کارگر، Metaspace قفسه‌ی نقشه‌های فنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** متغیر محلی primitive و «ارجاع» روی stack هستند، ولی خود آبجکت همیشه روی heap ساخته می‌شود. وقتی متد تمام شود، frame از stack برداشته می‌شود و اگر ارجاع دیگری به آبجکت نماند، آبجکت قابل جمع‌آوری توسط GC می‌شود. مصاحبه‌گر می‌خواهد ببیند تفاوت «ارجاع» و «آبجکت» را می‌فهمی.
>
> 🧠 **قلاب حافظه:** روی میز (stack) فقط یادداشتِ «آدرس انبار» است؛ خود کالا در انبار (heap) است.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** جاوا همیشه pass-by-value است: هر چیزی که داخل متغیر است کپی و ارسال می‌شود. برای primitive خود عدد کپی می‌شود و برای آبجکت «آدرس» (مقدار ارجاع) کپی می‌شود. پس داخل متد می‌توانی محتوای آبجکت مشترک را عوض کنی و بیرون هم دیده می‌شود، ولی اگر پارامتر را به آبجکت جدیدی اشاره بدهی، فقط کپی محلی عوض شده و متغیر caller دست‌نخورده می‌ماند. سؤال tricky را هم با همین قانون حل کن: تغییر قبل از new دیده می‌شود، بعدش نه.
>
> 🧠 **قلاب حافظه:** به دوستت «فتوکپی آدرس خانه‌ات» را می‌دهی: می‌تواند برود و دیوار خانه را رنگ کند (mutate)، ولی اگر آدرس روی کاغذ خودش را خط بزند، خانه‌ی تو جابه‌جا نمی‌شود (reassign).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** final فقط ارجاع را قفل می‌کند، نه محتوای آبجکت را. یعنی final Person p نمی‌تواند به Person دیگری اشاره کند، ولی p.name را می‌توانی عوض کنی؛ final List هم همچنان add می‌پذیرد. برای اینکه خود آبجکت تغییرناپذیر باشد باید کلاس را immutable طراحی کنی یا از List.of استفاده کنی.
>
> 🧠 **قلاب حافظه:** final قلاده‌ای است که سگ را به یک تیرک بسته؛ سگ نمی‌تواند تیرک عوض کند، ولی هنوز می‌تواند پارس کند و غذایش را عوض کنند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** GC بر اساس «دسترسی‌پذیری از ریشه‌ها» (GC Roots) کار می‌کند: متغیرهای محلی thread ها، فیلدهای static، thread های فعال و... . هر آبجکتی که از ریشه‌ها مسیری به آن نباشد زباله است، حتی اگر دو آبجکت به هم ارجاع حلقوی داشته باشند، چون جاوا reference counting نیست. System.gc() هم فقط یک پیشنهاد است و تضمینی ندارد.
>
> 🧠 **قلاب حافظه:** GC از ریشه‌ها طناب می‌کشد؛ هر بادکنکی که به هیچ طنابی وصل نیست می‌ترکد، حتی اگر دو بادکنک به هم گره خورده باشند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** literal های رشته‌ای در String Pool ذخیره و به اشتراک گذاشته می‌شوند، پس دو "java" با == برابرند. new String همیشه آبجکت جدیدی روی heap می‌سازد و intern ارجاع نسخه‌ی pool را برمی‌گرداند. الحاق ثابت‌ها در زمان کامپایل («ja» + «va») pool می‌شود، ولی الحاق با متغیر در زمان اجرا آبجکت جدید می‌سازد. نتیجه‌ی اخلاقی: محتوا را همیشه با equals مقایسه کن.
>
> 🧠 **قلاب حافظه:** String Pool «کمد لباس فرم مشترک» است؛ new String یعنی برای خودت لباس جدید دوختی، intern یعنی برگشتی سراغ کمد مشترک.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Integer.valueOf اعداد بین ۱۲۸- تا ۱۲۷ را cache می‌کند، برای همین 127 == 127 درست است ولی 128 == 128 غلط. وقتی یکی از طرفین primitive باشد، wrapper ابتدا unbox می‌شود و مقایسه عددی است. unbox کردن null به NullPointerException می‌رسد، و autoboxing در حلقه‌های داغ میلیون‌ها آبجکت زائد می‌سازد.
>
> 🧠 **قلاب حافظه:** اعداد ۱۲۸- تا ۱۲۷ «اعضای باشگاه» هستند و کارت عضویت مشترک دارند؛ بقیه هر بار کارت مهمان تازه می‌گیرند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Heap به نسل جوان (Eden و دو Survivor) و نسل پیر تقسیم می‌شود، چون فرضیه‌ی نسلی می‌گوید بیشتر آبجکت‌ها جوان می‌میرند. Minor GC مکرر و سریع است و فقط بازماندگان را کپی می‌کند؛ آبجکت‌هایی که چند بار زنده بمانند به Old Gen منتقل (promote) می‌شوند و Major/Full GC کمتر ولی کندتر رخ می‌دهد. آبجکت‌های بزرگ ممکن است مستقیم وارد Old Gen شوند یا در G1 «humongous» شوند. Stop-The-World یعنی همه‌ی thread های برنامه متوقف می‌شوند.
>
> 🧠 **قلاب حافظه:** Eden زایشگاه است، Survivor مهدکودک، Old Gen خانه‌ی سالمندان؛ رفتگر هر روز مهدکودک را تمیز می‌کند ولی سالی یک بار سراغ خانه‌ی سالمندان می‌رود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Serial تک‌نخی و برای heap های کوچک است، Parallel برای بیشترین throughput در کارهای batch، G1 پیش‌فرض از جاوا ۹ است و heap را به region ها تقسیم می‌کند تا pause قابل پیش‌بینی بدهد، و ZGC و Shenandoah برای pause زیر میلی‌ثانیه روی heap های بزرگ‌اند. CMS در جاوا ۱۴ حذف شد. جواب سنیور همیشه با «اول اندازه‌گیری» شروع می‌شود: GC log، JFR و latency p99، بعد انتخاب collector و سپس tuning اندازه‌ی heap.
>
> 🧠 **قلاب حافظه:** Parallel کامیون (بار زیاد، توقف طولانی)، G1 اتوبوس شهری (ایستگاه‌های منظم)، ZGC مترو (تقریباً هیچ‌وقت نمی‌ایستد).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Escape analysis بررسی می‌کند آیا آبجکت از متد «فرار» می‌کند یا نه؛ یعنی return شود، در فیلد ذخیره شود یا به thread دیگری برسد. اگر فرار نکند، JIT ممکن است با scalar replacement کل تخصیص را حذف کند و فیلدها را به متغیرهای محلی تبدیل کند، یا حتی قفل‌ها را حذف کند. دقت کن که HotSpot آبجکت را واقعاً «روی stack» نمی‌سازد، بلکه تخصیص را حذف می‌کند.
>
> 🧠 **قلاب حافظه:** آبجکتی که از اتاق بیرون نمی‌رود اصلاً لازم نیست برایش اتاق رزرو کنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** ارجاع strong تا وقتی قابل دسترسی است هرگز جمع نمی‌شود. Soft فقط وقتی حافظه تحت فشار است جمع می‌شود (مناسب cache)، Weak در اولین GC بعدی (مثل WeakHashMap)، و Phantom بعد از finalization برای کارهای پاک‌سازی (Cleaner) استفاده می‌شود و get آن همیشه null است. مصاحبه‌گرها معمولاً مقایسه‌ی Soft و Weak را می‌خواهند.
>
> 🧠 **قلاب حافظه:** Strong طناب فولادی، Soft نخ کاموا (فقط در طوفان پاره می‌شود)، Weak تار عنکبوت (با اولین نسیم)، Phantom فقط سایه‌ای که خبر رفتن می‌دهد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** finalize تضمینی برای اجرا ندارد، GC را کند می‌کند و حتی می‌تواند آبجکت را دوباره زنده کند؛ از جاوا ۹ deprecated و از ۱۸ برای حذف علامت خورده است. راه درست برای آزادسازی قطعی منابع AutoCloseable با try-with-resources است، و Cleaner فقط به‌عنوان تور ایمنی برای وقتی که کسی close را صدا نزد.
>
> 🧠 **قلاب حافظه:** به finalize مثل قول «یه روزی برمی‌گردم» نگاه کن؛ close یعنی همین الان در را پشت سرت ببند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** نشت حافظه در جاوا یعنی آبجکت‌هایی که هنوز قابل دسترسی‌اند ولی دیگر لازم نیستند؛ موضوع فقط static نیست. منابع رایج: cache بدون eviction، ThreadLocal بدون remove، listener هایی که unregister نشده‌اند، inner class، کلید mutable و session هایبرنیت پر از entity. روش تشخیص: نمودار heap بعد از Full GC که کفش مدام بالا می‌رود، گرفتن heap dump با jcmd و تحلیل در Eclipse MAT با «مسیر تا GC root».
>
> 🧠 **قلاب حافظه:** نشت حافظه مثل انباری خانه است که هیچ‌چیز را دور نمی‌ریزی چون «شاید یه روز لازم شه» — تا روزی که در دیگر بسته نشود (OOM).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** در هر Thread یک ThreadLocalMap هست که کلیدش (ThreadLocal) weak ولی مقدارش strong است. در thread pool، thread ها هیچ‌وقت نمی‌میرند، پس مقدار هرگز آزاد نمی‌شود؛ بدتر از آن، داده‌ی درخواست قبلی (مثلاً کاربر قبلی) به درخواست بعدی نشت می‌کند که یک باگ امنیتی است. قانون: همیشه remove را در finally صدا بزن.
>
> 🧠 **قلاب حافظه:** ThreadLocal در pool مثل کمد هتل است: اگر مهمان قبلی وسایلش را خالی نکند، مهمان بعدی چمدان او را تحویل می‌گیرد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** ClassLoader ها سلسله‌مراتب دارند: Bootstrap، Platform و Application. هر loader اول از والدش می‌پرسد و فقط اگر والد پیدا نکرد خودش کلاس را بارگذاری می‌کند (delegation)، پس هیچ‌کس نمی‌تواند java.lang.String خودش را جا بزند. یک کلاس با یک نام که توسط دو loader بارگذاری شود، از نظر JVM دو کلاس متفاوت است و به ClassCastException عجیب «X cannot be cast to X» می‌رسد.
>
> 🧠 **قلاب حافظه:** بچه قبل از هر کاری از پدرش می‌پرسد «تو داری؟»؛ فقط اگر پدر نداشت، خودش می‌خرد.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** همروندی یعنی چند آشپز در یک آشپزخانه: اگر قانون نباشد، دو نفر هم‌زمان در یک قابلمه نمک می‌ریزند (race condition)، یا هر کدام چاقوی دیگری را می‌خواهد و هیچ‌کس کار نمی‌کند (deadlock). این فصل قانون‌های این آشپزخانه است و جایی است که مصاحبه‌گر عمق تجربه‌ی واقعی تو را می‌سنجد.

</div>

### 4.1 Thread lifecycle and states

<div dir="rtl">

> 🪶 **به زبان ساده:** Thread در جاوا شش حالت رسمی دارد: NEW، RUNNABLE، BLOCKED، WAITING، TIMED_WAITING و TERMINATED؛ حالت RUNNING جداگانه وجود ندارد و داخل RUNNABLE است. BLOCKED یعنی منتظر قفل synchronized است و WAITING یعنی منتظر کار thread دیگری (wait، join، park). تله‌ها: run() روی همان thread فعلی اجرا می‌شود و فقط start() thread جدید می‌سازد، و start دوباره exception می‌دهد.
>
> 🧠 **قلاب حافظه:** NEW نوزاد، RUNNABLE دونده‌ی آماده در خط، BLOCKED پشت در بسته، WAITING منتظر تلفن، TERMINATED خط پایان.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** مدل حافظه‌ی جاوا (JMM) سه مشکل را تعریف می‌کند: visibility (نوشتن یک thread ممکن است هرگز به thread دیگر دیده نشود چون در cache هسته‌ی CPU مانده)، ordering (کامپایلر و CPU دستورها را جابه‌جا می‌کنند) و atomicity (count++ سه مرحله است). راه‌حل رابطه‌ی happens-before است: unlock قبل از lock بعدی، نوشتن volatile قبل از خواندن آن، start و join و ترایایی. هر چیزی که قبل از نوشتن یک متغیر volatile نوشته شده، بعد از خواندن آن هم دیده می‌شود.
>
> 🧠 **قلاب حافظه:** هر هسته‌ی CPU دفترچه‌ی یادداشت شخصی دارد؛ volatile یعنی «همیشه از تابلوی اعلانات اصلی بخوان و روی همان بنویس».

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** هر آبجکت یک monitor دارد. synchronized هم انحصار متقابل (mutual exclusion) می‌دهد و هم visibility، و reentrant است یعنی همان thread می‌تواند دوباره واردش شود. wait قفل را رها می‌کند و thread را به wait set می‌برد، notify یکی و notifyAll همه را بیدار می‌کند؛ wait همیشه باید داخل حلقه‌ی while باشد چون بیدارشدن کاذب (spurious wakeup) وجود دارد. sleep قفل را رها نمی‌کند، و هرگز روی String literal یا آبجکت عمومی قفل نکن.
>
> 🧠 **قلاب حافظه:** monitor یک دستشویی با یک کلید است: صف پشت در (entry set) و کسانی که گفتند «وقتی آب آمد صدام کن» (wait set).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** volatile فقط visibility و ترتیب را تضمین می‌کند، نه اتمی بودن عملیات ترکیبی مثل i++؛ برای پرچم‌ها مناسب است. synchronized یا Lock برای حفظ invariant بین چند متغیر لازم است. کلاس‌های Atomic با CAS بدون قفل کار می‌کنند و برای شمارنده‌ها عالی‌اند، و LongAdder در رقابت شدید بهتر است چون به‌روزرسانی‌ها را بین چند cell پخش می‌کند. مشکل ABA را هم با AtomicStampedReference حل می‌کنند.
>
> 🧠 **قلاب حافظه:** volatile بلندگوی اعلام است (همه می‌شنوند)، synchronized اتاق دربسته است، CAS یعنی «اگر هنوز همان بود که دیدم، عوضش کن، وگرنه دوباره تلاش کن».

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** در double-checked locking یک بار بدون قفل و یک بار با قفل null بودن را بررسی می‌کنیم، ولی فیلد باید حتماً volatile باشد. دلیلش این است که new سه مرحله دارد (تخصیص، اجرای سازنده، انتساب) و CPU ممکن است انتساب را قبل از سازنده انجام دهد، پس thread دیگر آبجکت نیمه‌ساخته را می‌بیند. راه‌های ساده‌تر و امن‌تر enum singleton و holder idiom هستند.
>
> 🧠 **قلاب حافظه:** بدون volatile مثل این است که تابلوی «رستوران باز است» را قبل از آمدن آشپز نصب کنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** ReentrantLock همان کار synchronized را می‌کند ولی امکانات بیشتری دارد: tryLock با timeout، انتظار قابل interrupt، قفل منصفانه (fair) و چند Condition. هزینه‌اش این است که باید unlock را حتماً در finally بنویسی. ReadWriteLock اجازه می‌دهد چند خواننده هم‌زمان وارد شوند ولی نویسنده انحصاری باشد، و StampedLock با خواندن optimistic حتی سریع‌تر است ولی reentrant نیست.
>
> 🧠 **قلاب حافظه:** ReadWriteLock کتابخانه است: همه می‌توانند هم‌زمان کتاب بخوانند، ولی وقتی کتابدار قفسه را جابه‌جا می‌کند همه بیرون می‌روند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Deadlock وقتی رخ می‌دهد که هر thread قفلی را نگه داشته که دیگری لازم دارد و همه برای همیشه منتظر می‌مانند. چهار شرط Coffman باید هم‌زمان برقرار باشند: انحصار، نگه‌داشتن و انتظار، عدم پس‌گیری و انتظار حلقوی؛ کافی است یکی را بشکنی. راه‌حل‌ها: ترتیب سراسری قفل‌ها (مثلاً بر اساس id حساب)، tryLock با timeout و عقب‌نشینی، کوتاه نگه‌داشتن بخش بحرانی و استفاده از ابزارهای سطح بالا. تشخیص با jstack یا jcmd Thread.print یا ThreadMXBean.findDeadlockedThreads انجام می‌شود. در دیتابیس هم deadlock داریم که DB خودش یکی از تراکنش‌ها را abort می‌کند و باید retry کنی.
>
> 🧠 **قلاب حافظه:** دو ماشین روی پل یک‌طرفه رو‌به‌روی هم: هیچ‌کدام عقب نمی‌رود. قانون «همیشه اول از شمال به جنوب» (lock ordering) ماجرا را تمام می‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** در deadlock همه متوقف و BLOCKED هستند. در livelock همه فعال‌اند و مدام به هم واکنش نشان می‌دهند ولی هیچ پیشرفتی نیست؛ درمانش تأخیر تصادفی (jitter) است. در starvation یک thread هیچ‌وقت نوبت نمی‌گیرد چون دیگران همیشه برنده می‌شوند؛ قفل منصفانه و نگه‌نداشتن طولانی قفل کمک می‌کند.
>
> 🧠 **قلاب حافظه:** Deadlock دو مجسمه، livelock دو نفر در راهرو که مدام با هم به یک طرف کنار می‌روند، starvation بچه‌ی کوچکی که در صف شلوغ هیچ‌وقت به پیشخوان نمی‌رسد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** ThreadLocal به هر thread نسخه‌ی مستقل خودش از یک متغیر را می‌دهد، پس اشتراکی وجود ندارد و همگام‌سازی لازم نیست. کاربرد واقعی‌اش context هر درخواست است: SecurityContextHolder در Spring Security، اتصال تراکنش به thread، MDC در لاگ و آبجکت‌های غیر thread-safe. خطرهایش: نشت حافظه در pool، منتقل نشدن context به @Async و CompletableFuture، و هزینه‌ی زیاد با میلیون‌ها virtual thread؛ جایگزین مدرن ScopedValue است.
>
> 🧠 **قلاب حافظه:** ThreadLocal کمد شخصی هر کارمند است: یک کلید، ولی هر کس کمد خودش را باز می‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** ThreadPoolExecutor این‌طور تصمیم می‌گیرد: تا corePoolSize، thread جدید می‌سازد؛ بعد task را در صف می‌گذارد؛ اگر صف پر شد تا maximumPoolSize thread اضافه می‌سازد؛ و در نهایت با RejectionPolicy رد می‌کند. CallerRunsPolicy یک back-pressure طبیعی می‌سازد. تله‌ی مهم این است که newFixedThreadPool صف نامحدود دارد و newCachedThreadPool تعداد thread نامحدود، و هر دو زیر بار می‌توانند OOM بدهند. exception های task های submit شده در Future گیر می‌کنند و اگر get صدا نزنی گم می‌شوند.
>
> 🧠 **قلاب حافظه:** رستوران: اول گارسون‌های ثابت (core)، بعد صف مشتری‌ها (queue)، وقتی صف پر شد گارسون پاره‌وقت (max)، و وقتی جا نیست دربان (rejection) مشتری را برمی‌گرداند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Future فقط یک get مسدودکننده دارد؛ CompletableFuture یک pipeline غیرمسدود می‌سازد. thenApply تبدیل همگام است، thenCompose مثل flatMap برای عملیات async بعدی، thenCombine دو نتیجه را ترکیب می‌کند، و exceptionally و handle مسیر خطا را مدیریت می‌کنند. allOf و anyOf برای منتظر ماندن چند کار است و orTimeout از جاوا ۹ آمده. تله‌ی بزرگ این است که بدون executor اختصاصی همه‌چیز روی commonPool اجرا می‌شود و I/O مسدودکننده کل JVM را خفه می‌کند.
>
> 🧠 **قلاب حافظه:** Future رسید خشکشویی است (باید بروی و منتظر بمانی)؛ CompletableFuture پیکی است که وقتی لباس آماده شد خودش می‌برد اتو و بعد درِ خانه تحویل می‌دهد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** CountDownLatch دروازه‌ای است که وقتی شمارنده به صفر برسد باز می‌شود و یک‌بارمصرف است. CyclicBarrier چند thread را در یک نقطه جمع می‌کند و بعد همه با هم ادامه می‌دهند و قابل استفاده‌ی مجدد است. Semaphore تعداد مجوزها را محدود می‌کند و برای محدود کردن فراخوانی هم‌زمان DB یا API بیرونی ایده‌آل است. Phaser نسخه‌ی پویا با فازهای متعدد است.
>
> 🧠 **قلاب حافظه:** Latch شمارش معکوس پرتاب موشک، Barrier قرار گروهی کوهنوردی (همه به ایستگاه برسند بعد ادامه)، Semaphore جای پارک با تعداد محدود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Fork/Join برای کارهای «تقسیم و غلبه» است: کار بزرگ را آن‌قدر نصف می‌کنی (fork) تا به اندازه‌ی کوچک برسد، مستقیم حل می‌کنی (compute) و نتایج را ترکیب می‌کنی (join). هر worker یک deque دارد و worker بیکار از ته deque بقیه کار می‌دزدد (work stealing) تا هیچ هسته‌ای بیکار نماند. parallel stream و CompletableFuture پیش‌فرض هم روی همین ForkJoinPool اجرا می‌شوند.
>
> 🧠 **قلاب حافظه:** خانواده‌ای که دیوار بزرگی را رنگ می‌کنند: هر کس سهمش را نصف می‌کند، و هر کس کارش زودتر تمام شد از سهم بقیه برمی‌دارد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Hashtable و synchronizedMap یک قفل برای کل map دارند و زیر بار گلوگاه می‌شوند. ConcurrentHashMap در جاوا ۸ به بعد برای bucket خالی از CAS و برای bucket پر از synchronized روی اولین node همان bucket استفاده می‌کند، خواندن‌ها بدون قفل است و null نمی‌پذیرد (جواب قدیمی Segment مربوط به جاوا ۷ است). حتی روی map همروند هم «بررسی و بعد عمل» race دارد، پس از putIfAbsent، computeIfAbsent و merge استفاده کن. CopyOnWriteArrayList برای خواندن زیاد و نوشتن کم مناسب است و BlockingQueue پایه‌ی الگوی producer-consumer است.
>
> 🧠 **قلاب حافظه:** synchronizedMap یک صندوقدار برای کل فروشگاه است؛ ConcurrentHashMap برای هر راهرو یک صندوقدار جدا گذاشته.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Virtual thread ها thread های سبکی هستند که JVM آن‌ها را روی چند carrier thread واقعی سوار می‌کند؛ وقتی روی I/O مسدود می‌شوند، stack شان روی heap ذخیره و carrier آزاد می‌شود. پس می‌توانی میلیون‌ها thread داشته باشی و کد ساده‌ی thread-per-request بنویسی. برای کار CPU-bound سودی ندارند، نباید pool شوند و برای محدود کردن هم‌زمانی از Semaphore استفاده می‌کنی. تله‌ی pinning: در جاوا ۲۱ تا ۲۳ مسدود شدن داخل synchronized، virtual thread را به carrier می‌چسباند (در جاوا ۲۴ حل شد).
>
> 🧠 **قلاب حافظه:** Platform thread ماشین شخصی با راننده است؛ virtual thread مسافری است که فقط وقتی واقعاً حرکت می‌کند در تاکسی (carrier) می‌نشیند و موقع انتظار پیاده می‌شود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** خلاصه‌ی فلسفه‌ی سنیور: بهترین همگام‌سازی آن است که لازم نباشد، یعنی immutability و محدود کردن داده به یک thread. از ابزارهای سطح بالا استفاده کن، صف‌ها را محدود کن تا back-pressure داشته باشی، unlock و remove و shutdown را فراموش نکن، thread ها را نام‌گذاری کن و InterruptedException را درست مدیریت کن. و همیشه با thread dump و JFR اندازه بگیر، حدس نزن.
>
> 🧠 **قلاب حافظه:** قانون طلایی آشپزخانه: هر کس قابلمه‌ی خودش را داشته باشد (confinement)، و غذای آماده را دیگر هم نزنند (immutability).

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** Collection ها جعبه‌ابزار روزمره‌ی ما هستند، ولی سنیور کسی است که می‌داند داخل هر جعبه چه خبر است: چرا HashMap سریع است، کی کند می‌شود، و چرا LinkedList تقریباً هیچ‌وقت انتخاب درستی نیست.

</div>

### 5.1 Collections hierarchy

<div dir="rtl">

> 🪶 **به زبان ساده:** در ریشه Iterable و Collection هستند که به List (ترتیب‌دار، تکراری مجاز)، Set (بدون تکرار) و Queue تقسیم می‌شوند. Map یک درخت جداست و از Collection ارث نمی‌برد. LinkedList هم List است و هم Deque، و از جاوا ۲۱ SequencedCollection بالای مجموعه‌های ترتیب‌دار قرار گرفته.
>
> 🧠 **قلاب حافظه:** List صف نانوایی (ترتیب و تکرار)، Set لیست مهمانان (هر نفر یک بار)، Map دفترچه‌ی تلفن (اسم ← شماره).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** HashMap آرایه‌ای از bucket هاست. hashCode کلید با بیت‌های بالایش XOR می‌شود و با (n-1) & hash اندیس bucket به دست می‌آید. اگر bucket پر باشد، زنجیره با equals پیمایش می‌شود؛ وقتی یک bucket به ۸ عنصر برسد و ظرفیت حداقل ۶۴ باشد، به درخت red-black تبدیل می‌شود و بدترین حالت O(log n) می‌شود. ظرفیت پیش‌فرض ۱۶ و load factor برابر ۰.۷۵ است و با رد شدن از آستانه، جدول دو برابر می‌شود. یک کلید null مجاز است و thread-safe نیست.
>
> 🧠 **قلاب حافظه:** HashMap ساختمان کمدهای فرودگاه است: hashCode شماره‌ی ردیف را می‌دهد، equals در همان ردیف چمدان خودت را پیدا می‌کند؛ ردیف شلوغ شد؟ قفسه را مرتب (درخت) می‌کنند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** اگر کلیدی که در HashMap گذاشته‌ای بعداً تغییر کند و hashCode اش عوض شود، get در bucket اشتباه دنبالش می‌گردد و null برمی‌گرداند، در حالی که entry هنوز در map هست و حافظه را اشغال می‌کند. برای همین کلیدها باید immutable باشند: String، Integer، enum یا record با فیلدهای تغییرناپذیر.
>
> 🧠 **قلاب حافظه:** کلید mutable مثل این است که بعد از تحویل چمدان، برچسبش را عوض کنی؛ دیگر هیچ‌وقت در نوار بار پیدایش نمی‌کنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** ArrayList دسترسی تصادفی O(1) دارد و اضافه کردن به انتها هم به‌طور سرشکن O(1) است؛ درج در وسط به‌خاطر جابه‌جایی O(n) است. LinkedList فقط وقتی O(1) درج می‌کند که node را در دست داشته باشی، و چون node ها در حافظه پراکنده‌اند برای cache پردازنده بد است. HashMap و HashSet به‌طور میانگین O(1)، TreeMap و TreeSet مرتب با O(log n) و EnumMap سریع‌ترین گزینه برای کلید enum است.
>
> 🧠 **قلاب حافظه:** ArrayList قطار با واگن‌های چسبیده (پردازنده عاشقش است)، LinkedList گنج‌یابی که هر سرنخ تو را به جای دیگری می‌فرستد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Iterator های fail-fast (مثل ArrayList و HashMap) یک modCount مورد انتظار دارند و اگر ساختار حین پیمایش تغییر کند، ConcurrentModificationException می‌دهند، حتی در یک thread. Iterator های fail-safe یا weakly consistent (مثل CopyOnWriteArrayList و ConcurrentHashMap) روی snapshot یا با تحمل تغییرات کار می‌کنند. برای حذف حین پیمایش از iterator.remove یا removeIf استفاده کن.
>
> 🧠 **قلاب حافظه:** fail-fast مثل معلمی است که اگر وسط حضور و غیاب کسی از کلاس برود، داد می‌زند؛ fail-safe از روی عکس کلاس حضور و غیاب می‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Comparable ترتیب «طبیعی» را داخل خود کلاس تعریف می‌کند (compareTo)، ولی Comparator ترتیب‌های جایگزین را از بیرون و قابل ترکیب می‌سازد (comparing، thenComparing، reversed، nullsLast). هرگز با a - b مقایسه نکن چون overflow می‌دهد. TreeSet برای یکتایی از compareTo استفاده می‌کند نه equals، پس compareTo ناسازگار باعث گم شدن عناصر می‌شود.
>
> 🧠 **قلاب حافظه:** Comparable قد طبیعی آدم است؛ Comparator متر خیاطی که هر بار می‌توانی با آن چیز دیگری بسنجی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** LinkedHashMap با accessOrder=true هر عنصری را که خوانده شود به انتهای لیست می‌برد و قدیمی‌ترین در ابتدا می‌ماند. با override کردن removeEldestEntry یک cache از نوع LRU در چند خط داری. thread-safe نیست، پس در پروداکشن Caffeine انتخاب بهتری است.
>
> 🧠 **قلاب حافظه:** کمد لباس: هر لباسی را که پوشیدی جلوی کمد آویزان می‌کنی؛ وقتی جا تمام شد، ته کمدی‌ها را می‌بخشی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** WeakHashMap کلیدها را weak نگه می‌دارد و وقتی کلید دیگر جایی استفاده نشود entry ناپدید می‌شود. IdentityHashMap به‌جای equals از == استفاده می‌کند و برای پیمایش گراف مفید است. EnumMap و EnumSet روی آرایه و bit vector ساخته شده‌اند و بسیار سریع‌اند. PriorityQueue یک heap دودویی است که برای مسائل top-K و زمان‌بندی کاربرد دارد.
>
> 🧠 **قلاب حافظه:** هر ابزار ویژه یک «ابرقدرت» دارد: Weak فراموشکار، Identity سخت‌گیر، Enum سریع، Priority همیشه کوچک‌ترین را اول می‌دهد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Arrays.asList یک view با اندازه‌ی ثابت روی آرایه است (set مجاز، add نه). Collections.unmodifiableList فقط یک view فقط‌خواندنی است و اگر لیست اصلی تغییر کند، تغییر در view هم دیده می‌شود. List.of و List.copyOf واقعاً immutable هستند و null نمی‌پذیرند.
>
> 🧠 **قلاب حافظه:** unmodifiableList پنجره‌ی شیشه‌ای است (دست نمی‌زنی ولی تغییرات داخل را می‌بینی)؛ List.copyOf عکسی است که گرفتی و دیگر عوض نمی‌شود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** HashMap سریع ولی غیر thread-safe است. Hashtable قدیمی است و کل جدول را قفل می‌کند. synchronizedMap یک wrapper با قفل کلی است و برای iterate کردن هم باید دستی synchronize کنی. ConcurrentHashMap انتخاب درست برای همروندی است، و TreeMap یا ConcurrentSkipListMap وقتی ترتیب مرتب لازم داری.
>
> 🧠 **قلاب حافظه:** برای هر موقعیت یک map: خانه (HashMap)، موزه (Hashtable)، بانک شلوغ (ConcurrentHashMap)، کتابخانه‌ی الفبایی (TreeMap).

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** اصول طراحی زبان مشترک مهندسان باتجربه است. وقتی در مصاحبه می‌گویی «اینجا Strategy گذاشتیم تا Open/Closed حفظ شود»، مصاحبه‌گر می‌فهمد فقط کد نمی‌نویسی، معماری می‌کنی.

</div>

### 6.1 SOLID

<div dir="rtl">

> 🪶 **به زبان ساده:** SOLID: هر کلاس یک دلیل برای تغییر (S)، قابل گسترش بدون دستکاری کد موجود (O)، زیرکلاس باید همه‌جا جایگزین والد شود بدون شگفتی (L)، interface های کوچک به‌جای یک interface چاق (I)، و وابستگی به انتزاع نه به پیاده‌سازی (D). مثال کلاسیک نقض L، Square است که از Rectangle ارث می‌برد. در Spring تزریق List از همه‌ی پیاده‌سازی‌ها O و D را با هم نشان می‌دهد.
>
> 🧠 **قلاب حافظه:** S یک چاقو یک کار، O پریز برق (دستگاه جدید وصل کن، سیم‌کشی را نکن)، L بچه‌ای که جای پدر رانندگی کند بدون تصادف، I کنترل‌های جدا به‌جای یک کنترل هزار دکمه، D پریز استاندارد نه سیم لخت.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Singleton یعنی یک نمونه؛ بهترین پیاده‌سازی enum است چون در برابر serialization و reflection امن است. Factory ساختن آبجکت را پشت یک متد پنهان می‌کند و Builder برای آبجکت‌هایی با پارامترهای اختیاری زیاد خوانایی و immutability می‌دهد. تفاوت مهم: bean های singleton در Spring «یکی در هر container» هستند، نه singleton سراسری GoF در کل JVM.
>
> 🧠 **قلاب حافظه:** Singleton رئیس‌جمهور (فقط یکی)، Factory کارخانه (سفارش بده، نپرس چطور)، Builder ساندویچی که خودت مواد را یکی‌یکی انتخاب می‌کنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Spring پر از الگوست: Proxy در AOP و @Transactional و lazy loading هایبرنیت، Template Method در JdbcTemplate، Strategy در PasswordEncoder و TransactionManager، Observer در رویدادها، Chain of Responsibility در filter های امنیتی، Front Controller در DispatcherServlet و Adapter در HandlerAdapter. اگر در مصاحبه الگو را با محل واقعی‌اش در Spring وصل کنی، جوابت چند برابر قوی‌تر است.
>
> 🧠 **قلاب حافظه:** Spring یک موزه‌ی زنده‌ی Design Pattern است؛ هر سالنش یک الگو را به نمایش گذاشته.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** وراثت رابطه‌ای «جعبه‌سفید» و محکم است: تغییر کلاس والد می‌تواند فرزند را بشکند (مثال معروف InstrumentedHashSet که addAll داخلاً add را صدا می‌زند و دوبار می‌شمارد). ترکیب (composition) رابطه‌ی has-a و «جعبه‌سیاه» است، پیاده‌سازی را می‌شود در زمان اجرا یا تست عوض کرد. قانون: اول ترکیب، و وراثت فقط وقتی رابطه‌ی واقعی IS-A وجود دارد.
>
> 🧠 **قلاب حافظه:** وراثت یعنی ژن (عوضش نمی‌کنی)، ترکیب یعنی تیم (هر وقت لازم شد بازیکن عوض می‌کنی).

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** هایبرنیت مترجمی است بین دو دنیای متفاوت: دنیای آبجکت‌ها و دنیای جدول‌ها. مترجم خوب کار را ساده می‌کند، ولی اگر ندانی پشت صحنه چه جمله‌هایی (SQL) می‌سازد، یک‌شبه با صدها کوئری اضافه غافلگیر می‌شوی. سنیور کسی است که SQL پشت هر خط هایبرنیت را می‌بیند.

</div>

### 7.1 JPA vs Hibernate vs Spring Data JPA vs JDBC

<div dir="rtl">

> 🪶 **به زبان ساده:** JPA (Jakarta Persistence) فقط مشخصات (specification) است: EntityManager، @Entity و JPQL. هایبرنیت یک پیاده‌سازی از JPA است که امکانات اضافه دارد (Session، @BatchSize، filter ها). Spring Data JPA روی JPA می‌نشیند و پیاده‌سازی repository ها را خودکار می‌سازد، و زیر همه‌ی این‌ها JDBC و connection pool (HikariCP) قرار دارد. SessionFactory سنگین و thread-safe است، ولی Session یا EntityManager سبک و غیر thread-safe است.
>
> 🧠 **قلاب حافظه:** JPA قانون راهنمایی و رانندگی است، هایبرنیت ماشین، Spring Data راننده‌ی شخصی، JDBC جاده.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Persistence context (همان cache سطح اول) یک identity map است: برای هر سطر دیتابیس در یک context فقط یک آبجکت جاوا وجود دارد، پس find دوم با همان id بدون SQL و همان instance را برمی‌گرداند. هایبرنیت از هر entity یک snapshot نگه می‌دارد تا dirty checking کند و SQL ها را تا flush به تأخیر می‌اندازد (write-behind). در کارهای batch با هزاران entity باید flush و clear کنی وگرنه حافظه و زمان flush منفجر می‌شود.
>
> 🧠 **قلاب حافظه:** Persistence context میز کار موقت توست: هر پرونده را یک بار از بایگانی می‌آوری، رویش کار می‌کنی، و آخر روز (flush) همه را یکجا برمی‌گردانی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Entity چهار حالت دارد: Transient (تازه ساخته شده و هایبرنیت از آن خبر ندارد)، Managed (در persistence context و تغییراتش خودکار ذخیره می‌شود)، Detached (سطر در DB هست ولی context بسته شده یا detach شده) و Removed (برای حذف در flush زمان‌بندی شده). persist آبجکت جدید را managed می‌کند، merge یک کپی managed از آبجکت detached برمی‌گرداند، و بسته شدن تراکنش همه را detached می‌کند.
>
> 🧠 **قلاب حافظه:** Transient غریبه، Managed عضو فعال باشگاه، Detached عضو سابقی که کارتش هنوز معتبر است ولی در باشگاه نیست، Removed عضوی که فرم انصراف داده.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** persist فقط برای entity جدید است و خود همان آبجکت را managed می‌کند. merge وضعیت آبجکت ورودی را روی یک نسخه‌ی managed کپی می‌کند (اگر لازم باشد اول SELECT می‌زند) و آن نسخه را برمی‌گرداند، در حالی که خود آرگومان detached می‌ماند. save و update و saveOrUpdate متدهای قدیمی هایبرنیت‌اند که در نسخه‌ی ۶ deprecated شده‌اند. تله‌ی Spring Data: save اگر id دستی داشته باشی فکر می‌کند entity جدید نیست، merge می‌کند و یک SELECT اضافه می‌زند؛ راه‌حل @Version یا Persistable است.
>
> 🧠 **قلاب حافظه:** persist یعنی «این بچه‌ی خودمه، ثبت‌نامش کن»؛ merge یعنی «از روی این عکس، پرونده‌ی اصلی را به‌روز کن و پرونده‌ی اصلی را به من بده».

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** find همان لحظه (یا از context) entity واقعی را می‌آورد و اگر نباشد null برمی‌گرداند. getReference فقط یک proxy با id برمی‌گرداند و تا وقتی به فیلد غیر id دست نزنی SQL اجرا نمی‌کند؛ اگر سطر وجود نداشته باشد هنگام دسترسی EntityNotFoundException می‌دهد. کاربرد طلایی getReference: تنظیم کلید خارجی بدون load کردن والد.
>
> 🧠 **قلاب حافظه:** find یعنی خود آدم را صدا می‌زنی؛ getReference یعنی کارت ویزیتش را می‌گیری و فقط وقتی لازم شد زنگ می‌زنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** وقتی entity managed است، لازم نیست save صدا بزنی: در flush، هایبرنیت هر entity را با snapshot اش مقایسه می‌کند و برای تغییرات UPDATE می‌سازد (dirty checking). FlushMode.AUTO قبل از commit، قبل از کوئری‌ای که روی جدول‌های تغییرکرده اجرا می‌شود، و هنگام flush دستی، flush می‌کند. flush با commit فرق دارد و هنوز قابل rollback است. readOnly=true در Spring flush را MANUAL می‌کند و dirty checking را حذف می‌کند.
>
> 🧠 **قلاب حافظه:** Dirty checking مثل معلمی است که برگه‌ی امتحان را با کپی اولیه مقایسه می‌کند و فقط تغییرات را تصحیح می‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** پیش‌فرض JPA برای @ManyToOne و @OneToOne «EAGER» است (تله!) و برای @OneToMany و @ManyToMany «LAZY». قانون سنیور: همه‌ی ارتباط‌ها را LAZY کن و برای هر use-case دقیقاً همان چیزی را که لازم است با JOIN FETCH، EntityGraph یا DTO بیاور. @OneToOne دوطرفه در سمت mappedBy بدون bytecode enhancement نمی‌تواند lazy باشد، پس @MapsId یا یک‌طرفه را ترجیح بده.
>
> 🧠 **قلاب حافظه:** EAGER یعنی با خرید یک پیراهن، کل کمد فروشنده را به خانه بیاوری؛ LAZY یعنی هر وقت لازم شد می‌روی و همان یکی را می‌گیری.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** LazyInitializationException وقتی رخ می‌دهد که بعد از بسته شدن تراکنش و EntityManager، به یک collection یا proxy تنبل دست بزنی. راه‌حل‌های درست: fetch در خود کوئری، EntityGraph، یا تبدیل به DTO داخل تراکنش. Spring Boot به‌طور پیش‌فرض Open Session In View را روشن دارد که خطا را پنهان می‌کند ولی connection را تا آخر درخواست نگه می‌دارد و N+1 مخفی در لایه‌ی وب می‌سازد؛ جواب سنیور: خاموشش کن.
>
> 🧠 **قلاب حافظه:** OSIV مثل این است که چون حوصله‌ی لیست خرید نداری، کل روز در سوپرمارکت بمانی و هر بار یک قلم بخری.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** مسئله‌ی N+1: یک کوئری برای لیست والدها و سپس برای هر والد یک کوئری جداگانه برای فرزند lazy؛ ۱۰۰ سفارش یعنی ۱۰۱ رفت‌وبرگشت به دیتابیس. راه‌حل‌ها: JOIN FETCH یا EntityGraph (یک کوئری)، @BatchSize یا default_batch_fetch_size (کوئری‌های IN گروهی)، DTO projection و SUBSELECT. تله‌ها: JOIN FETCH روی collection به‌همراه pagination باعث می‌شود هایبرنیت همه را بیاورد و در حافظه صفحه‌بندی کند، و fetch هم‌زمان دو List به MultipleBagFetchException می‌رسد.
>
> 🧠 **قلاب حافظه:** N+1 مثل پیکی است که برای هر بسته یک بار جداگانه به انبار برمی‌گردد؛ JOIN FETCH یعنی همه را یکجا در وانت بار بزن.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** در رابطه‌ی دوطرفه، سمتی که کلید خارجی را دارد (معمولاً @ManyToOne) owning side است و هایبرنیت فقط از روی همین سمت SQL می‌نویسد؛ سمت mappedBy فقط آینه است. اگر فقط به لیست والد اضافه کنی و setOrder را روی فرزند صدا نزنی، FK خالی می‌ماند. برای همین متدهای کمکی addLine و removeLine می‌نویسیم تا هر دو طرف همگام بمانند. @OneToMany یک‌طرفه بدون JoinColumn جدول واسط می‌سازد، و @ManyToMany با List برای حذف یک عنصر همه را پاک و دوباره درج می‌کند، پس Set بهتر است.
>
> 🧠 **قلاب حافظه:** سند خانه (FK) دست هر کس باشد، صاحب واقعی (owning side) اوست؛ mappedBy فقط عکس سند را به دیوار زده.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Cascade یعنی عملیاتی که روی والد انجام می‌دهی (persist، merge، remove و...) به فرزندان هم منتقل شود. orphanRemoval یعنی اگر فرزند از collection والد حذف شود، سطرش هم DELETE شود، در حالی که CascadeType.REMOVE فقط وقتی والد حذف شود فرزندان را حذف می‌کند. هرگز از فرزند به والد cascade نکن، و REMOVE روی @ManyToMany خود entity های مشترک را پاک می‌کند نه فقط لینک را.
>
> 🧠 **قلاب حافظه:** Cascade مثل دومینوی بالا به پایین است؛ orphanRemoval یعنی بچه‌ای که از خانواده جدا شد، دیگر جایی در شهر ندارد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** IDENTITY از auto-increment دیتابیس استفاده می‌کند، پس INSERT باید فوراً اجرا شود تا id برگردد و batch insert غیرفعال می‌شود. SEQUENCE با pooled optimizer (allocationSize) با یک فراخوانی DB چند id را در حافظه رزرو می‌کند و برای PostgreSQL و Oracle بهترین است؛ فقط INCREMENT BY دنباله باید با allocationSize یکی باشد. TABLE کند است و UUID بدون رفت‌وبرگشت است ولی UUID تصادفی ایندکس را تکه‌تکه می‌کند، پس نسخه‌ی زمان‌محور (v7) بهتر است.
>
> 🧠 **قلاب حافظه:** IDENTITY یعنی برای هر مشتری جداگانه از دستگاه نوبت‌دهی شماره بگیری؛ SEQUENCE با allocationSize یعنی یک دسته‌ی ۵۰تایی شماره بگیری و خودت پخش کنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** SINGLE_TABLE همه‌ی زیرکلاس‌ها را در یک جدول با ستون discriminator می‌گذارد: سریع‌ترین، ولی ستون‌های زیرکلاس باید nullable باشند. JOINED نرمال‌شده است و برای هر زیرکلاس جدول جدا با join دارد، که در کوئری‌های چندریخت کند می‌شود. TABLE_PER_CLASS برای هر کلاس concrete جدول کامل دارد و کوئری چندریخت آن UNION است. @MappedSuperclass فقط فیلدها را به اشتراک می‌گذارد و خودش entity نیست.
>
> 🧠 **قلاب حافظه:** SINGLE_TABLE یک کمد بزرگ با برچسب، JOINED کمد مشترک به‌علاوه‌ی کشوهای شخصی، TABLE_PER_CLASS برای هر نفر یک کمد کامل جدا.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** مشکل entity این است که id تا قبل از persist یا flush برابر null است؛ اگر hashCode بر اساس id باشد، بعد از ذخیره عوض می‌شود و entity در HashSet گم می‌شود. دو راه درست: کلید طبیعی کسب‌وکار (مثل IBAN با @NaturalId) یا equals بر اساس id به‌همراه hashCode ثابت (مثلاً getClass().hashCode()). از instanceof و getter استفاده کن تا با proxy ها درست کار کند، و Lombok @Data را روی entity نگذار.
>
> 🧠 **قلاب حافظه:** id مثل شماره‌ی شناسنامه‌ای است که فقط بعد از ثبت احوال صادر می‌شود؛ نوزاد را با آن در فهرست نگذار، چون شماره‌اش بعداً عوض می‌شود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** هایبرنیت برای lazy loading یک زیرکلاس از entity تو در زمان اجرا می‌سازد (با ByteBuddy) که getter ها را رهگیری می‌کند و در اولین دسترسی entity واقعی را load می‌کند. برای همین کلاس و متدهای entity نباید final باشند و سازنده‌ی بدون آرگومان لازم است. همچنین getClass() یک proxy با خود کلاس برابر نیست، پس از instanceof یا Hibernate.getClass استفاده کن.
>
> 🧠 **قلاب حافظه:** Proxy بدلکار سینماست: تا وقتی صحنه‌ی خطرناک (دسترسی به داده) نرسیده جای ستاره می‌ایستد؛ ولی بدلکار نمی‌تواند جای ستاره‌ای بایستد که final است.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Cache سطح دوم در سطح SessionFactory است و بین همه‌ی session ها مشترک است؛ ترتیب جستجو L1 بعد L2 بعد دیتابیس است. باید صریحاً با @Cacheable و یک provider (مثل Ehcache یا Caffeine از طریق JCache) فعالش کنی و استراتژی همزمانی (READ_ONLY، READ_WRITE و...) انتخاب کنی. برای داده‌ی مرجع کم‌تغییر مثل ارز و کشور عالی است، ولی برای جدول‌های پرتغییر یا محیط چندنودی بدون cache توزیع‌شده خطر داده‌ی کهنه دارد. Query cache با هر تغییر در جدول‌های مربوط باطل می‌شود.
>
> 🧠 **قلاب حافظه:** L1 یادداشت روی میز خودت، L2 تابلوی اعلانات مشترک اداره؛ ولی اگر اطلاعات تابلو هر دقیقه عوض شود، دیگر کسی به آن اعتماد نمی‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Optimistic locking با ستون @Version قفلی نمی‌گذارد و فقط هنگام UPDATE بررسی می‌کند که نسخه عوض نشده باشد؛ اگر صفر سطر تغییر کند، OptimisticLockException می‌گیری و retry می‌کنی. Pessimistic locking با SELECT ... FOR UPDATE سطر را در دیتابیس قفل می‌کند و بقیه منتظر می‌مانند. optimistic برای تعارض کم و فرم‌هایی با زمان فکر طولانی کاربر مناسب است، و pessimistic برای سطرهای داغ مثل انتقال پول یا رزرو صندلی.
>
> 🧠 **قلاب حافظه:** Optimistic مثل ویکی‌پدیا است: هر کس ویرایش کند، اگر دیگری زودتر ذخیره کرده بود، به تو می‌گوید «دوباره نگاه کن». Pessimistic مثل اتاق پرو است: داخلش که رفتی در را قفل می‌کنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** چهار ناهنجاری را بشناس: dirty read (خواندن داده‌ی commit نشده)، non-repeatable read (یک سطر دو بار خوانده شود و مقدارش فرق کند)، phantom read (یک کوئری دو بار اجرا شود و سطرها فرق کنند) و lost update. سطح‌ها از READ_UNCOMMITTED تا SERIALIZABLE به ترتیب ناهنجاری‌های بیشتری را حذف می‌کنند و هزینه‌ی بیشتری دارند. پیش‌فرض PostgreSQL و Oracle سطح READ COMMITTED است و MySQL InnoDB سطح REPEATABLE READ. اکثر سیستم‌های بانکی READ COMMITTED به‌علاوه‌ی @Version و FOR UPDATE روی سطرهای داغ را انتخاب می‌کنند.
>
> 🧠 **قلاب حافظه:** سطح ایزولیشن ضخامت دیوار بین اتاق‌هاست: هر چه ضخیم‌تر، صدای همسایه کمتر می‌آید ولی خانه گران‌تر و رفت‌وآمد کندتر می‌شود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** برای insert انبوه، hibernate.jdbc.batch_size و order_inserts را تنظیم کن تا INSERT ها گروهی و در یک رفت‌وبرگشت ارسال شوند، و هر چند صد رکورد flush و clear کن تا persistence context پر نشود. یادت باشد IDENTITY این batching را خاموش می‌کند. برای میلیون‌ها رکورد StatelessSession، JDBC batchUpdate یا ابزار bulk خود دیتابیس (مثل COPY) بهتر است، و JPQL bulk update از persistence context، @Version و callback ها عبور می‌کند.
>
> 🧠 **قلاب حافظه:** Batch یعنی نامه‌ها را در یک کیسه‌ی پستی بفرستی نه یکی‌یکی؛ clear یعنی بعد از هر کیسه میزت را خالی کنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** JPQL روی نام entity ها و فیلدها کار می‌کند و قابل‌حمل است. Criteria API برای فیلترهای پویا و type-safe مناسب است ولی پرحرف است. Native SQL برای امکانات خاص دیتابیس مثل window function و CTE است. در همه‌ی حالت‌ها از bind parameter استفاده کن، چون الحاق رشته هم injection می‌سازد و هم cache دستورات را خراب می‌کند.
>
> 🧠 **قلاب حافظه:** JPQL فارسی معیار (همه‌جا می‌فهمند)، Criteria فرم اداری (دقیق ولی طولانی)، Native لهجه‌ی محلی (قوی ولی فقط در همان شهر).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** callback های JPA مثل @PrePersist و @PreUpdate اجازه می‌دهند قبل یا بعد از رویدادهای چرخه‌ی عمر کاری انجام دهی. Spring Data Auditing با @CreatedDate، @LastModifiedDate و @CreatedBy زمان و کاربر را خودکار پر می‌کند (با @EnableJpaAuditing و AuditorAware). Hibernate Envers با @Audited تاریخچه‌ی کامل نسخه‌ها را در جدول‌های جداگانه نگه می‌دارد که در سیستم‌های بانکی بسیار رایج است.
>
> 🧠 **قلاب حافظه:** Auditing دوربین مداربسته‌ای است که روی هر ردیف جدول نصب کرده‌ای: چه کسی، کی، چه چیزی را عوض کرد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** چک‌لیست عملکرد هایبرنیت را مثل چک‌لیست قبل از پرواز حفظ کن: همه‌چیز LAZY، OSIV خاموش، SEQUENCE به‌همراه batch، readOnly و DTO برای خواندن، صفحه‌بندی روی والد، flush و clear در batch، ایندکس روی FK ها، پایش با statistics و slow query log، و هرگز ddl-auto=update در پروداکشن؛ به‌جایش Flyway یا Liquibase.
>
> 🧠 **قلاب حافظه:** خلبان هر چقدر هم باتجربه باشد، قبل از پرواز چک‌لیست را می‌خواند؛ تو هم قبل از هر release.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** Spring در واقع یک «کارخانه‌ی آبجکت» هوشمند است: آبجکت‌ها را می‌سازد، به هم وصل می‌کند، دورشان لفاف (proxy) می‌پیچد و در پایان جمعشان می‌کند. اگر چرخه‌ی عمر bean و مفهوم proxy را بفهمی، نیمی از سؤال‌های Spring خودشان جواب داده می‌شوند.

</div>

### 8.1 IoC and dependency injection

<div dir="rtl">

> 🪶 **به زبان ساده:** IoC یعنی کنترل ساخت و اتصال آبجکت‌ها از کد تو به container منتقل شود، و DI روشی است که وابستگی‌ها از بیرون تزریق می‌شوند. تزریق از طریق سازنده بهترین است: فیلدها final می‌شوند، وابستگی‌های اجباری صریح‌اند، تست واحد بدون Spring ممکن است و خطا زود دیده می‌شود. تزریق فیلدی وابستگی‌ها را پنهان می‌کند و برای تست به reflection نیاز دارد. سازنده‌ی خیلی طولانی هم نشانه‌ی نقض SRP است.
>
> 🧠 **قلاب حافظه:** بدون IoC خودت باید آجر بپزی؛ با IoC پیمانکار مصالح را درِ خانه تحویل می‌دهد و تو فقط می‌سازی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** BeanFactory container پایه است و bean ها را تنبل می‌سازد. ApplicationContext همه‌ی امکانات BeanFactory را دارد به‌علاوه‌ی ساخت مشتاقانه‌ی singleton ها در شروع (خطاها زود دیده می‌شوند)، رویدادها، i18n، Environment و profile ها، و ثبت خودکار BeanPostProcessor ها که AOP روی آن ساخته شده. در عمل همیشه با ApplicationContext کار می‌کنیم.
>
> 🧠 **قلاب حافظه:** BeanFactory موتور خالی است؛ ApplicationContext ماشین کامل با داشبورد و چراغ و کولر.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** ترتیب را حفظ کن: ساخت با سازنده، تزریق فیلد و setter، callback های Aware، سپس BeanPostProcessor قبل از initialization که @PostConstruct در آن اجرا می‌شود، بعد afterPropertiesSet و init-method، و در نهایت BeanPostProcessor بعد از initialization که proxy های AOP (برای @Transactional و @Async) در آن ساخته می‌شوند. در پایان @PreDestroy و destroy اجرا می‌شوند، ولی برای bean های prototype نه. کارهای شروع برنامه که تراکنش لازم دارند را به ApplicationReadyEvent بسپار.
>
> 🧠 **قلاب حافظه:** تولد (constructor)، لباس پوشیدن (injection)، گرفتن کارت شناسایی (Aware)، آموزش (PostConstruct)، گرفتن یونیفرم رسمی (proxy)، کار، بازنشستگی (PreDestroy).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** scope پیش‌فرض singleton است، یعنی یک نمونه در هر container که باید بی‌حالت (stateless) و thread-safe باشد. prototype با هر تزریق نمونه‌ی جدید می‌سازد و Spring نابودی‌اش را مدیریت نمی‌کند. request و session و application برای وب هستند. تله‌ی معروف: prototype تزریق‌شده در singleton فقط یک بار ساخته می‌شود؛ راه‌حل ObjectProvider، @Lookup یا scoped proxy است.
>
> 🧠 **قلاب حافظه:** Singleton میز پذیرش مشترک یک هتل است؛ prototype حوله‌ی یک‌بارمصرف؛ request scope کلید اتاقی که فقط برای همین اقامت معتبر است.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** @Component و stereotype هایش (@Service، @Repository، @Controller) روی کلاس‌های خودت هستند و با component scan پیدا می‌شوند؛ @Repository ترجمه‌ی exception های persistence را هم اضافه می‌کند. @Bean متدی کارخانه‌ای داخل @Configuration است برای کلاس‌های کتابخانه‌های بیرونی یا ساخت شرطی. کلاس @Configuration با CGLIB proxy می‌شود تا فراخوانی متدهای @Bean همان singleton را برگرداند؛ با proxyBeanMethods=false این رفتار خاموش و startup سریع‌تر می‌شود.
>
> 🧠 **قلاب حافظه:** @Component یعنی «من خودم داوطلبم»؛ @Bean یعنی «من این آدم را معرفی می‌کنم».

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Spring اول بر اساس نوع جستجو می‌کند. اگر چند bean پیدا شود، @Qualifier، سپس @Primary و در نهایت نام پارامتر تعیین‌کننده است، وگرنه NoUniqueBeanDefinitionException می‌گیری. می‌توانی همه‌ی پیاده‌سازی‌ها را به‌صورت List (مرتب با @Order) یا Map با نام bean تزریق کنی که پایه‌ی الگوی Strategy است. Optional یا ObjectProvider برای وابستگی اختیاری است.
>
> 🧠 **قلاب حافظه:** وقتی سه نفر به اسم «علی» در کلاس هستند: یا فامیلی را می‌گویی (@Qualifier)، یا نماینده‌ی کلاس جواب می‌دهد (@Primary)، یا همه را صدا می‌زنی (List).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** وابستگی حلقوی یعنی A به B و B به A نیاز دارد. با تزریق سازنده Spring نمی‌تواند هیچ‌کدام را بسازد و از Spring Boot 2.6 حتی با تزریق فیلدی هم به‌طور پیش‌فرض ممنوع است. بهترین راه بازطراحی و بیرون کشیدن منطق مشترک در bean سوم است؛ رویدادها یا @Lazy روی یکی از پارامترها راه‌های بعدی‌اند. معمولاً این خودش نشانه‌ی ضعف طراحی است.
>
> 🧠 **قلاب حافظه:** مرغ و تخم‌مرغ: Spring نمی‌داند کدام را اول بسازد؛ تو مرغداری (bean سوم) را طراحی کن.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** AOP منطق‌های بین‌لایه‌ای مثل لاگ، تراکنش، امنیت و متریک را در aspect جدا می‌کند. Join point در Spring فقط فراخوانی متد است، pointcut عبارتی است که join point ها را انتخاب می‌کند، و advice کاری است که انجام می‌شود (@Before، @After، @Around و...). Spring با proxy در زمان اجرا weaving می‌کند: JDK dynamic proxy بر اساس interface و CGLIB با ساخت زیرکلاس، که پیش‌فرض Spring Boot است و نمی‌تواند کلاس یا متد final را proxy کند.
>
> 🧠 **قلاب حافظه:** Proxy منشی رئیس است: هر کس با رئیس کار دارد اول از منشی رد می‌شود و منشی قبل و بعدش کارهای اداری (aspect) را انجام می‌دهد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** وقتی متدی از همان کلاس، متد دیگری را با this صدا می‌زند، فراخوانی مستقیم روی آبجکت اصلی است و از proxy عبور نمی‌کند؛ بنابراین @Transactional، @Async، @Cacheable و @PreAuthorize روی متد دوم نادیده گرفته می‌شوند. راه‌حل تمیز انتقال متد به bean دیگر است؛ تزریق خود با @Lazy، TransactionTemplate یا AspectJ گزینه‌های بعدی‌اند. این یکی از پرتکرارترین سؤال‌های Spring در مصاحبه‌های سنیور است.
>
> 🧠 **قلاب حافظه:** اگر رئیس از اتاق خودش با خودش حرف بزند، منشی (proxy) اصلاً خبردار نمی‌شود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** @Value برای یک مقدار تکی با مقدار پیش‌فرض بعد از دونقطه است. @ConfigurationProperties تنظیمات را به‌صورت گروهی، type-safe و قابل اعتبارسنجی (مثلاً با record و @Validated) bind می‌کند و انتخاب بهتری است. Profile ها پیکربندی محیط‌ها را جدا می‌کنند و annotation های شرطی مثل @ConditionalOnProperty یک bean را فقط در شرایط خاص فعال می‌کنند.
>
> 🧠 **قلاب حافظه:** @Value یک یادداشت چسبان است؛ @ConfigurationProperties یک پوشه‌ی مرتب با برچسب و فهرست.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** با ApplicationEventPublisher رویداد منتشر می‌کنی و با @EventListener گوش می‌دهی؛ پیش‌فرض همگام، روی همان thread و داخل همان تراکنش است. @TransactionalEventListener با فاز AFTER_COMMIT تضمین می‌کند کار جانبی (مثل ارسال SMS) فقط وقتی تراکنش واقعاً commit شده انجام شود. ترکیب با @Async آن را به thread دیگری می‌برد. این روش سرویس‌ها را از هم جدا (decoupled) می‌کند.
>
> 🧠 **قلاب حافظه:** AFTER_COMMIT یعنی کارت تبریک عروسی را بعد از «بله» گفتن بفرست، نه قبلش.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** تراکنش قول «همه یا هیچ» است. در Spring این قول را یک proxy می‌دهد؛ پس هر جا proxy دور زده شود، قول هم شکسته می‌شود. این فصل نشان می‌دهد این قول کجا محکم است و کجا بی‌صدا زیرش زده می‌شود.

</div>

### 9.1 How @Transactional works

<div dir="rtl">

> 🪶 **به زبان ساده:** روی bean دارای @Transactional یک proxy قرار می‌گیرد که با TransactionInterceptor ویژگی‌ها را می‌خواند، از PlatformTransactionManager (مثلاً JpaTransactionManager) تراکنش می‌گیرد و connection یا EntityManager را به ThreadLocal همان thread می‌چسباند. بعد متد اصلی اجرا می‌شود و در صورت موفقیت commit و در صورت RuntimeException یا Error rollback انجام می‌شود. چون تراکنش به thread بسته است، کار در @Async، CompletableFuture یا thread جدید بیرون از تراکنش اجرا می‌شود.
>
> 🧠 **قلاب حافظه:** تراکنش کیف پولی است که به مچ دست همان thread بسته شده؛ thread دیگر به آن دسترسی ندارد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** REQUIRED (پیش‌فرض) به تراکنش موجود ملحق می‌شود یا یکی می‌سازد. REQUIRES_NEW تراکنش فعلی را معلق می‌کند و یک تراکنش مستقل با commit جداگانه می‌سازد، مثلاً برای لاگ audit که حتی با rollback بیرونی باید بماند؛ ولی connection دوم می‌خواهد و می‌تواند pool را تمام کند. NESTED با savepoint اجازه‌ی rollback جزئی می‌دهد. SUPPORTS، NOT_SUPPORTED، MANDATORY و NEVER رفتارهای مرزی را تعریف می‌کنند.
>
> 🧠 **قلاب حافظه:** REQUIRED سوار همان اتوبوس شو، REQUIRES_NEW تاکسی جداگانه بگیر، NESTED در همان اتوبوس صندلی‌ای که اگر خراب شد فقط به ایستگاه قبل برگردی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** پیش‌فرض Spring فقط برای RuntimeException و Error rollback می‌کند و برای exception های checked commit می‌کند؛ با rollbackFor این را تغییر می‌دهی. تله‌ی معروف UnexpectedRollbackException: متد داخلی با REQUIRED خطا می‌دهد و تراکنش مشترک rollback-only علامت می‌خورد؛ اگر متد بیرونی exception را catch کند و بخواهد commit کند، Spring اجازه نمی‌دهد. راه‌حل: بگذار exception بالا برود، یا متد داخلی REQUIRES_NEW یا NESTED باشد.
>
> 🧠 **قلاب حافظه:** وقتی یکی از اعضای تیم پرچم قرمز (rollback-only) را بالا برد، کاپیتان حتی اگر وانمود کند چیزی نشده نمی‌تواند بازی را ادامه دهد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** مرور تله‌ها: self-invocation، متد private، exception checked، catch کردن خطا داخل خود متد، کلاس یا متد final، bean ساخته‌شده با new، تراکنش طولانی همراه با فراخوانی HTTP بیرونی، و @Async. کارهای درست: readOnly برای خواندن، timeout، و گذاشتن @Transactional روی لایه‌ی service که مرز use-case است. در تست هم @Transactional به‌طور پیش‌فرض در پایان rollback می‌کند.
>
> 🧠 **قلاب حافظه:** هر تله یک راه است که نامه از کنار منشی (proxy) رد شود بدون اینکه مهر بخورد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** TransactionTemplate تراکنش را به‌صورت برنامه‌ای و با مرزهای دقیق می‌سازد، مثلاً هر chunk در یک تراکنش مستقل، و مشکل self-invocation را هم ندارد. می‌توانی propagation را تنظیم کنی و با setRollbackOnly تراکنش جاری را برای rollback علامت بزنی.
>
> 🧠 **قلاب حافظه:** @Transactional ترموستات خودکار است؛ TransactionTemplate کلیدی که خودت دقیقاً همان لحظه که می‌خواهی روشن و خاموش می‌کنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** 2PC (XA) با یک هماهنگ‌کننده همه‌ی منابع را prepare و بعد commit می‌کند؛ اتمی است ولی مسدودکننده، کند و تک‌نقطه‌ی شکست دارد و با Kafka و REST خوب کار نمی‌کند. Saga زنجیره‌ای از تراکنش‌های محلی همراه با اقدامات جبرانی است (choreography یا orchestration). Transactional Outbox مشکل «DB commit شد ولی پیام Kafka نرفت» را حل می‌کند: رویداد در همان تراکنش محلی در جدول outbox نوشته می‌شود و یک relay یا CDC (مثل Debezium) آن را منتشر می‌کند، پس مصرف‌کننده باید idempotent باشد.
>
> 🧠 **قلاب حافظه:** Saga مثل سفر با چند پرواز است: اگر پرواز سوم کنسل شد، بلیت‌های قبلی را refund می‌کنی (جبران)، نه اینکه زمان را به عقب برگردانی.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** Spring Boot همان Spring است با یک دستیار باتجربه که قبل از رسیدن تو خانه را مبله کرده؛ ولی مهندس سنیور باید بداند هر مبل را چه کسی، چرا و با چه شرطی آنجا گذاشته و چطور عوضش کند.

</div>

### 10.1 What Spring Boot adds

<div dir="rtl">

> 🪶 **به زبان ساده:** Spring Boot روی Spring چند لایه اضافه می‌کند: auto-configuration که bean ها را بر اساس classpath و properties می‌سازد، starter ها که مجموعه‌ی وابستگی‌های هماهنگ‌اند، سرور embedded که برنامه را با java -jar اجرا می‌کند، پیکربندی خارجی، Actuator برای سلامت و متریک، و پیش‌فرض‌های منطقی مثل HikariCP و Jackson.
>
> 🧠 **قلاب حافظه:** Spring جعبه‌ی قطعات لگوست؛ Spring Boot همان لگو به‌همراه دفترچه و قطعات نیمه‌مونتاژ.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** @SpringBootApplication ترکیب @Configuration، @EnableAutoConfiguration و @ComponentScan است. auto-configuration فهرست کلاس‌ها را از فایل AutoConfiguration.imports (از Boot 2.7؛ قبلاً spring.factories) می‌خواند و هر کدام با شرط‌هایی مثل @ConditionalOnClass و @ConditionalOnMissingBean فعال می‌شود. مهم‌ترین مفهوم back-off است: اگر خودت bean تعریف کنی، auto-config کنار می‌کشد. با --debug یا /actuator/conditions می‌بینی چه چیزی چرا فعال شده.
>
> 🧠 **قلاب حافظه:** auto-config پیشخدمتی است که فقط وقتی میز خالی است غذا می‌آورد (@ConditionalOnMissingBean)؛ اگر خودت غذا آورده باشی، مؤدبانه عقب می‌رود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** SpringApplication.run اول Environment و profile ها را می‌سازد، سپس ApplicationContext مناسب را ایجاد می‌کند، تعریف bean ها را از scan و auto-config بارگذاری می‌کند، با refresh همه‌ی singleton ها و post-processor ها را می‌سازد، سرور وب را راه می‌اندازد، CommandLineRunner ها را اجرا می‌کند و در نهایت ApplicationReadyEvent منتشر می‌شود؛ از این لحظه برنامه آماده‌ی ترافیک است.
>
> 🧠 **قلاب حافظه:** صبح یک رستوران: کلید (Environment)، روشن کردن چراغ‌ها (context)، آمدن آشپزها (beans)، باز کردن در (server)، تابلوی «باز است» (Ready).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Spring تنظیمات را از چند منبع با اولویت می‌خواند: آرگومان خط فرمان بالاترین اولویت را دارد، بعد متغیرهای محیطی و system property ها، و سپس فایل‌های application-{profile} و application، که نسخه‌ی بیرون jar بر نسخه‌ی داخل jar مقدم است. اسرار (secret) هرگز نباید داخل jar باشند؛ از متغیر محیطی، Vault یا secret های Kubernetes استفاده کن.
>
> 🧠 **قلاب حافظه:** هرچه منبع به «لحظه‌ی اجرا» نزدیک‌تر باشد حرفش بیشتر خریدار دارد: دستور شفاهی مدیر (command line) بر آیین‌نامه‌ی چاپی (jar) غلبه می‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Actuator اندپوینت‌هایی مثل health، metrics، info و prometheus می‌دهد و probe های liveness و readiness برای Kubernetes دارد. Micrometer نمای متریک است که به Prometheus و Grafana می‌رسد و Micrometer Tracing (جایگزین Sleuth در Boot 3) traceId و spanId را به لاگ‌ها اضافه می‌کند. اندپوینت‌های حساس مثل env و heapdump را هرگز عمومی نکن.
>
> 🧠 **قلاب حافظه:** Actuator داشبورد ماشین است: دماسنج، سرعت‌سنج و چراغ هشدار؛ ولی کاپوت (env، heapdump) را برای رهگذرها باز نمی‌گذاری.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Spring Boot 3 روی Spring Framework 6 و جاوا ۱۷ ساخته شده و مهم‌ترین تغییرش مهاجرت از javax به jakarta است. Hibernate 6، پشتیبانی از GraalVM native و AOT، observability داخلی با Micrometer، ProblemDetail برای خطاها، RestClient و HTTP interface ها، virtual thread ها (از 3.2) و حذف WebSecurityConfigurerAdapter به نفع bean های SecurityFilterChain از تغییرات کلیدی‌اند.
>
> 🧠 **قلاب حافظه:** Boot 3 اسباب‌کشی بزرگ است: آدرس پستی همه عوض شد (javax ← jakarta) و خانه‌ی جدید سریع‌تر و شفاف‌تر است.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** لایه‌ی وب در خانه‌ی برنامه است. هر درخواست HTTP از چند نگهبان رد می‌شود تا به اتاق درست برسد، و پاسخ هم همین مسیر را برمی‌گردد. شناختن این مسیر یعنی بدانی هر مشکل (امنیت، لاگ، خطا، تبدیل JSON) دقیقاً کجا باید حل شود.

</div>

### 11.1 DispatcherServlet request flow

<div dir="rtl">

> 🪶 **به زبان ساده:** درخواست ابتدا از filter های Servlet عبور می‌کند و به DispatcherServlet (Front Controller) می‌رسد. HandlerMapping متد کنترلر را پیدا می‌کند، preHandle اینترسپتورها اجرا می‌شود، HandlerAdapter آرگومان‌ها را آماده می‌کند (با HttpMessageConverter برای @RequestBody و اعتبارسنجی @Valid)، متد کنترلر اجرا می‌شود، خروجی به JSON تبدیل یا به View داده می‌شود، و exception ها توسط HandlerExceptionResolver و @ControllerAdvice مدیریت می‌شوند.
>
> 🧠 **قلاب حافظه:** DispatcherServlet پذیرش بیمارستان است: پرونده تشکیل می‌دهد، تو را به بخش درست می‌فرستد و در آخر نسخه را تحویلت می‌دهد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Filter در سطح Servlet container است و همه‌ی درخواست‌ها را با request و response خام می‌بیند؛ مناسب احراز هویت، CORS، فشرده‌سازی و correlation id. Interceptor در سطح Spring MVC است و می‌داند کدام متد handler اجرا می‌شود. AOP در سطح هر bean است و هیچ ربطی به HTTP ندارد؛ مناسب audit، تراکنش و retry. فراموش نکن MDC هم ThreadLocal است و باید در finally پاک شود.
>
> 🧠 **قلاب حافظه:** Filter نگهبان درِ ساختمان، Interceptor منشی طبقه، AOP دستیار شخصی داخل اتاق.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** در کنترلر REST از @Valid برای اعتبارسنجی ورودی، از ResponseEntity.created برای 201 به‌همراه هدر Location، و از Pageable برای صفحه‌بندی استفاده کن. خطاها را متمرکز در @RestControllerAdvice مدیریت کن و از Spring 6 به بعد ProblemDetail (استاندارد RFC 9457) برگردان تا همه‌ی خطاها یک قالب یکسان داشته باشند.
>
> 🧠 **قلاب حافظه:** ControllerAdvice بخش اورژانس است: هر خطایی از هر بخشی بیاید، همان‌جا با یک پروتکل استاندارد رسیدگی می‌شود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** GET امن و idempotent است؛ PUT و DELETE idempotent هستند یعنی تکرارشان نتیجه را عوض نمی‌کند؛ POST هیچ‌کدام نیست. برای پرداخت‌ها POST را با هدر Idempotency-Key و unique constraint idempotent کن تا درخواست تکراری دوباره پول کم نکند. کدهای وضعیت را دقیق انتخاب کن: 401 یعنی احراز نشده، 403 یعنی اجازه نداری، 409 برای تعارض نسخه و 429 برای محدودیت نرخ.
>
> 🧠 **قلاب حافظه:** Idempotent یعنی دکمه‌ی آسانسور: ده بار هم فشارش بدهی، آسانسور فقط یک بار می‌آید.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** RestTemplate مسدودکننده و در حالت نگهداری است. WebClient واکنشی و غیرمسدود است ولی وابستگی WebFlux می‌خواهد. RestClient از Spring 6.1 همان سادگی مسدودکننده را با API روان و مدرن می‌دهد و برای برنامه‌های MVC انتخاب امروز است، و HTTP interface ها کلاینت اعلانی مثل Feign می‌سازند. همیشه timeout اتصال و خواندن را تنظیم کن.
>
> 🧠 **قلاب حافظه:** تماس بدون timeout مثل منتظر ماندن پشت خط تلفنی است که هیچ‌وقت جواب نمی‌دهد و قطع هم نمی‌شود.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Spring MVC مدل یک thread برای هر درخواست و مسدودکننده دارد و با JDBC و JPA طبیعی کار می‌کند. WebFlux روی event loop با thread های کم و غیرمسدود کار می‌کند و باید تا انتها واکنشی باشد (R2DBC)، با Mono و Flux و back-pressure. جواب سنیور: کد مسدودکننده را وارد WebFlux نکن، و با virtual thread های جاوا ۲۱، MVC بیشتر نیازهای همروندی I/O را با کد ساده‌تر پوشش می‌دهد.
>
> 🧠 **قلاب حافظه:** MVC رستورانی است که برای هر میز یک گارسون دارد؛ WebFlux یک گارسون فرز که بین همه‌ی میزها می‌چرخد و هیچ‌وقت کنار یک میز منتظر نمی‌ایستد.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** Spring Data JPA یعنی دیگر لازم نیست repository بنویسی؛ فقط اسم متد را درست انتخاب کن. ولی این جادو هم قوانین خودش را دارد، به‌خصوص درباره‌ی save، تراکنش‌ها و هزینه‌ی صفحه‌بندی.

</div>

### 12.1 Repository hierarchy and query methods

<div dir="rtl">

> 🪶 **به زبان ساده:** سلسله‌مراتب از Repository تا CrudRepository و JpaRepository می‌رود و پیاده‌سازی واقعی SimpleJpaRepository است که به‌طور پیش‌فرض readOnly تراکنشی است. کوئری‌ها را می‌توانی از روی نام متد (derived query) بسازی، با @Query بنویسی، یا با @Modifying آپدیت گروهی انجام دهی. Page یک count query اضافه دارد، ولی Slice فقط می‌گوید صفحه‌ی بعدی هست یا نه و ارزان‌تر است.
>
> 🧠 **قلاب حافظه:** نام متد در Spring Data یک جمله‌ی دستوری است: «پیدا کن کارت‌ها را بر اساس وضعیت و انقضای قبل از...» و Spring آن را به SQL ترجمه می‌کند.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Projection یعنی فقط ستون‌هایی را که لازم داری بخوانی: با interface (closed projection)، با record یا کلاس DTO در JPQL، یا به‌صورت پویا با پارامتر Class. چون entity ساخته نمی‌شود، dirty checking و persistence context و ستون‌های اضافی حذف می‌شوند و API های خواندنی بسیار سبک‌تر می‌شوند.
>
> 🧠 **قلاب حافظه:** Projection یعنی از کل پرونده‌ی پرسنلی فقط کارت ویزیت را کپی کنی.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** Specification ها شرط‌های کوچک و قابل‌ترکیب می‌سازند که با and و or کنار هم قرار می‌گیرند؛ برای صفحه‌های جستجو با فیلترهای اختیاری ایده‌آل‌اند. اگر شرطی null برگرداند نادیده گرفته می‌شود. جایگزین‌ها Querydsl، jOOQ یا Criteria API مستقیم هستند.
>
> 🧠 **قلاب حافظه:** Specification ها قطعات لگوی فیلتر هستند؛ هر بار همان‌هایی را که کاربر انتخاب کرده کنار هم می‌چینی.

</div>

```java
static Specification<Card> hasStatus(Status s) { return (r, q, cb) -> s == null ? null : cb.equal(r.get("status"), s); }
static Specification<Card> holderLike(String h) { return (r, q, cb) -> h == null ? null : cb.like(r.get("holder"), "%" + h + "%"); }

Page<Card> page = repo.findAll(Specification.where(hasStatus(st)).and(holderLike(name)), pageable);
```

(Alternatives: Querydsl, jOOQ, Criteria API directly.)

---

[⬆ Back to top](#table-of-contents)

## 13. Spring Security

<div dir="rtl">

📜 **دروازه‌ی فصل:** امنیت در Spring یک صف از نگهبانان است که هر کدام یک سؤال می‌پرسند: «کی هستی؟»، «توکنت معتبر است؟»، «اجازه داری؟». اگر ترتیب و وظیفه‌ی هر نگهبان را بدانی، هم پیکربندی و هم دیباگ امنیت ساده می‌شود.

</div>

### 13.1 Filter chain architecture

<div dir="rtl">

> 🪶 **به زبان ساده:** Servlet container فقط یک filter به نام DelegatingFilterProxy را می‌شناسد که کار را به bean ای به نام FilterChainProxy می‌سپارد. FilterChainProxy اولین SecurityFilterChain منطبق با درخواست را انتخاب می‌کند و filter های آن به ترتیب اجرا می‌شوند: context، CORS، CSRF، احراز هویت (فرم یا JWT)، ExceptionTranslationFilter که 401 و 403 می‌سازد، و AuthorizationFilter. بعد از آن @PreAuthorize روی متدها با AOP بررسی می‌شود.
>
> 🧠 **قلاب حافظه:** فرودگاه: درِ ورودی (DelegatingFilterProxy)، انتخاب ترمینال (FilterChainProxy)، کنترل گذرنامه (authentication) و در آخر کارت سوار شدن (authorization).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** filter یک Authentication احراز نشده می‌سازد و به AuthenticationManager (ProviderManager) می‌دهد. ProviderManager آن را به AuthenticationProvider مناسب می‌سپارد؛ مثلاً DaoAuthenticationProvider کاربر را از UserDetailsService می‌خواند و رمز را با PasswordEncoder (مثل BCrypt) مقایسه می‌کند. نتیجه‌ی موفق در SecurityContextHolder قرار می‌گیرد که به‌طور پیش‌فرض ThreadLocal است. در Spring Security 6 پیکربندی با bean های SecurityFilterChain و authorizeHttpRequests انجام می‌شود.
>
> 🧠 **قلاب حافظه:** مدیر (Manager) پرونده را به کارشناس مربوط (Provider) می‌دهد؛ کارشناس از بایگانی (UserDetailsService) پرونده را می‌آورد و امضا را با دستگاه (PasswordEncoder) تطبیق می‌دهد.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** پاسخ‌های سریع را حفظ کن: 401 یعنی هویت نامعلوم و 403 یعنی هویت معلوم ولی بدون اجازه؛ hasRole('ADMIN') همان hasAuthority('ROLE_ADMIN') است؛ CSRF برای احراز با cookie لازم است ولی برای API بدون حالت با Bearer token نه؛ JWT به‌طور پیش‌فرض امضاشده است نه رمزشده و باید امضا، exp، iss و aud را بررسی کرد. برای کاربران Authorization Code به‌همراه PKCE و برای سرویس‌به‌سرویس Client Credentials یا mTLS؛ و SecurityContext به @Async منتقل نمی‌شود مگر با executor مخصوص.
>
> 🧠 **قلاب حافظه:** JWT مثل کارت شناسایی پلمب‌شده است: همه می‌توانند بخوانندش، ولی کسی نمی‌تواند بدون شکستن مهر عوضش کند.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** تست خوب یعنی اعتماد به release. سنیور می‌داند کدام لایه را با کدام نوع تست بسنجد تا هم مطمئن باشد و هم مجموعه‌ی تست ساعت‌ها طول نکشد.

</div>

### 14.1 Test pyramid and Spring test slices

<div dir="rtl">

> 🪶 **به زبان ساده:** هرم تست: پایه‌ی پهن تست‌های واحد با JUnit و Mockito بدون context، بالاتر تست‌های slice مثل @WebMvcTest برای کنترلر و @DataJpaTest برای repository، سپس تست‌های یکپارچه با @SpringBootTest و Testcontainers با دیتابیس واقعی، و در نوک تعداد کمی تست E2E یا contract. از Boot 3.4 به‌جای @MockBean از @MockitoBean استفاده می‌شود و از Boot 3.1 @ServiceConnection اتصال container را خودکار می‌کند. Spring context ها را cache می‌کند، پس ترکیب‌های متفاوت mock ها تست‌ها را کند می‌کند.
>
> 🧠 **قلاب حافظه:** هرم تست مثل هرم غذایی است: پایه‌ی پهن سبزیجات (unit)، و نوک باریک شیرینی (E2E)؛ برعکسش بیمار می‌شوی.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** در دنیای میکروسرویس شکست قطعی است، سؤال فقط این است که سیستم چطور شکست را تحمل کند. الگوهای این بخش کمربند ایمنی سرویس‌ها هستند: circuit breaker، retry، timeout، bulkhead و پیام‌رسانی idempotent.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** مهندس بک‌اند باید بداند بسته‌ها چطور از سرویسی به سرویس دیگر می‌روند، چه چیزی رمز می‌شود و چرا یک اتصال کند است. این فصل از یادداشت‌های خودت آمده و کمی گسترش پیدا کرده است.

</div>

### 16.1 Network layers (TCP/IP vs OSI)

<div dir="rtl">

> 🪶 **به زبان ساده:** مدل OSI هفت لایه دارد و مدل TCP/IP چهار لایه: Application (HTTP، DNS، gRPC)، Transport (TCP، UDP)، Internet (IP) و Link (Ethernet). TLS بین لایه‌ی برنامه و انتقال قرار می‌گیرد. در مصاحبه کافی است بدانی هر پروتکل در کدام لایه است و هر لایه چه مسئولیتی دارد.
>
> 🧠 **قلاب حافظه:** ارسال نامه: متن نامه (Application)، پست سفارشی با رسید (TCP)، آدرس شهر و خیابان (IP)، و کامیون و جاده (Link).

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** TCP اتصال‌گرا، مرتب و قابل‌اعتماد است و با handshake سه‌مرحله‌ای (SYN، SYN-ACK، ACK) شروع می‌شود؛ برای وب، دیتابیس و ایمیل. UDP بدون اتصال و بدون تضمین ترتیب و تحویل است ولی سریع؛ برای استریم، VoIP، DNS و HTTP/3 (QUIC). TLS روی TCP رمزنگاری و احراز هویت اضافه می‌کند و در نسخه‌ی ۱.۳ handshake فقط یک رفت‌وبرگشت دارد.
>
> 🧠 **قلاب حافظه:** TCP پست سفارشی با رسید، UDP بروشوری که از پنجره پخش می‌کنی، TLS پاکت مهر و موم شده دور نامه.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** HTTP مدل درخواست و پاسخ بدون رمزنگاری دارد، HTTPS همان HTTP روی TLS است، و WebSocket بعد از یک Upgrade از HTTP یک اتصال دائمی و دوطرفه می‌سازد که سرور هم می‌تواند پیام push کند. HTTP/2 چند stream را روی یک اتصال TCP multiplex می‌کند (پایه‌ی gRPC) و HTTP/3 روی QUIC و UDP مشکل head-of-line blocking در TCP را حل می‌کند.
>
> 🧠 **قلاب حافظه:** HTTP نامه‌نگاری است (سؤال و جواب)، WebSocket تماس تلفنی باز که هر دو طرف هر وقت خواستند حرف می‌زنند.

</div>

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

<div dir="rtl">

📜 **دروازه‌ی فصل:** شب قبل از مصاحبه وقت یادگیری مطلب جدید نیست؛ وقت مرور فشرده و آماده کردن داستان‌های واقعی است. این فصل همان کارت‌های مرور آخر است.

</div>

### 17.1 Rapid-fire one-liners

<div dir="rtl">

> 🪶 **به زبان ساده:** این خطوط را مثل فلش‌کارت چند بار بلند بخوان. هر خط خلاصه‌ی یک مبحث کامل است؛ اگر جایی نتوانستی توضیح بیشتری بدهی، به همان بخش برگرد. هدف این است که در مصاحبه اولین جمله‌ات دقیق و مطمئن باشد و بعد عمق بدهی.
>
> 🧠 **قلاب حافظه:** این فهرست کلید فهرست کتاب ذهنی توست: هر خط، دری به یک فصل کامل.

</div>

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

<div dir="rtl">

> 🪶 **به زبان ساده:** پاسخ سنیور پنج مرحله دارد: تعریف یک‌خطی، توضیح سازوکار داخلی (ترجیحاً با یک شکل)، trade-off ها، تجربه‌ی واقعی از پروژه‌ی خودت (مثلاً سیستم Card Issuing، تست‌های performance و incident های production)، و نتیجه با عدد. برای هر مورد چک‌لیست یک داستان STAR آماده کن؛ مصاحبه‌گر سنیور بیشتر از تعریف، دنبال «تو چه کردی و چه نتیجه‌ای گرفتی» است.
>
> 🧠 **قلاب حافظه:** جواب جونیور «تعریف» است، جواب سنیور «داستان با عدد».

</div>

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
