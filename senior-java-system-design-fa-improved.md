

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
پاسخ Senior باید metrics، logs، tracing، alerting، [^9]deployment، security، capacity planning و rollback را نیز پوشش دهد.

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
**پاسخ:** برای تولید short code می‌توان از base62 encoding روی یک auto-increment ID یا از hashing (مثل MD5 با truncation و collision check) استفاده کرد[^6]. Data store می‌تواند یک key-value store مثل DynamoDB یا یک relational database با index روی short code باشد. برای caching، URL های پرترافیک در Redis نگه‌داری می‌شوند تا read latency کم شود. سیستم باید read-heavy design داشته باشد چون تعداد redirect بسیار بیشتر از تعداد create است.

---

## ۳. Database ها و Storage

**سوال:** SQL در مقابل NoSQL: چه فاکتورهایی انتخاب را تعیین می‌کنند و trade-off های consistency و schema flexibility چیست؟
**پاسخ:** SQL برای داده‌های structured با relationship پیچیده و نیاز به strong consistency و ACID transaction مناسب است (مثل سیستم مالی). NoSQL برای scalability بالا، schema flexibility و مدل‌های داده‌ای مثل document، key-value، wide-column مناسب است (مثل MongoDB، Cassandra). NoSQL معمولاً eventual consistency[^7] را می‌پذیرد تا availability و partition tolerance بهتری داشته باشد.

**سوال:** چطور schema و indexing strategy را برای یک سیستم high-write (مثل order management) با JDBC/JPA طراحی می‌کنید؟
**پاسخ:** برای high-write باید تعداد index ها را محدود کرد چون هر index خودش write overhead دارد. از composite index بر اساس pattern query استفاده می‌شود، جداول با partitioning (مثل partition بر اساس تاریخ) تقسیم می‌شوند، و از batch insert در JPA (`hibernate.jdbc.batch_size`) برای کاهش round-trip استفاده می‌شود. همچنین می‌توان write را از طریق یک write-optimized table انجام داد و بعد async به read model sync کرد (CQRS)[^8].

**سوال:** استراتژی‌های database sharding (range-based، hash-based، directory-based) را توضیح دهید. چطور با Java و connection pool مثل HikariCP این را پیاده می‌کنید؟
**پاسخ:** Range-based sharding داده را بر اساس بازه یک key تقسیم می‌کند (ساده اما ممکن است hotspot ایجاد کند). Hash-based sharding از hash کلید برای توزیع یکنواخت استفاده می‌کند اما resharding سخت‌تر است. Directory-based از یک lookup service برای mapping key به shard استفاده می‌کند و انعطاف بیشتری دارد. در Java معمولاً با یک routing layer یا ShardingSphere و چند DataSource جداگانه (هرکدام با HikariCP pool خودش) این کار پیاده می‌شود.

**سوال:** N+1 query problem در JPA/Hibernate چیست و چطور حل می‌شود؟
**پاسخ:** وقتی یک entity با لیستی از entity های وابسته lazy-load می‌شود، برای هر آیتم لیست یک query جداگانه اجرا می‌شود که منجر به N+1 query می‌گردد. راه‌حل‌ها شامل استفاده از JOIN FETCH در JPQL، @EntityGraph، یا batch fetching (`hibernate.default_batch_fetch_size`) است.

