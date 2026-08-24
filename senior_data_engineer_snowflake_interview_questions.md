# Senior Data Engineer Interview Preparation
## Snowflake • Fivetran • Kafka • ELT/ETL • CDC • SQL • Data Governance

> هدف این فایل: آمادگی برای مصاحبه‌ی Senior Data Engineer با تمرکز روی شرح شغل ارائه‌شده.
>
> **روش استفاده:** ابتدا سؤال‌های ⭐ را تمرین کن. برای هر سؤال senior-level سعی کن پاسخ را با این ساختار بدهی:
> **Context → Design/Decision → Trade-off → Reliability/Cost → Monitoring → Failure handling**

---

# 0. Top 30 — Must Know Questions ⭐

این ۳۰ سؤال بیشترین تطابق را با شرح شغل دارند.

1. ⭐ Walk me through an end-to-end data pipeline you designed, from source systems to Snowflake and downstream consumers.
2. ⭐ What is the difference between ETL and ELT, and why is ELT commonly used with Snowflake?
3. ⭐ How would you design ingestion from multiple operational databases into Snowflake using Fivetran?
4. ⭐ What is Change Data Capture (CDC), and when would you prefer it over timestamp-based incremental loading?
5. ⭐ How do you design an incremental data load so that rerunning the pipeline does not create duplicates?
6. ⭐ How would you handle inserts, updates, and deletes from a source database in Snowflake?
7. ⭐ What is the difference between incremental load, CDC, and full refresh?
8. ⭐ How would you recover a pipeline after it has been failing for several hours?
9. ⭐ What happens when the schema of a source table changes unexpectedly?
10. ⭐ What is schema evolution, and how would you make it safe for downstream consumers?
11. ⭐ Explain Snowflake's architecture and the separation of storage and compute.
12. ⭐ What is a Snowflake virtual warehouse, and how do you choose its size?
13. ⭐ How would you troubleshoot a slow Snowflake query?
14. ⭐ How do micro-partitions work in Snowflake?
15. ⭐ What is clustering in Snowflake, and when is a clustering key useful?
16. ⭐ What is Snowflake Time Travel, and where is it useful in data engineering?
17. ⭐ What are Snowflake Streams and Tasks?
18. ⭐ When would you use Dynamic Tables instead of Streams + Tasks?
19. ⭐ How would you reduce Snowflake cost without hurting SLAs?
20. ⭐ What is Fivetran's role in an ELT architecture?
21. ⭐ How does Fivetran perform incremental synchronization?
22. ⭐ What is the difference between Fivetran soft-delete mode and history mode?
23. ⭐ What would you monitor for a Fivetran connector in production?
24. ⭐ Explain Kafka topics, partitions, offsets, and consumer groups.
25. ⭐ How would you ingest Kafka events into Snowflake while handling duplicates and late events?
26. ⭐ What does "exactly once" really mean in a distributed data pipeline?
27. ⭐ How would you implement data-quality checks in a production pipeline?
28. ⭐ What is a data contract, and what should it contain?
29. ⭐ How would you design a reverse ETL pipeline from Snowflake to an operational API?
30. ⭐ Describe a difficult production data incident you would expect a Senior Data Engineer to own. How would you diagnose and resolve it?

---

# 1. Data Engineering Fundamentals

31. What are the main responsibilities of a Data Engineer compared with a Data Analyst, Analytics Engineer, and Data Scientist?
32. What are the major components of a modern data platform?
33. What properties make a data pipeline production-grade?
34. What is the difference between batch, micro-batch, and streaming processing?
35. When would you choose batch processing instead of streaming?
36. What is data latency, and how would you define a latency SLA?
37. What is data freshness?
38. What is data completeness?
39. What is data correctness?
40. What is data consistency?
41. What is idempotency, and why is it important in data pipelines?
42. What is an idempotency key?
43. What is backfilling?
44. How do you safely perform a backfill without corrupting current production data?
45. What is replayability in a data pipeline?
46. What is a watermark in data processing?
47. What is event time versus processing time?
48. How do you handle late-arriving data?
49. How do you deal with out-of-order events?
50. How would you handle duplicate records arriving from a source?
51. What is the difference between at-most-once, at-least-once, and exactly-once processing?
52. What is eventual consistency, and where is it acceptable in an analytics platform?
53. What is a dead-letter queue, and when should you use one?
54. What is a poison message?
55. How would you design retry behavior for a data pipeline?
56. Why can unlimited retries be dangerous?
57. How would you prevent a bad source record from blocking an entire ingestion pipeline?
58. What metadata would you attach to ingested records for observability and auditability?
59. How would you identify the source and ingestion time of every row in a warehouse?
60. What does reproducibility mean for analytical datasets?

---

# 2. ETL vs ELT

61. Explain ETL.
62. Explain ELT.
63. What are the advantages of ELT with cloud data warehouses?
64. What are the disadvantages of ELT?
65. In what situation would traditional ETL still be preferable?
66. Why might you keep a raw/staging layer before transformations?
67. Should raw data ever be modified after ingestion?
68. How would you structure RAW, STAGING, INTERMEDIATE, and MART layers?
69. What transformations belong in the ingestion layer versus the transformation layer?
70. How would you make transformations testable and repeatable?
71. What is pushdown processing?
72. Why is pushing transformations into Snowflake often efficient?
73. When can excessive transformation inside Snowflake become expensive?
74. What is orchestration, and how is it different from transformation?
75. How do you model dependencies between ingestion and transformation jobs?
76. How do you avoid running transformations before all required source data is available?

---

# 3. Incremental Loads, CDC, Full Refresh

