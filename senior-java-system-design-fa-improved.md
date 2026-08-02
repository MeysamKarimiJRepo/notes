

# راهنمای جامع System Design برای مصاحبه Senior Java Developer

این نسخه برای **یادگیری عمیق و پاسخ‌گویی در مصاحبه** بازطراحی شده است. به‌جای حفظ کردن جواب‌های کوتاه، برای هر موضوع باید بتوانید:
1. Requirement را روشن کنید.
2. فرضیات و اعداد تقریبی بدهید.
3. معماری را مرحله‌به‌مرحله توضیح دهید.
4. Bottleneck و failure mode را پیدا کنید.
5. Trade-off تصمیم خود را دفاع کنید.
6. تصمیم را به Java/Spring/JVM و production وصل کنید.

> **قالب پیشنهادی پاسخ در مصاحبه:**  
> **Clarify → Estimate → Design → Data/API → Scale → Failure → Observability/Security → Trade-offs**

---

## ۰. چارچوب عملی پاسخ به هر سوال System Design

### مرحله ۱ — Clarify requirements
قبل از طراحی، مستقیم سراغ Kafka، Redis یا Kubernetes نروید. بپرسید:
- کاربران چه کسانی هستند؟
- مهم‌ترین use case چیست؟
- read/write ratio چقدر است؟
- consistency کجا حیاتی است؟
- latency هدف چیست؟
- حجم و peak traffic چقدر است؟
- multi-region لازم است؟
- data retention و compliance چیست؟

### مرحله ۲ — Capacity estimation
مثال: اگر 10M کاربر فعال روزانه، هر کاربر 20 request/day داشته باشد:
- Average QPS ≈ `10,000,000 × 20 / 86,400 ≈ 2,315`
- اگر peak factor برابر 5 باشد: Peak ≈ `11.6K QPS`
- اگر response متوسط 5 KB باشد، outbound traffic در peak حدود `58 MB/s` است.

هدف estimation عدد دقیق نیست؛ هدف نشان دادن این است که انتخاب‌های معماری شما با scale سازگارند.
number of active users * (request/day)

### مرحله ۳ — High-level design

```text
Clients
   |
   v
[CDN / WAF]
   |
   v
[Load Balancer / API Gateway]
   |
   +-------------------+
   |                   |
   v                   v
[Service A]         [Service B]
   | \                 / |
   |  \----[Kafka]-----/  |
   v                    v
[Redis]             [Database]
                         |
                         v
                    [Replica]
```

### مرحله ۴ — Deep dive
مصاحبه‌کننده معمولاً یکی از این بخش‌ها را عمیق می‌کند: database، caching، messaging، concurrency، consistency، resilience یا JVM performance.

### مرحله ۵ — Failure thinking
برای هر component بپرسید: «اگر این بخش down/slow شود چه می‌شود؟» سپس timeout، retry، circuit breaker، [^1]DLQ، replication، idempotency، [^2]graceful degradation و [^4]recovery را توضیح دهید.

### مرحله ۶ — Production readiness
پاسخ Senior باید metrics، logs، tracing، alerting، deployment، security، capacity planning و rollback را نیز پوشش دهد.

---

مجموعه‌ای دسته‌بندی‌شده از سوالات System Design به همراه پاسخ، مخصوص موقعیت Senior Java Developer. اصطلاحات تخصصی به همان شکل انگلیسی نوشته شده‌اند.

---

## ۱. مبانی System Design

**سوال:** مهم‌ترین attribute هایی که هنگام طراحی یک سیستم بررسی می‌کنید کدام‌اند (scalability، availability، consistency، latency، durability)؟ چطور اولویت‌بندی‌شان می‌کنید؟
**پاسخ:** اولویت‌بندی بستگی به نیاز کسب‌وکار دارد. مثلاً در یک سیستم پرداخت، consistency و durability اولویت اول‌اند، در حالی‌که در یک سیستم پیام‌رسان یا فید خبری، availability و latency مهم‌تر می‌شوند. باید ابتدا requirement های functional و non-functional را از stakeholder ها جمع کرد، سپس trade-off ها را بر اساس [^3]CAP theorem و SLA مورد نیاز مشخص کرد.

**سوال:** تفاوت vertical scaling و horizontal scaling چیست؟ چه زمانی هرکدام را انتخاب می‌کنید؟
**پاسخ:** Vertical scaling یعنی افزایش منابع یک node واحد (CPU، RAM قوی‌تر) و ساده‌تر است اما یک سقف physical و single point of failure دارد. Horizontal scaling یعنی افزودن node های بیشتر و توزیع load، که scalability و fault tolerance بهتری می‌دهد اما پیچیدگی‌هایی مثل data partitioning، consistency و network overhead به همراه دارد. برای سیستم‌های بزرگ production معمولاً horizontal scaling ترجیح داده می‌شود.

**سوال:** روند estimation ظرفیت (back-of-the-envelope: QPS، storage، bandwidth) برای یک سیستم جدید را توضیح دهید.
**پاسخ:** ابتدا تعداد کاربر فعال (DAU/MAU) و نسبت read/write را تخمین می‌زنیم، سپس QPS متوسط و peak را محاسبه می‌کنیم (مثلاً DAU × تعداد request در روز ÷ ثانیه‌های روز، ضربدر peak factor). برای storage، حجم هر رکورد را در تعداد رکوردهای روزانه/سالانه ضرب می‌کنیم و replication factor را اعمال می‌کنیم. برای bandwidth، اندازه متوسط response را در QPS ضرب می‌کنیم. این تخمین‌ها به انتخاب تعداد server، نوع database و c[^5]aching strategy کمک می‌کند.

**سوال:** CAP theorem چیست و چطور روی تصمیمات معماری واقعی تاثیر می‌گذارد؟
**پاسخ:** CAP theorem می‌گوید در یک distributed system، در صورت وقوع network partition نمی‌توان همزمان consistency و availability کامل داشت؛ باید یکی را فدای دیگری کرد (CP یا AP). مثلاً MongoDB و HBase بیشتر گرایش CP دارند، در حالی‌که Cassandra و DynamoDB بیشتر AP هستند. این انتخاب باید بر اساس نیاز business (مثلاً banking نیاز به CP دارد، social feed می‌تواند AP باشد) صورت گیرد.

**سوال:** تفاوت latency و throughput چیست و چطور سیستم را برای هرکدام بهینه می‌کنید؟
**پاسخ:** Latency یعنی زمان پاسخ به یک request واحد و throughput یعنی تعداد request هایی که در واحد زمان پردازش می‌شوند. برای کاهش latency از caching، CDN، connection pooling و کاهش hop های network استفاده می‌شود. برای افزایش throughput از horizontal scaling، asynchronous processing، batching و load balancing استفاده می‌شود. گاهی این دو در تضادند (مثلاً batching throughput را بالا می‌برد اما latency هر request را افزایش می‌دهد).

**سوال:** رویکرد شما در یک مصاحبه System Design از جمع‌آوری requirement تا high-level design و deep dive چیست؟
**پاسخ:** ابتدا requirement های functional و non-functional را روشن می‌کنم و سوال می‌پرسم، سپس capacity estimation انجام می‌دهم، بعد یک high-level design با اجزای اصلی (API، service ها، database، cache، queue) رسم می‌کنم، سپس روی data model و API contract تمرکز می‌کنم، و در نهایت به bottleneck ها و deep dive (مثل sharding، caching strategy، failure handling) می‌پردازم.

---

## ۲. Networking، API ها و ارتباطات

**سوال:** REST، gRPC و GraphQL را مقایسه کنید. برای یک Java-based service کدام را انتخاب می‌کنید؟
**پاسخ:** REST ساده، stateless و widely-adopted است و برای public API مناسب است. gRPC از HTTP/2 و Protocol Buffers استفاده می‌کند، latency پایین‌تر و type-safety بهتری دارد و برای internal service-to-service communication در microservice ها مناسب‌تر است. GraphQL به client اجازه می‌دهد دقیقاً داده مورد نیازش را query کند و برای client هایی با نیازهای متفاوت (mobile vs web) مفید است، اما caching و rate limiting آن پیچیده‌تر است. برای internal Java microservices معمولاً gRPC انتخاب می‌شود.

**سوال:** چطور یک API rate limiter طراحی می‌کنید؟ الگوریتم‌های token bucket، leaky bucket و sliding window را توضیح دهید.
**پاسخ:** Token bucket یک bucket با ظرفیت مشخص دارد که با نرخ ثابت token اضافه می‌کند؛ هر request یک token مصرف می‌کند و burst traffic را تا حدی اجازه می‌دهد. Leaky bucket، request ها را در یک queue با نرخ ثابت پردازش می‌کند و traffic را smooth می‌کند اما burst را اجازه نمی‌دهد. Sliding window با شمارش request ها در یک بازه زمانی متحرک، دقت بیشتری نسبت به fixed window دارد. در Java معمولاً با Redis و Lua script یا کتابخانه‌هایی مثل Resilience4j RateLimiter پیاده‌سازی می‌شود، به‌ویژه برای distributed rate limiting.

**سوال:** تفاوت synchronous و asynchronous communication چیست؟ در یک Java microservice چه زمانی هرکدام مناسب است؟
**پاسخ:** در synchronous، caller منتظر پاسخ می‌ماند (مثل REST call با blocking I/O)، مناسب برای عملیاتی که نیاز به پاسخ فوری دارند. در asynchronous، caller بدون انتظار ادامه می‌دهد و پاسخ بعداً از طریق callback، message queue یا event می‌رسد (مثل Kafka)، مناسب برای عملیات long-running یا decoupling سرویس‌ها. در Java می‌توان با CompletableFuture یا reactive stack (Project Reactor) asynchronous کدنویسی کرد.

**سوال:** چطور API versioning را در یک Java service طولانی‌مدت مدیریت می‌کنید؟
**پاسخ:** روش‌های رایج شامل URI versioning (مثل /v1/orders)، header-based versioning، و content negotiation هستند. باید backward compatibility حفظ شود، deprecated endpoint ها با مدت زمان مشخص اعلام شوند و از Semantic Versioning برای internal library ها استفاده شود. در Spring می‌توان با @RequestMapping های جداگانه یا API Gateway routing این کار را انجام داد.

**سوال:** Load balancer چطور کار می‌کند؟ تفاوت L4 و L7 load balancing چیست؟
**پاسخ:** Load balancer traffic ورودی را بین چند instance توزیع می‌کند تا از overload جلوگیری شود و availability بالا برود. L4 load balancing در سطح transport (TCP/UDP) کار می‌کند و فقط بر اساس IP و port تصمیم می‌گیرد، سریع‌تر است. L7 load balancing در سطح application (HTTP) کار می‌کند و می‌تواند بر اساس URL، header یا cookie routing کند، انعطاف بیشتری دارد اما overhead بیشتری هم دارد.

