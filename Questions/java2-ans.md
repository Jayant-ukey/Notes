
**1. Slow after deployment**
"First I'd check what actually changed in this release — code diff, config, dependency versions. Then I'd look at infra signals: CPU, memory, GC logs, and DB connection pool metrics via HikariCP. If those look fine, I'd use Actuator or an APM tool to see which specific layer got slower — DB, external call, or app logic. In my experience it's usually one of three things: an N+1 query that got introduced, a connection pool that's now undersized for new traffic, or a cold cache right after restart."

**2. @Transactional not rolling back**
"The most common cause is self-invocation — Spring's @Transactional works through a proxy, so if a method calls another transactional method on `this` within the same class, it bypasses the proxy and the annotation is silently ignored. Second most common: the code throws a checked exception, and by default Spring only rolls back on unchecked exceptions unless you set `rollbackFor = Exception.class`. I'd also check if the exception is being caught and swallowed somewhere, because then Spring never even sees the failure."

**3. Response time up, CPU/memory normal**
"If CPU and memory are fine, the bottleneck is almost always wait time, not compute — threads are blocked, not busy. I'd take a thread dump to see what state threads are in. Usual suspects: a slow downstream call or DB query holding threads longer than before, connection pool exhaustion, or lock contention on a shared resource. I'd also check GC pause times specifically, since a few long pauses can spike latency without moving average memory numbers."

**4. DB connections exhausted at peak**
"I'd check three things: pool sizing versus the DB's actual max_connections limit, whether there's a connection leak — like a resource not closed properly or a long transaction holding a connection during an external call — and whether a slow query is holding connections longer than it should. HikariCP metrics for pending/active connections, plus `pg_stat_activity` or `SHOW PROCESSLIST` on the DB side, usually tell you which of the three it is pretty quickly."

**5. Scheduled job runs multiple times after scaling**
"This happens because Spring's @Scheduled is JVM-local — it has no concept of other instances, so every pod runs its own copy of the job independently. The fix is adding a distributed lock, like ShedLock, so only one instance executes at a time, or moving the job to a clustered Quartz setup or an external scheduler like a Kubernetes CronJob that's inherently single-trigger."

**6. Works locally, fails in production**
"I treat this as an environment-diff problem before assuming it's a code bug. I'd check active Spring profiles and config values, Java version parity, and whether production data has edge cases — nulls, scale, legacy records — that local test data doesn't have. I'd also check for concurrency issues, since local testing is usually single-user and production has real concurrent traffic that can expose race conditions we'd never see otherwise."

**7. Kafka lag increasing, consumers healthy**
"Since consumers aren't failing, this is a throughput problem, not a bug. I'd check consumption rate versus production rate, and look at lag per partition rather than the aggregate, since a hot partition can look fine on average but be badly behind. Usually it comes down to slow per-message processing — like a downstream DB call inside the consumer loop — or simply not having enough partitions to parallelize further."

**8. Retries creating duplicate transactions**
"The core issue is that a client can't always tell 'the request never arrived' from 'it succeeded but the response got lost' — so blind retries can re-execute something that already happened. The fix is idempotency: generate an idempotency key per operation, and have the server check if that key was already processed before executing again. I'd also make sure retries only happen for genuinely safe failure types, with backoff."

**9. One slow query affects the whole app**
"This is resource contention — the slow query holds a DB connection and a thread longer, and since both pools are shared and finite, enough slow requests exhaust that pool for everyone, even calls that don't touch that slow query. I'd fix the query itself first, but longer-term I'd isolate pools per critical path using the bulkhead pattern, add aggressive timeouts, and put a circuit breaker in front of failing dependencies so it doesn't cascade."

**10. Cache stale data**
"I'd first check if there's an explicit invalidation step on the write path, or if we're relying purely on TTL. If we have multiple instances with local in-memory caches instead of a shared cache like Redis, that's a common cause — one instance updates, others don't know. I'd move to cache-aside with explicit eviction on write, keep a TTL as a safety net, and if local caching is required for latency, add event-driven invalidation so all instances stay in sync."

**11. Random 500s, infra looks healthy**
"'Healthy' usually means we're not measuring the right thing. I'd add correlation IDs and distributed tracing to actually catch the failing request path, and check per-instance metrics instead of aggregates, since one bad pod can hide in a healthy-looking average. I'd also specifically check GC pause logs and connection pool metrics at the exact failure timestamps, since brief spikes often don't show up in dashboards sampled per minute."

**12. JVM memory increasing daily**
"I'd confirm it's a real leak first — does memory drop after GC, or does it never come back down even after a full GC? Then I'd take heap dumps at two points in time and diff them using Eclipse MAT's leak suspects report, following the GC roots. Usual culprits in my experience: an unbounded static cache, a ThreadLocal that's never cleared in a pooled thread environment, or listeners that get registered but never deregistered."

**13. Thread pool exhausted, CPU low**
"Low CPU with exhausted threads means threads are blocked on I/O, not computing. I'd take a thread dump immediately and look at what state most threads are in — usually blocked waiting on a slow downstream call, a lock, or a DB query with no timeout set. The fix is adding explicit timeouts everywhere, isolating pools per dependency with the bulkhead pattern, and right-sizing the pool based on actual throughput times latency."