77. ⭐ Compare full refresh, incremental load, and CDC.
78. What are common ways to identify changed rows?
79. How does timestamp-based incremental loading work?
80. What can go wrong when using `updated_at` as an incremental cursor?
81. How do you handle two rows with the same `updated_at` timestamp?
82. What happens if source clocks are inaccurate?
83. How can a high-water mark be used?
84. Where should the pipeline persist its high-water mark?
85. When should the high-water mark be committed?
86. What is log-based CDC?
87. Why is log-based CDC generally more reliable than polling `updated_at`?
88. What operational impact can CDC have on a transactional database?
89. How would you ingest database deletes using CDC?
90. How would you model hard deletes in an analytical warehouse?
91. When would you use soft deletes?
92. How do you handle a CDC connector being offline longer than the source's log-retention period?
93. What is an initial snapshot?
94. How do you transition safely from an initial snapshot to continuous CDC?
95. How do you guarantee there is no gap between snapshot data and CDC events?
96. When is a full refresh safer than incremental processing?
97. What risks are involved in running a full refresh on a multi-terabyte table?
98. How would you perform a zero/minimal-downtime full reload?
99. How would you compare source and destination to prove that an incremental pipeline is complete?
100. What reconciliation metrics would you maintain?

---

# 4. Snowflake Architecture ⭐

101. ⭐ Explain Snowflake's three major architectural layers.
102. What does separation of compute and storage mean?
103. Why is separation of compute and storage useful for concurrent workloads?
104. What is a virtual warehouse?
105. Can multiple virtual warehouses query the same data?
106. When would you create separate warehouses for ingestion, transformation, BI, and data science?
107. What is warehouse auto-suspend?
108. What is warehouse auto-resume?
109. How would you choose an appropriate auto-suspend value?
110. What is a multi-cluster warehouse?
111. What type of problem does a multi-cluster warehouse solve?
112. Scaling up versus scaling out in Snowflake: what is the difference?
113. When is resizing a warehouse useful?
114. What are Snowflake credits?
115. What are the main drivers of Snowflake cost?
116. How would you allocate Snowflake cost to different teams or workloads?
117. What are resource monitors?
118. How would you protect the company from a runaway Snowflake workload?

---

# 5. Snowflake Storage, Micro-Partitions, Clustering

119. ⭐ What is a Snowflake micro-partition?
120. Does a developer manually create Snowflake micro-partitions?
121. What metadata does Snowflake maintain for micro-partitions?
122. What is partition pruning?
123. Why can partition pruning dramatically improve performance?
124. What query patterns reduce pruning effectiveness?
125. What is clustering depth?
126. What is a clustering key?
127. When should you consider defining a clustering key?
128. Why should you not add clustering keys to every table?
129. How can very high-cardinality columns affect clustering decisions?
130. How can frequent DML affect clustering?
131. What is automatic clustering?
132. What trade-off exists between clustering performance and clustering cost?
133. How would you determine whether clustering actually improved a workload?

---

# 6. Snowflake Query Performance ⭐

134. ⭐ A query that used to run in 20 seconds now takes 8 minutes. How do you investigate it?
135. What would you look for in Snowflake Query Profile?
136. What is partition pruning, and how can Query Profile reveal poor pruning?
137. How can joins cause a Snowflake query to become expensive?
138. What happens if a join key is not selective?
139. What is data skew?
140. How can data skew affect large joins or aggregations?
141. How would you optimize a query scanning far more data than expected?
142. How would you optimize repeated dashboard queries?
143. What is Snowflake result caching?
144. What is warehouse/local disk caching?
145. Why might a query suddenly become slower immediately after warehouse resume?
146. When is materialization useful?
147. How would you decide between a view, materialized view, table, and dynamic table?
148. Why can `SELECT *` be problematic in analytical workloads?
149. How can poorly written CTEs or repeated transformations affect cost?
150. When would you pre-aggregate data?
151. What metrics would you use to compare performance before and after optimization?
152. How do you optimize performance without simply increasing warehouse size?

---

# 7. Snowflake Streams, Tasks, Dynamic Tables ⭐

153. ⭐ What is a Snowflake Stream?
154. Does a Stream store a complete copy of changed table data?
155. What types of changes can a standard Stream expose?
156. What are `METADATA$ACTION`, `METADATA$ISUPDATE`, and `METADATA$ROW_ID`?
157. How would you use a Stream to implement incremental transformation?
158. What does it mean for a Stream to become stale?
159. How would you prevent Stream staleness?
160. What is a Snowflake Task?
161. How can Tasks be chained?
162. How would you execute a Task only when new Stream data exists?
163. What happens when a Task fails?
164. How would you make the DML executed by a Task idempotent?
165. ⭐ What is a Dynamic Table?
166. How is a Dynamic Table different from a normal table?
167. How is a Dynamic Table different from a materialized view?
168. What is `TARGET_LAG`?
169. Why is `TARGET_LAG` a freshness target rather than simply a cron interval?
170. What is incremental refresh for Dynamic Tables?
171. When might a Dynamic Table fall back to or require full refresh?
172. ⭐ When would you choose Dynamic Tables instead of Streams + Tasks?
173. When would Streams + Tasks still be preferable?
174. How would you monitor Dynamic Table refresh failures and freshness?
175. Can Streams be created on Dynamic Tables, and why might that be useful?

---

# 8. Snowflake Time Travel, Zero-Copy Cloning, Recovery

176. What is Snowflake Time Travel?
177. What use cases do you have for Time Travel?
178. How can Time Travel help recover accidentally deleted or updated data?
179. What is the relationship between Time Travel and data retention?
180. What is Fail-safe conceptually?
181. What is zero-copy cloning?
182. Why is zero-copy cloning useful for development and testing?
183. How would you use cloning to test a risky migration?
184. What happens to storage consumption after a clone starts diverging from its source?
185. How could cloning help a data backfill or incident investigation?

---

# 9. Snowflake Security and Access Control ⭐

