# آمادگی مصاحبه: سوالات فنی

Oct 22, 2026 · @Meysam

## روش جواب دادن در ۱ تا ۲ دقیقه

هر جواب را با همین چهار قدم بگو تا نه پراکنده‌گویی کنی و نه ذهنت قفل شود.

1. **تعریف** در یک جمله.
2. **چطور کار می‌کند** در ۲ تا ۳ جمله.
3. **Trade-off یا pitfall**: کجا خراب می‌شود و چه هزینه‌ای دارد.
4. **مثال خودت**: یک مورد واقعی از Worldline، OCS یا Novin Kish.

اگر جزئیاتی را نمی‌دانی، همان چیزی را که می‌دانی بگو و توضیح بده چطور بررسی‌اش می‌کنی. مصاحبه‌گر این را بیشتر از یک حدس ارزش‌گذاری می‌کند.

## Databases

### Index (B-tree، composite، وقتی index استفاده نمی‌شود)

- **B-tree:** یک درخت متوازن از کلیدهای مرتب. جستجو O(log n) است و چون leaf pageها به هم لینک شده‌اند، range scan و ORDER BY ارزان است. fan-out بالا باعث می‌شود درخت فقط ۳ تا ۴ سطح عمق داشته باشد، پس disk read کم است.
- **Composite index (a, b, c):** از چپ به راست کار می‌کند؛ قانون *leftmost prefix*. به `WHERE a = ?` و `WHERE a = ? AND b = ?` کمک می‌کند، ولی به `WHERE b = ?` به‌تنهایی نه. ستون‌های equality را اول بگذار و ستون range را آخر.
- **Covering index:** همه ستون‌هایی که query لازم دارد داخل index است، پس DB اصلاً سراغ جدول نمی‌رود (index-only scan؛ `INCLUDE` در PostgreSQL و SQL Server).
- **وقتی index استفاده نمی‌شود:**
  - تابع یا محاسبه روی ستون: `WHERE UPPER(name) = 'X'` (راه‌حل: function-based index)
  - implicit type conversion، مثلاً مقایسه ستون VARCHAR با عدد
  - wildcard در ابتدا: `LIKE '%abc'`
  - selectivity پایین: query بخش بزرگی از ردیف‌ها را برمی‌گرداند، پس full scan ارزان‌تر است
  - رد شدن از ستون اول composite index
  - statistics قدیمی، یا جدول خیلی کوچک
- **هزینه:** هر index عملیات INSERT/UPDATE/DELETE را کندتر می‌کند و فضا می‌گیرد. index را برای query patternهای واقعی بساز، نه «محض احتیاط».

**مثال خودت:** تحلیل performance و review اسکریپت‌های DB روی سیستم Card Issuing — خواندن execution plan، پیدا کردن full scan یا index گم‌شده/بلااستفاده، و تأیید با load test.

### ACID

- **Atomicity:** همه یا هیچ؛ در صورت خطا DB با استفاده از undo data همه چیز را rollback می‌کند.
- **Consistency:** constraintها (PK، FK، CHECK) بعد از commit همیشه برقرارند.
- **Isolation:** transactionهای هم‌زمان کار نیمه‌تمام همدیگر را نمی‌بینند.
- **Durability:** بعد از commit، داده از crash جان سالم به در می‌برد — write-ahead log (redo log) هنگام commit روی دیسک flush می‌شود.

### Isolation levelها و anomalyها

| Level | Dirty read | Non-repeatable read | Phantom read |
| --- | --- | --- | --- |
| Read Uncommitted | ممکن | ممکن | ممکن |
| Read Committed | جلوگیری | ممکن | ممکن |
| Repeatable Read | جلوگیری | جلوگیری | ممکن (طبق SQL standard) |
| Serializable | جلوگیری | جلوگیری | جلوگیری |

- **Dirty read:** تغییر commit‌نشده یک transaction دیگر را می‌خوانی.
- **Non-repeatable read:** یک ردیف را دو بار می‌خوانی و دو مقدار متفاوت می‌گیری.
- **Phantom:** یک range query بار دوم ردیف‌های جدید برمی‌گرداند.
- این دو را هم بلد باش: **lost update** (دو read-modify-write که یکی روی دیگری overwrite می‌کند) و **write skew** (دو transaction هر کدام یک قانون را چک می‌کنند و بعد هر دو write می‌کنند و قانون می‌شکند — مثلاً دو پزشک هم‌زمان از on-call خارج می‌شوند).
- **پیش‌فرض‌های واقعی:** PostgreSQL، Oracle و SQL Server پیش‌فرضشان Read Committed است؛ MySQL InnoDB پیش‌فرضش Repeatable Read است. Repeatable Read در PostgreSQL در واقع snapshot isolation است (phantom ندارد ولی write skew ممکن است)؛ Serializable آن از SSI استفاده می‌کند و transactionهای متعارض را abort می‌کند، پس اپلیکیشن باید retry کند.