**سوال:** چطور یک سیستم برای database failover و replication (leader-follower) طراحی می‌کنید؟
**پاسخ:** یک leader نوشتن‌ها را می‌پذیرد و follower ها به‌صورت async یا sync آن‌ها را replicate می‌کنند. برای failover از یک health check و leader election (مثلاً با Zookeeper یا ابزارهایی مثل Patroni برای PostgreSQL) استفاده می‌شود تا در صورت خرابی leader، یکی از follower ها promote شود. باید read/write splitting در application layer (مثلاً با Spring's `@Transactional(readOnly=true)` routing به replica) در نظر گرفته شود.

**سوال:** تفاوت optimistic locking و pessimistic locking چیست و چطور هرکدام را با JPA پیاده‌سازی می‌کنید (`@Version`، `SELECT FOR UPDATE`)؟
**پاسخ:** Optimistic locking فرض می‌کند conflict نادر است؛ با یک ستون `@Version` تغییرات هم‌زمان تشخیص داده می‌شوند و در صورت conflict یک `OptimisticLockException` پرتاب می‌شود. Pessimistic locking از ابتدا رکورد را با `SELECT ... FOR UPDATE` قفل می‌کند تا هیچ transaction دیگری نتواند آن را تغییر دهد، مناسب برای high-contention scenario ها اما throughput را کاهش می‌دهد.

**سوال:** یک distributed transaction بین چند microservice با database های جداگانه طراحی کنید (Saga pattern در مقابل 2PC).
**پاسخ:** Two-Phase Commit (2PC) یک consistency قوی می‌دهد اما blocking است و در microservice های مقیاس بزرگ scalability کمی دارد. Saga pattern یک sequence از local transaction هاست که هرکدام یک event منتشر می‌کنند؛ در صورت شکست، compensating transaction ها اجرا می‌شوند (choreography-based با Kafka یا orchestration-based با یک saga orchestrator). Saga معمولاً برای microservices ترجیح داده می‌شود چون loosely-coupled و scalable است.[^10]

---

## ۴. Caching

**سوال:** یک caching layer برای یک Java service با read زیاد طراحی کنید. local caching (Caffeine، Guava) را با distributed caching (Redis، Memcached) مقایسه کنید.
**پاسخ:** Local cache در heap همان JVM قرار دارد، latency بسیار پایینی دارد اما بین instance ها sync نیست و در horizontal scaling می‌تواند inconsistency ایجاد کند. Distributed cache (Redis) بین همه instance ها به اشتراک گذاشته می‌شود و consistency بهتری دارد اما یک network hop اضافه می‌کند. راه‌حل رایج ترکیبی است: یک local cache (Caffeine) به‌عنوان L1 برای hot data و Redis به‌عنوان L2.

**سوال:** eviction policy های cache (LRU، LFU، FIFO) را توضیح دهید و بگویید کی هرکدام را انتخاب می‌کنید.
**پاسخ:** LRU (Least Recently Used) آیتم‌هایی که اخیراً استفاده نشده‌اند را حذف می‌کند، مناسب اکثر use case ها. LFU (Least Frequently Used) بر اساس تعداد دفعات استفاده حذف می‌کند، مناسب زمانی که popularity پایدار است. FIFO ساده‌ترین است و بدون توجه به usage pattern، قدیمی‌ترین آیتم را حذف می‌کند، مناسب زمانی که پیچیدگی کمتر مهم‌تر از دقت است.

**سوال:** استراتژی‌های cache-aside، write-through و write-behind چیست؟ trade-off هرکدام؟
**پاسخ:** Cache-aside یعنی application ابتدا cache را چک می‌کند و در صورت miss، از database می‌خواند و cache را update می‌کند؛ ساده اما ممکن است stale data داشته باشد. Write-through یعنی هر write هم‌زمان به cache و database نوشته می‌شود، consistency بهتر اما write latency بیشتر. Write-behind یعنی write ابتدا در cache انجام و بعداً async به database نوشته می‌شود، throughput بالا اما ریسک data loss در صورت crash.

**سوال:** چطور از cache stampede / thundering herd جلوگیری می‌کنید؟
**پاسخ:** با استفاده از تکنیک‌هایی مثل lock/mutex (فقط یک thread اجازه دارد cache را از database پر کند و بقیه منتظر بمانند)، probabilistic early expiration، یا stale-while-revalidate (سرو کردن داده قدیمی هنگام refresh در پس‌زمینه).[^13]

**سوال:** چطور distributed cache را با source-of-truth database consistent نگه می‌دارید؟
**پاسخ:** با TTL مناسب برای expire خودکار، invalidation event ها (مثلاً از طریق Kafka وقتی رکورد database تغییر می‌کند) و write-through pattern. برای consistency قوی‌تر می‌توان از event-driven cache invalidation یا Change Data Capture (CDC) با ابزارهایی مثل Debezium استفاده کرد.[^14]

---

## ۵. Messaging، Queue ها و پردازش Asynchronous

**سوال:** چه زمانی از message queue (Kafka، RabbitMQ، SQS) به‌جای فراخوانی مستقیم synchronous بین سرویس‌ها استفاده می‌کنید؟
**پاسخ:** زمانی که نیاز به decoupling سرویس‌ها، مدیریت traffic spike، پردازش asynchronous یا اطمینان از تحویل پیام (حتی اگر consumer موقتاً down باشد) وجود دارد. مثلاً پردازش سفارش، ارسال notification، یا event-driven workflow ها.[^15]

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
	
	

[^6]:                 Create Short URL
	+--------+      +------------+      +------------------+
	| Client | ---> | API Server | ---> | ID Generator     |
	+--------+      +------------+      | (Auto Inc / UUID)|
	                                     +--------+---------+
	                                              |
	                                              v
	                                      Base62 Encoding
	                                              |
	                                              v
	                                   Short Code: "aZ91Kx"
	                                              |
	                                              v
	                     +-----------------------------------------+
	                     | Database                               |
	                     | shortCode -> Original Long URL         |
	                     +-----------------------------------------+
	
	
	                Redirect Request
	+--------+      +------------+
	| Client | ---> | API Server |
	+--------+      +------------+
	                      |
	                      v
	                +-----------+
	                |  Redis    |  (Cache)
	                +-----------+
	                 |        |
	          Cache Hit    Cache Miss
	             |             |
	             v             v
	      Return URL      Query Database
	             |             |
	             +-------> Store in Redis
	                           |
	                           v
	                   Return Original URL
	                   
	                   ### نکات مهم
	
	- **Hashing / Base62**
	    - اگر از **Auto Increment ID** استفاده شود:
	        
	        ```
	        1256789  → Base62 → "5Gt9"
	        ```
	        
	        مزیت: هیچ Collision ندارد و تولید بسیار سریع است.
	        
	    - اگر از **Hash** (مثل MD5 یا SHA-256) استفاده شود:
	        
	        ```
	        https://example.com/very/long/url
	                      ↓ MD5
	        9f86d081884...
	                      ↓
	                اولین 7 کاراکتر
	                      ↓
	                   a8F3kP2
	        ```
	        
	        چون Hash را کوتاه می‌کنیم، ممکن است دو URL یک Short Code مشابه تولید کنند (**Collision**)، بنابراین قبل از ذخیره باید بررسی شود و در صورت نیاز دوباره تولید گردد.

[^7]: **Eventual Consistency (سازگاری نهایی)** یکی از مدل‌های سازگاری در پایگاه‌داده‌های توزیع‌شده NoSQL است.
	
	تعریف ساده:
	
	> **بعد از اینکه داده‌ای نوشته (Write) شد، ممکن است همه سرورها بلافاصله مقدار جدید را نداشته باشند؛ اما اگر تغییر جدیدی روی آن داده انجام نشود، پس از مدت کوتاهی همه Replicaها به یک مقدار یکسان خواهند رسید.**
	
	---
	
	## با یک مثال
	
	فرض کنید اطلاعات یک کاربر روی سه سرور کپی شده است:
	
	```text
	          Update
	             |
	             v
	        +-----------+
	        | Primary   |
	        +-----------+
	         /    |    \
	        /     |     \
	      R1      R2     R3
	```
	
	کاربر نام خود را از **"علی"** به **"محمد"** تغییر می‌دهد.
	
	بلافاصله بعد از ثبت تغییر:
	
	```text
	Primary : محمد   ✅
	R1      : محمد   ✅
	R2      : علی    ❌
	R3      : علی    ❌
	```
	
	اگر کاربر دیگری در همین لحظه از **R2** اطلاعات را بخواند، هنوز مقدار قدیمی (**علی**) را مشاهده می‌کند.
	
	چند ثانیه بعد:
	
	```text
	Primary : محمد
	R1      : محمد
	R2      : محمد
	R3      : محمد
	```
	
	اکنون همه Replicaها یکسان شده‌اند؛ یعنی **Consistency برقرار شده است.**
	
	---
	
	## چرا این روش را انتخاب می‌کنند؟
	
	اگر سیستم مجبور باشد قبل از پاسخ دادن، منتظر بماند تا **همه Replicaها** آپدیت شوند:
	
	* سرعت نوشتن (Write) کاهش پیدا می‌کند.
	* اگر یکی از سرورها از دسترس خارج باشد، ممکن است کل سیستم نتواند درخواست را پاسخ دهد.
	
	در Eventual Consistency:
	
	1. درخواست Write سریع پذیرفته می‌شود.
	2. پاسخ به کاربر برمی‌گردد.
	3. سپس تغییرات در پس‌زمینه روی سایر Replicaها اعمال می‌شود.
	
	---
	
	## ارتباط با CAP Theorem
	
	در سیستم‌های توزیع‌شده هنگام بروز Partition، معمولاً باید بین این دو یکی را انتخاب کنیم:
	
	* **Consistency (C)** → همیشه جدیدترین داده را ببینیم.
	* **Availability (A)** → سیستم همیشه پاسخگو باشد.
	
	بسیاری از دیتابیس‌های NoSQL مانند **Cassandra** و **DynamoDB** در زمان Partition، **Availability** را ترجیح می‌دهند و بنابراین از **Eventual Consistency** استفاده می‌کنند.
	
	---
	
	## مثال واقعی
	
	فرض کنید در اینستاگرام عکس پروفایل خود را عوض می‌کنید.
	
	```
	12:00:00  عکس جدید را آپلود می‌کنید.
	12:00:01  خودتان عکس جدید را می‌بینید.
	12:00:02  دوستتان در اروپا هنوز عکس قدیمی را می‌بیند.
	12:00:04  او صفحه را رفرش می‌کند و عکس جدید نمایش داده می‌شود.
	```
	
	این چند ثانیه اختلاف، نمونه‌ای از **Eventual Consistency** است.
	
	---
	
	## چه زمانی مناسب است؟
	
	✅ مناسب برای:
	
	* شبکه‌های اجتماعی
	* تعداد لایک و بازدید
	* URL Shortener
	* DNS
	* سیستم‌های لاگ
	* کاتالوگ محصولات
	
	❌ مناسب نیست برای:
	
	* موجودی حساب بانکی
	* انتقال پول
	* خرید و فروش سهام
	* موجودی انبار (در صورت حساس بودن به فروش بیش از موجودی)
	
	---
	
	## Strong Consistency در مقابل Eventual Consistency
	
	| Strong Consistency                  | Eventual Consistency                                |
	| ----------------------------------- | --------------------------------------------------- |
	| همیشه آخرین داده نمایش داده می‌شود. | ممکن است برای مدت کوتاهی داده قدیمی نمایش داده شود. |
	| تأخیر بیشتر                         | تأخیر کمتر                                          |
	| Availability کمتر در زمان خطا       | Availability بیشتر                                  |
	| مناسب بانک‌ها                       | مناسب شبکه‌های اجتماعی و سرویس‌های مقیاس‌پذیر       |
	
	---
	
	## پاسخ مناسب مصاحبه (حدود ۳۰ ثانیه)
	
	> **Eventual Consistency** یعنی بعد از ثبت یک تغییر، ممکن است همه Replicaها بلافاصله به‌روز نشوند و برخی کاربران برای مدت کوتاهی داده قدیمی را مشاهده کنند. اما اگر تغییر جدیدی روی آن داده انجام نشود، تمام Replicaها در نهایت به یک وضعیت یکسان می‌رسند. این مدل باعث افزایش **Availability**، **مقیاس‌پذیری** و **سرعت Write** در سیستم‌های توزیع‌شده می‌شود و به همین دلیل در دیتابیس‌هایی مانند Cassandra و DynamoDB به‌کار می‌رود.
	

[^8]: منظور جمله این است که **مدلی که برای ثبت اطلاعات استفاده می‌کنیم، الزاماً همان مدلی نیست که برای خواندن و جست‌وجو استفاده می‌کنیم.**
	
	این الگو را معمولاً **CQRS** می‌نامند:
	
	> **Command Query Responsibility Segregation**  
	> جداسازی مسئولیت «نوشتن» از «خواندن»
	
	## تصویر کلی
	
	فرض کنید یک فروشگاه اینترنتی داریم:
	
	```text
	                    ┌──────────────────┐
	 POST /orders ────▶ │  Command Service │
	                    │  ثبت سفارش       │
	                    └────────┬─────────┘
	                             │
	                             ▼
	                    ┌──────────────────┐
	                    │ Write Database   │
	                    │ orders           │
	                    │ order_items      │
	                    │ payments         │
	                    └────────┬─────────┘
	                             │
	                      انتشار Event
	                       OrderCreated
	                             │
	                             ▼
	                    ┌──────────────────┐
	                    │ Kafka / RabbitMQ │
	                    └────────┬─────────┘
	                             │ Async
	                             ▼
	                    ┌──────────────────┐
	                    │ Read Model       │
	                    │ Updater          │
	                    └────────┬─────────┘
	                             │
	                             ▼
	                    ┌────────────────────────┐
	 GET /orders/123 ─▶ │ Read Database          │
	                    │ order_details          │
	                    │ آماده برای خواندن      │
	                    └────────────────────────┘
	```
	
	در این ساختار:
	
	- درخواست ایجاد یا تغییر سفارش وارد **Write Model** می‌شود.
	    
	- پس از ثبت موفق، یک Event مانند `OrderCreated` منتشر می‌شود.
	    
	- یک Consumer آن Event را به‌صورت asynchronous دریافت می‌کند.
	    
	- Consumer اطلاعات مناسب برای خواندن را در **Read Model** می‌سازد.
	    
	- درخواست‌های `GET` مستقیماً از Read Model پاسخ داده می‌شوند.
	    
	
	---
	
	# چرا دو مدل جدا داریم؟
	
	مدل مناسب برای نوشتن معمولاً نرمال‌شده است:
	
	```text
	orders
	--------
	id
	customer_id
	status
	created_at
	
	order_items
	-----------
	id
	order_id
	product_id
	quantity
	unit_price
	
	customers
	---------
	id
	name
	email
	
	products
	--------
	id
	name
	price
	```
	
	برای نمایش جزئیات سفارش باید چند جدول Join شوند:
	
	```sql
	SELECT
	    o.id,
	    o.status,
	    c.name,
	    c.email,
	    p.name,
	    oi.quantity,
	    oi.unit_price
	FROM orders o
	JOIN customers c ON c.id = o.customer_id
	JOIN order_items oi ON oi.order_id = o.id
	JOIN products p ON p.id = oi.product_id
	WHERE o.id = 123;
	```
	
	اگر این Query میلیون‌ها بار اجرا شود، ممکن است پرهزینه باشد.
	
	در Read Model می‌توان همان اطلاعات را از قبل آماده کرد:
	
	```text
	order_details
	------------------------------------------------
	order_id
	customer_name
	customer_email
	status
	total_amount
	items_json
	created_at
	```
	
	مثلاً:
	
	```json
	{
	  "orderId": 123,
	  "customerName": "Meysam Karimi",
	  "customerEmail": "meysam@example.com",
	  "status": "CREATED",
	  "totalAmount": 450000,
	  "items": [
	    {
	      "productName": "Keyboard",
	      "quantity": 1,
	      "price": 300000
	    },
	    {
	      "productName": "Mouse",
	      "quantity": 1,
	      "price": 150000
	    }
	  ]
	}
	```
	
	در نتیجه Query خواندن بسیار ساده می‌شود:
	
	```sql
	SELECT *
	FROM order_details
	WHERE order_id = 123;
	```
	
	یا Read Model حتی می‌تواند داخل Elasticsearch یا MongoDB باشد.
	
	---
	
	# منظور از Write-Optimized Table چیست؟
	
	جدول Write-optimized برای این طراحی می‌شود که:
	
	- Insert و Update سریع باشد.
	    
	- Constraintهای اصلی کسب‌وکار حفظ شوند.
	    
	- تراکنش‌ها قابل‌کنترل باشند.
	    
	- اطلاعات تکراری تا حد امکان کم باشد.
	    
	- عملیات نوشتن نیازی به Update کردن تعداد زیادی ستون و جدول نمایشی نداشته باشد.
	    
	
	مثلاً برای تغییر وضعیت سفارش:
	
	```sql
	UPDATE orders
	SET status = 'PAID'
	WHERE id = 123;
	```
	
	اما در مدل خواندنی ممکن است اطلاعات تکراری داشته باشیم:
	
	```text
	customer_order_list
	----------------------------------------
	order_id
	customer_id
	customer_name
	status
	total_amount
	item_count
	last_updated_at
	```
	
	تکرار `customer_name` در Read Model اشکالی ندارد؛ چون هدف اصلی آن **سریع خواندن** است، نه جلوگیری کامل از تکرار داده.
	
	---
	
	# نمونه پیاده‌سازی با Spring Boot و Kafka
	
	## ۱. دریافت Command
	
	```java
	public record CreateOrderCommand(
	        Long customerId,
	        List<CreateOrderItem> items
	) {
	}
	
	public record CreateOrderItem(
	        Long productId,
	        int quantity
	) {
	}
	```
	
	Controller:
	
	```java
	@RestController
	@RequestMapping("/orders")
	@RequiredArgsConstructor
	public class OrderCommandController {
	
	    private final OrderCommandService orderCommandService;
	
	    @PostMapping
	    public ResponseEntity<CreateOrderResponse> createOrder(
	            @RequestBody CreateOrderCommand command
	    ) {
	        Long orderId = orderCommandService.create(command);
	
	        return ResponseEntity
	                .accepted()
	                .body(new CreateOrderResponse(orderId));
	    }
	}
	```
	
	---
	
	## ۲. ذخیره در Write Model
	
	```java
	@Service
	@RequiredArgsConstructor
	public class OrderCommandService {
	
	    private final OrderRepository orderRepository;
	    private final KafkaTemplate<String, OrderCreatedEvent> kafkaTemplate;
	
	    @Transactional
	    public Long create(CreateOrderCommand command) {
	        Order order = new Order();
	        order.setCustomerId(command.customerId());
	        order.setStatus(OrderStatus.CREATED);
	        order.setCreatedAt(Instant.now());
	
	        for (CreateOrderItem item : command.items()) {
	            order.addItem(
	                    item.productId(),
	                    item.quantity()
	            );
	        }
	
	        Order savedOrder = orderRepository.save(order);
	
	        OrderCreatedEvent event =
	                OrderCreatedEvent.from(savedOrder);
	
	        kafkaTemplate.send(
	                "order-created",
	                savedOrder.getId().toString(),
	                event
	        );
	
	        return savedOrder.getId();
	    }
	}
	```
	
	Event:
	
	```java
	public record OrderCreatedEvent(
	        Long orderId,
	        Long customerId,
	        String status,
	        Instant createdAt,
	        List<OrderItemEvent> items
	) {
	    public static OrderCreatedEvent from(Order order) {
	        List<OrderItemEvent> items = order.getItems()
	                .stream()
	                .map(item -> new OrderItemEvent(
	                        item.getProductId(),
	                        item.getQuantity(),
	                        item.getUnitPrice()
	                ))
	                .toList();
	
	        return new OrderCreatedEvent(
	                order.getId(),
	                order.getCustomerId(),
	                order.getStatus().name(),
	                order.getCreatedAt(),
	                items
	        );
	    }
	}
	```
	
	---
	
	## ۳. همگام‌سازی Async با Read Model
	
	```java
	@Component
	@RequiredArgsConstructor
	public class OrderReadModelConsumer {
	
	    private final OrderReadRepository orderReadRepository;
	    private final CustomerRepository customerRepository;
	    private final ProductRepository productRepository;
	
	    @KafkaListener(
	            topics = "order-created",
	            groupId = "order-read-model"
	    )
	    public void handle(OrderCreatedEvent event) {
	
	        Customer customer = customerRepository
	                .findById(event.customerId())
	                .orElseThrow();
	
	        List<OrderReadItem> items = event.items()
	                .stream()
	                .map(item -> {
	                    Product product = productRepository
	                            .findById(item.productId())
	                            .orElseThrow();
	
	                    return new OrderReadItem(
	                            product.getName(),
	                            item.quantity(),
	                            item.unitPrice()
	                    );
	                })
	                .toList();
	
	        BigDecimal totalAmount = items.stream()
	                .map(item -> item.unitPrice()
	                        .multiply(BigDecimal.valueOf(item.quantity())))
	                .reduce(BigDecimal.ZERO, BigDecimal::add);
	
	        OrderReadModel readModel = new OrderReadModel(
	                event.orderId(),
	                customer.getName(),
	                customer.getEmail(),
	                event.status(),
	                totalAmount,
	                items,
	                event.createdAt()
	        );
	
	        orderReadRepository.save(readModel);
	    }
	}
	```
	
	این Consumer می‌تواند اطلاعات را در MongoDB ذخیره کند:
	
	```java
	@Document("order_details")
	public record OrderReadModel(
	        @Id Long orderId,
	        String customerName,
	        String customerEmail,
	        String status,
	        BigDecimal totalAmount,
	        List<OrderReadItem> items,
	        Instant createdAt
	) {
	}
	```
	
	---
	
	## ۴. خواندن از Read Model
	
	```java
	@RestController
	@RequestMapping("/orders")
	@RequiredArgsConstructor
	public class OrderQueryController {
	
	    private final OrderReadRepository orderReadRepository;
	
	    @GetMapping("/{orderId}")
	    public ResponseEntity<OrderReadModel> getOrder(
	            @PathVariable Long orderId
	    ) {
	        return orderReadRepository
	                .findById(orderId)
	                .map(ResponseEntity::ok)
	                .orElseGet(() -> ResponseEntity.notFound().build());
	    }
	}
	```
	
	در اینجا درخواست `GET` دیگر به جداول اصلی `orders` و `order_items` مراجعه نمی‌کند.
	
	---
	
	# جریان زمانی درخواست
	
	```text
	زمان T1
	کاربر سفارش ایجاد می‌کند.
	
	POST /orders
	       │
	       ▼
	Write DB:
	Order 123 = CREATED
	       │
	       ▼
	پاسخ:
	202 Accepted
	orderId = 123
	```
	
	کمی بعد:
	
	```text
	زمان T2
	Kafka Event مصرف می‌شود.
	
	OrderCreated
	      │
	      ▼
	Read Model ساخته می‌شود.
	
	order_details:
	Order 123 = CREATED
	```
	
	در فاصله بین `T1` و `T2` ممکن است این اتفاق بیفتد:
	
	```text
	POST /orders       → موفق
	GET /orders/123    → هنوز پیدا نشد
	```
	
	چون Read Model هنوز Update نشده است.
	
	چند میلی‌ثانیه یا چند ثانیه بعد:
	
	```text
	GET /orders/123    → سفارش برگردانده می‌شود
	```
	
	این رفتار را **Eventual Consistency** می‌نامیم.
	
	---
	
	# مشکل مهم: گم‌شدن Event
	
	کد زیر ریسک دارد:
	
	```java
	orderRepository.save(order);
	kafkaTemplate.send("order-created", event);
	```
	
	ممکن است:
	
	1. سفارش در دیتابیس ثبت شود.
	    
	2. برنامه قبل از ارسال Event کرش کند.
	    
	3. Read Model هرگز از سفارش جدید مطلع نشود.
	    
	
	```text
	Write DB               Kafka
	   │                     │
	   │ Order saved         │
	   │                     │
	   └── Application crash X
	                         │
	                    Event not sent
	```
	
	راه‌حل رایج، **Transactional Outbox Pattern** است.
	
	---
	
	# CQRS همراه با Outbox
	
	در همان Transaction، هم سفارش و هم Event در دیتابیس ذخیره می‌شوند:
	
	```text
	┌──────────────── Database Transaction ────────────────┐
	│                                                     │
	│  INSERT INTO orders ...                             │
	│                                                     │
	│  INSERT INTO outbox_events                          │
	│      (event_type, aggregate_id, payload)             │
	│                                                     │
	└────────────────── Commit ────────────────────────────┘
	```
	
	سپس یک Worker رکوردهای Outbox را به Kafka ارسال می‌کند:
	
	```text
	Write Database
	 ├── orders
	 └── outbox_events
	          │
	          ▼
	    Outbox Publisher
	          │
	          ▼
	         Kafka
	          │
	          ▼
	      Read Model
	```
	
	نمونه ساده:
	
	```java
	@Transactional
	public Long create(CreateOrderCommand command) {
	    Order order = buildOrder(command);
	    orderRepository.save(order);
	
	    OrderCreatedEvent event = OrderCreatedEvent.from(order);
	
	    OutboxEvent outboxEvent = new OutboxEvent(
	            UUID.randomUUID(),
	            "Order",
	            order.getId().toString(),
	            "OrderCreated",
	            serialize(event),
	            Instant.now(),
	            OutboxStatus.PENDING
	    );
	
	    outboxRepository.save(outboxEvent);
	
	    return order.getId();
	}
	```
	
	Publisher:
	
	```java
	@Scheduled(fixedDelay = 1000)
	public void publishPendingEvents() {
	    List<OutboxEvent> events =
	            outboxRepository.findPendingEvents();
	
	    for (OutboxEvent event : events) {
	        try {
	            kafkaTemplate.send(
	                    event.getEventType(),
	                    event.getAggregateId(),
	                    event.getPayload()
	            ).get();
	
	            event.markAsPublished();
	            outboxRepository.save(event);
	
	        } catch (Exception exception) {
	            event.increaseRetryCount();
	            outboxRepository.save(event);
	        }
	    }
	}
	```
	
	---
	
	# Consumer باید Idempotent باشد
	
	ممکن است یک Event بیشتر از یک بار دریافت شود:
	
	```text
	OrderCreated Event
	       │
	       ├────▶ Consumer اجرا شد
	       │
	       └────▶ همان Event دوباره ارسال شد
	```
	
	اگر Consumer بدون کنترل دو بار Insert کند، داده تکراری ایجاد می‌شود.
	
	برای جلوگیری، `eventId` را ذخیره می‌کنیم:
	
	```java
	@Transactional
	@KafkaListener(topics = "order-created")
	public void handle(OrderCreatedEvent event) {
	
	    if (processedEventRepository.existsById(event.eventId())) {
	        return;
	    }
	
	    updateReadModel(event);
	
	    processedEventRepository.save(
	            new ProcessedEvent(
	                    event.eventId(),
	                    Instant.now()
	            )
	    );
	}
	```
	
	یا از Upsert استفاده می‌کنیم:
	
	```sql
	INSERT INTO order_details (
	    order_id,
	    status,
	    total_amount
	)
	VALUES (
	    123,
	    'CREATED',
	    450000
	)
	ON CONFLICT (order_id)
	DO UPDATE SET
	    status = EXCLUDED.status,
	    total_amount = EXCLUDED.total_amount;
	```
	
	---
	
	# آیا همیشه دو دیتابیس لازم است؟
	
	خیر. سه حالت متداول داریم.
	
	### حالت ساده: یک دیتابیس و دو جدول
	
	```text
	PostgreSQL
	 ├── orders             ← Write Model
	 ├── order_items        ← Write Model
	 └── order_details      ← Read Model
	```
	
	### حالت متوسط: یک نوع دیتابیس، دو Instance
	
	```text
	PostgreSQL Write DB
	        │
	        ▼
	      Kafka
	        │
	        ▼
	PostgreSQL Read DB
	```
	
	### حالت پیشرفته: دیتابیس متفاوت برای هر نیاز
	
	```text
	Write Model: PostgreSQL
	       │
	       ▼
	     Kafka
	       │
	       ├──▶ Elasticsearch  برای جست‌وجو
	       ├──▶ Redis          برای پاسخ سریع
	       └──▶ MongoDB        برای Document View
	```
	
	مثلاً:
	
	```text
	ثبت سفارش و تراکنش مالی  → PostgreSQL
	جست‌وجوی سفارش‌ها         → Elasticsearch
	نمایش سفارش پرتکرار       → Redis
	```
	
	---
	
	# چه زمانی CQRS مناسب است؟
	
	CQRS زمانی مفید است که:
	
	- تعداد Read بسیار بیشتر از Write باشد.
	    
	- Queryها Join و محاسبات سنگین داشته باشند.
	    
	- شکل داده برای Write و Read بسیار متفاوت باشد.
	    
	- سیستم Event-driven باشد.
	    
	- بتوانیم کمی تأخیر در نمایش داده را تحمل کنیم.
	    
	- Read Modelهای مختلفی برای کاربردهای مختلف نیاز داشته باشیم.
	    
	
	برای مثال:
	
	```text
	یک سفارش ثبت می‌شود
	       │
	       ├──▶ Read Model صفحه جزئیات سفارش
	       ├──▶ Read Model پنل مدیریتی
	       ├──▶ Read Model گزارش مالی
	       ├──▶ Elasticsearch برای جست‌وجو
	       └──▶ Notification Service
	```
	
	---
	
	# چه زمانی CQRS انتخاب خوبی نیست؟
	
	برای یک سیستم CRUD ساده، CQRS ممکن است پیچیدگی غیرضروری ایجاد کند:
	
	```text
	بدون CQRS:
	
	Controller
	   │
	Service
	   │
	PostgreSQL
	```
	
	در مقابل:
	
	```text
	با CQRS:
	
	Command API
	Write Model
	Outbox
	Message Broker
	Consumer
	Read Model
	Retry
	DLQ
	Idempotency
	Monitoring
	Reconciliation
	```
	
	بنابراین هزینه‌هایی مانند این‌ها اضافه می‌شوند:
	
	- Eventual consistency
	    
	- مدیریت Eventهای تکراری
	    
	- Retry و DLQ
	    
	- ترتیب Eventها
	    
	- بازسازی Read Model
	    
	- مانیتورینگ Lag
	    
	- Versioning رویدادها
	    
	
	---
	
	## خلاصه بسیار ساده
	
	```text
	بدون CQRS:
	
	Write ──▶ Database ◀── Read
	```
	
	```text
	با CQRS:
	
	Write ──▶ Write Model
	              │
	              │ Event
	              ▼
	         Message Broker
	              │
	              ▼
	Read  ◀── Read Model
	```
	
	یعنی:
	
	> اطلاعات را در ساختاری ثبت می‌کنیم که برای صحت تراکنش و Write مناسب است؛ سپس به‌صورت asynchronous نسخه‌ای از آن را در ساختاری قرار می‌دهیم که برای Query و نمایش سریع بهینه شده است.

[^9]: 

[^10]: حتماً. بهترین راه فهمش این است که یک مثال واقعی بزنیم.
	
	فرض کن در یک فروشگاه اینترنتی برای ثبت سفارش، سه Microservice داریم و **هرکدام دیتابیس مستقل خودشان را دارند**:
	
	```text
	Order Service          Payment Service        Inventory Service
	     │                        │                       │
	     ▼                        ▼                       ▼
	 Order DB                 Payment DB              Stock DB
	```
	
	وقتی کاربر سفارش می‌دهد، باید هر سه کار انجام شوند:
	
	```text
	1. Order ساخته شود
	2. پول پرداخت شود
	3. موجودی کالا کم شود
	```
	
	مشکل اینجاست که اگر مرحله 1 و 2 موفق شوند ولی مرحله 3 شکست بخورد چه کنیم؟
	
	```text
	Create Order ✅
	     │
	     ▼
	Payment ✅
	     │
	     ▼
	Reserve Stock ❌
	
	حالا چه؟
	پول گرفته شده ولی کالا موجود نیست!
	```
	
	اینجاست که **Distributed Transaction** مطرح می‌شود.
	
	## روش اول: 2PC — Two Phase Commit
	
	در 2PC یک Coordinator مرکزی داریم که به همه Databaseها می‌گوید:
	
	> فعلاً تغییرات را آماده کنید، ولی هنوز Commit نکنید.
	
	شکل ساده:
	
	```text
	                  Transaction Coordinator
	                         │
	          ┌──────────────┼──────────────┐
	          ▼              ▼              ▼
	      Order DB       Payment DB      Stock DB
	```
	
	### Phase 1: Prepare
	
	Coordinator از همه می‌پرسد:
	
	```text
	Coordinator
	    │
	    ├──► Order DB     : Can you commit?
	    │                  ◄── YES
	    │
	    ├──► Payment DB   : Can you commit?
	    │                  ◄── YES
	    │
	    └──► Stock DB     : Can you commit?
	                       ◄── YES
	```
	
	در این مرحله transactionها هنوز کامل Commit نشده‌اند و ممکن است resource/lock نگه داشته شود.
	
	اگر همه بگویند YES:
	
	### Phase 2: Commit
	
	```text
	Coordinator
	    │
	    ├──► Order DB       COMMIT ✅
	    ├──► Payment DB     COMMIT ✅
	    └──► Stock DB       COMMIT ✅
	```
	
	ولی اگر یکی بگوید NO:
	
	```text
	Order DB       → YES
	Payment DB     → YES
	Stock DB       → NO ❌
	
	              Coordinator
	                   │
	                   ▼
	             ROLLBACK ALL
	```
	
	یعنی:
	
	```text
	Order    ❌ rollback
	Payment  ❌ rollback
	Stock    ❌ rollback
	```
	
	مزیت 2PC این است که از دید تراکنش، **همه با هم موفق می‌شوند یا همه با هم rollback می‌شوند**.
	
	اما مشکل اصلی این است که سرویس‌ها باید منتظر یکدیگر بمانند:
	
	```text
	Service A
	   │
	   │ WAIT...
	   │
	Service B
	   │
	   │ WAIT...
	   │
	Service C
	```
	
	اگر Coordinator یا یکی از Participantها دچار مشکل شود، transaction ممکن است مدت بیشتری در وضعیت نامشخص/منتظر باقی بماند و lock یا resource درگیر شود.
	
	پس در Microserviceهای بزرگ معمولاً این وابستگی مطلوب نیست.
	
	---
	
	# روش دوم: Saga Pattern
	
	Saga یک ایده متفاوت دارد:
	
	> به جای اینکه یک Transaction بزرگ داشته باشیم، چند **Local Transaction** مستقل داریم.
	
	مثلاً:
	
	```text
	Order Service
	    │
	    ▼
	Create Order
	Order DB COMMIT ✅
	    │
	    ▼
	OrderCreated Event
	    │
	    ▼
	Payment Service
	    │
	    ▼
	Take Payment
	Payment DB COMMIT ✅
	    │
	    ▼
	PaymentCompleted Event
	    │
	    ▼
	Inventory Service
	    │
	    ▼
	Reserve Stock
	Stock DB COMMIT ✅
	```
	
	نکته مهم این است که وقتی `Order Service` کارش را Commit کرد، دیگر transaction دیتابیس آن باز نمی‌ماند.
	
	```text
	Order DB
	BEGIN
	INSERT...
	COMMIT ✅
	
	تمام شد.
	```
	
	بعد Payment Service transaction خودش را دارد:
	
	```text
	Payment DB
	BEGIN
	PAY...
	COMMIT ✅
	```
	
	بنابراین:
	
	```text
	یک Transaction بزرگ نداریم.
	
	بلکه:
	
	Transaction 1 ✅
	      ↓
	Transaction 2 ✅
	      ↓
	Transaction 3 ✅
	```
	
	حالا سؤال مهم:
	
	**اگر Transaction سوم Fail شود چه؟**
	
	مثلاً:
	
	```text
	Create Order ✅
	      │
	      ▼
	Take Payment ✅
	      │
	      ▼
	Reserve Stock ❌
	```
	
	Saga می‌گوید transactionهای قبلی را DB-level rollback نمی‌کنیم؛ چون قبلاً Commit شده‌اند.
	
	در عوض عملیات جبرانی یا **Compensating Transaction** انجام می‌دهیم.
	
	مثلاً:
	
	```text
	Reserve Stock ❌
	      │
	      ▼
	Refund Payment
	      │
	      ▼
	Cancel Order
	```
	
	در نتیجه:
	
	```text
	Create Order ✅
	Take Payment ✅
	Reserve Stock ❌
	
	       ↓ compensation
	
	Refund Payment ✅
	Cancel Order ✅
	```
	
	پس جریان کامل می‌تواند چیزی شبیه این باشد:
	
	```text
	NORMAL FLOW
	===========
	
	[Create Order]
	      │
	      ▼
	[Take Payment]
	      │
	      ▼
	[Reserve Stock]
	      │
	      ▼
	[Ship Order]
	
	        ✅ Success
	
	
	FAILURE FLOW
	============
	
	[Create Order] ✅
	      │
	      ▼
	[Take Payment] ✅
	      │
	      ▼
	[Reserve Stock] ❌
	      │
	      ▼
	[Refund Payment]
	      │
	      ▼
	[Cancel Order]
	```
	
	این بخش، قلب Saga است:
	
	```text
	Transaction             Compensation
	-----------             ------------
	
	Create Order      <-->  Cancel Order
	
	Take Payment      <-->  Refund Payment
	
	Reserve Stock     <-->  Release Stock
	
	Create Shipment   <-->  Cancel Shipment
	```
	
	---
	
	# دو نوع Saga داریم
	
	## 1. Choreography
	
	در این حالت Coordinator مرکزی نداریم.
	
	هر سرویس Event منتشر می‌کند و سرویس بعدی آن Event را می‌شنود.
	
	مثلاً با Kafka:
	
	```text
	                  Kafka
	
	Order Service
	     │
	     │ OrderCreated
	     ▼
	   Kafka
	     │
	     ▼
	Payment Service
	     │
	     │ PaymentCompleted
	     ▼
	   Kafka
	     │
	     ▼
	Inventory Service
	     │
	     │ StockReserved
	     ▼
	   Kafka
	     │
	     ▼
	Shipping Service
	```
	
	هر سرویس فقط می‌داند:
	
	> وقتی فلان event آمد، من کار خودم را انجام می‌دهم.
	
	مثلاً:
	
	```text
	OrderCreated
	     │
	     ▼
	Payment Service
	
	PaymentCompleted
	     │
	     ▼
	Inventory Service
	
	StockReserved
	     │
	     ▼
	Shipping Service
	```
	
	اگر Inventory شکست بخورد:
	
	```text
	Inventory Service
	      │
	      │ StockReservationFailed
	      ▼
	    Kafka
	      │
	      ▼
	Payment Service
	      │
	      ▼
	Refund()
	```
	
	مزیت:
	
	```text
	Loosely coupled
	Scalable
	No central coordinator
	```
	
	اما اگر Saga خیلی بزرگ شود، دنبال کردن جریان Events ممکن است سخت شود:
	
	```text
	Service A ──event──► Service B
	    ▲                    │
	    │                    event
	 event                   ▼
	Service D ◄──event── Service C
	
	          😵
	```
	
	گاهی به این پیچیدگی اصطلاحاً **event spaghetti** می‌گویند.
	
	---
	
	# 2. Orchestration
	
	اینجا یک سرویس مخصوص داریم:
	
	```text
	             Saga Orchestrator
	                    │
	          ┌─────────┼─────────┐
	          ▼         ▼         ▼
	       Order      Payment   Inventory
	       Service    Service    Service
	```
	
	Orchestrator تصمیم می‌گیرد قدم بعدی چیست.
	
	مثلاً:
	
	```text
	Saga Orchestrator
	       │
	       ├──► Order Service
	       │        Create Order ✅
	       │
	       ├──► Payment Service
	       │        Pay ✅
	       │
	       ├──► Inventory Service
	       │        Reserve ❌
	       │
	       ├──► Payment Service
	       │        Refund ✅
	       │
	       └──► Order Service
	                Cancel ✅
	```
	
	یعنی منطق Workflow در یک جا مشخص است:
	
	```text
	START
	  │
	  ▼
	Create Order
	  │
	  ▼
	Payment
	  │
	  ├── failure ──► Cancel Order
	  │
	  ▼
	Reserve Stock
	  │
	  ├── failure ──► Refund ──► Cancel Order
	  │
	  ▼
	Shipping
	  │
	  ▼
	DONE
	```
	
	این روش معمولاً برای Sagaهای پیچیده قابل فهم‌تر است.
	
	---
	
	# تفاوت اصلی Saga و 2PC
	
	به این شکل به خاطر بسپار:
	
	### 2PC
	
	```text
	        ONE BIG TRANSACTION
	
	        ┌─────────────────────────────┐
	        │                             │
	Order DB → Payment DB → Inventory DB
	        │                             │
	        └──── COMMIT / ROLLBACK ──────┘
	
	همه باید با هم تصمیم بگیرند.
	```
	
	### Saga
	
	```text
	        MULTIPLE SMALL TRANSACTIONS
	
	Order Tx ✅
	    │
	    ▼
	Payment Tx ✅
	    │
	    ▼
	Inventory Tx ❌
	    │
	    ▼
	Compensate
	    │
	    ├── Refund Payment
	    └── Cancel Order
	```
	
	مقایسه خلاصه:
	
	|ویژگی|2PC|Saga|
	|---|---|---|
	|Transaction|یک distributed transaction|چند local transaction|
	|Consistency|قوی‌تر / atomic commit|معمولاً eventual consistency|
	|Lock/Resource holding|ممکن است طولانی‌تر باشد|هر local transaction سریع Commit می‌شود|
	|Coupling|بیشتر|کمتر|
	|Scalability|معمولاً ضعیف‌تر|بهتر|
	|Failure handling|Rollback|Compensation|
	|Microservices|کمتر رایج|بسیار رایج|
	|Kafka|الزامی نیست|در Choreography رایج است|
	
	### جمله‌ای که برای مصاحبه خیلی خوب است
	
	**2PC می‌گوید:**
	
	```text
	یا همه را همین الان Commit کن،
	یا هیچ‌کدام را Commit نکن.
	```
	
	**Saga می‌گوید:**
	
	```text
	هر مرحله را جداگانه Commit کن.
	
	اگر بعداً مشکلی پیش آمد،
	اثرات مراحل قبلی را با
	Compensating Transactions
	خنثی کن.
	```
	
	و مهم‌ترین نکته مفهومی Saga این است که **Compensation الزاماً همان Database Rollback نیست**. مثلاً اگر پول واقعاً از کارت مشتری گرفته شده باشد، دیگر `ROLLBACK` دیتابیس معنی ندارد؛ باید یک عملیات تجاری جدید یعنی `Refund` انجام دهیم.
	
	```text
	Payment = $100 ✅
	       ↓
	Stock failed ❌
	       ↓
	Refund $100 ✅
	
	Refund یک Transaction جدید است،
	نه rollback تراکنش قبلی.
	```
	
	برای مصاحبه‌های Senior Java/Microservices، بعد از این مفهوم معمولاً سؤال مهم بعدی **Transactional Outbox Pattern** [^11]است؛ چون مصاحبه‌گر می‌پرسد: «اگر DB commit شد ولی قبل از publish کردن Kafka event سرویس crash کرد چه؟» که دقیقاً یکی از مشکلات مهم پیاده‌سازی Saga است.
	منظورم این است که در Saga، هر Microservice فقط transaction مربوط به **دیتابیس خودش** را باز می‌کند و همان‌جا Commit می‌کند؛ transaction باز نمی‌ماند تا سرویس‌های بعدی هم کارشان تمام شود.


[^11]: Transactional Outbox Pattern برای حل این مشکل است:
	
	فرض کن در یک Microservice هم باید دیتابیس را تغییر بدهی، هم یک Event به Kafka بفرستی.
	
	```text
	Order Service
	    │
	    ├──► Order DB
	    │
	    └──► Kafka
	```
	
	مشکل اینجاست که این دو کار در یک transaction مشترک نیستند.
	
	مثلاً:
	
	```text
	1. Order در DB ذخیره شد ✅
	2. برنامه Crash کرد 💥
	3. Event به Kafka نرفت ❌
	```
	
	در نتیجه:
	
	```text
	Order DB:
	
	order_id=100
	status=CREATED     ✅
	
	Kafka:
	
	OrderCreated event ❌
	```
	
	پس Payment Service اصلاً متوجه ایجاد سفارش نمی‌شود.
	
	Transactional Outbox می‌گوید:
	
	> Event را مستقیماً به Kafka نفرست. اول خود Event را داخل همان Database و داخل همان transaction ذخیره کن.
	
	مثلاً دو جدول داریم:
	
	```text
	Order Database
	│
	├── orders
	│
	└── outbox_events
	```
	
	وقتی Order ساخته می‌شود:
	
	```text
	BEGIN TRANSACTION
	
	INSERT INTO orders ...
	
	INSERT INTO outbox_events ...
	
	COMMIT
	```
	
	یعنی:
	
	```text
	       یک Local Transaction
	┌──────────────────────────────┐
	│                              │
	│  INSERT orders               │
	│         +                    │
	│  INSERT outbox_events        │
	│                              │
	└──────────────┬───────────────┘
	               │
	             COMMIT
	               │
	               ▼
	              ✅
	```
	
	یا هر دو موفق می‌شوند:
	
	```text
	Order saved ✅
	Event saved ✅
	```
	
	یا هر دو rollback می‌شوند:
	
	```text
	Order saved ❌
	Event saved ❌
	```
	
	این همان تضمینی است که می‌خواهیم.
	
	---
	
	### با Spring Boot
	
	مثلاً Entity مربوط به Outbox:
	
	```java
	@Entity
	@Table(name = "outbox_events")
	@Getter
	@Setter
	public class OutboxEvent {
	
	    @Id
	    @GeneratedValue(strategy = GenerationType.IDENTITY)
	    private Long id;
	
	    private String aggregateType;
	
	    private Long aggregateId;
	
	    private String eventType;
	
	    @Column(columnDefinition = "TEXT")
	    private String payload;
	
	    private LocalDateTime createdAt;
	
	    private boolean published;
	}
	```
	
	و Service:
	
	```java
	@Service
	@RequiredArgsConstructor
	public class OrderService {
	
	    private final OrderRepository orderRepository;
	    private final OutboxEventRepository outboxRepository;
	    private final ObjectMapper objectMapper;
	
	    @Transactional
	    public Long createOrder(CreateOrderRequest request)
	            throws JsonProcessingException {
	
	        Order order = new Order();
	        order.setCustomerId(request.customerId());
	        order.setAmount(request.amount());
	        order.setStatus(OrderStatus.CREATED);
	
	        orderRepository.save(order);
	
	        OrderCreatedEvent event =
	            new OrderCreatedEvent(
	                order.getId(),
	                request.customerId(),
	                request.amount()
	            );
	
	        OutboxEvent outbox = new OutboxEvent();
	
	        outbox.setAggregateType("Order");
	        outbox.setAggregateId(order.getId());
	        outbox.setEventType("OrderCreated");
	        outbox.setPayload(
	            objectMapper.writeValueAsString(event)
	        );
	        outbox.setCreatedAt(LocalDateTime.now());
	        outbox.setPublished(false);
	
	        outboxRepository.save(outbox);
	
	        return order.getId();
	    }
	}
	```
	
	نکته مهم همین `@Transactional` است:
	
	```text
	@Transactional
	createOrder()
	
	     │
	     ├── save Order
	     │
	     └── save OutboxEvent
	
	             │
	             ▼
	          COMMIT
	```
	
	چون `orders` و `outbox_events` داخل یک Database هستند، Spring می‌تواند هر دو را در یک transaction واقعی انجام دهد.
	
	---
	
	بعد یک process جداگانه Eventهای Outbox را برمی‌دارد و به Kafka ارسال می‌کند.
	
	مثلاً ساده‌ترین روش:
	
	```java
	@Service
	@RequiredArgsConstructor
	public class OutboxPublisher {
	
	    private final OutboxEventRepository outboxRepository;
	    private final KafkaTemplate<String, String> kafkaTemplate;
	
	    @Scheduled(fixedDelay = 1000)
	    public void publishEvents() {
	
	        List<OutboxEvent> events =
	            outboxRepository.findByPublishedFalse();
	
	        for (OutboxEvent event : events) {
	
	            kafkaTemplate.send(
	                "order-events",
	                event.getAggregateId().toString(),
	                event.getPayload()
	            );
	
	            event.setPublished(true);
	
	            outboxRepository.save(event);
	        }
	    }
	}
	```
	
	جریان:
	
	```text
	Order Service
	     │
	     ▼
	┌──────────────────────┐
	│ Order DB             │
	│                      │
	│ orders               │
	│ outbox_events        │
	└──────────┬───────────┘
	           │
	           │ polling
	           ▼
	    Outbox Publisher
	           │
	           ▼
	         Kafka
	           │
	           ▼
	    Payment Service
	```
	
	مثلاً بعد از createOrder دیتابیس این شکلی است:
	
	```text
	orders
	+-----+----------+
	| id  | status   |
	+-----+----------+
	| 100 | CREATED  |
	+-----+----------+
	
	
	outbox_events
	+----+---------+--------------+-----------+
	| id | agg_id  | event_type   | published |
	+----+---------+--------------+-----------+
	| 51 | 100     | OrderCreated | false     |
	+----+---------+--------------+-----------+
	```
	
	Publisher بعداً Event را می‌فرستد:
	
	```text
	outbox_events
	     │
	     │ OrderCreated
	     ▼
	Kafka
	     │
	     ▼
	Payment Service
	```
	
	بعد:
	
	```text
	published = true
	```
	
	---
	
	### اگر سرویس بعد از Commit کردن Crash کند چه؟
	
	این حالت را ببین:
	
	```text
	@Transactional
	
	INSERT Order       ✅
	
	INSERT OutboxEvent ✅
	
	COMMIT             ✅
	
	        │
	        ▼
	
	     CRASH 💥
	```
	
	مشکلی نیست.
	
	چون Event هنوز داخل DB است:
	
	```text
	outbox_events
	
	OrderCreated
	published=false
	```
	
	وقتی برنامه دوباره بالا بیاید:
	
	```text
	Publisher
	    │
	    ▼
	find unpublished events
	    │
	    ▼
	Kafka
	```
	
	پس Event گم نمی‌شود.
	
	---
	
	### مشکل اصلی بدون Outbox
	
	این کد خطرناک است:
	
	```java
	@Transactional
	public void createOrder(...) {
	
	    orderRepository.save(order);
	
	    kafkaTemplate.send(
	        "order-events",
	        event
	    );
	}
	```
	
	چون دو سیستم مختلف داریم:
	
	```text
	PostgreSQL
	    +
	Kafka
	```
	
	ممکن است:
	
	```text
	DB COMMIT ✅
	Kafka send ❌
	```
	
	یا حالت‌های پیچیده دیگری رخ دهد.
	
	Transactional Outbox این را تبدیل می‌کند به:
	
	```text
	PostgreSQL Transaction:
	
	Order
	  +
	Event
	
	COMMIT ✅
	```
	
	و ارسال Kafka بعداً انجام می‌شود.
	
	---
	
	### در سیستم واقعی معمولاً دو روش Publish داریم
	
	روش ساده Polling است:
	
	```text
	outbox table
	     │
	     │ SELECT every 1 sec
	     ▼
	Publisher
	     │
	     ▼
	Kafka
	```
	
	مثلاً با:
	
	```java
	@Scheduled
	```
	
	ولی در سیستم‌های بزرگ‌تر معمولاً از **CDC — Change Data Capture** استفاده می‌کنند.
	
	یکی از ابزارهای معروف:
	
	```text
	Debezium
	```
	
	معماری:
	
	```text
	Order Service
	     │
	     ▼
	PostgreSQL
	│
	├── orders
	│
	└── outbox_events
	       │
	       │ DB transaction log
	       ▼
	    Debezium
	       │
	       ▼
	      Kafka
	       │
	       ▼
	Payment Service
	```
	
	یعنی Debezium تغییرات جدول Outbox را از WAL / transaction log دیتابیس می‌خواند و به Kafka می‌فرستد.
	
	مزیتش این است که دیگر لازم نیست دائماً:
	
	```sql
	SELECT *
	FROM outbox_events
	WHERE published = false;
	```
	
	اجرا کنیم.
	
	---
	
	### ارتباطش با Saga
	
	حالا Saga ما خیلی مطمئن‌تر می‌شود:
	
	```text
	Order Service
	      │
	      │ Transaction
	      ▼
	
	Order DB
	┌────────────────────────┐
	│ Order = CREATED        │
	│ Outbox = OrderCreated  │
	└────────────┬───────────┘
	             │
	           COMMIT
	             │
	             ▼
	         Debezium
	             │
	             ▼
	           Kafka
	             │
	             ▼
	      Payment Service
	             │
	             │ Transaction
	             ▼
	
	Payment DB
	┌─────────────────────────────┐
	│ Payment = PAID              │
	│ Outbox = PaymentCompleted   │
	└─────────────┬───────────────┘
	              │
	            COMMIT
	              │
	              ▼
	            Kafka
	              │
	              ▼
	      Inventory Service
	```
	
	هر Microservice عملاً همین Pattern را تکرار می‌کند.
	
	### جمله مناسب برای مصاحبه
	
	می‌توانی این‌طور بگویی:
	
	> **Transactional Outbox ensures atomicity between a local database change and event creation by storing both business data and the event in the same database transaction. A separate publisher or CDC mechanism such as Debezium then publishes the outbox event to Kafka.**
	
	و از نظر ذهنی فقط این شکل را حفظ کن:
	
	```text
	            ❌ Bad
	
	Database ────── Kafka
	    │             │
	 COMMIT         publish
	    │             │
	    └── atomic نیستند
	
	
	            ✅ Outbox
	
	       PostgreSQL Transaction
	     ┌───────────────────────┐
	     │ Business Data         │
	     │        +              │
	     │ Outbox Event          │
	     └──────────┬────────────┘
	                │
	              COMMIT
	                │
	                ▼
	         Publisher/Debezium
	                │
	                ▼
	              Kafka
	```
	
	یک نکته مهم دیگر هم اینجا وجود دارد: **Outbox معمولاً تضمین exactly-once end-to-end به تو نمی‌دهد و ممکن است یک Event دوبار deliver شود.** بنابراین Consumer باید `idempotent` طراحی شود[^12]. این هم یکی از سؤال‌های بسیار رایج بعدی در مصاحبه‌های Senior Backend است.

[^12]: حتماً. منظور از این جمله که:
	
	**Consumer باید `idempotent` باشد**
	
	این است که اگر یک Event به هر دلیلی **دو یا چند بار** به Consumer رسید، نتیجه نهایی فقط **یک بار** اعمال شود.
	
	مثلاً دوبار رسیدن `PaymentCompleted` نباید باعث شود موجودی انبار دوبار کم شود.
	
	---
	
	## اول ببینیم چرا Event ممکن است دوبار ارسال شود
	
	در مثال قبلی داشتیم:
	
	```text
	Order Service
	     │
	     ▼
	Outbox Table
	     │
	     ▼
	Publisher
	     │
	     ▼
	Kafka
	```
	
	فرض کن Outbox این Event را دارد:
	
	```text
	event_id = 123
	event_type = OrderCreated
	published = false
	```
	
	Publisher آن را می‌خواند:
	
	```text
	Publisher
	    │
	    ▼
	Kafka.send(event 123)
	```
	
	Kafka با موفقیت Event را دریافت می‌کند:
	
	```text
	Kafka ✅
	```
	
	اما قبل از اینکه Publisher این مقدار را در DB تغییر دهد:
	
	```text
	published = true
	```
	
	برنامه Crash می‌کند:
	
	```text
	Kafka send ✅
	
	      ↓
	
	Application Crash 💥
	
	      ↓
	
	published هنوز false است
	```
	
	وقتی برنامه دوباره بالا بیاید:
	
	```text
	Outbox:
	
	event_id = 123
	published = false
	```
	
	Publisher فکر می‌کند Event هنوز ارسال نشده است:
	
	```text
	Publisher
	    │
	    ▼
	Kafka.send(event 123)
	```
	
	بنابراین Kafka ممکن است همان Event را دوباره ببیند:
	
	```text
	Kafka
	
	OrderCreated(eventId=123)
	OrderCreated(eventId=123)
	```
	
	این نوع رفتار در معماری‌های distributed کاملاً طبیعی است.
	
	---
	
	# مشکل در Consumer
	
	فرض کنیم `Inventory Service` Consumer است.
	
	Event:
	
	```text
	OrderPaid
	
	orderId = 100
	productId = 500
	quantity = 2
	```
	
	Consumer ساده:
	
	```java
	@KafkaListener(topics = "payment-events")
	@Transactional
	public void handle(PaymentCompletedEvent event) {
	
	    Inventory inventory =
	        inventoryRepository.findByProductId(event.productId())
	            .orElseThrow();
	
	    inventory.setQuantity(
	        inventory.getQuantity() - event.quantity()
	    );
	}
	```
	
	در ابتدا:
	
	```text
	Product 500
	
	quantity = 10
	```
	
	Event بار اول می‌رسد:
	
	```text
	PaymentCompleted
	quantity = 2
	
	       ↓
	
	Inventory:
	
	10 - 2 = 8 ✅
	```
	
	اما اگر همان Event دوباره برسد:
	
	```text
	PaymentCompleted
	quantity = 2
	
	       ↓
	
	Inventory:
	
	8 - 2 = 6 ❌
	```
	
	در حالی که باید فقط یک بار موجودی کم می‌شد.
	
	نتیجه صحیح باید:
	
	```text
	quantity = 8
	```
	
	باشد، نه:
	
	```text
	quantity = 6
	```
	
	---
	
	# اینجاست که Idempotency وارد می‌شود
	
	Idempotent یعنی:
	
	```text
	یک Event را:
	
	1 بار اجرا کنیم  → نتیجه X
	2 بار اجرا کنیم  → باز هم نتیجه X
	10 بار اجرا کنیم → باز هم نتیجه X
	```
	
	مثلاً:
	
	```text
	Event 123
	    │
	    ▼
	First delivery
	    │
	    ▼
	Process ✅
	
	
	Event 123
	    │
	    ▼
	Second delivery
	    │
	    ▼
	Already processed
	    │
	    ▼
	Ignore ✅
	```
	
	---
	
	# چطور بفهمیم Event قبلاً پردازش شده؟
	
	معمولاً هر Event یک ID منحصر به فرد دارد.
	
	مثلاً:
	
	```json
	{
	  "eventId": "evt-123",
	  "eventType": "PaymentCompleted",
	  "orderId": 100,
	  "productId": 500,
	  "quantity": 2
	}
	```
	
	در Consumer یک جدول نگه می‌داریم:
	
	```text
	processed_events
	```
	
	مثلاً:
	
	```text
	+-----------+---------------------+
	| event_id  | processed_at        |
	+-----------+---------------------+
	| evt-123   | 2026-08-28 21:00    |
	+-----------+---------------------+
	```
	
	وقتی Event می‌رسد:
	
	```text
	PaymentCompleted
	eventId = evt-123
	```
	
	Consumer اول بررسی می‌کند:
	
	```text
	آیا evt-123 قبلاً پردازش شده؟
	
	        │
	    ┌───┴────┐
	    │        │
	   NO       YES
	    │        │
	    ▼        ▼
	Process    Ignore
	```
	
	---
	
	# پیاده‌سازی با Spring Boot
	
	Entity:
	
	```java
	@Entity
	@Table(name = "processed_events")
	@Getter
	@Setter
	public class ProcessedEvent {
	
	    @Id
	    private String eventId;
	
	    private LocalDateTime processedAt;
	}
	```
	
	Repository:
	
	```java
	public interface ProcessedEventRepository
	        extends JpaRepository<ProcessedEvent, String> {
	}
	```
	
	حالا Consumer:
	
	```java
	@Service
	@RequiredArgsConstructor
	public class PaymentEventConsumer {
	
	    private final InventoryRepository inventoryRepository;
	    private final ProcessedEventRepository processedEventRepository;
	
	    @KafkaListener(topics = "payment-events")
	    @Transactional
	    public void handle(PaymentCompletedEvent event) {
	
	        if (processedEventRepository.existsById(event.eventId())) {
	            return;
	        }
	
	        Inventory inventory =
	            inventoryRepository
	                .findByProductId(event.productId())
	                .orElseThrow();
	
	        inventory.setQuantity(
	            inventory.getQuantity() - event.quantity()
	        );
	
	        ProcessedEvent processedEvent = new ProcessedEvent();
	
	        processedEvent.setEventId(event.eventId());
	        processedEvent.setProcessedAt(LocalDateTime.now());
	
	        processedEventRepository.save(processedEvent);
	    }
	}
	```
	
	جریان این کد:
	
	```text
	Kafka Event
	eventId = evt-123
	      │
	      ▼
	Consumer
	      │
	      ▼
	existsById("evt-123")?
	      │
	      ├── YES → return
	      │
	      └── NO
	           │
	           ▼
	     update inventory
	           │
	           ▼
	     save evt-123
	           │
	           ▼
	         COMMIT
	```
	
	---
	
	# نکته خیلی مهم: هر دو عملیات باید در یک Transaction باشند
	
	این قسمت خیلی مهم است.
	
	ما می‌خواهیم این دو عملیات:
	
	```text
	1. Inventory update
	2. Mark event as processed
	```
	
	در یک transaction باشند.
	
	به همین دلیل:
	
	```java
	@Transactional
	```
	
	داریم.
	
	یعنی:
	
	```text
	BEGIN TRANSACTION
	
	    UPDATE inventory
	
	    INSERT processed_events
	
	COMMIT
	```
	
	شکل:
	
	```text
	          Local Transaction
	┌─────────────────────────────────┐
	│                                 │
	│ UPDATE inventory                │
	│ quantity = quantity - 2         │
	│                                 │
	│ INSERT processed_events         │
	│ event_id = evt-123              │
	│                                 │
	└────────────────┬────────────────┘
	                 │
	               COMMIT
	```
	
	اگر وسط کار Crash کنیم:
	
	```text
	UPDATE inventory ✅
	
	       ↓
	
	CRASH 💥
	
	       ↓
	
	INSERT processed_events انجام نشد
	```
	
	چون transaction هنوز Commit نشده:
	
	```text
	ROLLBACK
	```
	
	بنابراین Inventory هم به حالت قبل برمی‌گردد.
	
	---
	
	# بار اول Event
	
	فرض کنیم:
	
	```text
	Inventory = 10
	
	processed_events = empty
	```
	
	Event:
	
	```text
	evt-123
	quantity = 2
	```
	
	Consumer:
	
	```text
	evt-123 exists?
	       │
	       └── NO
	            │
	            ▼
	Inventory: 10 → 8
	            │
	            ▼
	INSERT evt-123
	            │
	            ▼
	COMMIT
	```
	
	نتیجه:
	
	```text
	Inventory = 8
	
	processed_events:
	
	evt-123
	```
	
	---
	
	# بار دوم همان Event
	
	Kafka دوباره می‌فرستد:
	
	```text
	evt-123
	```
	
	Consumer:
	
	```text
	evt-123 exists?
	       │
	       └── YES ✅
	            │
	            ▼
	          Ignore
	```
	
	Inventory:
	
	```text
	8
	```
	
	می‌ماند.
	
	بنابراین:
	
	```text
	First event:
	
	10 → 8
	
	
	Duplicate event:
	
	8 → 8
	```
	
	این یعنی Consumer ما `idempotent` شده است.
	
	---
	
	# اما یک مشکل ظریف‌تر داریم
	
	کد زیر:
	
	```java
	if (processedEventRepository.existsById(event.eventId())) {
	    return;
	}
	```
	
	در شرایط concurrency ممکن است مشکل داشته باشد.
	
	فرض کن دو Consumer تقریباً همزمان یک Event را بگیرند:
	
	```text
	Consumer A              Consumer B
	
	exists(evt-123)?
	NO                      
	
	                        exists(evt-123)?
	                        NO
	
	process                  process
	```
	
	هر دو فکر می‌کنند Event جدید است.
	
	بنابراین روش مطمئن‌تر این است که روی `event_id` یک Unique Constraint داشته باشیم.
	
	در مثال ما چون:
	
	```java
	@Id
	private String eventId;
	```
	
	وجود دارد، Event ID خودش unique است.
	
	می‌توانیم ابتدا Event را ثبت کنیم.
	
	مثلاً:
	
	```java
	@Transactional
	public void handle(PaymentCompletedEvent event) {
	
	    ProcessedEvent processedEvent = new ProcessedEvent();
	    processedEvent.setEventId(event.eventId());
	    processedEvent.setProcessedAt(LocalDateTime.now());
	
	    try {
	        processedEventRepository.saveAndFlush(processedEvent);
	    } catch (DataIntegrityViolationException ex) {
	
	        // Duplicate event
	        return;
	    }
	
	    Inventory inventory =
	        inventoryRepository
	            .findByProductId(event.productId())
	            .orElseThrow();
	
	    inventory.setQuantity(
	        inventory.getQuantity() - event.quantity()
	    );
	}
	```
	
	Conceptually:
	
	```text
	Event evt-123
	      │
	      ▼
	INSERT processed_events(evt-123)
	      │
	      ├── Success
	      │      │
	      │      ▼
	      │   Process business logic
	      │
	      └── Duplicate Key
	             │
	             ▼
	          Ignore
	```
	
	این روش در برابر concurrent processing هم مطمئن‌تر است.
	
	---
	
	# یک روش دیگر: Idempotency با Business Key
	
	همیشه لازم نیست جدول `processed_events` داشته باشیم.
	
	گاهی خود مدل business به ما اجازه می‌دهد عملیات را idempotent کنیم.
	
	مثلاً Payment Service قرار است Order را Paid کند.
	
	روش خطرناک:
	
	```java
	payment.setAmount(
	    payment.getAmount() + event.amount()
	);
	```
	
	اگر Event دوبار بیاید:
	
	```text
	100 + 100 = 200 ❌
	```
	
	اما اگر مدل ما این باشد:
	
	```java
	payment.setStatus(PaymentStatus.PAID);
	```
	
	بار اول:
	
	```text
	PENDING → PAID
	```
	
	بار دوم:
	
	```text
	PAID → PAID
	```
	
	نتیجه تغییر نمی‌کند.
	
	یعنی operation ذاتاً idempotent است.
	
	---
	
	# یک مثال خیلی واضح
	
	این عملیات idempotent نیست:
	
	```text
	balance = balance - 100
	```
	
	اگر دوبار اجرا شود:
	
	```text
	1000
	 ↓
	900
	 ↓
	800 ❌
	```
	
	اما این operation می‌تواند idempotent شود:
	
	```text
	Payment ID = pay-789
	
	اگر pay-789 قبلاً اعمال نشده:
	    subtract 100
	else:
	    do nothing
	```
	
	نتیجه:
	
	```text
	First:
	
	1000 → 900
	
	
	Duplicate:
	
	900 → 900
	```
	
	---
	
	# ارتباط کامل Saga + Outbox + Idempotent Consumer
	
	حالا این سه مفهوم کنار هم قرار می‌گیرند:
	
	```text
	Order Service
	     │
	     │ @Transactional
	     ▼
	
	┌───────────────────────┐
	│ orders                │
	│ outbox_events         │
	└──────────┬────────────┘
	           │
	           │
	           ▼
	      Outbox Publisher
	           │
	           ▼
	         Kafka
	           │
	           │
	           │ Event may be
	           │ delivered twice
	           ▼
	
	     Payment Service
	           │
	           ▼
	
	   Idempotent Consumer
	           │
	           ▼
	
	event already processed?
	      │
	   ┌──┴───┐
	   │      │
	  YES    NO
	   │      │
	Ignore  Process
	          │
	          ▼
	       Payment DB
	```
	
	در واقع:
	
	```text
	Transactional Outbox
	
	حل می‌کند:
	
	DB updated شد ولی Event گم نشود.
	
	
	Idempotent Consumer
	
	حل می‌کند:
	
	اگر Event دوبار رسید،
	business operation دوبار اجرا نشود.
	```
	
	---
	
	## جمله خوب برای مصاحبه
	
	می‌توانی بگویی:
	
	> **Because outbox-based messaging usually provides at-least-once delivery, the same event may be delivered more than once. Therefore, consumers should be idempotent. A common solution is to assign each event a unique event ID and store processed IDs in the consumer's database within the same local transaction as the business update. Duplicate events can then safely be ignored.**
	
	این زنجیره را برای مصاحبه حفظ کن:
	
	```text
	Saga
	  │
	  ▼
	Local Transactions
	  │
	  ▼
	Transactional Outbox
	  │
	  ▼
	Kafka
	  │
	  ▼
	At-least-once delivery
	  │
	  ▼
	Possible duplicates
	  │
	  ▼
	Idempotent Consumer
	```
	
	این دقیقاً یکی از مهم‌ترین زنجیره‌های مفهومی در طراحی Microserviceهای واقعی است.

[^13]: حتماً. اول خود مشکل را با یک مثال ساده ببینیم.
	
	فرض کن یک API داری:
	
	```text
	GET /products/123
	```
	
	برای سریع‌تر شدن، اطلاعات محصول را در Redis cache نگه می‌داری:
	
	```text
	Client
	  |
	  v
	Application
	  |
	  +----> Redis Cache
	  |
	  +----> Database
	```
	
	حالت عادی این است:
	
	```text
	Request
	   |
	   v
	Redis
	   |
	   | Cache HIT
	   v
	"Product 123"
	```
	
	پس اصلاً سراغ Database نمی‌رویم.
	
	اما فرض کن cache این محصول ساعت `10:00:00` منقضی شود.
	
	در همان لحظه 1000 درخواست می‌رسد:
	
	```text
	10:00:00
	
	Request 1 ----\
	Request 2 -----\
	Request 3 ------\
	Request 4 -------+----> Cache
	...             /
	Request 1000 ---/
	                  |
	                  v
	              Cache MISS
	```
	
	همه تقریباً همزمان می‌بینند:
	
	```text
	product:123 = NOT FOUND
	```
	
	و همه تصمیم می‌گیرند از Database بخوانند:
	
	```text
	Req 1 ------> DB
	Req 2 ------> DB
	Req 3 ------> DB
	Req 4 ------> DB
	...
	Req 1000 ---> DB
	```
	
	یعنی به جای اینکه **یک query** به DB بزنیم:
	
	```text
	1 query
	```
	
	ناگهان می‌شود:
	
	```text
	1000 queries
	```
	
	این همان:
	
	```text
	Cache Stampede
	یا
	Thundering Herd
	```
	
	است.
	
	ممکن است نتیجه این شود:
	
	```text
	Cache expired
	      |
	      v
	1000 requests
	      |
	      v
	1000 DB queries
	      |
	      v
	DB CPU = 100%
	      |
	      v
	slow response
	      |
	      v
	timeouts
	      |
	      v
	more retries
	      |
	      v
	even more load
	      |
	      v
	💥
	```
	
	حالا سه راه‌حلی که در جواب آمده را جدا ببینیم.
	
	### 1. Lock / Mutex
	
	ایده خیلی ساده است:
	
	وقتی cache خالی شد، فقط **یک thread** اجازه دارد برود DB.
	
	مثلاً:
	
	```text
	                Cache MISS
	                    |
	          +---------+---------+
	          |         |         |
	       Thread1   Thread2   Thread3
	          |         |         |
	          v         v         v
	        LOCK      LOCK      LOCK
	          |
	          | Thread1 wins
	          v
	          DB
	          |
	          v
	      Read Product
	          |
	          v
	      Update Cache
	          |
	          v
	       Unlock
	```
	
	Threadهای دیگر صبر می‌کنند:
	
	```text
	Thread 1 --> LOCK acquired --> DB
	Thread 2 --> waiting...
	Thread 3 --> waiting...
	Thread 4 --> waiting...
	```
	
	بعد Thread 1 cache را پر می‌کند:
	
	```text
	DB
	 |
	 v
	Product
	 |
	 v
	Redis
	
	product:123 = {...}
	```
	
	حالا بقیه threadها دیگر DB را نمی‌زنند:
	
	```text
	Thread 2 --> Cache HIT
	Thread 3 --> Cache HIT
	Thread 4 --> Cache HIT
	```
	
	پس به جای:
	
	```text
	1000 Requests
	     |
	     v
	1000 DB Queries
	```
	
	داریم:
	
	```text
	1000 Requests
	     |
	     v
	1 DB Query
	```
	
	در Java می‌توانی مفهومش را تقریباً این‌طوری تصور کنی:
	
	```java
	Product product = cache.get(id);
	
	if (product == null) {
	
	    synchronized (lock) {
	
	        product = cache.get(id);
	
	        if (product == null) {
	            product = database.findById(id);
	            cache.put(id, product);
	        }
	    }
	}
	
	return product;
	```
	
	نکته مهم این `cache.get` دوم است:
	
	```java
	synchronized (lock) {
	
	    product = cache.get(id); // check again
	```
	
	چرا؟
	
	فرض کن:
	
	```text
	Thread1 --> lock --> DB --> cache updated
	Thread2 --> waiting
	```
	
	وقتی Thread2 lock را گرفت، دیگر لازم نیست DB را بزند؛ چون Thread1 cache را پر کرده.
	
	به این الگو معمولاً می‌گویند:
	
	```text
	Double Check
	```
	
	اما یک مشکل دارد: اگر چند instance از برنامه داشته باشی:
	
	```text
	        Load Balancer
	        /     |     \
	       v      v      v
	     App1   App2   App3
	```
	
	`synchronized` فقط داخل همان JVM کار می‌کند.
	
	یعنی:
	
	```text
	App1 synchronized
	```
	
	هیچ اطلاعی از:
	
	```text
	App2 synchronized
	```
	
	ندارد.
	
	در distributed system معمولاً از distributed lock مثل Redis استفاده می‌کنند:
	
	```text
	App1 ----\
	App2 -----+----> Redis Lock
	App3 ----/
	```
	
	مثلاً با Redisson.
	
	---
	
	### 2. Stale-While-Revalidate
	
	این روش جالب‌تر است.
	
	می‌گوید وقتی cache منقضی شد، لازم نیست فوراً داده قبلی را دور بریزیم.
	
	مثلاً cache شامل این است:
	
	```text
	Product = iPhone
	Fresh until = 10:00
	Stale allowed until = 10:05
	```
	
	ساعت 10:01 درخواست می‌آید.
	
	به جای:
	
	```text
	Cache expired
	     |
	     v
	Wait for DB
	```
	
	می‌گوییم:
	
	```text
	Client
	  |
	  v
	Return old cached value immediately
	  |
	  +------> background refresh
	                |
	                v
	               DB
	                |
	                v
	             Cache
	```
	
	مثلاً:
	
	```text
	Request 1 --> old value returned
	Request 2 --> old value returned
	Request 3 --> old value returned
	Request 4 --> old value returned
	
	              |
	              +--> one background refresh
	                       |
	                       v
	                      DB
	```
	
	پس کاربران منتظر database نمی‌مانند.
	
	مثلاً cache:
	
	```text
	10:00
	
	price = $100
	```
	
	داده منقضی شده ولی هنوز قابل استفاده است.
	
	درخواست ساعت 10:01:
	
	```text
	Client
	  |
	  v
	price = $100   <-- stale response
	```
	
	همزمان:
	
	```text
	Background Thread
	      |
	      v
	Database
	      |
	      v
	price = $105
	      |
	      v
	Cache updated
	```
	
	درخواست بعدی:
	
	```text
	Client
	  |
	  v
	price = $105
	```
	
	نمای کلی:
	
	```text
	                   +------ DB
	                   |
	                   | refresh
	                   v
	Request ---> Cache(old) ----> Cache(new)
	              |
	              v
	        return immediately
	```
	
	مزیت بزرگش latency کم است.
	
	عیبش این است که ممکن است برای مدت کوتاهی اطلاعات قدیمی بدهی.
	
	برای چیزی مثل:
	
	```text
	Blog posts
	Product catalog
	News feed
	Configuration
	```
	
	معمولاً قابل قبول است.
	
	ولی برای چیزی مثل:
	
	```text
	Bank account balance
	Available credit
	Payment status
	```
	
	باید خیلی محتاط باشی.
	
	---
	
	### 3. Probabilistic Early Expiration
	
	این یکی کمی هوشمندانه‌تر است.
	
	فرض کن TTL برابر 10 دقیقه است:
	
	```text
	Cache created: 10:00
	Expires:       10:10
	```
	
	روش ساده می‌گوید تا `10:10` هیچ کاری نکن.
	
	مشکل:
	
	```text
	10:09:59 --> everyone Cache HIT
	
	10:10:00 --> everyone Cache MISS
	
	10:10:00 --> 💥 DB
	```
	
	Probabilistic Early Expiration می‌گوید قبل از رسیدن به انقضای واقعی، بعضی requestها با یک احتمال مشخص cache را refresh کنند.
	
	مثلاً:
	
	```text
	10:00 ------------------------ 10:10
	                               expiry
	
	                   ^
	                   |
	             Refresh may start
	```
	
	هرچه به زمان expiration نزدیک‌تر می‌شویم، احتمال refresh بیشتر می‌شود:
	
	```text
	10:05   1% chance
	10:07   5% chance
	10:08  15% chance
	10:09  40% chance
	10:09:50 80% chance
	```
	
	بنابراین ممکن است در 10:09:20 یک request بگوید:
	
	```text
	Cache هنوز valid است
	
	اما...
	
	من refresh را انجام می‌دهم.
	```
	
	پس:
	
	```text
	Request
	   |
	   v
	Cache HIT
	   |
	   +----> return current value
	   |
	   +----> refresh early
	             |
	             v
	            DB
	```
	
	و قبل از اینکه همه درخواست‌ها به expiration برسند، cache تازه شده است:
	
	```text
	10:09:20
	old cache
	   |
	   v
	refresh
	   |
	   v
	new cache
	
	TTL reset
	```
	
	در نتیجه اصلاً به این نقطه نمی‌رسیم:
	
	```text
	1000 simultaneous cache misses
	```
	
	---
	
	اگر بخواهیم سه روش را کنار هم بگذاریم:
	
	```text
	                 Cache Expired
	                      |
	        +-------------+-------------+
	        |             |             |
	        v             v             v
	      LOCK      Stale-While     Early Expiration
	                 Revalidate
	        |             |             |
	        v             v             v
	Only one        Return old       Refresh before
	request DB      data while       actual expiry
	                refreshing
	        |             |             |
	        v             v             v
	protect DB      low latency      avoid sudden
	                              expiration cliff
	```
	
	برای مصاحبه، یک پاسخ خوب و کوتاه می‌تواند این باشد:
	
	> Cache stampede زمانی رخ می‌دهد که یک hot cache entry منقضی شود و تعداد زیادی request همزمان cache miss بگیرند و همگی به database مراجعه کنند. برای جلوگیری از آن می‌توان از per-key locking یا distributed lock استفاده کرد تا فقط یک request داده را refresh کند، از stale-while-revalidate برای سرو کردن مقدار قبلی هنگام refresh، و از probabilistic early expiration برای refresh کردن cache کمی قبل از expiration واقعی استفاده کرد. همچنین معمولاً به TTLها مقداری jitter اضافه می‌کنیم تا تعداد زیادی key دقیقاً در یک زمان expire نشوند.
	
	یک تکنیک چهارم مهم هم همین **TTL Jitter** است. مثلاً به جای اینکه 100 هزار key همگی TTL=10 دقیقه داشته باشند:
	
	```text
	همه:
	TTL = 600 sec
	```
	
	تصادفی می‌کنیم:
	
	```text
	Key1 = 571 sec
	Key2 = 623 sec
	Key3 = 594 sec
	Key4 = 638 sec
	...
	```
	
	تا همه با هم expire نشوند.

[^14]: بله. مسئله اصلی این است که ما دو نسخه از یک داده داریم:
	
	```text
	Database              Cache
	---------             --------
	User #10              User #10
	name = Ali            name = Ali
	```
	
	Database معمولاً **source of truth** است، یعنی نسخه اصلی و معتبر داده آنجاست.
	
	حالا فرض کن نام کاربر در DB تغییر کند:
	
	```text
	Database              Cache
	---------             --------
	name = Reza           name = Ali   ❌
	```
	
	اینجا cache دیگر با DB consistent نیست.
	
	به این وضعیت می‌گوییم:
	
	```text
	stale cache
	```
	
	حالا روش‌های پاسخ را یکی‌یکی ببینیم.
	
	### 1. TTL — ساده‌ترین راه
	
	برای cache زمان انقضا تعیین می‌کنیم:
	
	```text
	key = user:10
	value = {name: Ali}
	TTL = 5 minutes
	```
	
	اگر DB تغییر کند:
	
	```text
	Database
	name = Reza
	
	Cache
	name = Ali
	```
	
	ممکن است تا حداکثر 5 دقیقه مقدار قدیمی را بدهیم.
	
	بعد از TTL:
	
	```text
	Cache expired
	      |
	      v
	Cache MISS
	      |
	      v
	Database
	name = Reza
	      |
	      v
	Cache = Reza
	```
	
	پس TTL تضمین نمی‌کند cache همیشه دقیق باشد؛ فقط تضمین می‌کند داده قدیمی **برای همیشه باقی نماند**.
	
	---
	
	## 2. Cache invalidation
	
	راه بهتر این است که وقتی DB تغییر کرد، cache همان موقع حذف شود.
	
	فرض کن داریم:
	
	```text
	PUT /users/10
	name = Reza
	```
	
	Application:
	
	```text
	        Application
	          /     \
	         v       v
	       DB       Cache
	```
	
	اول DB را update می‌کنیم:
	
	```text
	DB
	Ali ---> Reza
	```
	
	بعد cache را invalidate می‌کنیم:
	
	```text
	DEL user:10
	```
	
	حالا:
	
	```text
	Database              Redis
	---------             -----
	name = Reza           user:10 = NOT FOUND
	```
	
	درخواست بعدی:
	
	```text
	Request
	   |
	   v
	Redis
	   |
	 MISS
	   |
	   v
	Database
	   |
	 Reza
	   |
	   v
	Redis
	```
	
	در نتیجه cache دوباره با مقدار جدید پر می‌شود.
	
	این الگو در عمل خیلی رایج است:
	
	```text
	UPDATE DB
	   |
	   v
	DELETE CACHE
	```
	
	---
	
	## 3. Event-driven invalidation با Kafka
	
	در distributed system ممکن است چند application یا microservice داشته باشیم:
	
	```text
	                 +--> Service A
	                 |
	Database --------+--> Service B
	                 |
	                 +--> Service C
	```
	
	و هرکدام cache داشته باشند:
	
	```text
	Service A --> Redis/cache
	Service B --> Redis/cache
	Service C --> Redis/cache
	```
	
	اگر User Service اطلاعات user را تغییر دهد، چطور بقیه بفهمند؟
	
	اینجا Kafka مفید است.
	
	مثلاً:
	
	```text
	User Service
	     |
	     | UPDATE
	     v
	 Database
	     |
	     |
	     +----> Kafka
	            UserUpdated
	            userId=10
	```
	
	بقیه سرویس‌ها event را consume می‌کنند:
	
	```text
	                    Kafka
	                      |
	          +-----------+-----------+
	          |           |           |
	          v           v           v
	      Service A   Service B   Service C
	          |           |           |
	          v           v           v
	      DEL cache   DEL cache   DEL cache
	```
	
	مثلاً Kafka event:
	
	```json
	{
	  "type": "USER_UPDATED",
	  "userId": 10
	}
	```
	
	Consumer:
	
	```java
	@KafkaListener(topics = "user-events")
	public void handle(UserUpdatedEvent event) {
	    redisTemplate.delete("user:" + event.userId());
	}
	```
	
	پس جریان کلی:
	
	```text
	UPDATE DB
	   |
	   v
	Publish event
	   |
	   v
	Kafka
	   |
	   v
	Consumers
	   |
	   v
	Invalidate Cache
	```
	
	---
	
	## ولی اینجا یک مشکل مهم وجود دارد
	
	فرض کن کد این‌طور باشد:
	
	```java
	database.update(user);
	
	kafka.send(event);
	```
	
	DB موفق می‌شود:
	
	```text
	DB updated ✅
	```
	
	ولی درست قبل از `kafka.send` برنامه crash می‌کند:
	
	```text
	DB updated ✅
	Kafka event ❌
	```
	
	نتیجه:
	
	```text
	Database = Reza
	Cache    = Ali
	```
	
	و کسی نمی‌فهمد cache باید invalidate شود.
	
	این یکی از دلایلی است که **CDC / Debezium** مهم می‌شود.
	
	---
	
	# 4. CDC با Debezium
	
	CDC یعنی:
	
	**Change Data Capture**
	
	به جای اینکه application خودش بگوید:
	
	```text
	"من DB را تغییر دادم"
	```
	
	Debezium مستقیماً تغییرات database را مشاهده می‌کند.
	
	مثلاً:
	
	```text
	Application
	    |
	    v
	Database
	    |
	    | transaction log
	    v
	Debezium
	    |
	    v
	Kafka
	    |
	    v
	Cache Invalidation Consumer
	    |
	    v
	Redis
	```
	
	مثلاً DB:
	
	```sql
	UPDATE users
	SET name = 'Reza'
	WHERE id = 10;
	```
	
	PostgreSQL این تغییر را در WAL می‌نویسد:
	
	```text
	PostgreSQL
	   |
	   v
	WAL
	   |
	   v
	Debezium
	```
	
	Debezium آن را می‌خواند:
	
	```text
	user id=10 changed
	
	before:
	name=Ali
	
	after:
	name=Reza
	```
	
	و event به Kafka می‌فرستد:
	
	```text
	Debezium
	   |
	   v
	Kafka topic
	   |
	   v
	Cache Consumer
	   |
	   v
	DEL user:10
	```
	
	مزیت بزرگ این است که event مستقیماً از تغییر committed شده در DB ایجاد شده است.
	
	---
	
	# 5. Write-through Cache
	
	روش دیگری که در سؤال آمده `write-through` است.
	
	در این حالت application مستقیماً database و cache را جداگانه مدیریت نمی‌کند.
	
	از دید application:
	
	```text
	Application
	    |
	    v
	Cache
	    |
	    v
	Database
	```
	
	هنگام write:
	
	```text
	Application
	    |
	    | write
	    v
	Cache Layer
	    |
	    +----> Database
	    |
	    +----> Cache
	```
	
	مثلاً:
	
	```text
	set user:10 = Reza
	```
	
	cache layer مسئول است:
	
	```text
	1. update DB
	2. update cache
	```
	
	نتیجه:
	
	```text
	Database              Cache
	---------             -----
	Reza                   Reza
	```
	
	برخلاف cache-aside که معمولاً این شکلی است:
	
	```text
	Application
	  |
	  +--> Database
	  |
	  +--> Redis
	```
	
	در write-through:
	
	```text
	Application
	     |
	     v
	Cache Layer
	     |
	     v
	Database
	```
	
	---
	
	# یک تفاوت مهم: Update cache یا Delete cache؟
	
	فرض کن User تغییر کرده.
	
	دو انتخاب داریم.
	
	روش اول:
	
	```text
	UPDATE DB
	UPDATE CACHE
	```
	
	روش دوم:
	
	```text
	UPDATE DB
	DELETE CACHE
	```
	
	در بسیاری از سیستم‌ها روش دوم ساده‌تر و امن‌تر است:
	
	```text
	DB updated
	    |
	    v
	cache deleted
	    |
	    v
	next request
	    |
	    v
	reload from DB
	```
	
	چرا؟
	
	چون DB همچنان source of truth باقی می‌ماند.
	
	---
	
	# ترکیب واقعی در یک سیستم production
	
	معمولاً فقط یک تکنیک استفاده نمی‌کنیم.
	
	ممکن است معماری این باشد:
	
	```text
	                Write Request
	                     |
	                     v
	                User Service
	                     |
	                     v
	                 PostgreSQL
	                     |
	              Transaction Log
	                     |
	                     v
	                  Debezium
	                     |
	                     v
	                   Kafka
	                     |
	                     v
	           Cache Invalidator
	                     |
	                     v
	                   Redis
	```
	
	برای read:
	
	```text
	Client
	  |
	  v
	Service
	  |
	  v
	Redis
	  |
	  +--- HIT ---> return
	  |
	  +--- MISS
	         |
	         v
	        DB
	         |
	         v
	     Redis SET
	         |
	         v
	      return
	```
	
	و Redis همچنان TTL هم دارد:
	
	```text
	Redis key
	user:10
	
	TTL = 10 minutes
	```
	
	بنابراین اگر event invalidation به هر دلیلی مشکل پیدا کرد:
	
	```text
	Kafka failure
	Consumer failure
	Bug
	```
	
	TTL یک safety net است:
	
	```text
	stale data
	   |
	   v
	TTL expires
	   |
	   v
	reload from DB
	```
	
	---
	
	### برای مصاحبه، پاسخ خوب می‌تواند این باشد:
	
	> I usually treat the database as the source of truth and the cache as an eventually consistent copy. For reads, I commonly use cache-aside with a TTL as a safety mechanism. When the underlying record changes, I invalidate the corresponding cache key, ideally through an event such as Kafka. In more reliable architectures, CDC tools like Debezium can publish committed database changes to Kafka and consumers invalidate or refresh the cache. For some use cases, write-through caching can also keep database and cache updates coordinated.
	
	خلاصه تصویری:
	
	```text
	          Source of Truth
	               DB
	               |
	       +-------+-------+
	       |               |
	      TTL           DB change
	       |               |
	       |               v
	       |          Kafka / CDC
	       |               |
	       |               v
	       +----------> Invalidate
	                       |
	                       v
	                     Cache
	```
	
	نکته‌ای که در مصاحبه Senior خیلی ارزش دارد این است که بگویی **TTL به‌تنهایی consistency mechanism قوی نیست؛ بیشتر یک fallback/safety net است.** Event invalidation یا CDC زمان stale بودن cache را بسیار کمتر می‌کند.

[^15]: حتماً. ایده اصلی این است که بفهمیم **چه زمانی Service A باید منتظر جواب Service B بماند** و چه زمانی بهتر است فقط یک پیام بفرستد و کار خودش را ادامه دهد.
	
	فرض کن دو سرویس داریم:
	
	```text
	Order Service
	     |
	     v
	Payment Service
	```
	
	دو راه کلی برای ارتباط داریم:
	
	```text
	1) Synchronous
	   REST / HTTP / gRPC
	
	2) Asynchronous
	   Kafka / RabbitMQ / SQS
	```
	
	## 1. ارتباط Synchronous یعنی چه؟
	
	مثلاً کاربر سفارش ثبت می‌کند و `Order Service` فوراً `Payment Service` را صدا می‌زند:
	
	```text
	User
	 |
	 v
	Order Service
	 |
	 | HTTP: /pay
	 v
	Payment Service
	 |
	 | Response
	 v
	Order Service
	 |
	 v
	User
	```
	
	در این مدل، `Order Service` **منتظر جواب** است.
	
	مثلاً:
	
	```text
	Order Service ---> Payment Service
	                      |
	                      | processing...
	                      |
	Order Service <--- Payment successful
	```
	
	کد ذهنی ساده:
	
	```java
	PaymentResult result = paymentService.pay(order);
	
	if (result.isSuccessful()) {
	    order.setStatus(PAID);
	}
	```
	
	یعنی تا زمانی که `pay()` جواب ندهد، جریان ادامه پیدا نمی‌کند.
	
	این روش زمانی خوب است که **همین الان به جواب نیاز داری**.
	
	مثلاً:
	
	```text
	Login Service ---> Authentication Service
	
	"این password درست است؟"
	```
	
	بدون جواب Authentication Service نمی‌توانی ادامه بدهی.
	
	---
	
	# اما Message Queue چه فرقی دارد؟
	
	بین سرویس‌ها یک واسطه قرار می‌دهیم:
	
	```text
	Order Service
	     |
	     | OrderCreated
	     v
	+----------------+
	| Kafka / MQ     |
	+----------------+
	     |
	     v
	Payment Service
	```
	
	Order Service دیگر مستقیماً Payment Service را صدا نمی‌زند.
	
	فقط می‌گوید:
	
	```text
	"یک سفارش ایجاد شد."
	```
	
	و پیام را داخل Queue/Broker قرار می‌دهد.
	
	```text
	Order Service
	     |
	     | publish
	     v
	+----------------------+
	| OrderCreated         |
	| orderId = 123        |
	+----------------------+
	     |
	     v
	Kafka
	```
	
	بعداً Consumer آن را می‌گیرد:
	
	```text
	Kafka
	 |
	 | OrderCreated
	 v
	Payment Service
	```
	
	نکته مهم:
	
	```text
	Order Service
	     |
	     | publish
	     v
	Kafka
	     |
	     +----> Order Service کارش تمام شد
	```
	
	Order Service لازم نیست منتظر Payment Service بماند.
	
	---
	
	# 2. اولین دلیل: Decoupling
	
	فرض کن بدون Queue این معماری را داریم:
	
	```text
	              +--> Payment Service
	              |
	Order Service +--> Inventory Service
	              |
	              +--> Notification Service
	              |
	              +--> Analytics Service
	```
	
	Order Service باید همه سرویس‌ها را بشناسد:
	
	```text
	paymentService.pay()
	inventoryService.reserve()
	notificationService.send()
	analyticsService.track()
	```
	
	اگر فردا یک سرویس جدید اضافه کنیم:
	
	```text
	Fraud Detection Service
	```
	
	باید Order Service را تغییر دهیم.
	
	```text
	Order Service
	     |
	     +--> Payment
	     +--> Inventory
	     +--> Notification
	     +--> Analytics
	     +--> Fraud Detection
	```
	
	یعنی coupling زیاد شده است.
	
	حالا Kafka را وسط بگذاریم:
	
	```text
	                    +--> Payment Service
	                    |
	Order Service ---> Kafka ---> Inventory Service
	                    |
	                    +--> Notification Service
	                    |
	                    +--> Analytics Service
	```
	
	Order Service فقط می‌گوید:
	
	```text
	OrderCreated
	```
	
	مثلاً:
	
	```json
	{
	  "orderId": 123,
	  "userId": 55,
	  "amount": 100
	}
	```
	
	دیگر Order Service اهمیت نمی‌دهد چه کسی این پیام را مصرف می‌کند.
	
	فردا می‌توانیم بدون تغییر Order Service اضافه کنیم:
	
	```text
	                    +--> Payment
	                    +--> Inventory
	Order ---> Kafka ---+--> Notification
	                    +--> Analytics
	                    +--> Fraud Detection
	```
	
	این همان:
	
	```text
	Decoupling
	```
	
	است.
	
	---
	
	# 3. دلیل دوم: مدیریت Traffic Spike
	
	این یکی از مهم‌ترین کاربردهای Queue است.
	
	فرض کن سیستم معمولاً دارد:
	
	```text
	100 request / second
	```
	
	ولی Black Friday ناگهان می‌شود:
	
	```text
	10,000 request / second
	```
	
	بدون Queue:
	
	```text
	10,000 requests
	      |
	      v
	Order Service
	      |
	      v
	Payment Service
	      |
	      v
	Database
	```
	
	اگر Payment Service فقط بتواند:
	
	```text
	500 request/sec
	```
	
	پردازش کند، اتفاق احتمالی:
	
	```text
	10,000 req/sec
	       |
	       v
	Payment Service
	       |
	       X
	   overloaded
	       |
	       +--> timeout
	       +--> connection error
	       +--> CPU 100%
	       +--> DB overloaded
	```
	
	ممکن است سیستم cascade failure بدهد.
	
	اما با Queue:
	
	```text
	10,000 events/sec
	       |
	       v
	+----------------+
	| Kafka / SQS    |
	|                |
	| backlog        |
	| backlog        |
	| backlog        |
	+----------------+
	       |
	       | 500/sec
	       v
	Payment Service
	```
	
	Queue مثل یک **مخزن موقت فشار** عمل می‌کند.
	
	مثلاً:
	
	```text
	Producer speed:
	10,000 msg/sec
	
	Consumer capacity:
	500 msg/sec
	```
	
	Queue پیام‌ها را نگه می‌دارد:
	
	```text
	Kafka
	
	[ msg ]
	[ msg ]
	[ msg ]
	[ msg ]
	[ msg ]
	[ msg ]
	[ msg ]
	```
	
	Consumer هرچقدر ظرفیت دارد پردازش می‌کند.
	
	این مفهوم را معمولاً می‌توان این‌طور تصور کرد:
	
	```text
	Traffic Spike
	      |
	      v
	   BUFFER
	      |
	      v
	Consumer
	```
	
	به این کار گاهی:
	
	```text
	Load leveling
	```
	
	هم می‌گویند.
	
	---
	
	# 4. دلیل سوم: پردازش Asynchronous
	
	فرض کن کاربر سفارش ثبت می‌کند.
	
	بعد از ثبت سفارش باید این کارها انجام شوند:
	
	```text
	1. ذخیره سفارش
	2. ارسال email
	3. ارسال SMS
	4. ایجاد invoice
	5. update analytics
	6. loyalty points
	```
	
	اگر همه synchronous باشند:
	
	```text
	User
	 |
	 v
	Order Service
	 |
	 +--> Save Order       200ms
	 |
	 +--> Send Email       800ms
	 |
	 +--> Send SMS         500ms
	 |
	 +--> Invoice          700ms
	 |
	 +--> Analytics        300ms
	 |
	 +--> Loyalty          400ms
	 |
	 v
	
	Total ≈ 2900ms
	```
	
	کاربر تقریباً 3 ثانیه منتظر می‌ماند.
	
	اما واقعاً آیا کاربر لازم است منتظر ارسال ایمیل بماند؟
	
	نه.
	
	پس:
	
	```text
	User
	 |
	 v
	Order Service
	 |
	 +--> Save Order
	 |
	 +--> publish OrderCreated
	 |
	 v
	Response:
	"Order accepted"
	```
	
	مثلاً:
	
	```text
	300ms
	```
	
	سپس در background:
	
	```text
	                     +--> Email Service
	                     |
	OrderCreated ---> Kafka ---> SMS Service
	                     |
	                     +--> Invoice Service
	                     |
	                     +--> Analytics
	```
	
	بنابراین:
	
	```text
	Critical work
	    =
	synchronous
	
	Non-critical work
	    =
	asynchronous
	```
	
	---
	
	# 5. دلیل چهارم: Consumer ممکن است Down باشد
	
	فرض کن Notification Service موقتاً down شده:
	
	```text
	Order Service ---> Notification Service
	                         X
	                       DOWN
	```
	
	اگر synchronous باشد:
	
	```text
	POST /send-email
	      |
	      X
	Connection refused
	```
	
	ممکن است ایمیل کلاً از دست برود.
	
	ولی اگر Queue داشته باشیم:
	
	```text
	Order Service
	     |
	     | NotificationRequested
	     v
	Kafka
	     |
	     X
	Notification Service DOWN
	```
	
	پیام داخل Kafka باقی می‌ماند:
	
	```text
	Kafka
	
	[NotificationRequested]
	[NotificationRequested]
	[NotificationRequested]
	```
	
	بعد Notification Service بالا می‌آید:
	
	```text
	Kafka
	 |
	 v
	Notification Service
	 |
	 +--> message 1
	 +--> message 2
	 +--> message 3
	```
	
	این یکی از مزایای مهم messaging است:
	
	```text
	Producer و Consumer لازم نیست
	همزمان UP باشند.
	```
	
	در synchronous:
	
	```text
	Producer UP
	Consumer DOWN
	     =
	request fails
	```
	
	در async:
	
	```text
	Producer UP
	Consumer DOWN
	     =
	message waits
	```
	
	---
	
	# 6. یک مثال واقعی: Order Processing
	
	فرض کن معماری فروشگاه داریم:
	
	```text
	                +----------------+
	                | Order Service  |
	                +----------------+
	                        |
	                        | OrderCreated
	                        v
	                 +-------------+
	                 |    Kafka    |
	                 +-------------+
	                    /    |    \
	                   /     |     \
	                  v      v      v
	
	             Payment  Inventory Notification
	             Service   Service     Service
	```
	
	Order Service فقط این event را publish می‌کند:
	
	```text
	OrderCreated
	
	orderId = 1001
	amount = 120 EUR
	customerId = 77
	```
	
	حالا هر سرویس مستقل کار خودش را انجام می‌دهد:
	
	```text
	Payment Service
	    |
	    +--> charge customer
	
	Inventory Service
	    |
	    +--> reserve stock
	
	Notification Service
	    |
	    +--> send email
	```
	
	---
	
	# 7. Event-driven Workflow
	
	می‌توان حتی یک workflow کامل ساخت.
	
	مثلاً:
	
	```text
	OrderCreated
	     |
	     v
	Payment Service
	     |
	     | PaymentCompleted
	     v
	Kafka
	     |
	     v
	Inventory Service
	     |
	     | InventoryReserved
	     v
	Kafka
	     |
	     v
	Shipping Service
	```
	
	جریان کامل:
	
	```text
	Order
	 |
	 v
	OrderCreated
	 |
	 v
	Payment
	 |
	 v
	PaymentCompleted
	 |
	 v
	Inventory
	 |
	 v
	InventoryReserved
	 |
	 v
	Shipping
	```
	
	این همان چیزی است که در معماری‌های:
	
	```text
	Event-Driven Architecture
	```
	
	زیاد می‌بینی.
	
	---
	
	# 8. ولی Message Queue همیشه بهتر نیست
	
	این نکته در مصاحبه خیلی مهم است.
	
	فرض کن سرویس A می‌خواهد بداند:
	
	```text
	"موجودی فعلی این کالا چقدر است؟"
	```
	
	مثلاً:
	
	```text
	Product Service
	      |
	      | GET /inventory/123
	      v
	Inventory Service
	      |
	      | stock = 12
	      v
	Product Service
	```
	
	اینجا synchronous بسیار منطقی‌تر است.
	
	چرا؟
	
	چون Service A **الان به جواب نیاز دارد**.
	
	با Kafka خیلی پیچیده می‌شود:
	
	```text
	Product Service
	      |
	      | InventoryRequested
	      v
	Kafka
	      |
	      v
	Inventory Service
	      |
	      | InventoryResponse
	      v
	Kafka
	      |
	      v
	Product Service
	```
	
	در حالی که یک HTTP call ساده کافی بود.
	
	---
	
	# یک قانون ذهنی خیلی خوب
	
	از خودت بپرس:
	
	```text
	آیا caller برای ادامه کار
	همین الان به جواب نیاز دارد؟
	```
	
	اگر:
	
	```text
	YES
	 |
	 v
	Synchronous
	REST / gRPC
	```
	
	اگر:
	
	```text
	NO
	 |
	 v
	Message Queue
	Kafka / RabbitMQ / SQS
	```
	
	البته استثنا وجود دارد، ولی برای فهم اولیه قانون بسیار خوبی است.
	
	---
	
	# مقایسه تصویری
	
	### Synchronous
	
	```text
	Service A
	   |
	   | request
	   v
	Service B
	   |
	   | processing
	   |
	   v
	response
	   |
	   v
	Service A continues
	```
	
	Service A باید منتظر بماند:
	
	```text
	A: --------WAITING--------->
	B:       processing
	```
	
	---
	
	### Asynchronous
	
	```text
	Service A
	   |
	   | message
	   v
	Kafka
	   |
	   v
	Service B
	```
	
	Service A:
	
	```text
	A: publish ---> continue
	```
	
	Service B:
	
	```text
	            later
	              |
	              v
	Kafka ---> process
	```
	
	---
	
	# Kafka، RabbitMQ و SQS چه نقشی دارند؟
	
	هر سه برای asynchronous messaging استفاده می‌شوند، ولی مدلشان کمی متفاوت است.
	
	برای فهم اولیه:
	
	```text
	               Messaging
	                   |
	       +-----------+-----------+
	       |           |           |
	       v           v           v
	     Kafka      RabbitMQ      SQS
	```
	
	یک تصویر ذهنی ساده:
	
	```text
	Kafka
	بیشتر شبیه event log / event streaming
	
	RabbitMQ
	بیشتر شبیه message broker / queue
	
	SQS
	managed queue در AWS
	```
	
	مثلاً Kafka در چنین معماری‌ای خیلی رایج است:
	
	```text
	                    +--> Fraud
	                    |
	PaymentCompleted --> Kafka --> Analytics
	                    |
	                    +--> Notification
	                    |
	                    +--> Accounting
	```
	
	چون ممکن است یک event چند consumer داشته باشد.
	
	---
	
	# یک نکته بسیار مهم در مصاحبه
	
	استفاده از Queue مجانی نیست؛ complexity اضافه می‌کند.
	
	مثلاً باید درباره این مسائل فکر کنی:
	
	```text
	message duplication
	      |
	      v
	idempotency
	
	message failure
	      |
	      v
	retry
	
	repeated failure
	      |
	      v
	DLQ
	
	ordering
	      |
	      v
	partition / routing[^16]
	
	consumer down
	      |
	      v
	backlog
	
	schema changes
	      |
	      v
	event versioning
	```
	
	یعنی:
	
	```text
	Synchronous
	     |
	     +--> ساده‌تر
	
	Messaging
	     |
	     +--> resilient تر
	     +--> scalable تر
	     +--> decoupled تر
	     |
	     +--> ولی پیچیده‌تر
	```
	
	---
	
	## خلاصه‌ای که برای مصاحبه حفظ کنی
	
	اگر ازت پرسیدند:
	
	**When would you use a message queue instead of synchronous service-to-service communication?**
	
	در ذهن خودت این شکل را داشته باش:
	
	```text
	                 Need immediate response?
	                    /          \
	                  YES           NO
	                   |             |
	                   v             v
	              REST/gRPC       Queue/Kafka
	                                  |
	                 +----------------+----------------+
	                 |                |                |
	                 v                v                v
	             decoupling      traffic spikes    async work
	                                                  |
	                                                  v
	                                         consumer can be down
	```
	
	یعنی Message Queue را بیشتر زمانی انتخاب می‌کنیم که **نیازی به response فوری نداریم، می‌خواهیم producer و consumer از هم مستقل باشند، spikeهای ترافیکی را buffer کنیم، پردازش را asynchronous انجام دهیم یا پیام حتی هنگام down بودن consumer از بین نرود.**
	
	و مثال خیلی خوب مصاحبه‌ای:
	
	```text
	Order Service
	      |
	      | OrderCreated
	      v
	    Kafka
	   /  |   \
	  v   v    v
	Pay Stock Email
	```
	
	به‌جای:
	
	```text
	Order
	 |
	 +--> Payment
	 +--> Inventory
	 +--> Email
	```
	
	اولی معمولاً برای سیستم‌های بزرگ‌تر **decoupled، resilient و scalable**‌تر است.

[^16]: حتماً. این سه مورد از مهم‌ترین چالش‌های واقعی در سیستم‌های message-driven هستند. یکی‌یکی با مثال و شکل متنی توضیح می‌دهم.
	
	## 1) Ordering → Partition / Routing
	
	فرض کن برای یک حساب بانکی این eventها تولید می‌شوند:
	
	```text
	Account = A123
	
	1. Deposit  +100
	2. Withdraw  -50
	3. Deposit  +20
	```
	
	ترتیب درست:
	
	```text
	+100
	  |
	  v
	-50
	  |
	  v
	+20
	```
	
	موجودی نهایی:
	
	```text
	0
	+100 = 100
	-50  = 50
	+20  = 70
	```
	
	اما اگر messageها به ترتیب متفاوت پردازش شوند:
	
	```text
	Withdraw -50
	    |
	    v
	Deposit +20
	    |
	    v
	Deposit +100
	```
	
	ممکن است business logic اشتباه شود، مخصوصاً اگر مثلاً برداشت از موجودی صفر مجاز نباشد.
	
	### مشکل در Kafka از کجا می‌آید؟
	
	فرض کن topic سه partition دارد:
	
	```text
	Topic: account-events
	
	Partition 0
	Partition 1
	Partition 2
	```
	
	و eventهای یک account تصادفی بین partitionها پخش شوند:
	
	```text
	Deposit +100  ---> Partition 0
	Withdraw -50  ---> Partition 2
	Deposit +20   ---> Partition 1
	```
	
	حالا consumerها ممکن است موازی کار کنند:
	
	```text
	P0 ---> Consumer A
	P1 ---> Consumer B
	P2 ---> Consumer C
	```
	
	و ترتیب processing تضمین نمی‌شود.
	
	مثلاً:
	
	```text
	time --->
	
	Consumer C: Withdraw -50
	Consumer B: Deposit +20
	Consumer A: Deposit +100
	```
	
	در حالی که producer ترتیب دیگری فرستاده بود.
	
	### راه‌حل: Routing با Key
	
	در Kafka معمولاً message را با یک key منتشر می‌کنیم.
	
	مثلاً:
	
	```text
	key = accountId
	```
	
	برای همه eventهای حساب `A123`:
	
	```text
	key = A123
	```
	
	Kafka با hash کردن key، همه eventهای آن account را به یک partition مشخص می‌فرستد:
	
	```text
	A123 --> hash(A123) --> Partition 1
	```
	
	پس:
	
	```text
	Partition 1:
	
	[Deposit +100]
	      |
	[Withdraw -50]
	      |
	[Deposit +20]
	```
	
	و Kafka ترتیب پیام‌ها را **داخل یک partition** حفظ می‌کند.
	
	نکته مهم:
	
	```text
	Kafka ordering guarantee
	        |
	        v
	within a partition
	```
	
	نه لزوماً در کل topic.
	
	### شکل کلی
	
	```text
	Account A123 events
	       |
	       | key=A123
	       v
	+------------------+
	|   Partition 1    |
	|                  |
	| Deposit +100     |
	| Withdraw -50     |
	| Deposit +20      |
	+------------------+
	       |
	       v
	    Consumer
	```
	
	برای حساب دیگر:
	
	```text
	Account B555
	       |
	       | key=B555
	       v
	Partition 2
	```
	
	در نتیجه:
	
	```text
	A123 events ---> Partition 1 ---> ordered
	B555 events ---> Partition 2 ---> ordered
	```
	
	و در عین حال processing می‌تواند parallel باشد.
	
	### قانون ذهنی
	
	اگر ordering برای یک entity مهم است:
	
	```text
	orderId
	accountId
	customerId
	paymentId
	```
	
	اغلب همان entity را message key قرار می‌دهیم.
	
	مثلاً:
	
	```text
	key = orderId
	```
	
	تا همه eventهای یک سفارش بروند یک partition:
	
	```text
	OrderCreated
	PaymentCompleted
	InventoryReserved
	OrderShipped
	```
	
	به ترتیب:
	
	```text
	Partition X
	
	OrderCreated
	    |
	PaymentCompleted
	    |
	InventoryReserved
	    |
	OrderShipped
	```
	
	---
	
	# 2) Consumer Down → Backlog
	
	حالا فرض کن producer خیلی راحت message تولید می‌کند:
	
	```text
	Order Service
	     |
	     | OrderCreated
	     v
	Kafka
	     |
	     v
	Notification Service
	```
	
	ولی Notification Service down می‌شود:
	
	```text
	Order Service
	     |
	     v
	Kafka
	     |
	     X
	Notification Service DOWN
	```
	
	آیا messageها از بین می‌روند؟
	
	در Kafka معمولاً نه، چون messageها داخل topic نگهداری می‌شوند.
	
	مثلاً producer همچنان message می‌فرستد:
	
	```text
	Kafka
	
	[message 1]
	[message 2]
	[message 3]
	[message 4]
	[message 5]
	```
	
	Consumer خاموش است، پس پیام‌ها هنوز پردازش نشده‌اند.
	
	این پیام‌های پردازش‌نشده را به‌صورت مفهومی می‌توانیم backlog بنامیم.
	
	```text
	Producer
	   |
	   | 1000 msg/sec
	   v
	+----------------------+
	| Kafka                |
	|                      |
	| msg                   |
	| msg                   |
	| msg                   |
	| msg     <-- backlog   |
	| msg                   |
	+----------------------+
	       |
	       X
	   Consumer down
	```
	
	### وقتی Consumer دوباره بالا می‌آید
	
	```text
	Kafka
	 |
	 | pending messages
	 v
	Consumer
	```
	
	Consumer از جایی که قبلاً رسیده بود ادامه می‌دهد.
	
	مثلاً:
	
	```text
	Kafka offsets:
	
	0
	1
	2
	3
	4
	5
	6
	7
	8
	9
	```
	
	Consumer تا offset 4 را خوانده:
	
	```text
	0 1 2 3 4
	        ^
	   last processed
	```
	
	در زمان down شدن Kafka همچنان message می‌گیرد:
	
	```text
	5 6 7 8 9 10 11 12
	```
	
	وقتی consumer برگردد:
	
	```text
	starts from offset 5
	```
	
	و ادامه می‌دهد.
	
	### Consumer Lag
	
	در Kafka اصطلاح مهم‌تر معمولاً `consumer lag` است.
	
	مثلاً:
	
	```text
	latest Kafka offset = 10000
	
	consumer offset = 7000
	```
	
	پس:
	
	```text
	lag = 10000 - 7000
	    = 3000 messages
	```
	
	یعنی consumer حدود 3000 پیام عقب است.
	
	شکل:
	
	```text
	Kafka:
	
	0 ---------------- 7000 ---------------- 10000
	                  ^                     ^
	                  |                     |
	            Consumer offset        Latest offset
	
	                  <---- 3000 ---->
	                       lag
	```
	
	اگر consumer down باشد، lag رشد می‌کند:
	
	```text
	Time 10:00 -> lag = 0
	Time 10:05 -> lag = 5,000
	Time 10:10 -> lag = 20,000
	Time 10:30 -> lag = 100,000
	```
	
	این backlog ممکن است بعداً مشکل ایجاد کند.
	
	مثلاً consumer بعد از recovery باید حجم زیادی پیام پردازش کند:
	
	```text
	Backlog = 1,000,000 messages
	```
	
	اگر ظرفیت consumer:
	
	```text
	5,000 msg/sec
	```
	
	باشد، مدتی طول می‌کشد تا catch up کند.
	
	### راه‌حل‌های معمول
	
	مثلاً scale out کردن consumerها:
	
	```text
	Before:
	
	Partition 0 ---> Consumer A
	Partition 1 ---> Consumer A
	Partition 2 ---> Consumer A
	```
	
	بعد:
	
	```text
	Partition 0 ---> Consumer A
	Partition 1 ---> Consumer B
	Partition 2 ---> Consumer C
	```
	
	پس backlog سریع‌تر خالی می‌شود.
	
	اما تعداد consumerهای فعال در یک consumer group عملاً از تعداد partitionها بیشتر سود نمی‌برد.
	
	مثلاً:
	
	```text
	3 partitions
	10 consumers
	```
	
	نتیجه:
	
	```text
	3 consumers working
	7 consumers idle
	```
	
	---
	
	# 3) Schema Changes → Event Versioning
	
	این یکی در سیستم‌های واقعی خیلی مهم است.
	
	فرض کن امروز این event را داریم:
	
	```json
	{
	  "orderId": 1001,
	  "amount": 120
	}
	```
	
	Consumerها انتظار همین structure را دارند.
	
	```text
	Order Service
	     |
	     | OrderCreated
	     v
	Kafka
	     |
	     +--> Payment Service
	     |
	     +--> Analytics Service
	```
	
	هر consumer کدی دارد شبیه:
	
	```java
	event.getOrderId();
	event.getAmount();
	```
	
	حالا چند ماه بعد business می‌گوید currency هم اضافه کنیم:
	
	```json
	{
	  "orderId": 1001,
	  "amount": 120,
	  "currency": "EUR"
	}
	```
	
	این تغییر معمولاً اگر additive باشد، قابل مدیریت‌تر است.
	
	اما فرض کن field را rename کنیم:
	
	قبلاً:
	
	```json
	{
	  "amount": 120
	}
	```
	
	جدید:
	
	```json
	{
	  "totalAmount": 120
	}
	```
	
	Consumer قدیمی همچنان دنبال:
	
	```text
	amount
	```
	
	می‌گردد.
	
	ولی producer دیگر:
	
	```text
	totalAmount
	```
	
	می‌فرستد.
	
	پس ممکن است consumer بشکند:
	
	```text
	Producer V2
	    |
	    | totalAmount
	    v
	Kafka
	    |
	    v
	Consumer V1
	    |
	    X
	expects "amount"
	```
	
	این مشکل همان schema compatibility است.
	
	## Event Versioning یعنی چه؟
	
	یعنی eventها را version کنیم.
	
	مثلاً:
	
	```text
	OrderCreatedV1
	OrderCreatedV2
	```
	
	نسخه اول:
	
	```json
	{
	  "version": 1,
	  "orderId": 1001,
	  "amount": 120
	}
	```
	
	نسخه دوم:
	
	```json
	{
	  "version": 2,
	  "orderId": 1001,
	  "amount": 120,
	  "currency": "EUR"
	}
	```
	
	Consumer می‌تواند بگوید:
	
	```text
	if version == 1
	   process V1
	
	if version == 2
	   process V2
	```
	
	مثلاً:
	
	```text
	Kafka
	 |
	 +--> OrderCreated v1
	 |
	 +--> OrderCreated v2
	```
	
	یا event type جدا داشته باشیم:
	
	```text
	order.created.v1
	order.created.v2
	```
	
	بسته به design.
	
	### Backward Compatibility
	
	فرض کن schema قدیمی:
	
	```json
	{
	  "orderId": 1,
	  "amount": 100
	}
	```
	
	schema جدید:
	
	```json
	{
	  "orderId": 1,
	  "amount": 100,
	  "currency": "EUR"
	}
	```
	
	Consumer قدیمی `currency` را نمی‌شناسد ولی هنوز می‌تواند:
	
	```text
	orderId
	amount
	```
	
	را بخواند.
	
	پس تغییر backward-compatible است.
	
	به‌صورت ذهنی:
	
	```text
	Old Consumer
	     |
	     v
	New Event
	
	known fields:
	orderId   OK
	amount    OK
	
	unknown:
	currency  ignored
	```
	
	اما حذف field خطرناک‌تر است.
	
	مثلاً از:
	
	```json
	{
	  "orderId": 1,
	  "amount": 100
	}
	```
	
	برسیم به:
	
	```json
	{
	  "orderId": 1
	}
	```
	
	Consumer قدیمی:
	
	```text
	event.amount
	```
	
	را لازم دارد و ممکن است fail کند.
	
	### تغییرهای نسبتاً امن
	
	معمولاً:
	
	```text
	Adding optional field
	        |
	        v
	safer
	```
	
	مثلاً:
	
	```json
	{
	  "orderId": 100,
	  "currency": "EUR"
	}
	```
	
	### تغییرهای خطرناک
	
	```text
	rename field
	remove field
	change type
	change meaning
	```
	
	مثلاً type قبلاً:
	
	```json
	"amount": 100
	```
	
	و بعد شود:
	
	```json
	"amount": "100 EUR"
	```
	
	Consumer انتظار `number` داشته:
	
	```text
	number expected
	      |
	      v
	string received
	      |
	      X
	```
	
	---
	
	# یک تصویر کلی برای هر سه مفهوم
	
	```text
	                    Messaging Challenges
	                           |
	          +----------------+----------------+
	          |                |                |
	          v                v                v
	      Ordering        Consumer Down      Schema Change
	          |                |                |
	          v                v                v
	 Partition/Key         Backlog/Lag       Versioning
	          |                |                |
	          v                v                v
	same entity          messages wait      compatibility
	same partition       until recovery     between versions
	```
	
	اگر بخواهم خیلی خلاصه بگویم:
	
	```text
	Ordering
	---------
	همه eventهای مربوط به یک entity
	را به یک partition بفرست.
	
	Consumer Down
	-------------
	پیام‌ها باقی می‌مانند و backlog/lag
	ایجاد می‌شود تا consumer برگردد.
	
	Schema Changes
	--------------
	Producer و Consumer ممکن است
	نسخه‌های متفاوت داشته باشند،
	پس schema را backward-compatible
	و versioned طراحی کن.
	```
	
	برای مصاحبه، سه عبارت خیلی مهمی که کنار این‌ها باید یادت بماند این‌ها هستند:
	
	```text
	Ordering        -> Message Key / Partition
	Consumer Down   -> Consumer Lag / Backlog
	Schema Changes  -> Backward Compatibility / Event Versioning
	```