186. ⭐ Explain Snowflake RBAC.
187. What is the difference between a user, role, privilege, and object ownership?
188. Why is granting privileges to roles preferable to granting directly to users?
189. What does least privilege mean?
190. What is the difference between `USAGE` and `SELECT`?
191. What is a future grant?
192. How would you structure roles for Data Engineers, Analysts, Data Scientists, and BI tools?
193. What is a service account and how should its permissions differ from a human user?
194. What is Dynamic Data Masking?
195. What is a masking policy?
196. What is a Row Access Policy?
197. When would you use row-level security?
198. What are tags used for in Snowflake governance?
199. How would you protect PII columns?
200. How would you allow analysts to query customer data without seeing email addresses or phone numbers?
201. What audit information would you retain for sensitive-data access?
202. How would you rotate credentials without breaking ingestion pipelines?
203. Why should secrets never be stored directly in pipeline code?
204. How would you secure Fivetran access to Snowflake?
205. How would you secure Kafka credentials and Snowflake credentials?

---

# 10. Fivetran Fundamentals ⭐

206. ⭐ What problem does Fivetran solve?
207. Where does Fivetran fit into an ELT architecture?
208. How is a managed connector different from writing your own ingestion code?
209. What advantages does Fivetran provide over custom ingestion?
210. What disadvantages or limitations can a managed ingestion tool introduce?
211. What is a Fivetran connector?
212. What is a destination?
213. How would you configure multiple source systems to load into Snowflake?
214. What would you consider when deciding the sync frequency?
215. How does sync frequency affect freshness and cost?
216. How does Fivetran perform incremental synchronization conceptually?
217. Why do different source databases require different CDC mechanisms?
218. How would you verify that a connector is correctly capturing deletes?
219. What is a re-sync?
220. When would you perform a table re-sync?
221. What are the risks of performing a re-sync on a very large table?
222. How would you minimize downstream impact during a Fivetran re-sync?
223. What metadata columns added by Fivetran have you seen or would you expect?
224. Why are ingestion metadata fields important?
225. How would you troubleshoot a connector whose source data is not appearing in Snowflake?

---

# 11. Fivetran Soft Delete / History Mode / Cost ⭐

226. ⭐ What is Fivetran soft-delete mode?
227. What does `_fivetran_deleted` represent?
228. How should downstream models filter soft-deleted rows when they only need the current state?
229. ⭐ What is Fivetran history mode?
230. How is history mode related to Slowly Changing Dimension Type 2?
231. When would history mode be useful?
232. When would history mode be wasteful?
233. What happens to storage and ingestion volume when a frequently updated table uses history mode?
234. How would you decide which source tables need history mode?
235. What is MAR (Monthly Active Rows) conceptually?
236. Which pipeline design decisions can unexpectedly increase Fivetran cost?
237. How would you control Fivetran cost while maintaining required freshness?
238. What should be checked before enabling history mode for a large table?

---

# 12. Fivetran + dbt / Transformations

239. What role can dbt play after data is ingested by Fivetran?
240. Why is dbt usually considered part of the transformation layer rather than ingestion?
241. What are dbt models?
242. What are dbt tests?
243. What is the difference between a dbt view, table, incremental model, and ephemeral model?
244. What is a dbt incremental model?
245. How would you make a dbt incremental model idempotent?
246. What does `unique_key` mean in an incremental transformation?
247. How would you test uniqueness and non-null constraints in dbt?
248. What is a dbt source freshness test?
249. How can ingestion completion trigger downstream transformations?
250. How would you avoid running a transformation with only half of the expected source datasets loaded?
251. What should happen if ingestion succeeds but transformation fails?
252. How would you rerun only the failed transformation without re-ingesting source data?
253. Why is keeping raw source data useful after a transformation failure?

---

# 13. Kafka Fundamentals ⭐

254. ⭐ What is Kafka?
255. What is a topic?
256. What is a partition?
257. Why are Kafka topics partitioned?
258. What determines ordering guarantees in Kafka?
259. Is ordering guaranteed across an entire multi-partition topic?
260. What is an offset?
261. What is a consumer group?
262. How are partitions assigned to consumers in a group?
263. What happens when the number of consumers is greater than the number of partitions?
264. What is a consumer-group rebalance?
265. What can trigger a rebalance?
266. Why can frequent rebalancing be harmful?
267. What is consumer lag?
268. How would you monitor consumer lag?
269. What does a rapidly increasing consumer lag tell you?
270. What is message retention?
271. How is Kafka retention different from acknowledging/consuming a traditional queue message?
272. What is compaction?
273. When would a compacted topic be useful for data engineering?

---

# 14. Kafka Reliability, Delivery Semantics, Schema Evolution ⭐

274. ⭐ Explain at-most-once, at-least-once, and exactly-once semantics in Kafka.
275. Why is exactly-once across Kafka and an external database difficult?
276. What is an idempotent Kafka producer?
277. What are Kafka transactions conceptually?
278. How would you avoid duplicate rows if a Kafka event is delivered more than once?
279. Would you commit the Kafka offset before or after writing to the destination? Explain the trade-off.
280. How would you replay a Kafka topic from an older offset?
281. What happens if replayed events are written again to Snowflake?
282. What key would you use to deduplicate events?
283. What is Schema Registry?
284. ⭐ What is backward schema compatibility?
285. What is forward schema compatibility?
286. What is full compatibility?
287. How would you add a new field to an event without breaking existing consumers?
288. How would you remove or rename a field safely?
289. What is a breaking schema change?
290. Who should own the schema contract for a shared Kafka topic?
291. How would you test schema compatibility in CI/CD?
292. What would you do if a producer deploys an incompatible schema?

---

# 15. Kafka → Snowflake Design Scenarios