### MVCC

- Multi-Version Concurrency Control: هر update به جای overwrite، یک **version جدید** از ردیف می‌نویسد. هر transaction یک snapshot سازگار می‌خواند.
- نتیجه: **readerها writerها را block نمی‌کنند و writerها هم readerها را.** ولی writerها روی یک ردیف همچنان همدیگر را block می‌کنند.
- **PostgreSQL:** versionهای قدیمی در خود جدول می‌مانند (`xmin`/`xmax`)؛ VACUUM آن‌ها را پاک می‌کند، پس transactionهای طولانی باعث bloat می‌شوند.
- **Oracle:** versionهای قدیمی از undo segmentها بازسازی می‌شوند؛ queryهای طولانی ممکن است با ORA-01555 «snapshot too old» fail شوند.
- **SQL Server:** به‌طور پیش‌فرض locking؛ versioning فقط با READ\_COMMITTED\_SNAPSHOT یا SNAPSHOT، و version store در tempdb است.

### Locking

- قفل **shared (read) در برابر exclusive (write)**، معمولاً در سطح ردیف. SQL Server ممکن است تعداد زیادی row lock را به table lock تبدیل کند (lock escalation).
- **Pessimistic:** `SELECT … FOR UPDATE` ردیف را تا commit قفل می‌کند. برای contention بالا مثل به‌روزرسانی موجودی حساب مناسب است.
- **Optimistic:** یک ستون version؛ `UPDATE … WHERE id = ? AND version = ?` و اگر ۰ ردیف آپدیت شد یعنی کس دیگری برنده شده، پس retry کن. در JPA: `@Version`. وقتی تداخل کم است مناسب است.
- **Deadlock:** DB چرخه را تشخیص می‌دهد و یکی از transactionها را kill می‌کند. برای جلوگیری، ردیف‌ها را همیشه با ترتیب ثابت قفل کن (مثلاً بر اساس account id) و transactionها را کوتاه نگه دار.

### N+1 queries

- ORM یک query برای لیست parentها اجرا می‌کند، بعد برای children هر parent یک query جداگانه (lazy): یعنی 1 + N رفت‌وبرگشت.
- **تشخیص:** SQL logging، Hibernate statistics، APM traceهایی که صدها query یکسان در هر request نشان می‌دهند.
- **راه‌حل:** `JOIN FETCH` یا `@EntityGraph`، batch fetching (`hibernate.default_batch_fetch_size`)، یا DTO projection با یک query. مراقب باش: JOIN FETCH روی collection همراه با pagination، صفحه‌بندی را در حافظه انجام می‌دهد.

## Concurrency

### Race condition در برابر deadlock

- **Race condition:** نتیجه به زمان‌بندی threadها بستگی دارد. مثال کلاسیک: `count++` شامل read، add و write است — دو thread هر دو 5 را می‌خوانند و هر دو 6 می‌نویسند. راه‌حل: عملیات را atomic کن (با lock یا کلاس atomic) یا shared mutable state را حذف کن.
- **Deadlock:** threadها تا ابد منتظر lockهای همدیگر می‌مانند. Thread A قفل 1 را دارد و قفل 2 را می‌خواهد؛ thread B قفل 2 را دارد و قفل 1 را می‌خواهد.
- **جلوگیری از deadlock:** lockها را همیشه با یک ترتیب سراسری ثابت بگیر، از `tryLock(timeout)` استفاده کن، و lockهای کمتری را برای زمان کوتاه‌تری نگه دار.
- **تشخیص:** یک thread dump (`jstack` یا `jcmd <pid> Thread.print`) پیام «Found one Java-level deadlock» را گزارش می‌دهد.
- مفاهیم مرتبط: **livelock** (threadها مدام به هم واکنش نشان می‌دهند ولی پیشرفتی نیست) و **starvation** (یک thread هیچ‌وقت به resource نمی‌رسد).