**سوال:** یک URL shortener (مثل bit.ly) طراحی کنید. چه data store، hashing scheme و caching strategy استفاده می‌کنید؟
**پاسخ:** برای تولید short code می‌توان از base62 encoding روی یک auto-increment ID یا از hashing (مثل MD5 با truncation و collision check) استفاده کرد. Data store می‌تواند یک key-value store مثل DynamoDB یا یک relational database با index روی short code باشد. برای caching، URL های پرترافیک در Redis نگه‌داری می‌شوند تا read latency کم شود. سیستم باید read-heavy design داشته باشد چون تعداد redirect بسیار بیشتر از تعداد create است.

---

## ۳. Database ها و Storage

**سوال:** SQL در مقابل NoSQL: چه فاکتورهایی انتخاب را تعیین می‌کنند و trade-off های consistency و schema flexibility چیست؟
**پاسخ:** SQL برای داده‌های structured با relationship پیچیده و نیاز به strong consistency و ACID transaction مناسب است (مثل سیستم مالی). NoSQL برای scalability بالا، schema flexibility و مدل‌های داده‌ای مثل document، key-value، wide-column مناسب است (مثل MongoDB، Cassandra). NoSQL معمولاً eventual consistency را می‌پذیرد تا availability و partition tolerance بهتری داشته باشد.

**سوال:** چطور schema و indexing strategy را برای یک سیستم high-write (مثل order management) با JDBC/JPA طراحی می‌کنید؟
**پاسخ:** برای high-write باید تعداد index ها را محدود کرد چون هر index خودش write overhead دارد. از composite index بر اساس pattern query استفاده می‌شود، جداول با partitioning (مثل partition بر اساس تاریخ) تقسیم می‌شوند، و از batch insert در JPA (`hibernate.jdbc.batch_size`) برای کاهش round-trip استفاده می‌شود. همچنین می‌توان write را از طریق یک write-optimized table انجام داد و بعد async به read model sync کرد (CQRS).

**سوال:** استراتژی‌های database sharding (range-based، hash-based، directory-based) را توضیح دهید. چطور با Java و connection pool مثل HikariCP این را پیاده می‌کنید؟
**پاسخ:** Range-based sharding داده را بر اساس بازه یک key تقسیم می‌کند (ساده اما ممکن است hotspot ایجاد کند). Hash-based sharding از hash کلید برای توزیع یکنواخت استفاده می‌کند اما resharding سخت‌تر است. Directory-based از یک lookup service برای mapping key به shard استفاده می‌کند و انعطاف بیشتری دارد. در Java معمولاً با یک routing layer یا ShardingSphere و چند DataSource جداگانه (هرکدام با HikariCP pool خودش) این کار پیاده می‌شود.

**سوال:** N+1 query problem در JPA/Hibernate چیست و چطور حل می‌شود؟
**پاسخ:** وقتی یک entity با لیستی از entity های وابسته lazy-load می‌شود، برای هر آیتم لیست یک query جداگانه اجرا می‌شود که منجر به N+1 query می‌گردد. راه‌حل‌ها شامل استفاده از JOIN FETCH در JPQL، @EntityGraph، یا batch fetching (`hibernate.default_batch_fetch_size`) است.