293. ⭐ Design a pipeline that ingests millions of Kafka events per hour into Snowflake.
294. Would you insert each Kafka event into Snowflake individually? Why or why not?
295. What batching strategy would you consider?
296. How would you balance throughput against ingestion latency?
297. How would you handle duplicate Kafka events?
298. How would you handle malformed messages?
299. Where would malformed events be stored for later investigation?
300. How would you handle a Snowflake outage while Kafka continues receiving events?
301. What prevents data loss during a prolonged downstream outage?
302. What retention configuration would you verify before relying on Kafka for replay?
303. How would you reconcile the number of source Kafka events with rows loaded into Snowflake?
304. How would you handle events arriving several days late?
305. How would you partition a high-volume event topic?
306. What factors would you use when choosing the Kafka message key?
307. How could a poor message-key choice create hot partitions?
308. How would you monitor this end-to-end pipeline?
309. Which metrics would tell you whether the bottleneck is Kafka, ingestion, Snowflake, or transformation?

---

# 16. SQL — Senior Level Core Questions ⭐

310. ⭐ Explain `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, and `FULL OUTER JOIN`.
311. What is the difference between `WHERE` and `HAVING`?
312. What is the difference between `UNION` and `UNION ALL`?
313. What is the difference between `DISTINCT` and `GROUP BY`?
314. What is a window function?
315. What is the difference between `GROUP BY` aggregation and a window function?
316. Explain `ROW_NUMBER()`, `RANK()`, and `DENSE_RANK()`.
317. How do you find the latest record for each customer?
318. How do you find duplicate records?
319. How do you delete or logically remove duplicates while preserving one canonical record?
320. How do you calculate a running total?
321. How do you calculate a moving 7-day average?
322. How do `LAG()` and `LEAD()` work?
323. How would you detect changes between consecutive versions of a record?
324. What is a CTE?
325. When can a recursive CTE be useful?
326. What is a correlated subquery?
327. What is the difference between `EXISTS` and `IN`?
328. When can `NOT IN` behave unexpectedly because of NULLs?
329. What does `MERGE` do?
330. Why is `MERGE` useful for incremental pipelines?
331. What conditions can cause a `MERGE` to create incorrect results?
332. How do NULLs behave in equality comparisons?
333. What is `COALESCE`?
334. How would you safely divide when the denominator might be zero?
335. What is the logical execution order of a SQL query?
336. Why can filtering before a join improve performance?
337. What is a Cartesian product and how can it happen accidentally?

---

# 17. SQL Practical Exercises

## Exercise 1 — Latest Customer Version

Table:

```text
customer_history
----------------
customer_id
name
email
updated_at
```

338. Write a query that returns only the latest row for each `customer_id`.

---

## Exercise 2 — Find Duplicates

Table:

```text
orders
------
order_id
customer_id
amount
created_at
```

339. Find every `order_id` that appears more than once.
340. Return the complete duplicated rows, not just the IDs.

---

## Exercise 3 — Daily Revenue

341. Calculate total revenue by day.
342. Add a 7-day moving average.
343. Add cumulative revenue from the beginning of the month.

---

## Exercise 4 — Top Customers

344. Return the top 3 customers by revenue for each month.

---

## Exercise 5 — CDC Merge

Staging table:

```text
order_changes
-------------
order_id
amount
status
operation       -- I / U / D
event_timestamp
```

Target table:

```text
orders
------
order_id
amount
status
updated_at
```

345. Design a Snowflake `MERGE` strategy for inserts and updates.
346. How would you handle delete events?
347. What happens if two changes for the same `order_id` arrive in the same batch?
348. Modify your approach so only the latest change for each `order_id` is applied.

---

## Exercise 6 — Late Events

349. A daily sales table has already been aggregated, but an order from three days ago arrives late. How would you update the aggregate correctly?

---

## Exercise 7 — Data Reconciliation

350. Source contains 10,000,000 orders. Destination contains 9,998,400.

How would you identify the missing records efficiently without blindly transferring all source data?

---

# 18. Data Modeling

351. What is dimensional modeling?
352. What is a fact table?
353. What is a dimension table?
354. What is the grain of a fact table?
355. Why should grain be defined before designing a fact table?
356. What is a star schema?
357. What is a snowflake schema?
358. What are the trade-offs between normalized and denormalized analytical models?
359. What is a surrogate key?
360. Why might a dimension use a surrogate key instead of a source-system natural key?
361. What is a Slowly Changing Dimension?
362. Explain SCD Type 1.
363. Explain SCD Type 2.
364. When would you choose Type 1 versus Type 2?
365. How would you model a customer changing address while preserving history?
366. What is an accumulating snapshot fact table?
367. How would you model many-to-many relationships in analytics?
368. What is a bridge table?
369. What is a semantic layer?
370. Why is consistent business metric definition important?

---

# 19. Data Quality & Testing ⭐

371. ⭐ What dimensions of data quality do you monitor?
372. What is the difference between data validation and data reconciliation?
373. What are examples of schema-level tests?
374. What are examples of row-level tests?
375. What are examples of aggregate/business-level tests?
376. How would you test that a pipeline did not lose records?
377. How would you test freshness?
378. How would you test uniqueness?
379. How would you test referential integrity?
380. How would you detect unexpected NULL increases?
381. How would you detect a sudden 90% reduction in daily source volume?
382. Should a pipeline stop when a data-quality test fails?
383. Which quality failures should block publication versus only create warnings?
384. What is quarantine data?
385. How would you route invalid records into quarantine?
386. How would you allow corrected quarantined records to be replayed?
387. What is a data-quality SLA/SLO?
388. Who should be alerted when data quality fails?
389. How do you avoid alert fatigue?
390. How would you prove that a repaired pipeline produced correct data after an incident?

---

# 20. Observability & Monitoring ⭐

391. ⭐ What would you monitor in an end-to-end data pipeline?
392. What is the difference between infrastructure monitoring and data observability?
393. What pipeline metrics would you expose?
394. What is pipeline lag?
395. How would you calculate end-to-end freshness?
396. What is throughput?
397. What is error rate?
398. What is retry rate?
399. What is backlog?
400. How would you detect a pipeline that is technically "green" but loading stale data?
401. How would you detect silent data loss?
402. Why should row counts alone not be your only reconciliation mechanism?
403. What metadata would you log for each pipeline run?
404. How would you correlate a source batch with Snowflake data?
405. What dashboards would you provide to Data Engineering operations?
406. What alerts should wake someone up immediately versus wait until business hours?
407. What would an incident runbook for a broken ingestion pipeline contain?
408. What should be included in a postmortem after a major data incident?

---

# 21. Data Contracts & Schema Evolution ⭐

409. ⭐ What is a data contract?
410. Who are the producer and consumer in a data contract?
411. What fields would you include in a data contract?
412. Should a data contract contain only schema information?
413. How would you include data-quality expectations in a contract?
414. How would you define ownership in a data contract?
415. How would you define freshness expectations?
416. How would you version a data contract?
417. How should breaking changes be communicated?
418. What is backward-compatible schema evolution?
419. What is a breaking schema change in a database table?
420. Is adding a nullable column generally backward compatible?
421. Is renaming a column backward compatible?
422. How would you safely rename an important column used by many consumers?
423. How would you deprecate an old field?
424. How long should a compatibility/deprecation period last?
425. How could CI/CD prevent incompatible schema changes?
426. What should the ingestion pipeline do if a source suddenly changes a column from numeric to string?
427. How do you avoid silently coercing bad schema changes?
428. What is schema drift?
429. Which schema changes can be handled automatically and which should require human approval?

---

# 22. Governance, Ownership, Lineage, Discoverability ⭐

430. ⭐ What is data governance?
431. What is the difference between data owner and data steward?
432. Who should own a dataset: the platform team or the domain/business team?
433. What is data lineage?
434. Why is column-level lineage useful?
435. How would lineage help during an incident?
436. What is data discoverability?
437. What information should be available in a data catalog?
438. What makes data documentation trustworthy?
439. How do you prevent documentation from becoming stale?
440. What is a certified dataset?
441. How would you identify authoritative datasets when several teams have created similar tables?
442. How would you document business definitions for critical metrics?
443. What is data classification?
444. How would you classify PII, confidential, internal, and public data?
445. How could tags be used to enforce governance?
446. What is retention policy?
447. How would you implement deletion requirements for personal data?
448. How do governance controls affect downstream analytics and ML?

---

# 23. Reverse ETL ⭐

449. ⭐ What is reverse ETL?
450. How is reverse ETL different from normal ELT?
451. Give examples of reverse ETL destinations.
452. Why might CRM or operational systems need data computed in Snowflake?
453. How would you send customer scores from Snowflake to an external REST API?
454. How would you design the reverse ETL pipeline to be idempotent?
455. What should the idempotency key be?
456. How would you prevent sending the same update twice?
457. How do you handle API rate limits?
458. How do you handle HTTP 429 responses?
459. Which HTTP errors should be retried?
460. How would you implement exponential backoff?
461. Why should retries include jitter?
462. How would you handle partial success when sending a batch?
463. How would you track which Snowflake rows were successfully delivered?
464. What happens if the API accepts a request but your process crashes before recording success?
465. How would you reconcile Snowflake state with the operational system?
466. How would you protect sensitive data sent through reverse ETL?
467. What would you monitor in a reverse ETL pipeline?
468. How would you pause delivery if downstream data quality becomes invalid?

---

# 24. Security & Privacy

469. What does least-privilege access look like in a modern data platform?
470. How would you separate production and non-production data access?
471. Should developers have unrestricted access to production PII?
472. How would you create anonymized or masked development datasets?
473. How do encryption at rest and encryption in transit differ?
474. How should credentials be stored?
475. What is credential rotation?
476. What is network allowlisting?
477. When would private networking/private endpoints be useful?
478. What should be logged for compliance?
479. How would you handle a request to delete all data belonging to a customer?
480. How could replicated data create privacy-compliance risks?
481. How would you discover every copy of a sensitive field across the platform?
482. Why is lineage useful for privacy requests?
483. What security considerations apply to Fivetran connectors?
484. What security considerations apply to reverse ETL?
485. What security considerations apply to Kafka topics containing PII?

---

# 25. Reliability & Failure Scenarios ⭐

486. ⭐ The Fivetran dashboard says a sync succeeded, but today's data is missing. What do you check?
487. ⭐ A source table has 20 million rows, but Snowflake only has 19.8 million. How do you investigate?
488. ⭐ A pipeline accidentally loaded the same batch twice. How do you fix the data and prevent recurrence?
489. ⭐ A source system added a column and your downstream dbt model started failing. What do you do?
490. ⭐ A source system renamed a critical column without notice. What is your incident response?
491. ⭐ Kafka consumer lag is increasing continuously. How do you troubleshoot it?
492. ⭐ One Kafka partition has 20x the traffic of all other partitions. What likely happened?
493. ⭐ Snowflake cost doubled this month, but data volume only grew 10%. How would you investigate?
494. ⭐ A Snowflake dashboard query suddenly scans ten times more data. What might have changed?
495. ⭐ A Stream has become stale. How do you recover?
496. ⭐ A Dynamic Table can no longer meet its target lag. What do you investigate?
497. ⭐ An incremental pipeline missed records because of a timestamp-boundary bug. How do you recover?
498. ⭐ You need to backfill six months of data while production ingestion must continue. Design the approach.
499. ⭐ Source data arrives twice: once through batch and once through an event stream. How do you prevent double counting?
500. ⭐ A downstream API is unavailable for four hours. How should reverse ETL behave?
501. ⭐ A data-quality check finds negative transaction amounts that should be impossible. Do you stop the entire pipeline?
502. ⭐ A business stakeholder says today's dashboard is wrong but all pipelines are green. How do you approach the problem?
503. ⭐ You discover an incorrect transformation has been running for three months. How do you assess impact and repair the data?
504. ⭐ A full refresh would take 12 hours but the SLA is 30 minutes. What alternatives would you consider?
505. ⭐ A source system cannot provide CDC. How would you build efficient incremental ingestion anyway?

---

# 26. Architecture / System Design Questions ⭐

## Scenario A — SaaS + Database → Snowflake

You have:

```text
Salesforce
PostgreSQL
MySQL
REST API
        │
        ▼
     Fivetran
        │
        ▼
     Snowflake
        │
        ▼
       dbt
        │
        ▼
 BI / Analytics / ML