### volatile در برابر atomics در برابر locks

| ابزار | Visibility | Atomic compound ops | کاربرد معمول |
| --- | --- | --- | --- |
| `volatile` | بله | خیر | stop flag، رفرنس config منتشرشده |
| `AtomicInteger` / `AtomicReference` | بله | بله، یک متغیر (CAS) | counter، sequence number، آپدیت lock-free |
| `synchronized` / `ReentrantLock` | بله | بله، هر بلوک کد | invariantهایی که چند فیلد را در بر می‌گیرند |

- `volatile` یک رابطه happens-before ایجاد می‌کند: writeها فوراً توسط threadهای دیگر دیده می‌شوند و reorder نمی‌شوند. ولی `x++` را **امن نمی‌کند**.
- Atomicها از compare-and-swap (CAS) استفاده می‌کنند: بدون blocking، و در صورت تداخل retry. در contention شدید، `LongAdder` برای counter از `AtomicLong` بهتر است.
- Lockها mutual exclusion می‌دهند. `ReentrantLock` امکانات tryLock، timeout، fairness و condition اضافه می‌کند؛ `ReadWriteLock` اجازه چند reader یا یک writer می‌دهد.

### Thread pools

- به جای ساختن یک thread برای هر task، مجموعه ثابتی از threadها دوباره استفاده می‌شوند؛ این کار concurrency و حافظه را محدود می‌کند.
- تنظیمات `ThreadPoolExecutor`: core size، max size، queue، rejection policy.
- **Sizing:** برای کار CPU-bound تقریباً برابر تعداد coreها. برای IO-bound تقریباً cores × (1 + wait time ÷ compute time).
- **Pitfallها:** `Executors.newFixedThreadPool` از یک queue نامحدود استفاده می‌کند — زیر بار رشد می‌کند تا OutOfMemory. از bounded queue همراه با rejection policy مثل `CallerRunsPolicy` برای backpressure استفاده کن. threadها را نام‌گذاری کن و poolها را همیشه shutdown کن.

### CompletableFuture

- مراحل async را بدون blocking ترکیب می‌کند: `supplyAsync` ← `thenApply` (transform) ← `thenCompose` (زنجیر کردن یک فراخوانی async دیگر) ← `thenCombine` / `allOf` (اجرای موازی و سپس join).
- خطاها: `exceptionally` یا `handle`. Timeout: `orTimeout` و `completeOnTimeout` (از Java 9 به بعد).
- **Pitfall:** بدون آرگومان executor، روی `ForkJoinPool.commonPool()` اجرا می‌شود که برای کار CPU اندازه‌گذاری شده. برای blocking IO همیشه executor خودت را بده.
- مثال: سرویس‌های card-status، limits و fraud-score را موازی صدا بزن و نتایج را ترکیب کن — latency برابر کندترین فراخوانی می‌شود، نه مجموع آن‌ها.

### Virtual threads (Java 21)

- threadهای سبکی که JVM مدیریتشان می‌کند و روی یک pool کوچک از carrier threadها (platform threads) mount می‌شوند. یک فراخوانی blocking IO، virtual thread را unmount می‌کند و carrier آزاد می‌شود.
- می‌توانی میلیون‌ها از آن‌ها اجرا کنی، پس کد ساده thread-per-request با blocking مثل کد async scale می‌شود.
- **مناسب برای** کار IO-bound: فراخوانی HTTP و DB. برای کار CPU-bound **سریع‌تر نیست**.
- **Pitfallها:** آن‌ها را pool نکن، برای هر task یکی بساز. دسترسی به resourceهای محدود (DB connection) را با `Semaphore` کنترل کن. قبل از Java 24، blocking داخل `synchronized` باعث pin شدن carrier می‌شود — `ReentrantLock` را ترجیح بده. در استفاده سنگین از `ThreadLocal` محتاط باش.

**ارتباط با Rust:** Rust با traitهای `Send` و `Sync` جلوی data race را در زمان compile می‌گیرد — همان مشکلاتی که در Java با دقت و code review حل می‌کنی.

## Distributed systems

### CAP و consistency modelها