**سوال:** چطور یک سیستم برای database failover و replication (leader-follower) طراحی می‌کنید؟
**پاسخ:** یک leader نوشتن‌ها را می‌پذیرد و follower ها به‌صورت async یا sync آن‌ها را replicate می‌کنند. برای failover از یک health check و leader election (مثلاً با Zookeeper یا ابزارهایی مثل Patroni برای PostgreSQL) استفاده می‌شود تا در صورت خرابی leader، یکی از follower ها promote شود. باید read/write splitting در application layer (مثلاً با Spring's `@Transactional(readOnly=true)` routing به replica) در نظر گرفته شود.

**سوال:** تفاوت optimistic locking و pessimistic locking چیست و چطور هرکدام را با JPA پیاده‌سازی می‌کنید (`@Version`، `SELECT FOR UPDATE`)؟
**پاسخ:** Optimistic locking فرض می‌کند conflict نادر است؛ با یک ستون `@Version` تغییرات هم‌زمان تشخیص داده می‌شوند و در صورت conflict یک `OptimisticLockException` پرتاب می‌شود. Pessimistic locking از ابتدا رکورد را با `SELECT ... FOR UPDATE` قفل می‌کند تا هیچ transaction دیگری نتواند آن را تغییر دهد، مناسب برای high-contention scenario ها اما throughput را کاهش می‌دهد.

**سوال:** یک distributed transaction بین چند microservice با database های جداگانه طراحی کنید (Saga pattern در مقابل 2PC).
**پاسخ:** Two-Phase Commit (2PC) یک consistency قوی می‌دهد اما blocking است و در microservice های مقیاس بزرگ scalability کمی دارد. Saga pattern یک sequence از local transaction هاست که هرکدام یک event منتشر می‌کنند؛ در صورت شکست، compensating transaction ها اجرا می‌شوند (choreography-based با Kafka یا orchestration-based با یک saga orchestrator). Saga معمولاً برای microservices ترجیح داده می‌شود چون loosely-coupled و scalable است.

---

## ۴. Caching

**سوال:** یک caching layer برای یک Java service با read زیاد طراحی کنید. local caching (Caffeine، Guava) را با distributed caching (Redis، Memcached) مقایسه کنید.
**پاسخ:** Local cache در heap همان JVM قرار دارد، latency بسیار پایینی دارد اما بین instance ها sync نیست و در horizontal scaling می‌تواند inconsistency ایجاد کند. Distributed cache (Redis) بین همه instance ها به اشتراک گذاشته می‌شود و consistency بهتری دارد اما یک network hop اضافه می‌کند. راه‌حل رایج ترکیبی است: یک local cache (Caffeine) به‌عنوان L1 برای hot data و Redis به‌عنوان L2.

**سوال:** eviction policy های cache (LRU، LFU، FIFO) را توضیح دهید و بگویید کی هرکدام را انتخاب می‌کنید.
**پاسخ:** LRU (Least Recently Used) آیتم‌هایی که اخیراً استفاده نشده‌اند را حذف می‌کند، مناسب اکثر use case ها. LFU (Least Frequently Used) بر اساس تعداد دفعات استفاده حذف می‌کند، مناسب زمانی که popularity پایدار است. FIFO ساده‌ترین است و بدون توجه به usage pattern، قدیمی‌ترین آیتم را حذف می‌کند، مناسب زمانی که پیچیدگی کمتر مهم‌تر از دقت است.

**سوال:** استراتژی‌های cache-aside، write-through و write-behind چیست؟ trade-off هرکدام؟
**پاسخ:** Cache-aside یعنی application ابتدا cache را چک می‌کند و در صورت miss، از database می‌خواند و cache را update می‌کند؛ ساده اما ممکن است stale data داشته باشد. Write-through یعنی هر write هم‌زمان به cache و database نوشته می‌شود، consistency بهتر اما write latency بیشتر. Write-behind یعنی write ابتدا در cache انجام و بعداً async به database نوشته می‌شود، throughput بالا اما ریسک data loss در صورت crash.

**سوال:** چطور از cache stampede / thundering herd جلوگیری می‌کنید؟
**پاسخ:** با استفاده از تکنیک‌هایی مثل lock/mutex (فقط یک thread اجازه دارد cache را از database پر کند و بقیه منتظر بمانند)، probabilistic early expiration، یا stale-while-revalidate (سرو کردن داده قدیمی هنگام refresh در پس‌زمینه).

**سوال:** چطور distributed cache را با source-of-truth database consistent نگه می‌دارید؟
**پاسخ:** با TTL مناسب برای expire خودکار، invalidation event ها (مثلاً از طریق Kafka وقتی رکورد database تغییر می‌کند) و write-through pattern. برای consistency قوی‌تر می‌توان از event-driven cache invalidation یا Change Data Capture (CDC) با ابزارهایی مثل Debezium استفاده کرد.

---

## ۵. Messaging، Queue ها و پردازش Asynchronous

**سوال:** چه زمانی از message queue (Kafka، RabbitMQ، SQS) به‌جای فراخوانی مستقیم synchronous بین سرویس‌ها استفاده می‌کنید؟
**پاسخ:** زمانی که نیاز به decoupling سرویس‌ها، مدیریت traffic spike، پردازش asynchronous یا اطمینان از تحویل پیام (حتی اگر consumer موقتاً down باشد) وجود دارد. مثلاً پردازش سفارش، ارسال notification، یا event-driven workflow ها.

**سوال:** معماری Kafka را توضیح دهید: partition ها، consumer group ها، offset ها و replication. این‌ها چطور روی ordering guarantee تاثیر می‌گذارند؟
**پاسخ:** یک topic به چند partition تقسیم می‌شود و هر پیام یک offset منحصربه‌فرد در آن partition دارد. Ordering فقط درون یک partition تضمین می‌شود، نه در کل topic. Consumer group به چند consumer اجازه می‌دهد partition ها را بین خودشان تقسیم کنند تا parallel processing انجام شود. Replication (با replication factor) کپی‌هایی از هر partition روی broker های مختلف نگه می‌دارد تا در صورت خرابی یک broker، داده از دست نرود.

**سوال:** یک سیستم event-driven پردازش سفارش با Kafka و Java producer/consumer طراحی کنید. چطور exactly-once را از at-least-once delivery متمایز می‌کنید؟
**پاسخ:** At-least-once یعنی ممکن است یک پیام بیش از یک بار پردازش شود (نیازمند idempotent consumer). Exactly-once semantics در Kafka با idempotent producer و transactional API (`transactional.id`) قابل دستیابی است که تضمین می‌کند هر پیام دقیقاً یک بار در طول pipeline پردازش شود. طراحی باید شامل یک idempotency key (مثل order ID) در database برای جلوگیری از پردازش تکراری باشد.

**سوال:** چطور poison message ها / dead-letter queue را در یک Java consumer مدیریت می‌کنید؟
**پاسخ:** پیام‌هایی که بارها پردازش‌شان با خطا مواجه می‌شود (مثلاً بعد از retry با exponential backoff)، به یک Dead Letter Topic/Queue منتقل می‌شوند تا از blocking شدن کل consumer جلوگیری شود. سپس این پیام‌ها می‌توانند به‌صورت جداگانه بررسی یا reprocess شوند.

**سوال:** backpressure چیست و چطور آن را در یک سیستم reactive Java (Project Reactor، RxJava) پیاده‌سازی می‌کنید؟
**پاسخ:** Backpressure مکانیزمی است که به consumer اجازه می‌دهد نرخ داده دریافتی از producer را کنترل کند تا از overwhelm شدن جلوگیری شود. در Project Reactor با Reactive Streams API، subscriber می‌تواند از طریق `request(n)` تعداد item مورد نیاز را اعلام کند و همچنین با عملگرهایی مثل `onBackpressureBuffer` یا `onBackpressureDrop` رفتار در صورت overflow را کنترل کرد.

---

## ۶. معماری Microservices

**سوال:** trade-off های حرکت از monolith به microservices چیست؟ کی monolith هنوز انتخاب درستی است؟
**پاسخ:** Microservices استقلال deploy، scalability مستقل هر سرویس و انتخاب technology stack متنوع را ممکن می‌کند، اما پیچیدگی operational (monitoring، distributed tracing، network reliability) بالا می‌رود. Monolith برای تیم‌های کوچک، MVP یا زمانی که domain boundary هنوز واضح نیست بهتر است چون development و deployment ساده‌تری دارد.

**سوال:** چطور service discovery و configuration management را مدیریت می‌کنید (Eureka، Consul، Spring Cloud Config)؟
**پاسخ:** Service discovery (مثل Eureka یا Consul) به سرویس‌ها اجازه می‌دهد به‌صورت dynamic یکدیگر را در network پیدا کنند بدون hardcode کردن IP. Spring Cloud Config یک central configuration server فراهم می‌کند که تنظیمات را بدون نیاز به redeploy، به سرویس‌ها می‌دهد (با پشتیبانی از refresh dynamic).

**سوال:** یک زنجیره فراخوانی microservice مقاوم با circuit breaker، retry و timeout طراحی کنید (Resilience4j / Hystrix).
**پاسخ:** Circuit breaker وقتی نرخ خطای یک سرویس از یک threshold عبور کند، به‌صورت موقت فراخوانی‌ها را قطع می‌کند تا سرویس downstream فرصت بهبود پیدا کند (fail-fast). Retry با exponential backoff برای خطاهای گذرا استفاده می‌شود. Timeout جلوی block شدن نامحدود thread ها را می‌گیرد. در Java، Resilience4j این pattern ها را به‌صورت annotation-based یا functional فراهم می‌کند.

**سوال:** چطور یک راه‌حل distributed logging و tracing بین microservice ها طراحی می‌کنید (correlation ID، OpenTelemetry، Zipkin/Jaeger)؟
**پاسخ:** یک correlation ID (trace ID) در ابتدای هر request تولید و در تمام header های بین‌سرویسی propagate می‌شود. OpenTelemetry SDK این trace ها را جمع‌آوری و به backend هایی مثل Jaeger یا Zipkin ارسال می‌کند تا کل مسیر یک request بین سرویس‌ها قابل visualize باشد. لاگ‌ها هم باید این correlation ID را شامل شوند تا در ELK قابل جستجو باشند.

**سوال:** API Gateway pattern و مسئولیت‌های آن (auth، rate limiting، routing) را توضیح دهید.
**پاسخ:** API Gateway یک entry point واحد برای همه client ها فراهم می‌کند که مسئولیت‌هایی مثل authentication/authorization، rate limiting، request routing به سرویس مناسب، response aggregation و SSL termination را متمرکز می‌کند تا این logic از خود microservice ها جدا شود.

**سوال:** چطور shared data model ها را مدیریت می‌کنید و از coupling شدید بین microservice ها جلوگیری می‌کنید؟
**پاسخ:** هر سرویس باید database و data model خودش را داشته باشد (database-per-service). به‌جای share کردن مستقیم entity، از یک contract مشخص (API یا event schema) و ابزارهایی مثل Avro/Protobuf با schema registry استفاده می‌شود تا تغییرات backward-compatible باشند.

**سوال:** یک سیستم notification (email/SMS/push) طراحی کنید که در چند microservice مقیاس‌پذیر باشد.
**پاسخ:** سرویس‌های دیگر یک event (مثل OrderPlaced) به یک topic در Kafka منتشر می‌کنند. یک Notification Service این event ها را consume کرده و بر اساس نوع notification (email/SMS/push) به provider مربوطه (SendGrid، Twilio، FCM) ارسال می‌کند. برای reliability از retry، dead-letter queue و idempotency استفاده می‌شود.

---

## ۷. مفاهیم Distributed Systems

**سوال:** eventual consistency در مقابل strong consistency را با مثال واقعی توضیح دهید.
**پاسخ:** Strong consistency یعنی بلافاصله بعد از یک write، همه read ها آخرین مقدار را می‌بینند (مثل transaction بانکی). Eventual consistency یعنی ممکن است برای مدتی replica های مختلف مقادیر متفاوتی نشان دهند اما در نهایت همگرا می‌شوند (مثل like count در شبکه اجتماعی که تاخیر کوتاهی در sync شدن قابل قبول است).

**سوال:** الگوریتم consensus توزیع‌شده (Raft، Paxos) چیست؟ کجا در عمل استفاده می‌شود (مثلاً Zookeeper، etcd)؟
**پاسخ:** این الگوریتم‌ها به مجموعه‌ای از node ها اجازه می‌دهند حتی در حضور خرابی یا network partition، روی یک مقدار واحد توافق کنند. Raft ساده‌تر از Paxos برای فهمیدن است و در سیستم‌هایی مثل etcd و Consul استفاده می‌شود. Zookeeper از یک الگوریتم مشابه (ZAB) برای leader election و coordination استفاده می‌کند.

**سوال:** چطور یک distributed lock در Java پیاده‌سازی می‌کنید (با Redis، Zookeeper یا روش‌های database-based)؟
**پاسخ:** با Redis می‌توان از دستور `SET key value NX PX ttl` برای گرفتن lock با expiration استفاده کرد (الگوریتم Redlock برای reliability بیشتر). با Zookeeper از ephemeral sequential node ها برای پیاده‌سازی lock استفاده می‌شود. Database-based lock هم می‌تواند با یک unique constraint یا `SELECT FOR UPDATE` پیاده‌سازی شود، اما معمولاً throughput پایین‌تری دارد.

**سوال:** idempotency چیست و چطور API/consumer های idempotent در یک distributed system طراحی می‌کنید؟
**پاسخ:** Idempotency یعنی اجرای چندباره یک عملیات، همان نتیجه یک بار اجرا را دارد. برای API ها معمولاً از یک idempotency key که client ارسال می‌کند استفاده می‌شود و server نتیجه اولین request را cache می‌کند. برای consumer ها، قبل از پردازش پیام، وجود آن در یک جدول processed_messages چک می‌شود یا از upsert به‌جای insert استفاده می‌شود.

**سوال:** مفهوم leader election چیست و چطور برای coordination استفاده می‌شود (مثلاً leader election مبتنی بر Zookeeper در Java)؟
**پاسخ:** Leader election فرآیندی است که در آن یک node از میان چند node به‌عنوان leader انتخاب می‌شود تا عملیات هماهنگ‌سازی‌شده (مثل scheduling یک job) را انجام دهد. در Zookeeper این کار با ایجاد ephemeral sequential znode ها انجام می‌شود؛ node ای که کمترین sequence number را دارد leader می‌شود و در صورت disconnect شدن leader، znode آن حذف و node بعدی leader می‌شود.

**سوال:** چطور clock skew و ordering رویدادها بین node های توزیع‌شده را مدیریت می‌کنید (vector clock، logical clock)؟
**پاسخ:** به‌جای تکیه بر physical clock (که بین node ها sync نیست)، از logical clock مثل Lamport timestamp برای ترتیب نسبی event ها استفاده می‌شود. Vector clock پیشرفته‌تر است و می‌تواند causality بین event ها را در سیستم‌های چند-node تشخیص دهد (که event A قبل از B رخ داده یا concurrent هستند).

---

## ۸. Reliability، Scalability و Observability

**سوال:** یک سیستم با availability ۹۹.۹۹٪ طراحی کنید. چه استراتژی‌های redundancy و failover اعمال می‌کنید؟
**پاسخ:** استفاده از multiple availability zone یا region، redundant instance پشت load balancer، health check و auto-scaling، database replication با automatic failover، و circuit breaker برای جلوگیری از cascading failure. باید همچنین یک disaster recovery plan و chaos engineering (مثل Chaos Monkey) برای تست resilience وجود داشته باشد.

**سوال:** چطور health check و readiness/liveness probe را برای یک Java service در Kubernetes طراحی می‌کنید؟
**پاسخ:** Liveness probe مشخص می‌کند آیا application هنوز زنده است یا باید restart شود (مثلاً endpoint `/actuator/health/liveness` در Spring Boot). Readiness probe مشخص می‌کند آیا application آماده دریافت traffic است (مثلاً بعد از اتصال موفق به database و cache)، و Kubernetes تا زمانی که readiness موفق نباشد traffic ارسال نمی‌کند.

**سوال:** bulkhead pattern را توضیح دهید و بگویید چطور از cascading failure جلوگیری می‌کند.
**پاسخ:** Bulkhead pattern منابع (مثل thread pool یا connection pool) را بین بخش‌های مختلف سیستم isolate می‌کند تا اگر یک بخش دچار overload یا failure شود، بقیه بخش‌ها تحت تاثیر قرار نگیرند؛ شبیه به دیواره‌های ضدآب یک کشتی.

**سوال:** یک سیستم monitoring و alerting برای مجموعه‌ای از Java microservices با Prometheus/Grafana و ELK stack طراحی کنید.
**پاسخ:** هر سرویس metric های خودش (latency، error rate، throughput) را از طریق Micrometer در قالب Prometheus expose می‌کند. Prometheus این metric ها را scrape و ذخیره می‌کند و Grafana برای visualization و alerting استفاده می‌شود. برای log ها، هر سرویس log های structured (JSON) با correlation ID تولید می‌کند که توسط Filebeat/Logstash جمع‌آوری و در Elasticsearch ایندکس شده و در Kibana قابل جستجو می‌شود.

**سوال:** چطور capacity planning و load testing را برای یک Java service قبل از یک رویداد ترافیک بزرگ انجام می‌دهید؟
**پاسخ:** با ابزارهایی مثل JMeter، Gatling یا k6، traffic pattern واقعی شبیه‌سازی می‌شود، bottleneck ها (CPU، DB connection، GC pause) شناسایی و tuning می‌شوند، و بر اساس نتایج، تعداد instance و auto-scaling threshold تنظیم می‌شود.

**سوال:** یک استراتژی graceful degradation برای یک سرویس تحت فشار زیاد طراحی کنید.
**پاسخ:** می‌توان features غیرضروری (مثل recommendation یا analytics) را موقتاً غیرفعال کرد، از cached یا stale data به‌جای real-time data استفاده کرد، یا با load shedding، درخواست‌های کم‌اولویت را رد کرد تا core functionality در دسترس بماند.

---

## ۹. امنیت (Security)

**سوال:** چطور authentication و authorization را برای یک معماری microservices طراحی می‌کنید (OAuth2، JWT، API key)؟
**پاسخ:** یک Identity Provider (مثل Keycloak یا Auth0) با پروتکل OAuth2/OIDC، token صادر می‌کند. JWT به‌عنوان access token بین سرویس‌ها propagate می‌شود و هر سرویس می‌تواند آن را بدون round-trip اضافه به identity provider، verify کند (با public key). API Gateway معمولاً authentication اولیه را انجام می‌دهد و authorization دقیق‌تر (role/scope-based) در خود سرویس‌ها بررسی می‌شود.

**سوال:** چطور ارتباط service-to-service در microservice های Java را امن می‌کنید (mTLS)؟
**پاسخ:** با mutual TLS، هر دو طرف (client و server) certificate خود را ارائه می‌دهند و یکدیگر را احراز هویت می‌کنند. در Kubernetes معمولاً این کار با یک service mesh مثل Istio یا Linkerd به‌صورت خودکار (بدون تغییر کد Java) انجام می‌شود.

**سوال:** چطور از vulnerability های رایج (SQL injection، XSS، CSRF) در یک اپلیکیشن Spring-based جلوگیری می‌کنید؟
**پاسخ:** برای SQL injection از prepared statement و JPA/Hibernate (به‌جای string concatenation) استفاده می‌شود. برای XSS، ورودی و خروجی sanitize و encode می‌شود (مثلاً Thymeleaf به‌صورت پیش‌فرض auto-escape می‌کند). برای CSRF، Spring Security از CSRF token برای state-changing request ها استفاده می‌کند.

**سوال:** یک روش امن برای مدیریت secrets (API key، DB credential) طراحی کنید (Vault، AWS Secrets Manager).
**پاسخ:** Secrets هرگز نباید در کد یا config file plaintext ذخیره شوند. از ابزارهایی مثل HashiCorp Vault یا AWS Secrets Manager استفاده می‌شود که secrets را encrypted نگه می‌دارند و application در runtime آن‌ها را با یک short-lived token دریافت می‌کند، همراه با rotation دوره‌ای.

---

## ۱۰. موضوعات تخصصی Java (Concurrency، JVM و Performance)

**سوال:** Java Memory Model (JMM) را توضیح دهید و بگویید `volatile`، `synchronized` و `final` چطور با آن تعامل دارند.
**پاسخ:** JMM قوانینی تعریف می‌کند که مشخص می‌کند تغییرات یک thread چه زمانی برای thread های دیگر قابل مشاهده است. `volatile` تضمین می‌کند خواندن/نوشتن مستقیم به main memory انجام شود (visibility) اما atomicity کامپوند عملیات را تضمین نمی‌کند. `synchronized` هم visibility و هم mutual exclusion (atomicity) فراهم می‌کند از طریق monitor lock. `final` field ها اگر درست initialize شوند، بعد از constructor بدون نیاز به synchronization اضافی برای thread های دیگر safely visible هستند.

**سوال:** `ExecutorService`، `CompletableFuture` و virtual thread (Project Loom) را برای مدیریت concurrent workload مقایسه کنید.
**پاسخ:** `ExecutorService` یک thread pool سنتی است که task ها را روی تعداد ثابتی platform thread اجرا می‌کند؛ برای workload های blocking I/O زیاد می‌تواند منجر به thread starvation شود. `CompletableFuture` امکان composition غیرهمزمان (chaining، combining) عملیات را فراهم می‌کند. Virtual thread (Project Loom) thread های سبک JVM-managed هستند که امکان ایجاد میلیون‌ها thread بدون overhead سنگین OS thread را می‌دهند، بسیار مناسب برای workload های I/O-bound با کد blocking-style ساده.

**سوال:** یک سیستم producer-consumer با throughput بالا و thread-safe در Java طراحی کنید.
**پاسخ:** با استفاده از `BlockingQueue` (مثل `LinkedBlockingQueue` یا `ArrayBlockingQueue`) که به‌صورت داخلی thread-safe است، چند producer thread داده تولید کرده و در queue قرار می‌دهند و چند consumer thread از queue مصرف می‌کنند. برای throughput بالاتر می‌توان از `Disruptor` (LMAX) که یک ring buffer با کمترین contention است استفاده کرد.

**سوال:** استراتژی‌های garbage collection در JVM (G1، ZGC، Shenandoah) را بررسی کنید. چطور GC را برای یک سرویس low-latency tune می‌کنید؟
**پاسخ:** G1 GC برای اکثر application ها با heap متوسط تا بزرگ مناسب است و pause time هدفمند دارد. ZGC و Shenandoah برای heap های خیلی بزرگ و latency پایین (زیر ۱۰ میلی‌ثانیه pause) طراحی شده‌اند چون بیشتر کار GC را concurrent با application انجام می‌دهند. Tuning شامل تنظیم heap size مناسب، انتخاب collector مناسب، و monitoring GC log ها برای شناسایی pause های طولانی است.

**سوال:** چطور یک memory leak یا thread contention issue را در یک Java application در production تشخیص و رفع می‌کنید؟
**پاسخ:** با ابزارهایی مثل heap dump (`jmap`) و تحلیل با Eclipse MAT، می‌توان object هایی که به‌اشتباه reference نگه‌داشته‌اند (مثل static collection که پاک نمی‌شود) را پیدا کرد. برای thread contention، از thread dump (`jstack`) یا profiler هایی مثل async-profiler برای شناسایی lock contention و hot method ها استفاده می‌شود.

**سوال:** تفاوت `ConcurrentHashMap`، `synchronizedMap` و `CopyOnWriteArrayList` چیست و کی هرکدام را استفاده می‌کنید؟
**پاسخ:** `ConcurrentHashMap` از segment-based (یا bucket-based در نسخه‌های جدید) locking برای concurrency بالا با read بدون lock استفاده می‌کند، مناسب read/write زیاد. `synchronizedMap` کل map را با یک lock واحد محافظت می‌کند که throughput پایین‌تری در concurrency بالا دارد. `CopyOnWriteArrayList` هر write یک کپی جدید از آرایه می‌سازد؛ مناسب برای read بسیار زیاد و write نادر (مثل لیست listener ها).

**سوال:** چطور یک connection pool را از صفر طراحی می‌کنید و از چه concurrency primitive هایی استفاده می‌کنید؟
**پاسخ:** یک pool از connection های از پیش‌ساخته در یک `BlockingQueue` نگه‌داری می‌شود. وقتی client یک connection درخواست می‌کند، از queue برداشته می‌شود (با timeout در صورت خالی بودن) و بعد از استفاده به queue برگردانده می‌شود. باید health check دوره‌ای برای connection های idle/stale، حداکثر و حداقل pool size، و مدیریت leak (اگر connection برنگردد) در نظر گرفته شود.

---

## ۱۱. مطالعات موردی End-to-End System Design

**سوال:** یک URL shortener (bit.ly) طراحی کنید — از API تا storage تا caching.
**پاسخ:** جزئیات کامل در بخش ۲ (Networking) آمده است: base62 encoding برای short code، key-value store برای mapping، Redis برای cache کردن URL های پرترافیک، و طراحی read-heavy با replica های متعدد برای redirect service.

**سوال:** یک distributed rate limiter طراحی کنید که بین چند instance از API gateway استفاده شود.
**پاسخ:** به‌جای نگه‌داری counter در حافظه هر instance (که باعث inconsistency می‌شود)، از یک central store مثل Redis با اتمی بودن عملیات (`INCR` + `EXPIRE` یا Lua script برای token bucket) استفاده می‌شود تا همه instance ها یک counter مشترک را ببینند. برای کاهش latency می‌توان از local cache با sync دوره‌ای هم استفاده کرد (trade-off بین دقت و performance).

**سوال:** یک سیستم پردازش سفارش e-commerce با مدیریت موجودی (inventory) مقیاس‌پذیر طراحی کنید.
**پاسخ:** یک Order Service سفارش را دریافت و یک event (OrderCreated) منتشر می‌کند. Inventory Service موجودی را با یک عملیات atomic (مثل optimistic locking یا Redis `DECR`) کاهش می‌دهد. اگر موجودی کافی نبود، یک compensating event (OrderFailed) منتشر می‌شود (Saga pattern). Payment Service به‌صورت جداگانه پرداخت را پردازش می‌کند و در صورت شکست، inventory دوباره افزایش می‌یابد.

**سوال:** یک اپلیکیشن chat بلادرنگ (real-time) طراحی کنید (WebSocket، ordering پیام‌ها، delivery guarantee).
**پاسخ:** از WebSocket برای ارتباط دوطرفه real-time بین client و server استفاده می‌شود. برای scale کردن به چند instance، از یک message broker (مثل Redis Pub/Sub یا Kafka) برای route کردن پیام بین instance های مختلف server استفاده می‌شود. Ordering با یک sequence number per-conversation تضمین می‌شود و delivery guarantee با ack از سمت client و ذخیره پیام‌های unread در database تامین می‌شود.

**سوال:** یک سیستم news feed / social media timeline طراحی کنید (fan-out on write در مقابل fan-out on read).
**پاسخ:** Fan-out on write یعنی هنگام post کردن، پیام بلافاصله به timeline همه follower ها نوشته می‌شود؛ سریع برای read اما برای کاربران با میلیون‌ها follower (celebrity) پرهزینه است. Fan-out on read یعنی timeline در لحظه request از posts افرادی که follow شده‌اند aggregate می‌شود؛ برای write سبک‌تر اما read کندتر است. راه‌حل رایج ترکیبی است: fan-out on write برای اکثر کاربران و fan-out on read برای celebrity ها.

**سوال:** یک distributed job scheduler (مثل Quartz در مقیاس بزرگ) طراحی کنید که از duplicate execution یک job جلوگیری کند.
**پاسخ:** از یک distributed lock (مثل Zookeeper یا Redis) قبل از اجرای هر job استفاده می‌شود تا فقط یک instance آن را اجرا کند. Job های scheduled در یک database مرکزی با وضعیت (pending/running/completed) نگه‌داری می‌شوند و یک leader election بین scheduler instance ها مشخص می‌کند کدام instance مسئول trigger کردن job هاست.

**سوال:** یک سیستم پردازش پرداخت با نیازمندی‌های consistency قوی و auditability طراحی کنید.
**پاسخ:** از ACID transaction در سطح database برای عملیات‌های critical مثل کسر موجودی استفاده می‌شود. هر تراکنش یک idempotency key دارد تا از double-charging جلوگیری شود. یک audit log (event sourcing) تمام تغییرات state را immutable ذخیره می‌کند تا قابلیت traceability و reconciliation کامل وجود داشته باشد. برای ارتباط با payment gateway خارجی از retry با idempotency و reconciliation job دوره‌ای استفاده می‌شود.

**سوال:** یک سرویس file storage/upload (مثل Dropbox) با chunking، dedup و مدیریت metadata طراحی کنید.
**پاسخ:** فایل‌های بزرگ به chunk های کوچک‌تر تقسیم می‌شوند تا آپلود resume-able و parallel باشد. هر chunk یک hash (مثل SHA-256) دارد که برای deduplication استفاده می‌شود (اگر chunk قبلاً وجود دارد، دوباره ذخیره نمی‌شود). Metadata (نام فایل، owner، لیست chunk ها) در یک database جداگانه نگه‌داری می‌شود در حالی‌که خود chunk ها در یک object storage مثل S3 ذخیره می‌شوند.

---

### نحوه استفاده از این راهنما
- **برای مصاحبه‌کننده‌ها:** بسته به focus نقش (مثلاً تمرکز بیشتر روی messaging/microservices برای نقش backend platform، یا تمرکز روی JVM internals برای نقش performance-critical)، از هر دسته ۱ تا ۲ سوال انتخاب کنید.
- **برای کاندیدها:** از هر دسته به‌عنوان یک checklist مطالعه استفاده کنید — آماده باشید حداقل یک case study را کامل روی whiteboard طراحی کنید و trade-off هر تصمیم را توجیه کنید.

---

# ۱۲. Deep Diveهای ضروری برای سطح Senior

## ۱۲.۱ CAP را دقیق‌تر از پاسخ حفظی توضیح دهید

CAP نمی‌گوید همیشه فقط دو مورد از سه مورد C/A/P را انتخاب می‌کنیم. در یک distributed system واقعی، network partition اجتناب‌ناپذیر است؛ **هنگام partition** باید تصمیم بگیریم آیا request را reject/timeout کنیم تا consistency حفظ شود (CP)، یا پاسخ بدهیم و احتمال stale/conflicting data را بپذیریم (AP).

```text
Normal:
Client -> Node A <------> Node B

Network partition:
Client -> Node A    X    Node B <- Client
             |             |
        accept write?   accept write?
```

برای payment ledger معمولاً duplicate debit یا conflicting balance قابل قبول نیست؛ برای like count یا feed، چند ثانیه stale بودن اغلب قابل قبول است. حتی در یک محصول، تصمیم consistency می‌تواند **per operation** متفاوت باشد.

**Follow-up محتمل:** آیا PostgreSQL را می‌توان صرفاً CP نامید؟  
پاسخ بهتر: CAP property فقط نام database نیست؛ topology، replication mode، failover policy و رفتار application هنگام partition تعیین‌کننده‌اند.

## ۱۲.۲ Consistency، transaction و idempotency در پرداخت

سناریو: Client درخواست پرداخت می‌فرستد، Payment Service پول را نزد provider کم می‌کند، اما قبل از ارسال response crash می‌کند. Client timeout می‌گیرد و retry می‌کند.

اگر idempotency نداشته باشیم، ممکن است double charge رخ دهد.

```text
Client
  |
  | POST /payments
  | Idempotency-Key: abc-123
  v
[Payment Service]
  |
  +--> [Idempotency Store]
  |       abc-123 -> PROCESSING/SUCCEEDED
  |
  +--> [Payment Provider]
  |
  +--> [Ledger DB]
```

الگوی مناسب:
1. idempotency key با unique constraint ذخیره شود.
2. درخواست تکراری نتیجه قبلی را برگرداند.
3. ledger entries immutable باشند.
4. status transitionها کنترل شوند.
5. interaction خارجی reconciliation شود.
6. event publication با Transactional Outbox قابل اتکاتر شود.

### Transactional Outbox

مشکل dual-write:

```text
DB commit succeeds
       |
       X  application crashes
       |
Kafka publish never happens
```

راه‌حل:

```text
BEGIN TX
  update orders
  insert into outbox
COMMIT

[CDC / Outbox Publisher] ---> Kafka
```

Database state و outbox record در یک local transaction نوشته می‌شوند. Publisher ممکن است event را بیش از یک بار منتشر کند؛ بنابراین consumer همچنان باید idempotent باشد.

## ۱۲.۳ Kafka: چیزی که در مصاحبه Senior باید بگویید

```text
Topic: order-events

Partition 0: [O1][O4][O8] ---> Consumer A
Partition 1: [O2][O5]     ---> Consumer B
Partition 2: [O3][O6][O7] ---> Consumer C

             Consumer Group: inventory-service
```

- Ordering در سطح partition است.
- key مناسب، مثل `orderId`، eventهای یک aggregate را به یک partition می‌فرستد.
- تعداد active consumerهای یک group نمی‌تواند برای یک topic از تعداد partitionها بیشتر شود.
- at-least-once یعنی duplicate processing ممکن است.
- offset را زمانی commit کنید که processing مورد نظر safely انجام شده باشد.
- retry بی‌نهایت روی همان partition می‌تواند processing را متوقف کند؛ retry topic/DLT یک گزینه است.
- «Kafka exactly once» به معنی exactly-once شدن arbitrary side effect در database خارجی نیست.

### Idempotent consumer

```text
eventId = e-1007

BEGIN TX
  INSERT processed_event(event_id) VALUES ('e-1007')
  -- unique constraint
  UPDATE inventory ...
COMMIT
```

اگر insert به علت duplicate key شکست خورد، event قبلاً پردازش شده است.

## ۱۲.۴ Cache consistency و failure modes

Cache-aside:

```text
Request
  |
  v
[Service] --GET--> [Redis]
   | miss
   v
[Database]
   |
   +---- result ----> Redis (TTL)
   |
   +---- response --> Client
```

برای update معمولاً:

```text
UPDATE DB
   |
   v
DELETE cache key
```

چرا invalidate معمولاً از «update کردن cache» ساده‌تر است؟ چون database source of truth باقی می‌ماند و raceهای کمتری ایجاد می‌شود.

Failureهای مهم:
- cache stampede
- hot key
- stale data
- Redis outage
- cache penetration برای keyهای ناموجود
- memory pressure/eviction

راهکارها: TTL jitter، request coalescing/single-flight، negative caching، replication، local fallback و rate limiting.

## ۱۲.۵ Database indexing با مثال

فرض کنید query پرتکرار:

```sql
SELECT id, customer_id, status, created_at
FROM orders
WHERE customer_id = ?
  AND status = ?
ORDER BY created_at DESC
LIMIT 50;
```

Index محتمل:

```sql
CREATE INDEX idx_orders_customer_status_created
ON orders(customer_id, status, created_at DESC);
```

اما index رایگان نیست: storage می‌گیرد و INSERT/UPDATE را کندتر می‌کند. Senior answer باید از query pattern شروع شود، نه اینکه «روی همه ستون‌ها index می‌زنیم».

در production با `EXPLAIN (ANALYZE, BUFFERS)`، cardinality/selectivity، statistics، lockها، I/O و slow-query metrics تصمیم را validate می‌کنیم.

## ۱۲.۶ Optimistic locking در Java/JPA

```java
@Entity
class Account {
    @Id
    private Long id;

    @Version
    private long version;

    private BigDecimal balance;
}
```

دو transaction یک version را می‌خوانند. اولین commit، version را تغییر می‌دهد؛ commit دوم update count صفر می‌گیرد و Hibernate conflict را گزارش می‌کند.

این روش برای contention پایین مناسب است. برای contention بالا، retry زیاد می‌تواند بدتر از locking/serialization مناسب باشد. در money movement صرفاً تغییر یک `balance` کافی نیست؛ ledger/audit و invariantهای مالی نیز مهم‌اند.

## ۱۲.۷ Timeout، Retry و Circuit Breaker

```text
Order Service
    |
    | timeout = 300ms
    v
Payment Service
    |
    X slow/down

Without protection:
threads wait -> pool exhausted -> cascading failure
```

ترتیب فکری مناسب:
- timeout محدود؛
- retry فقط برای failureهای transient و operationهای safe/idempotent؛
- exponential backoff + jitter؛
- circuit breaker برای fail-fast؛
- bulkhead برای resource isolation؛
- fallback فقط اگر از نظر business درست باشد.

**اشتباه رایج:** retry در چند layer. سه retry در gateway × سه retry در service × سه retry در client می‌تواند یک failure را به ده‌ها call تبدیل کند.

## ۱۲.۸ Thread pool و connection pool sizing

اگر request threadها 200 باشند ولی DB pool فقط 20 connection داشته باشد، افزایش thread لزوماً throughput را بالا نمی‌برد:

```text
200 request threads
       |
       v
 [20 DB connections]
       |
       v
   PostgreSQL
```

باید queueing، DB capacity، query latency و downstream limits را ببینید. Little's Law یک intuition مفید می‌دهد:

`Concurrency ≈ Throughput × Latency`

مثلاً 500 req/s با latency متوسط 0.1s حدود 50 request concurrent ایجاد می‌کند.

## ۱۲.۹ Virtual Threads: پاسخ دقیق‌تر

Virtual threadها برای workloadهای I/O-bound که blocking هستند عالی‌اند و programming model را ساده می‌کنند، اما:
- database connection را virtual نمی‌کنند؛
- CPU-bound workload را سریع‌تر نمی‌کنند؛
- downstream capacity همچنان محدود است؛
- concurrency نامحدود می‌تواند DB/API downstream را overload کند؛
- باید concurrency limiting/backpressure داشته باشید.

## ۱۲.۱۰ Observability با Golden Signals

برای هر service حداقل:
- **Latency**: p50/p95/p99، نه فقط average
- **Traffic**: RPS/QPS
- **Errors**: error rate و error category
- **Saturation**: CPU، heap، thread pool، DB pool، queue lag

```text
Request
  |
 traceId=xyz
  v
API -> Order -> Payment -> DB
 |       |        |
 +-------+--------+----> OpenTelemetry
                          |
                 Trace backend / metrics
```

Alert باید actionable باشد. مثال بهتر از «CPU > 80%»: «p99 latency بالاتر از SLO همراه با افزایش saturation برای 10 دقیقه».

---

# ۱۳. Case Study کامل: طراحی Order + Inventory + Payment

## ۱۳.۱ Requirementها

Functional:
- ایجاد سفارش
- reserve کردن inventory
- پرداخت
- مشاهده وضعیت سفارش
- cancel/refund

Non-functional:
- duplicate charge ممنوع
- overselling باید کنترل شود
- auditability بالا
- availability بالا
- peak traffic قابل جذب
- eventual consistency بین بعضی serviceها قابل قبول است

فرض نمونه:
- 5M سفارش/day
- average ≈ 58 orders/s
- peak factor 10 → حدود 580 orders/s
- هر سفارش حدود 2 KB metadata → تقریباً 10 GB/day قبل از index/replication

## ۱۳.۲ High-level architecture

```text
                 +------------------+
Client --------> | API Gateway      |
                 +--------+---------+
                          |
                          v
                 +------------------+
                 | Order Service    |
                 +---+----------+---+
                     |          |
                     |          +------> Order DB
                     |
                     v
                 +--------+
                 | Kafka  |
                 +---+----+
                     |
          +----------+-----------+
          |                      |
          v                      v
 +----------------+      +----------------+
 | Inventory Svc  |      | Payment Svc    |
 +-------+--------+      +-------+--------+
         |                       |
         v                       v
  Inventory DB             Payment DB
                                 |
                                 v
                         External Provider
```

## ۱۳.۳ Saga flow

```text
OrderCreated
    |
    v
ReserveInventory
    |
 success
    v
ProcessPayment
   / \
  /   \
OK    FAIL
|       |
v       v
Order  ReleaseInventory
Paid       |
           v
       OrderFailed
```

برای workflow پیچیده، orchestration اغلب visibility و کنترل بهتری نسبت به choreography پراکنده دارد.

## ۱۳.۴ Inventory race

روش SQL atomic:

```sql
UPDATE inventory
SET available = available - :qty
WHERE product_id = :id
  AND available >= :qty;
```

اگر affected rows = 0 باشد، موجودی کافی نیست. این approach می‌تواند از read-then-write race جلوگیری کند.

## ۱۳.۵ API

```text
POST /orders
Idempotency-Key: 91f...

GET /orders/{orderId}

POST /orders/{orderId}/cancel
```

`POST /orders` می‌تواند سریع `202 Accepted` یا یک order با status `PENDING` برگرداند و workflow async ادامه پیدا کند، اگر business اجازه دهد.

## ۱۳.۶ State machine

```text
CREATED
   |
   v
INVENTORY_RESERVED
   |
   v
PAYMENT_PENDING
  /       \
 v         v
PAID      PAYMENT_FAILED
 |              |
 v              v
COMPLETED    CANCELLED
```

Transitionها باید validate شوند؛ event تکراری نباید state را عقب ببرد.

## ۱۳.۷ Failure scenarios که باید خودتان مطرح کنید

1. Kafka unavailable هنگام create order → outbox.
2. Inventory event دوبار می‌رسد → idempotent consumer.
3. Payment provider timeout می‌دهد ولی charge انجام شده → query/reconciliation با idempotency key.
4. consumer عقب می‌افتد → consumer lag alert + scale consumers/partitions.
5. DB slow می‌شود → pool saturation، timeout، slow-query analysis.
6. یک SKU بسیار محبوب است → hot-row/hot-key strategy.
7. compensation شکست می‌خورد → retry/DLT + operational workflow؛ compensation نیز باید idempotent باشد.

---

# ۱۴. Case Study کامل: طراحی Payment/Ledger Service

## هدف
سیستم باید money movement را traceable و قابل reconciliation کند. «balance» فقط یک cache/derived value می‌تواند باشد؛ ledger منبع audit است.

```text
                 +------------------+
Client --------> | Payment API      |
                 +--------+---------+
                          |
                 idempotency
                          |
                          v
                 +------------------+
                 | Payment Service  |
                 +---+----------+---+
                     |          |
                     v          v
                [Ledger DB]  [Outbox]
                                |
                                v
                              Kafka
```

نمونه double-entry:

```text
Transaction T100
--------------------------------
Account A   Debit    100
Merchant B  Credit   100
--------------------------------
Sum = 0
```

Invariant مهم: مجموع debit و credit یک transaction باید balance شود. Entries immutable هستند؛ correction با entry جدید انجام می‌شود، نه ویرایش تاریخچه.

مواردی که در مصاحبه مطرح کنید: unique business reference، ACID transaction، isolation level، idempotency، audit log، encryption، access control، reconciliation، immutable history، disaster recovery و observability.

---

# ۱۵. Case Study کامل: Distributed Rate Limiter

فرض: هر user حداکثر 100 request/minute، چند API Gateway instance داریم.

```text
              +--> Gateway 1 --+
Client -------+--> Gateway 2 --+--> Redis
              +--> Gateway 3 --+
```

Local counter کافی نیست، چون user می‌تواند بین gatewayها پخش شود.

Token Bucket:
- capacity = 100
- refill rate مشخص
- request در صورت وجود token پذیرفته می‌شود
- check + decrement باید atomic باشد؛ در Redis می‌توان Lua script استفاده کرد.

Trade-offها:
- Redis central dependency است.
- cluster/sharding برای scale لازم می‌شود.
- strict global accuracy latency بیشتری دارد.
- برای بعضی endpointها approximate/local limiting کافی است.
- policy باید per user/API/tenant قابل تنظیم باشد.

---

# ۱۶. سوالات Follow-up که سطح Senior را مشخص می‌کنند

1. اگر Redis down شود، fail-open می‌کنید یا fail-closed؟ چرا؟
2. اگر DB replica 5 ثانیه lag داشته باشد، کدام readها هنوز می‌توانند replica را استفاده کنند؟
3. چگونه schema یک Kafka event را بدون شکستن consumerهای قدیمی تغییر می‌دهید؟
4. اگر یک Kafka partition hot شود چه می‌کنید؟
5. retry چه زمانی خطرناک است؟
6. چرا average latency برای production کافی نیست؟
7. چگونه duplicate payment را بعد از timeout تشخیص می‌دهید؟
8. چه زمانی synchronous call بهتر از event است؟
9. چه زمانی microservice انتخاب بدتری از modular monolith است؟
10. چگونه migration database را بدون downtime انجام می‌دهید؟
11. اگر cache و DB مقدار متفاوت داشته باشند source of truth چیست؟
12. چگونه backpressure را در consumer pipeline اعمال می‌کنید؟
13. اگر downstream فقط 100 RPS ظرفیت دارد ولی شما 1000 RPS دریافت کنید چه می‌کنید؟
14. چگونه graceful shutdown یک Spring Boot consumer را طراحی می‌کنید؟
15. چه metricهایی نشان می‌دهند HikariCP bottleneck شده؟
16. p99 latency بالا ولی CPU پایین است؛ کجاها را بررسی می‌کنید؟
17. memory usage بالا می‌رود اما GC آن را برنمی‌گرداند؛ روند diagnosis چیست؟
18. optimistic locking تحت contention بالا چه رفتاری دارد؟
19. چرا distributed lock همیشه بهترین راه جلوگیری از duplicate work نیست؟
20. RPO و RTO چه اثری بر replication/backup design دارند؟

---

# ۱۷. نکات اصلاحی برای چند پاسخ رایج

- **`volatile`:** بهتر است نگویید «هر read/write مستقیماً به main memory می‌رود». مفهوم کلیدی visibility و happens-before semantics است؛ implementation جزئیات بیشتری دارد.
- **`synchronized`:** mutual exclusion و memory visibility/happens-before فراهم می‌کند، اما «atomicity همه چیز» تعبیر دقیقی نیست.
- **`ConcurrentHashMap`:** در Java جدید توضیح قدیمی «segment locking» دقیق نیست؛ implementation از تکنیک‌های finer-grained/CAS و synchronization در شرایط مختلف استفاده می‌کند.
- **Exactly-once:** Kafka EOS را با exactly-once شدن side effect در PostgreSQL، HTTP API یا email یکی نگیرید.
- **Redlock:** distributed locking با Redis trade-offهای جدی دارد؛ ابتدا مشخص کنید واقعاً lock لازم است یا unique constraint/idempotency/partition ownership مسئله را ساده‌تر حل می‌کند.
- **Read replica:** صرف `@Transactional(readOnly=true)` به‌تنهایی traffic را به replica route نمی‌کند؛ routing datasource/infrastructure لازم است.
- **Readiness:** وابسته کردن readiness به هر dependency می‌تواند هنگام outage باعث حذف همه podها از service شود؛ dependency و desired failure behavior را آگاهانه انتخاب کنید.
- **JWT:** JWT الزاماً بهترین token format برای همه معماری‌ها نیست؛ revocation، lifetime و token leakage را در نظر بگیرید.
- **Virtual threads:** «میلیون‌ها thread» را به‌عنوان مجوز concurrency نامحدود مطرح نکنید؛ downstream resource limits همچنان پابرجاست.
- **NoSQL:** NoSQL مترادف eventual consistency نیست؛ consistency model به محصول و configuration وابسته است.

---

# ۱۸. Cheat Sheet مصاحبه

هنگام شروع:
> «اول functional/non-functional requirements و scale را مشخص می‌کنم، بعد high-level architecture می‌دهم و سپس روی consistency، data model و failure handling عمیق می‌شوم.»

هنگام انتخاب تکنولوژی:
> «قبل از انتخاب Kafka/Redis/NoSQL، access pattern و consistency requirement را مشخص می‌کنم.»

هنگام failure:
> «فرض می‌کنم network call می‌تواند timeout شود، message می‌تواند duplicate شود و instance می‌تواند وسط عملیات crash کند.»

هنگام scalability:
> «اول bottleneck را اندازه می‌گیرم؛ horizontal scaling application اگر database bottleneck باشد مشکل را حل نمی‌کند.»

هنگام consistency:
> «Consistency requirement را per workflow مشخص می‌کنم؛ payment ledger و social feed نیاز یکسانی ندارند.»

هنگام Java:
> «Thread pool، DB pool، heap/GC و downstream concurrency را به‌صورت یک سیستم واحد بررسی می‌کنم.»

---

# ۱۹. برنامه مطالعه پیشنهادی

**روز ۱:** Requirements، estimation، CAP، consistency  
**روز ۲:** SQL/indexing/transactions/locking  
**روز ۳:** Kafka، idempotency، outbox، Saga  
**روز ۴:** Cache، rate limiting، resilience patterns  
**روز ۵:** JVM، concurrency، virtual threads، GC  
**روز ۶:** Observability، Kubernetes، security  
**روز ۷:** طراحی کامل Order/Payment روی کاغذ در 45 دقیقه

برای تمرین، هر case را یک بار بدون نگاه کردن به جواب و با صدای بلند توضیح دهید. پاسخ Senior باید بیشتر شبیه **reasoning درباره trade-offها** باشد تا فهرست کردن technologyها.



[^1]: In software engineering, especially in **distributed systems and microservices**, **DLQ** usually means **Dead Letter Queue**.
	
	A DLQ is a special queue where messages are sent when they **cannot be processed successfully** after one or more attempts.
	
	### Simple example
	
	Imagine a Java/Spring Boot service consuming payment events from Kafka:
	
	```text
	Payment Service
	      ↓
	Kafka Topic
	      ↓
	Consumer
	      ↓
	Process Payment
	   ↙       ↘
	Success    Failure
	             ↓
	          Retry
	             ↓
	        Still fails
	             ↓
	            DLQ
	```
	
	For example, a message arrives:
	
	```json
	{
	  "transactionId": "TX123",
	  "amount": 500
	}
	```
	
	Your consumer tries to process it, but perhaps the database is unavailable or the message contains invalid data.
	
	Instead of retrying **forever** or blocking other messages:
	
	```text
	Attempt 1 → Failed
	Attempt 2 → Failed
	Attempt 3 → Failed
	              ↓
	        Send to DLQ
	```
	
	The original consumer can then continue processing other messages.
	
	### Why DLQs are useful
	
	They help with **fault isolation and recovery**. You can inspect failed messages later, understand why they failed, fix the underlying problem, and potentially **replay/reprocess** them.
	
	In systems you're likely familiar with:
	
	**Kafka**
	
	```text
	payments
	   ↓
	PaymentConsumer
	   ↓ failure after retries
	payments.DLT
	```
	
	In the Spring ecosystem, you'll also often see **DLT (Dead Letter Topic)** rather than DLQ because Kafka uses _topics_, not traditional queues.
	
	For example, with Spring Kafka:
	
	```java
	@KafkaListener(topics = "payments")
	public void process(Payment payment) {
	    paymentService.process(payment);
	}
	```
	
	After configured retries are exhausted, Spring Kafka can publish the failed record to something like:
	
	```text
	payments.DLT
	```
	
	### DLQ vs Retry Queue
	
	The important distinction is:
	
	```text
	Retry Queue
	    ↓
	Temporary failures
	    ↓
	Try processing again
	
	
	DLQ
	    ↓
	Repeated/permanent failures
	    ↓
	Investigation / manual or automated recovery
	```
	
	So a good mental model is:
	
	**DLQ = a safe place for messages your system couldn't successfully process, so they don't block normal processing and aren't silently lost.**

[^2]: **Graceful degradation** means:
	
	> When part of a system fails, the system **continues working with reduced functionality** instead of completely failing.
	
	It is especially important in **microservices and distributed systems**.
	
	### Simple example
	
	Suppose an e-commerce page depends on several services:
	
	```text
	Product Page
	 ├── Product Service      ✅
	 ├── Pricing Service      ✅
	 ├── Recommendation       ❌
	 └── Review Service       ✅
	```
	
	If the **Recommendation Service** goes down, you don't want:
	
	```text
	❌ "500 Internal Server Error"
	```
	
	Instead, the page still loads:
	
	```text
	✅ Product information
	✅ Price
	✅ Reviews
	⚠️ Recommendations temporarily unavailable
	```
	
	That's **graceful degradation**.
	
	### Another example: banking/fintech
	
	Imagine:
	
	```text
	Payment API
	 ├── Core Payment Service  ✅
	 ├── Fraud Service         ✅
	 ├── Notification Service  ❌
	 └── Analytics Service     ❌
	```
	
	If the notification service is unavailable, you generally don't want a successfully processed payment to fail just because an SMS/email couldn't be sent.
	
	Instead:
	
	```text
	Payment → SUCCESS ✅
	Notification → queued/retried ⚠️
	Analytics → processed later ⚠️
	```
	
	The **critical functionality remains available**, while non-critical functionality is temporarily degraded.
	
	### Common techniques
	
	Graceful degradation is often implemented using **timeouts, circuit breakers, fallbacks, caching, retries, asynchronous processing, and DLQs**.
	
	For example:
	
	```java
	try {
	    recommendations = recommendationService.getRecommendations(userId);
	} catch (Exception e) {
	    recommendations = Collections.emptyList();
	}
	```
	
	A more production-ready approach might use a **Circuit Breaker**:
	
	```text
	Request
	   ↓
	Circuit Breaker
	   ↓
	Recommendation Service ❌
	   ↓
	Fallback
	   ↓
	Return empty recommendations
	```
	
	### Graceful degradation vs fault tolerance
	
	They're related but slightly different:
	
	**Fault tolerance** tries to keep the system functioning **normally despite failures**.
	
	**Graceful degradation** accepts that some functionality may be lost, but ensures the **important parts continue working**.
	
	For a senior backend interview, a good one-sentence definition is:
	
	> **Graceful degradation is designing a system so that when a dependency or component fails, the system continues providing its core functionality with reduced capabilities rather than experiencing a complete failure.**

[^3]: **CAP Theorem** is a fundamental concept in **distributed systems**. It says that when a **network partition** happens, a distributed system can guarantee either **Consistency** or **Availability**, but not both.
	
	**CAP = Consistency + Availability + Partition Tolerance**
	
	### The three properties
	
	**C — Consistency**
	
	Every node sees the **same/latest data**.
	
	```text
	Node A: balance = 100
	Node B: balance = 100
	Node C: balance = 100
	```
	
	After an update to `80`, a consistent system won't return the old `100` as if it were current.
	
	**A — Availability**
	
	Every request receives a response, even if some nodes have problems.
	
	```text
	Client → Node A ❌
	       → Node B ✅ → Response
	```
	
	Availability here means the system doesn't refuse the request merely to preserve consistency. The response might not contain the latest data.
	
	**P — Partition Tolerance**
	
	The system continues operating when nodes **cannot communicate with each other** because of a network failure.
	
	```text
	Node A  ❌ network ❌  Node B
	
	Node A is alive
	Node B is alive
	but they cannot communicate.
	```
	
	### The important part of CAP
	
	People sometimes say:
	
	> "You can choose only 2 out of 3."
	
	That's a useful shortcut, but slightly misleading.
	
	In a real distributed system, **network partitions can happen**, so you generally have to tolerate **P**. When a partition actually occurs, your practical choice becomes:
	
	```text
	        Network Partition
	               |
	        ┌──────┴──────┐
	        ↓             ↓
	       CP             AP
	 Consistency      Availability
	```
	
	### CP — Consistency + Partition Tolerance
	
	Suppose you're transferring money:
	
	```text
	Account balance = $1,000
	
	Node A ←X→ Node B
	       network failure
	```
	
	Node B isn't sure whether Node A has already processed a $500 transfer.
	
	A **CP-oriented** design may say:
	
	> "I cannot guarantee the correct balance, so I will reject/wait rather than return potentially incorrect data."
	
	```text
	Correctness > Availability
	```
	
	This is often desirable for operations where stale/conflicting data could be dangerous.
	
	### AP — Availability + Partition Tolerance
	
	Imagine a social-media "Like" counter.
	
	```text
	Node A ←X→ Node B
	
	Node A: 101 likes
	Node B: 100 likes
	```
	
	Instead of rejecting requests, both sides can continue operating.
	
	When communication is restored:
	
	```text
	Node A ───── Node B
	       ↓
	   reconciliation
	       ↓
	   101 likes
	```
	
	The system remained **available**, but temporarily sacrificed strong consistency. This is associated with **eventual consistency**.
	
	### Easy way to remember
	
	```text
	CAP
	
	C = "Do I always get consistent data?"
	
	A = "Do I always get a response?"
	
	P = "Can the system handle broken communication
	     between its nodes?"
	```
	
	And during a network partition:
	
	```text
	Financial/critical operation
	        → often favor C
	        → CP
	
	Social feed / likes / some caches
	        → often favor A
	        → AP
	```
	
	For a **senior Java/backend interview**, a strong short answer is:
	
	> **CAP theorem states that during a network partition, a distributed system cannot simultaneously guarantee both consistency and availability. It must choose whether to reject/delay some operations to preserve consistency (CP), or continue serving requests while potentially returning stale/inconsistent data (AP).**

[^4]: In software engineering, **recovery** is the process of **restoring a system or service to a healthy, operational state after a failure**.
	
	Recovery is a broader concept than retry or graceful degradation—it encompasses all the mechanisms used to resume normal operation.
	
	## Examples
	
	### 1. Service Recovery
	
	A microservice crashes due to an OutOfMemoryError.
	
	```text
	Service Running
	      ↓
	OutOfMemoryError
	      ↓
	Container crashes
	      ↓
	Kubernetes restarts pod
	      ↓
	Service healthy again
	```
	
	This is **automatic recovery**.
	
	---
	
	### 2. Database Recovery
	
	A database server unexpectedly shuts down.
	
	```text
	Crash
	   ↓
	Restart
	   ↓
	Replay transaction log (WAL/Redo Log)
	   ↓
	Restore consistent state
	```
	
	The database recovers to the last committed transaction.
	
	---
	
	### 3. Message Recovery
	
	A Kafka consumer cannot process a message because a downstream service is unavailable.
	
	```text
	Receive Message
	      ↓
	Call Payment API
	      ↓
	Timeout
	      ↓
	Retry
	      ↓
	Success
	```
	
	If retries fail:
	
	```text
	Retry
	   ↓
	DLQ
	   ↓
	Fix issue
	   ↓
	Replay DLQ
	```
	
	Reprocessing the DLQ is also a form of recovery.
	
	---
	
	### 4. Disaster Recovery (DR)
	
	An entire data center becomes unavailable.
	
	```text
	Primary Region ❌
	        ↓
	Failover
	        ↓
	Secondary Region ✅
	```
	
	The system recovers by switching to another region.
	
	---
	
	## Recovery vs Related Concepts
	
	|Concept|Purpose|Example|
	|---|---|---|
	|**Retry**|Re-attempt a failed operation|Retry an HTTP request after a timeout|
	|**Graceful Degradation**|Continue operating with reduced functionality|Show products without recommendations|
	|**Circuit Breaker**|Stop calling an unhealthy dependency|Open the circuit after repeated failures|
	|**Fallback**|Use an alternative behavior|Return cached data|
	|**DLQ**|Store permanently failed messages|Move Kafka record to `payments.DLT`|
	|**Recovery**|Restore the system to a healthy state|Restart service, replay messages, fail over|
	
	## Common Recovery Strategies
	
	- **Automatic restart** (Kubernetes, Docker)
	    
	- **Retry with exponential backoff**
	    
	- **Replay messages from Kafka or a DLQ**
	    
	- **Database transaction log recovery**
	    
	- **Failover to another node or region**
	    
	- **Restore from backups**
	    
	- **State synchronization between replicas**
	    
	- **Auto-scaling** to replace failed instances
	    
	
	## Interview Definition
	
	A concise interview answer would be:
	
	> **Recovery is the process of returning a system to a normal, healthy state after a failure. Depending on the failure, recovery may involve restarting services, retrying operations, replaying messages, restoring data, or failing over to redundant infrastructure.**
	
	### Typical Resilience Flow
	
	```text
	Request
	   ↓
	Failure
	   ↓
	Retry
	   ↓
	Still failing?
	   ↓
	Circuit Breaker
	   ↓
	Graceful Degradation / Fallback
	   ↓
	Background Recovery
	   ↓
	Normal Operation Restored
	```
	
	This sequence illustrates how modern distributed systems maintain resilience: they first try to recover quickly (retry), protect themselves from cascading failures (circuit breaker), continue serving users in a degraded mode if necessary, and eventually restore full functionality through recovery mechanisms.

[^5]: سؤال خیلی خوبی است. در System Design، **Caching Strategy مستقیماً از چند پارامتر کلیدی که تخمین می‌زنیم به دست می‌آید**، نه اینکه یک فرمول مشخص داشته باشد. این تخمین‌ها به ما نشان می‌دهند آیا اصلاً Cache لازم است یا نه، چه چیزی را Cache کنیم، کجا Cache کنیم و TTL چقدر باشد.
	
	بیایید ارتباط این اعداد را با Cache ببینیم.
	
	---
	
	## 1. Read/Write Ratio (مهم‌ترین عامل)
	
	اولین چیزی که نگاه می‌کنیم نسبت Read به Write است.
	
	### مثال اول
	
	```
	Reads : Writes
	
	1000 : 1
	```
	
	مثل:
	
	- Product Catalog
	    
	- User Profile
	    
	- Exchange Rates
	    
	- News
	    
	
	در این حالت تقریباً همیشه Cache ارزش دارد.
	
	زیرا:
	
	```
	DB
	 ↑
	1000 reads
	
	↓
	
	Redis
	
	فقط 1 write
	```
	
	اکثر درخواست‌ها از Redis پاسخ داده می‌شوند.
	
	---
	
	### مثال دوم
	
	```
	Reads : Writes
	
	1 : 1
	```
	
	مثل Chat یا Trading.
	
	در اینجا Cache معمولاً سود زیادی ندارد چون داده دائماً تغییر می‌کند.
	
	---
	
	## 2. QPS
	
	فرض کنید محاسبه کردیم:
	
	```
	Average QPS = 500
	
	Peak QPS = 5000
	```
	
	حالا سؤال:
	
	آیا دیتابیس می‌تواند 5000 Query در ثانیه را جواب بدهد؟
	
	اگر خیر،
	
	باید Cache اضافه کنیم.
	
	مثلاً
	
	```
	5000 req/s
	
	↓
	
	Redis
	
	4500
	
	↓
	
	DB
	
	500
	```
	
	یعنی Cache فشار را از روی DB برمی‌دارد.
	
	---
	
	## 3. Response Time Requirement
	
	فرض کنید SLA گفته است:
	
	```
	P95 < 50 ms
	```
	
	اما
	
	```
	DB Query = 80 ms
	```
	
	پس حتی اگر DB توان تحمل Load را داشته باشد، باز هم Cache لازم است.
	
	```
	Redis
	
	1~2 ms
	
	DB
	
	80 ms
	```
	
	---
	
	## 4. Data Freshness
	
	بعد سؤال بعدی:
	
	چقدر داده می‌تواند قدیمی باشد؟
	
	مثلاً
	
	### قیمت دلار
	
	```
	TTL = 5 seconds
	```
	
	اشکالی ندارد.
	
	اما
	
	### موجودی حساب بانکی
	
	```
	TTL = 0
	```
	
	تقریباً نباید Cache شود.
	
	---
	
	## 5. Storage Size
	
	فرض کنید:
	
	```
	100 million products
	
	1 KB each
	```
	
	یعنی
	
	```
	100 GB
	```
	
	آیا Redis ما می‌تواند همه را نگه دارد؟
	
	اگر نه،
	
	ممکن است فقط Hot Data را Cache کنیم.
	
	مثلاً
	
	```
	Top 5%
	
	Popular Products
	```
	
	---
	
	## 6. Bandwidth
	
	فرض کنید:
	
	```
	Response
	
	500 KB
	
	Peak
	
	2000 req/s
	```
	
	Bandwidth می‌شود
	
	```
	≈ 1 GB/s
	```
	
	اگر این پاسخ‌ها تکراری باشند،
	
	Cache جلوی تولید مجدد Response را می‌گیرد.
	
	---
	
	# انتخاب نوع Cache
	
	بعد از این تخمین‌ها تصمیم می‌گیریم چه نوع Cache مناسب است.
	
	---
	
	## Case 1
	
	```
	Read بسیار زیاد
	
	Write کم
	```
	
	مثلاً فروشگاه اینترنتی.
	
	```
	Client
	
	↓
	
	Redis
	
	↓
	
	MySQL
	```
	
	الگو:
	
	```
	Cache Aside (Lazy Loading)
	```
	
	---
	
	## Case 2
	
	```
	Read بسیار زیاد
	
	Write هم زیاد
	```
	
	مثلاً Timeline شبکه اجتماعی.
	
	ممکن است از
	
	```
	Write Through
	```
	
	یا
	
	```
	Write Behind
	```
	
	استفاده شود.
	
	---
	
	## Case 3
	
	```
	Data بسیار حساس
	```
	
	مثل Banking.
	
	ممکن است اصلاً Cache نکنیم.
	
	یا فقط
	
	```
	Reference Data
	
	Country
	
	Currency
	
	Branches
	```
	
	را Cache کنیم.
	
	---
	
	## Case 4
	
	```
	Response خیلی سنگین است.
	```
	
	مثلاً
	
	```
	Dashboard
	
	20 Query
	
	Join
	
	Aggregation
	```
	
	به جای Cache کردن رکوردها،
	
	کل Response را Cache می‌کنیم.
	
	---
	
	# مثال مصاحبه
	
	فرض کنید سؤال طراحی دیجی‌کالا باشد.
	
	شما تخمین می‌زنید:
	
	```
	DAU = 5M
	
	Read/Write = 95/5
	
	Peak = 30K QPS
	```
	
	از این نتیجه می‌گیرید:
	
	- Read بسیار بیشتر از Write است، پس Cache ارزش بالایی دارد.
	    
	- Peak QPS برای اعمال فشار مستقیم روی دیتابیس زیاد است، بنابراین Redis را جلوی دیتابیس قرار می‌دهیم.
	    
	- اطلاعات محصولات نسبتاً پایدار هستند، بنابراین از **Cache-Aside** با TTL حدود ۵ تا ۳۰ دقیقه (بسته به نرخ تغییرات) استفاده می‌کنیم.
	    
	- فقط محصولات پربازدید یا نتایج جستجوی پرتکرار را Cache می‌کنیم تا مصرف حافظه کنترل شود.
	    
	
	---
	
	## خلاصه ذهنی برای مصاحبه
	
	می‌توانید این زنجیره تصمیم‌گیری را همیشه در ذهن داشته باشید:
	
	```text
	Read/Write Ratio
	      │
	      ▼
	آیا Cache ارزش دارد؟
	
	      │
	      ▼
	QPS
	      │
	      ▼
	آیا DB تحمل Load را دارد؟
	
	      │
	      ▼
	Data Freshness
	      │
	      ▼
	TTL چقدر باشد؟
	
	      │
	      ▼
	Storage
	      │
	      ▼
	همه داده را Cache کنیم یا فقط Hot Data؟
	
	      │
	      ▼
	Response Size
	      │
	      ▼
	Object Cache یا Full Response Cache؟
	
	      │
	      ▼
	انتخاب Strategy
	(Cache Aside / Read Through /
	Write Through / Write Behind)
	```
	
	به طور خلاصه، **Read/Write Ratio تعیین می‌کند که آیا Cache مفید است، QPS تعیین می‌کند که چقدر به Cache برای کاهش بار نیاز داریم، Freshness مشخص می‌کند TTL و سیاست Invalidating چگونه باشد، و Storage/Bandwidth تعیین می‌کنند چه چیزی را Cache کنیم و در چه سطحی (رکورد، Query یا کل Response).** این همان فرآیند فکری است که معمولاً در مصاحبه‌های System Design از یک مهندس ارشد انتظار می‌رود.
	
	
	**Write Behind (یا Write Back Cache)** یعنی:
	
	> **ابتدا داده در Cache نوشته می‌شود و به کاربر پاسخ داده می‌شود، سپس Cache در پس‌زمینه (Asynchronously) داده را در Database ذخیره می‌کند.**
	
	یعنی Database مستقیماً در مسیر درخواست کاربر نیست.
	
	### روند کار
	
	```text
	Client
	   │
	   ▼
	Redis (Write)
	   │
	   ├── پاسخ فوری به کاربر ✅
	   │
	   ▼
	Background Worker
	   │
	   ▼
	Database
	```
	
	---
	
	## مثال
	
	فرض کنید کاربر امتیاز یک پست را ثبت می‌کند.
	
	بدون Write Behind:
	
	```text
	Client
	   │
	   ▼
	Database Write (50 ms)
	   │
	   ▼
	Response
	```
	
	کاربر باید منتظر بماند تا Database عملیات را تمام کند.
	
	---
	
	با Write Behind:
	
	```text
	Client
	   │
	   ▼
	Redis Write (1 ms)
	   │
	   ▼
	Response
	```
	
	بعد از آن:
	
	```text
	Redis
	   │
	   ▼
	Background Sync
	   │
	   ▼
	Database
	```
	
	در نتیجه کاربر تقریباً بلافاصله پاسخ می‌گیرد.
	
	---
	
	## مزایا
	
	✅ سرعت بسیار بالا
	
	✅ کاهش تعداد Write روی Database
	
	مثلاً اگر کاربر ۱۰ بار پشت سر هم تنظیماتش را تغییر دهد:
	
	بدون Write Behind:
	
	```text
	10 Write
	
	↓
	
	Database
	```
	
	اما با Write Behind:
	
	```text
	10 Write
	
	↓
	
	Redis
	
	↓
	
	1 Write
	
	↓
	
	Database
	```
	
	یعنی چندین تغییر را می‌توان در یک عملیات روی دیتابیس ادغام کرد.
	
	---
	
	## مشکل اصلی
	
	فرض کنید:
	
	```text
	Redis
	
	↓
	
	هنوز به Database نرسیده
	
	↓
	
	Server Crash
	```
	
	در این حالت ممکن است داده‌ای که فقط در Cache بوده از بین برود.
	
	به همین دلیل Write Behind برای داده‌های بسیار حساس مانند:
	
	- انتقال پول
	    
	- موجودی حساب
	    
	- سفارش‌های بانکی
	    
	
	معمولاً **انتخاب مناسبی نیست**.
	
	---
	
	## چه جاهایی مناسب است؟
	
	- شمارنده بازدید (View Count)
	    
	- تعداد لایک
	    
	- لاگ‌ها
	    
	- آمار و Analytics
	    
	- متریک‌ها
	    
	- Sessionهای کم‌اهمیت
	    
	
	این داده‌ها اگر با چند ثانیه تأخیر در Database ثبت شوند یا حتی تعداد کمی از آن‌ها از دست بروند، معمولاً مشکلی ایجاد نمی‌کند.
	
	---
	
	## تفاوت با Write Through
	
	### Write Through
	
	```text
	Client
	   │
	   ▼
	Cache
	   │
	   ▼
	Database
	   │
	   ▼
	Response
	```
	
	- ابتدا هم Cache و هم Database به‌روزرسانی می‌شوند.
	    
	- پاسخ بعد از موفقیت هر دو برمی‌گردد.
	    
	- **امن‌تر** است، اما کندتر.
	    
	
	---
	
	### Write Behind
	
	```text
	Client
	   │
	   ▼
	Cache
	   │
	   ▼
	Response ✅
	
	(بعداً)
	
	Cache
	   │
	   ▼
	Database
	```
	
	- پاسخ خیلی سریع‌تر است.
	    
	- اما اگر قبل از همگام‌سازی مشکلی رخ دهد، احتمال از دست رفتن داده وجود دارد.
	    
	
	### در مصاحبه
	
	اگر از شما بپرسند **چه زمانی از Write Behind استفاده می‌کنید؟**، پاسخ خوبی این است:
	
	> _از Write Behind زمانی استفاده می‌کنم که سرعت نوشتن اهمیت زیادی داشته باشد و بتوانیم تأخیر در ثبت روی Database یا حتی از دست رفتن مقدار کمی از داده را بپذیریم؛ مانند شمارنده بازدید، لایک‌ها، متریک‌ها و داده‌های تحلیلی. برای داده‌های مالی یا تراکنش‌های بانکی از آن استفاده نمی‌کنم، چون پایداری و دوام داده (Durability) اولویت دارد._
	
	