```

506. Design this platform for reliability and scalability.
507. How would you separate raw data from curated data?
508. How would you orchestrate transformations after ingestion?
509. How would you monitor source freshness?
510. How would you handle schema changes?
511. How would you secure source credentials?
512. How would you isolate workloads in Snowflake?
513. How would you reduce cost?
514. How would you implement lineage and ownership?
515. How would you backfill one source without impacting all others?

---

## Scenario B — Real-Time Events

```text
Applications
    │
    ▼
  Kafka
    │
    ▼
Ingestion layer
    │
    ▼
 Snowflake
    │
    ├── BI
    ├── ML
    └── Reverse ETL
```

516. Design the pipeline.
517. Where do you guarantee ordering?
518. How do you manage event schemas?
519. How do you deduplicate events?
520. How do you recover after consumer failure?
521. How do you replay historical events?
522. How do you handle late events?
523. How do you handle poison events?
524. How do you monitor end-to-end latency?
525. What happens if Snowflake is unavailable?

---

## Scenario C — 5 TB Table

A transactional table has 5 TB of data and changes continuously.

526. Would you perform a daily full refresh? Why?
527. What CDC strategy would you choose?
528. What if the database does not expose transaction logs?
529. How would you perform the initial load?
530. How would you validate the initial load?
531. How would you reconcile CDC after the snapshot?
532. How would you handle deletes?
533. How would you perform a historical backfill?
534. How would you recover if the CDC cursor is lost?
535. What observability would you implement?

---

# 27. Cost Optimization Questions ⭐

536. What are the major Snowflake cost categories?
537. How can warehouse auto-suspend reduce cost?
538. Can an auto-suspend setting that is too aggressive hurt performance?
539. How would you determine whether a warehouse is oversized?
540. How would you determine whether a warehouse is undersized?
541. Why might separate warehouses improve both reliability and cost attribution?
542. How can badly designed queries increase Snowflake cost?
543. How can poor partition pruning increase cost?
544. How can clustering improve performance but also increase cost?
545. How can repeated full refreshes increase cost?
546. How can Fivetran history mode affect cost?
547. How can unnecessary source columns affect ingestion and warehouse cost?
548. How would you prioritize cost optimization without violating business SLAs?
549. What cost KPIs would you report monthly?
550. How would you investigate a sudden increase in credits consumed?

---

# 28. Cloud Data Platform Comparison

551. Compare Snowflake, Databricks, and Redshift at a high level.
552. What workloads are naturally suited to Snowflake?
553. What workloads are naturally suited to Databricks?
554. What is a data warehouse?
555. What is a data lake?
556. What is a lakehouse?
557. What are the trade-offs between warehouse and lakehouse architectures?
558. What is object storage and why is it important in modern data platforms?
559. How does compute/storage separation appear in different cloud data platforms?
560. What factors would you evaluate before migrating from Redshift to Snowflake?
561. What factors would you evaluate before using Databricks together with Snowflake?
562. Why might an organization intentionally use more than one data platform?

---

# 29. CI/CD for Data Engineering

563. How would you implement CI/CD for SQL and data transformations?
564. What should happen when a pull request changes a production data model?
565. Which tests should run before merge?
566. How would you test schema compatibility?
567. How would you validate SQL syntax and dependencies?
568. How would you deploy Snowflake objects between dev, staging, and production?
569. How would you handle environment-specific configuration?
570. How would you handle secrets in CI/CD?
571. How would you roll back a bad transformation deployment?
572. Can data changes always be rolled back in the same way as application code?
573. How would you test a data migration before production?
574. How can zero-copy clones help release testing?
575. What is Infrastructure as Code, and which data-platform resources could be managed that way?
576. What approvals should be required for destructive schema changes?

---

# 30. Senior-Level Design & Trade-Off Questions ⭐

577. ⭐ Build versus buy: when would you choose Fivetran and when would you build a custom connector?
578. ⭐ When does real-time ingestion provide enough business value to justify its complexity?
579. ⭐ When would you prefer a managed service over an open-source framework?
580. ⭐ How do you choose between latency, cost, reliability, and implementation complexity?
581. ⭐ When is a technically elegant solution the wrong business solution?
582. ⭐ How do you decide whether to fix a pipeline or redesign it?
583. ⭐ How do you introduce a new ingestion pattern without disrupting existing consumers?
584. ⭐ How do you migrate a critical pipeline with minimal risk?
585. ⭐ How do you define and enforce engineering standards across multiple data pipelines?
586. ⭐ How do you prevent every team from inventing its own ingestion architecture?
587. ⭐ How would you standardize CDC pipelines?
588. ⭐ How would you standardize full-refresh pipelines?
589. ⭐ How would you standardize reverse ETL pipelines?
590. ⭐ What belongs in a reusable "pipeline template"?
591. ⭐ What operational ownership should remain with the team after a new pipeline goes live?
592. ⭐ How do you decide which problems deserve automation?
593. ⭐ What is technical debt in a data platform?
594. ⭐ How would you prioritize technical debt against new business requests?

---

# 31. Behavioral / Leadership Questions ⭐

595. ⭐ Tell me about a data or production incident you owned end-to-end.
596. ⭐ Tell me about a time you found the root cause of a difficult performance issue.
597. ⭐ Tell me about a time you disagreed with an architectural decision.
598. ⭐ Tell me about a time you improved reliability through automation.
599. ⭐ Tell me about a time you reduced infrastructure or cloud cost.
600. ⭐ Tell me about a time requirements from a business stakeholder were unclear.
601. How do you explain a data problem to a non-technical stakeholder?
602. Tell me about a time you had to choose between speed and quality.
603. How do you prioritize several broken pipelines at the same time?
604. How do you handle an incident where your own change caused the issue?
605. What do you expect from a good code review?
606. How do you review complex SQL written by another engineer?
607. How do you mentor less experienced Data Engineers?
608. How do you introduce engineering standards without slowing a team down?
609. How do you document architectural decisions?
610. How do you handle ownership when several teams contribute to one data product?
611. How would you respond when an analyst reports incorrect data five minutes before an executive meeting?
612. How do you determine when an incident is resolved?
613. What should happen after a major incident besides fixing the immediate bug?
614. How do you measure whether a data platform is improving over time?

---

# 32. Questions Specifically Testing Seniority

615. What would make you reject an otherwise working pipeline design?
616. What failure modes do junior engineers commonly overlook in incremental pipelines?
617. Why is "the pipeline completed successfully" not enough evidence that the data is correct?
618. What are the most dangerous assumptions when building CDC pipelines?
619. How do you design for replay from day one?
620. How do you design pipelines so backfills are safe?
621. What decisions should be reversible?
622. Which architecture decisions are expensive to reverse?
623. How do you prevent hidden coupling between datasets?
624. What is your approach to backward compatibility?
625. When should consumers be insulated from source-system schema?
626. How do you create a stable canonical model when upstream sources change frequently?
627. How do you handle ownership of shared dimensions?
628. How do you define an SLO for a dataset?
629. What information must be available before declaring a dataset production-ready?
630. How do you decide whether a pipeline needs 24/7 on-call support?
631. What types of data errors justify paging an engineer?
632. How do you make data incidents easier to diagnose six months later?
633. What is the difference between fixing data and fixing the pipeline that created bad data?
634. How do you verify that an incident remediation did not create a second problem?

---

# 33. Rapid-Fire Snowflake Round

جواب هر کدام را در 20–40 ثانیه تمرین کن.

635. Database vs schema in Snowflake?
636. Table vs view?
637. Temporary vs transient vs permanent table?
638. What is a stage?
639. Internal vs external stage?
640. What is `COPY INTO`?
641. What is a file format object?
642. What is Snowpipe?
643. What problem does Snowpipe solve?
644. What is Time Travel?
645. What is zero-copy clone?
646. What is a Stream?
647. What is a Task?
648. What is a Dynamic Table?
649. What is a virtual warehouse?
650. What is a resource monitor?
651. What is a micro-partition?
652. What is pruning?
653. What is clustering?
654. What is Query Profile?
655. What is RBAC?
656. What is a masking policy?
657. What is a row access policy?
658. What are tags?
659. What is secure data sharing?
660. What is the result cache?

---

# 34. Rapid-Fire Fivetran Round

661. What is a connector?
662. What is a destination?
663. What is incremental sync?
664. What is CDC?
665. What is soft-delete mode?
666. What is history mode?
667. What does `_fivetran_deleted` mean?
668. What is a re-sync?
669. What causes schema drift?
670. How would you detect a connector delay?
671. How would you verify deletes are replicated?
672. How can history tracking increase cost?
673. What is the benefit of managed connectors?
674. What is the biggest risk of depending on a managed connector?
675. What do you do when a source API changes unexpectedly?

---

# 35. Rapid-Fire Kafka Round

676. Topic?
677. Partition?
678. Offset?
679. Broker?
680. Producer?
681. Consumer?
682. Consumer group?
683. Rebalance?
684. Consumer lag?
685. Retention?
686. Compaction?
687. Message key?
688. At-least-once?
689. Idempotent producer?
690. Schema Registry?
691. Backward compatibility?
692. Dead-letter topic?
693. Replay?
694. Hot partition?
695. Why are duplicates possible?

---

# 36. Interviewer Follow-up Questions You Should Expect

بعد از هر پاسخ معماری، احتمال دارد مصاحبه‌گر یکی از این‌ها را بپرسد:

696. Why did you choose that approach?
697. What alternatives did you consider?
698. What are the trade-offs?
699. What happens if it fails?
700. How do you retry it safely?
701. How do you avoid duplicates?
702. How do you recover missing data?
703. How do you backfill?
704. How do you monitor it?
705. How do you know the data is correct?
706. How do you know it is fresh?
707. How does it scale?
708. What is the bottleneck?
709. How much will it cost?
710. How do you secure it?
711. How do you test it?
712. How do you deploy it?
713. How do you roll it back?
714. How do you handle schema changes?
715. Who owns this pipeline?
716. What documentation would you create?
717. What SLA/SLO would you define?
718. What happens when the downstream system is unavailable?
719. What happens when the upstream system sends bad data?
720. How would your design change at 10x data volume?

---

# 37. Three Mock Interview Sets

## Mock Interview A — Snowflake + ELT (45–60 min)

1. Explain ETL vs ELT.
2. Describe Snowflake architecture.
3. Design PostgreSQL → Fivetran → Snowflake → dbt.
4. Explain incremental load vs CDC vs full refresh.
5. Handle updates and deletes.
6. Explain micro-partitions and pruning.
7. Troubleshoot a slow query.
8. Explain Streams and Tasks.
9. Explain Dynamic Tables.
10. Reduce Snowflake cost.
11. Implement data-quality monitoring.
12. Handle an unexpected schema change.
13. SQL: latest record per customer.
14. SQL: deduplicate a CDC batch.
15. Behavioral: describe a production incident.

---

## Mock Interview B — Kafka + Reliability (45–60 min)

1. Explain Kafka partitions and consumer groups.
2. Explain offsets.
3. Explain consumer lag.
4. Design Kafka → Snowflake ingestion.
5. Explain at-least-once versus exactly-once.
6. Prevent duplicate Snowflake rows.
7. Handle a malformed event.
8. Handle late events.
9. Handle a four-hour Snowflake outage.
10. Replay one day of events.
11. Explain Schema Registry.
12. Explain backward-compatible schema evolution.
13. Design monitoring.
14. Explain reconciliation.
15. Behavioral: describe a difficult root-cause investigation.

---

## Mock Interview C — Senior Architecture (60 min)

1. Design an enterprise data platform using Snowflake and Fivetran.
2. Define standard ingestion patterns for CDC, incremental, and full refresh.
3. Design data contracts.
4. Design schema-evolution governance.
5. Design data-quality gates.
6. Define data ownership and stewardship.
7. Design lineage and discoverability.
8. Design reverse ETL.
9. Design error handling and replay.
10. Define SLAs/SLOs.
11. Define security for PII.
12. Explain cloud-cost controls.
13. Explain CI/CD.
14. Explain disaster/recovery strategy.
15. Describe how you would migrate an existing unreliable pipeline to the new platform.

---

# 38. Self-Assessment Checklist

قبل از مصاحبه باید بتوانی بدون جستجو درباره‌ی موارد زیر 2–5 دقیقه صحبت کنی:

- [ ] Snowflake architecture
- [ ] Virtual warehouses
- [ ] Micro-partitions and pruning
- [ ] Query Profile / performance troubleshooting
- [ ] Clustering
- [ ] Streams
- [ ] Tasks
- [ ] Dynamic Tables
- [ ] Time Travel
- [ ] Zero-copy cloning
- [ ] Snowflake RBAC
- [ ] Masking and row-access policies
- [ ] ETL vs ELT
- [ ] Incremental loading
- [ ] CDC
- [ ] Full refresh
- [ ] Idempotency
- [ ] Backfill/replay
- [ ] Fivetran connector concepts
- [ ] Fivetran soft delete
- [ ] Fivetran history mode
- [ ] Fivetran re-sync
- [ ] Kafka topic/partition/offset
- [ ] Consumer groups and lag
- [ ] Delivery semantics
- [ ] Schema Registry / schema evolution
- [ ] Advanced SQL/window functions
- [ ] `MERGE`
- [ ] Data quality
- [ ] Data contracts
- [ ] Governance
- [ ] Data ownership/stewardship
- [ ] Lineage
- [ ] Reverse ETL
- [ ] Security/PII
- [ ] Monitoring and incident response
- [ ] Cost optimization
- [ ] CI/CD for data pipelines

---

# 39. Best Answer Framework for Scenario Questions

برای سؤال‌های senior مانند:

> "How would you design...?"
>
> "What would you do if...?"
>
> "How would you troubleshoot...?"

پاسخ را با این ترتیب بده:

```text
1. Clarify requirements
       │
       ├── Data volume
       ├── Latency / freshness SLA
       ├── Source capabilities
       ├── Update/delete semantics
       └── Security / compliance
       │
       ▼