- **CAP:** وقتی network partition رخ می‌دهد، سیستم باید بین **Consistency** (رد کردن یا به تأخیر انداختن requestها) و **Availability** (جواب دادن، شاید با داده قدیمی) یکی را انتخاب کند. partition اجتناب‌ناپذیر است، پس انتخاب واقعی بین C و A *در زمان* partition است.
- **مثال‌های CP:** ZooKeeper، etcd، یک SQL database با single leader. **مثال‌های AP:** Cassandra، DynamoDB در حالت پیش‌فرض.
- **PACELC** اضافه می‌کند: Else، یعنی وقتی partition نیست، بین **Latency** و **Consistency** trade-off داری.
- **Consistency modelها، از قوی به ضعیف:**
  - **Linearizable:** مثل یک نسخه واحد رفتار می‌کند؛ هر read آخرین write را می‌بیند.
  - **Causal:** ترتیب علت و معلول حفظ می‌شود (جواب هیچ‌وقت قبل از سوال ظاهر نمی‌شود).
  - **Read-your-writes:** همیشه آپدیت‌های خودت را می‌بینی.
  - **Eventual:** اگر writeها متوقف شوند، replicaها در نهایت یکسان می‌شوند.
- **Quorum:** با N replica، اگر read quorum R + write quorum W > N باشد، هر read با آخرین write هم‌پوشانی دارد.
- **زاویه payments:** موجودی حساب به strong consistency نیاز دارد؛ نمای تاریخچه تراکنش‌ها می‌تواند eventually consistent باشد.

### Idempotency و retry

- در شبکه، timeout یعنی **نتیجه نامعلوم** — ممکن است request موفق شده باشد. پس retry لازم است و فقط وقتی امن است که عملیات **idempotent** باشد (دو بار اجرا = یک بار اجرا).
- **Idempotency key:** کلاینت یک request id یکتا می‌فرستد؛ سرور آن را با نتیجه زیر یک unique constraint ذخیره می‌کند. request تکراری به جای شارژ دوباره کارت، نتیجه ذخیره‌شده را برمی‌گرداند.
- **قوانین retry:** فقط خطاهای transient را retry کن (timeout، 503)، هرگز validation error را. از exponential backoff **همراه با jitter**، حداکثر تعداد تلاش و یک deadline کلی استفاده کن.
- **محافظت از downstream:** وقتی یک سرویس down است، circuit breaker جلوی retry storm را می‌گیرد (مثلاً Resilience4j).

### At-least-once در برابر exactly-once (Kafka)

| Semantics | روش | ریسک |
| --- | --- | --- |
| At-most-once | اول commit offset، بعد process | در crash پیام از دست می‌رود |
| At-least-once | اول process، بعد commit offset | در crash پیام تکراری ← consumer باید idempotent باشد |
| Exactly-once | idempotent producer + transactions | فقط داخل Kafka؛ side effectهای بیرونی همچنان idempotency می‌خواهند |

- **Idempotent producer** (`enable.idempotence=true`، پیش‌فرض از Kafka 3.0): broker با producer id + sequence number، retryهای تکراری را دور می‌ریزد.
- **Transactions** (`transactional.id`): consume، process و produce به‌همراه commit offset، همه در یک قدم atomic. consumerها از `isolation.level=read_committed` استفاده می‌کنند.
- **جواب صادقانه:** exactly-once سرتاسری با یک database یا API بیرونی = at-least-once delivery + idempotent processing (جدول dedupe، upsert).
- **Transactional outbox:** ردیف business و یک ردیف outbox را در یک DB transaction بنویس؛ یک relay (یا Debezium CDC) آن را به Kafka publish می‌کند. این از حالت «در DB ذخیره شد ولی event گم شد» جلوگیری می‌کند.

### Partitioning

- داده را بر اساس key بین nodeها تقسیم کن تا سیستم scale out شود. **Hash partitioning** بار را یکنواخت پخش می‌کند؛ **range partitioning** رنج‌ها را کنار هم نگه می‌دارد و برای range query خوب است، ولی ریسک hot spot دارد.
- **Consistent hashing:** با اضافه یا حذف یک node، فقط حدود 1/N از keyها جابه‌جا می‌شوند.
- **Kafka:** ترتیب فقط **داخل یک partition** تضمین می‌شود؛ message key تعیین می‌کند پیام به کدام partition برود. بر اساس card یا account id کلید بزن تا همه eventهای یک کارت مرتب بمانند. در یک consumer group هر partition حداکثر به یک consumer می‌رسد، پس تعداد partitionها سقف parallelism است.
- **Pitfallها:** hot key (یک merchant خیلی بزرگ)، توقف‌های rebalancing، و query یا transactionهای cross-partition.