**14. Health checks pass, users still fail**
"Health checks passing usually just means the process is alive, not that it's functional — most default checks don't verify the app can actually reach its real dependencies. I'd add readiness probes tied to actual dependencies like the DB or a critical downstream API, separate from basic liveness checks, and pair that with real error-rate monitoring, since that's what actually reflects what users are experiencing."

**15. External service slow, all APIs impacted**
"I'd apply the standard resilience trio: timeouts so calls fail fast instead of hanging, a circuit breaker like Resilience4j so we stop hammering a failing service after a threshold, and bulkheads so that dependency's slowness is contained to its own pool instead of starving everything else. I'd also add a sensible fallback response so the rest of the app stays functional even when that one dependency is down."

**16. Autoscaling doesn't help**
"Autoscaling only helps if the app tier itself is the bottleneck. If the real constraint is the database connection limit or a rate-limited external API, adding more pods just means more competitors for the same limited resource — and can actually make it worse. I'd check where time is actually being spent using tracing, and compare combined connection pool usage across all pods against the DB's max limit before assuming more pods is the answer."

**17. Deployment increases 4xx / latency, same traffic**
"Since traffic didn't change, the cause is almost always in what shipped. For 4xx spikes, I'd check for a breaking API contract change, stricter validation, or an auth/permission change. For latency with flat traffic, I'd check for a new blocking call, a changed caching behavior, or a dependency version bump. Either way, I'd diff this release directly against the last one rather than guessing."

**18. Hard to trace one request across services**
"The fix is propagating a single trace ID through every hop, sync and async, and using MDC so every log line includes it automatically. I'd adopt OpenTelemetry or Sleuth for actual distributed tracing with a backend like Jaeger or Zipkin, and centralize logs in something like ELK so I can pull an entire request's journey with one trace ID instead of manually correlating timestamps across a dozen services."

**19. Message ordering breaks**
"Kafka only guarantees order within a partition, not across the whole topic, so the first thing I'd check is whether we're using a consistent partition key, like order ID, so related events always land in the same partition. If we are and still see reordering, I'd check whether the consumer is processing messages in parallel instead of sequentially, or whether retries are causing out-of-order reprocessing."

**20. Fine for hours, then unstable**
"'Sudden' is usually misleading — something's been degrading gradually and only becomes visible once it crosses a threshold. I'd correlate the exact failure time against resource trends leading up to it: memory, connection count, thread count, disk space. Common causes are a slow leak hitting a GC death spiral, a connection or file descriptor leak hitting an OS limit, or an unbounded queue that finally fills up under sustained traffic."

**21. Works in staging, fails behind prod gateway**
"If app logs show nothing but the request still fails, that's a strong signal it's being rejected at the gateway before it even reaches my app. I'd check gateway-level logs first, then compare configs between staging and production — header handling, since gateways often strip or rewrite headers like Authorization, rate limits, payload size limits, and auth checks happening at the gateway layer that staging might not replicate."

---

**22. Circuit breakers configured, but cascading failures still happen**

"A circuit breaker only stops one specific failure mode — repeated calls to something already failing. It doesn't isolate shared resources, so if all my dependencies share one thread pool, threads already in flight to the failing service before the breaker opened can still exhaust that pool and affect calls to healthy dependencies too. I'd pair the circuit breaker with a bulkhead so failures are contained per-dependency, make sure breakers exist at every hop in the call chain and not just the entry point, and double-check the fallback itself isn't doing something slow or blocking, because a slow fallback defeats the purpose."

---

**23. Same Kafka event processed twice**

"Kafka gives at-least-once delivery by default, so duplicates are expected, not a bug — the fix is making the consumer idempotent. I'd track a processed-event ID in a dedup store, ideally in the same transaction as the actual business write, so a crash between processing and marking it done doesn't leave a gap. Where possible I'd also design the write itself to be naturally idempotent, like an upsert instead of a plain insert. And I'd make sure offsets are committed manually, after processing succeeds, not on auto-commit before."

---

**24. Monolith vs. microservices**

"I'd default to a monolith, ideally a well-modularized one, for a small team or when domain boundaries are still evolving — microservices add real cost: network calls, distributed transactions, more infra to run and monitor. I'd only move to microservices once a specific pain point shows up, like needing to scale one component independently or needing separate deployment for separate teams, rather than splitting upfront on best-practice assumptions. A modular monolith is also much easier to split later once the boundaries are clear, than it is to fix microservices that were split wrong the first time."

---

A couple of delivery tips for the actual interview:
- End almost every answer with **"how I'd investigate"** in one sentence — interviewers consistently rate this higher than a list of causes, because it shows real hands-on habits.
- If you get a blank moment, the safe fallback opener that works for nearly all of these is: *"I'd start by checking whether this is a resource/pool problem or a downstream dependency problem, then narrow down with metrics or a thread dump."* It buys you thinking time and sounds methodical rather than stalling.

Want me to do a quick verbal mock — I'll throw one of these at you cold, you answer, and I'll critique pacing/content?