2. Choose ingestion pattern
       │
       ├── CDC
       ├── Incremental
       ├── Full refresh
       └── Event streaming
       │
       ▼
3. Define correctness
       │
       ├── Idempotency
       ├── Deduplication
       ├── Ordering
       └── Reconciliation
       │
       ▼
4. Failure handling
       │
       ├── Retry
       ├── DLQ / quarantine
       ├── Replay
       └── Backfill
       │
       ▼
5. Observability
       │
       ├── Freshness
       ├── Volume
       ├── Error rate
       ├── Lag
       └── Data-quality tests
       │
       ▼
6. Performance & cost
       │
       ├── Warehouse sizing
       ├── Query optimization
       ├── Incremental processing
       └── Cost controls
       │
       ▼
7. Governance
       │
       ├── Ownership
       ├── Data contract
       ├── Lineage
       ├── PII/security
       └── Documentation
```

اگر در پاسخ فقط happy path را توضیح بدهی، پاسخ بیشتر mid-level به نظر می‌رسد.

برای senior-level حتماً درباره‌ی **failure, replay, observability, correctness, schema evolution, security و cost** هم صحبت کن.

---

# 40. Final 15 Questions to Rehearse the Night Before

اگر زمان کم بود، فقط این‌ها را چند بار با صدای بلند جواب بده:

1. Describe an end-to-end Snowflake data pipeline you would design for this company.
2. ETL vs ELT — why Snowflake favors ELT?
3. Incremental vs CDC vs full refresh.
4. How do you guarantee idempotency?
5. How do you handle inserts, updates, and deletes?
6. How does Fivetran incremental sync work?
7. Fivetran soft-delete vs history mode.
8. Explain Snowflake architecture, warehouses, and micro-partitions.
9. How do you troubleshoot a slow Snowflake query?
10. Streams + Tasks vs Dynamic Tables.
11. Kafka partitions, consumer groups, offsets, and consumer lag.
12. How do you handle duplicates and late Kafka events?
13. How do you implement data quality and reconciliation?
14. What is a data contract and how do you handle schema evolution?
15. Design a reliable reverse ETL pipeline from Snowflake to a REST API.

---

_End of interview question bank._