### Leader election و consensus

- **Consensus:** nodeها با وجود failure روی یک مقدار یا یک log مرتب به توافق می‌رسند. Raft و Paxos الگوریتم‌های اصلی‌اند؛ توضیح Raft راحت‌تر است.
- **Raft به‌طور خلاصه:**
  1. زمان به **term**ها تقسیم می‌شود. followerی که heartbeat نمی‌شنود candidate می‌شود و رأی می‌خواهد.
  2. candidateی که رأی **majority** را بگیرد، leader آن term می‌شود.
  3. leader دستورات کلاینت را به log خودش اضافه و replicate می‌کند؛ یک entry وقتی **committed** است که majority آن را ذخیره کرده باشد.
  4. nodeی با log قدیمی نمی‌تواند election را ببرد، پس داده committed هرگز گم نمی‌شود.
- **Majority quorum:** با 2f + 1 node می‌توان f failure را تحمل کرد (3 node ← 1 failure، 5 ← 2). برای همین clusterها تعداد فرد دارند.
- **Split brain:** با نیاز به majority جلوگیری می‌شود، به‌علاوه **fencing token** / epoch number تا writeهای leader قدیمی رد شوند.
- **کجا با آن روبه‌رو می‌شوی:** etcd (Kubernetes)، ZooKeeper (ZAB)، Kafka KRaft controllerها.

## Data structures

### خلاصه Big-O (Java)

| Structure | Get / search | Insert | Delete | مرتب؟ | کی استفاده کنیم |
| --- | --- | --- | --- | --- | --- |
| `ArrayList` | O(1) با index، O(n) جستجو | O(1) amortized در انتها | O(n) | ترتیب درج | لیست پیش‌فرض، دسترسی سریع با index |
| `LinkedList` / `ArrayDeque` | O(n) | O(1) در دو سر | O(1) در دو سر | ترتیب درج | queue یا stack (`ArrayDeque` بهتر است) |
| `HashMap` / `HashSet` | O(1) میانگین | O(1) میانگین | O(1) میانگین | خیر | جستجوی سریع با key، حذف تکراری، شمارش |
| `TreeMap` / `TreeSet` | O(log n) | O(log n) | O(log n) | مرتب | keyهای مرتب، range query یا nearest key |
| `PriorityQueue` (heap) | O(1) peek min | O(log n) | O(log n) poll | فقط min/max | top-K، scheduling، کوچک‌ترین بعدی |
| `LinkedHashMap` | O(1) | O(1) | O(1) | ترتیب درج یا دسترسی | LRU cache |

برای ساختارهای hash بگو «میانگین»: وقتی تعداد زیادی key با هم collision دارند، worst case بدتر می‌شود.

### ساختار داخلی HashMap

1. یک آرایه از **bucket**ها. تابع `hash()` بیت‌های بالای `hashCode()` را با بیت‌های پایین ترکیب می‌کند؛ index = `hash & (capacity − 1)` (capacity توانی از 2 است).
2. **Collision:** هر bucket یک linked list دارد. از Java 8، bucketی با بیش از 8 entry (و capacity جدول ≥ 64) به **red-black tree** تبدیل می‌شود، پس worst case به جای O(n)، برابر O(log n) است.
3. **Resize:** وقتی size > capacity × load factor (پیش‌فرض 0.75) شود، جدول دو برابر می‌شود و entryها دوباره پخش می‌شوند.
4. **Contract:** objectهای equal باید `hashCode()` برابر داشته باشند. هیچ‌وقت key را بعد از درج تغییر نده — در bucket اشتباه گم می‌شود.
5. **Thread-safe نیست.** از `ConcurrentHashMap` استفاده کن: CAS برای binهای خالی، lock برای هر bin، بدون lock سراسری؛ `compute` / `merge` برای آپدیت atomic.

### Heap

- یک complete binary tree که در آرایه ذخیره شده: فرزندان index i در 2i+1 و 2i+2 هستند. در min-heap هر parent ≤ فرزندانش است.
- peek برابر O(1)، insert و poll برابر O(log n)، ساختن از n آیتم O(n).
- `PriorityQueue` در Java یک min-heap است؛ برای max-heap از `Comparator.reverseOrder()` استفاده کن.
- **کاربردهای کلاسیک:** top-K آیتم (یک min-heap با اندازه K نگه دار ← O(n log K))، merge کردن K لیست مرتب، task scheduling بر اساس زمان، الگوریتم Dijkstra.

### Treeها

- **Binary search tree:** left < node < right. اگر نامتوازن باشد به O(n) می‌رسد؛ اگر متوازن باشد (red-black، AVL) در O(log n) می‌ماند. `TreeMap` یک red-black tree است، با `floorKey`، `ceilingKey` و `subMap` برای range query.
- **B-tree / B+ tree:** هر node کلیدهای زیادی دارد، پس درخت کم‌عمق است — برای disk page طراحی شده. به همین دلیل database indexها از آن استفاده می‌کنند (لینک به بخش Databases).
- **Trie:** درختی از کاراکترها، برای prefix search و autocomplete.

### انتخاب — یک قانون کوتاه برای گفتن

«جستجو با key لازم داری ← HashMap. مرتب یا بر اساس range ← TreeMap. مدام کوچک‌ترین یا بزرگ‌ترین را می‌خواهی ← heap. دسترسی با index ← ArrayList. FIFO ← ArrayDeque.»

## آشنایی با Rust

### Ownership

- هر value دقیقاً **یک owner** دارد. وقتی owner از scope خارج شود، value اصطلاحاً drop و حافظه‌اش آزاد می‌شود — در نقطه‌ای مشخص که در زمان compile تعیین شده (مثل RAII در C++).
- assign یا پاس دادن یک value روی heap، ownership را **move** می‌کند: بعد از `let b = a;` دیگر نمی‌توان از `a` استفاده کرد. برای کپی صریح از `.clone()` استفاده کن.

### Borrowing

- به جای move، یک reference قرض می‌دهی: **چند `&T` (shared و فقط خواندنی) یا یک `&mut T` (انحصاری و قابل نوشتن) — هرگز هر دو با هم.**
- **Borrow checker** این را در زمان compile اجبار می‌کند. **Lifetime**ها تضمین می‌کنند یک reference هیچ‌وقت بیشتر از داده‌ای که به آن اشاره می‌کند عمر نکند.
- همین یک قانون data race را حذف می‌کند: دو thread نمی‌توانند هم‌زمان reference قابل نوشتن داشته باشند.

### چرا memory safety بدون GC

- Java در runtime با garbage collector زباله‌ها را پیدا می‌کند. Rust در زمان compile ثابت می‌کند حافظه کی آزاد می‌شود، پس GC pause نداریم و مصرف حافظه قابل پیش‌بینی است.
- compiler جلوی use-after-free، double free، dangling pointer و data race را می‌گیرد. null وجود ندارد: `Option<T>`. خطاها value هستند: `Result<T, E>` همراه با عملگر `?`.
- **Shared ownership در صورت نیاز:** `Rc<T>` (یک thread)، `Arc<T>` (چند thread). **Shared mutation:** `Mutex<T>` / `RwLock<T>` — lock *داده را در بر می‌گیرد*، پس بدون قفل کردن نمی‌توانی به داده دست بزنی (در Java، lock و داده جدا هستند و باید یادت بماند).
- traitهای `Send` / `Sync` مشخص می‌کنند چه چیزی می‌تواند بین threadها منتقل یا share شود؛ compiler آن‌ها را چک می‌کند.
- Async در Rust: `async`/`await` روی یک runtime مثل Tokio — قابل مقایسه با CompletableFuture و virtual threads.

### «چرا به یک نقش Rust علاقه داری؟» — جواب تو

این جواب را عیناً به انگلیسی بگو، چون در مصاحبه همین را می‌گویی:

> "I've built Java systems professionally for over 13 years, mostly high-throughput banking and card-processing services where performance, concurrency and correctness matter. A lot of my work has been finding latency, memory and concurrency problems in production. Rust appeals to me because it prevents many of those problems at compile time — no data races, no GC pauses, predictable performance. The concepts transfer directly from my experience: ownership is close to how I already reason about thread safety and resource lifetimes. I'm actively learning Rust, and for the coding exercise today I'll use Java so you can see my problem solving at full speed."

**اگر روی عمق دانش Rust فشار آوردند:** صادق باش و بگو: "I'm not yet at production level in Rust. I'm comfortable with ownership, borrowing, Result/Option and the basic concurrency types, and I learn fast — I've moved between Java, C# and Angular/TypeScript in production before."
