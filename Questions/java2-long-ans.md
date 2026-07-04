
## 1. Spring Boot app becomes slow after deployment — where do you start?

I'd answer this by walking through a systematic approach, not jumping to conclusions:

**Step 1 — Confirm what changed.** Slowness right after deployment usually means the deployment itself introduced the regression. I'd check:
- What actually changed in this release (code diff, config diff, dependency version bumps)
- Whether it's a new environment/infra change (different instance size, new DB connection pool config, changed JVM flags)

**Step 2 — Check the obvious infra signals first.**
- CPU, memory, GC logs, thread count on the new instances
- DB connection pool metrics (HikariCP) — is the pool exhausted or misconfigured after deployment?
- Check if a new N+1 query or a missing index got introduced with the release

**Step 3 — Narrow down using APM/logs.**
- Use tools like Micrometer/Actuator (`/actuator/metrics`, `/actuator/httptrace`), or an APM tool like New Relic/Dynatrace/Grafana to see which endpoint or layer (DB, external API call, internal computation) got slower.
- Check application logs and DB slow query logs.

**Step 4 — Common culprits I'd specifically rule out:**
- Lazy-loading Hibernate entities causing N+1 queries that weren't hit before
- A new `@Transactional` boundary holding connections longer than needed
- Caching not warmed up yet (cold cache after restart)
- Connection pool size too small for new traffic pattern
- A new blocking call (e.g., synchronous external API call) added in the release
- JVM not warmed up (JIT compilation) — sometimes the "slowness" is just the first few minutes post-deploy

**How I'd phrase it in the interview:** *"I wouldn't guess — I'd first isolate whether it's infra, DB, or code level, using metrics and logs, then correlate with what actually shipped in this deployment."*

---

## 2. `@Transactional` method executes successfully but data is partially saved / doesn't roll back

This is a very common Spring gotcha question — they're testing if you understand **how Spring's proxy-based transaction management actually works**, not just the annotation.

Key reasons this happens:

**a) Self-invocation problem (most common answer expected)**
Spring's `@Transactional` works via **AOP proxies**. If a method inside the same class calls another `@Transactional` method internally (`this.methodB()`), the call **bypasses the proxy**, so no transaction is applied to that inner call.

```java
@Service
public class OrderService {
    public void placeOrder() {
        this.saveOrder(); // NOT going through proxy - @Transactional ignored!
    }

    @Transactional
    public void saveOrder() { ... }
}
```
Fix: move the transactional method to a different bean, or use `AopContext.currentProxy()`.

**b) Wrong exception type**
By default, Spring only rolls back on **unchecked exceptions** (`RuntimeException`/`Error`). If a checked exception is thrown, it won't trigger a rollback unless explicitly configured:

```java
@Transactional(rollbackFor = Exception.class)
```

**c) Exception is caught and swallowed**
If the method internally catches the exception (e.g., try-catch that logs and continues), Spring never sees the exception, so it thinks the method completed successfully — no rollback happens.

**d) Wrong propagation setting**
If `Propagation.REQUIRES_NEW` or `NOT_SUPPORTED` is used somewhere in the call chain, a nested failure might not roll back the outer transaction, or vice versa.

**e) Non-transactional resources involved**
If part of the "save" involves something outside the transactional resource (e.g., writing to a file, calling an external API, or using JDBC directly without going through the JPA/Hibernate session), transactional rollback can't undo those.

**f) Underlying datasource/driver not participating in the transaction properly** — e.g., using a different DataSource, auto-commit mode enabled, or multiple databases without proper distributed transaction handling (JTA).

**How I'd phrase it:** *"90% of the time in my experience, it's either self-invocation bypassing the proxy, or a checked exception that doesn't trigger the default rollback rule."*

---

## 3. API response time increases but CPU and memory look normal — what's wrong?

This is designed to test if you know that **slowness isn't always a compute problem** — often it's **waiting**, not **working**. I'd frame my answer around "if CPU/memory are normal, the bottleneck is likely I/O or contention, not computation."

Things I'd investigate:

**a) Thread pool / connection pool exhaustion**
- Requests might be waiting for an available thread (Tomcat thread pool) or a DB connection (HikariCP pool) even though the machine itself isn't under load. Check active vs. idle connections/threads.

**b) Downstream/external dependency latency**
- A slow third-party API, slow DB query, or a slow microservice call downstream will increase response time without touching CPU — the thread is just blocked waiting.

**c) Database-level issues**
- Lock contention (row/table locks), long-running transactions blocking others, missing index causing slow query but not necessarily high CPU on the app side (CPU load shows up on the DB server, not the app server).

**d) Network latency**
- DNS resolution delays, increased network hops, TLS handshake overhead, or a noisy-neighbor issue in cloud/container environments.

**e) GC pauses (subtle case)**
- Even if *average* CPU/memory looks fine, long/frequent GC pauses can spike p99 latency. I'd check GC logs specifically, not just heap usage snapshots.

**f) Synchronization / lock contention in code**
- Threads waiting on a `synchronized` block or a lock (e.g., a shared cache with poor concurrency design) — CPU stays low because threads are *blocked*, not computing.

**g) Increased request volume / queuing**
- More concurrent requests than the app can process at once leads to queuing delays even if per-request resource usage hasn't changed.

**How I'd phrase it:** *"If CPU and memory are normal, I'd shift my focus from compute-bound to wait-bound issues — thread pools, connection pools, downstream calls, and lock contention — and check thread dumps to see what threads are actually blocked on."*

---

## 4. Database connections getting exhausted during peak traffic

I'd frame this as checking three layers: **pool configuration, connection leaks, and query/traffic patterns.**

**a) Connection pool sizing**
- Check HikariCP (or whichever pool) settings: `maximumPoolSize`, `minimumIdle`, `connectionTimeout`. Is the pool size too small for peak concurrency, or is it too large and overwhelming the DB's own `max_connections` limit?
- Check the DB server's own max connection limit — app-side pool might be fine but DB-side limit is the actual bottleneck, especially with multiple app instances each holding their own pool.

**b) Connection leaks**
- Look for code paths where a `Connection`/`EntityManager`/`Session` is opened but not closed properly (missing try-with-resources, exception thrown before `close()`).
- Check for long-running or "stuck" transactions holding connections — e.g., a transaction that calls an external API in the middle of it, holding the DB connection idle for the duration of that call.

**c) Slow queries holding connections longer**
- If queries are slow (missing index, lock contention, table scan), each request holds its connection longer, so the pool exhausts faster under load even if the pool size itself is fine.

**d) Traffic pattern issues**
- Sudden traffic spikes without autoscaling in place fast enough.
- No circuit breaker/backpressure — every incoming request tries to grab a DB connection instead of failing fast or queueing gracefully.

**e) Monitoring I'd check specifically**
- HikariCP metrics: active connections, idle connections, pending threads waiting for a connection (`/actuator/metrics/hikaricp.connections.pending`)
- DB-side: `SHOW PROCESSLIST` (MySQL) or `pg_stat_activity` (Postgres) to see what's actually holding connections open and for how long.

**How I'd phrase it:** *"I'd first check whether it's a pool-sizing issue or a leak — pool metrics tell you that quickly. If connections are being held too long, I'd look at slow queries or long transactions, especially ones that call external systems mid-transaction."*

---

## 5. A scheduled job runs twice / multiple times after scaling

This is a very common **distributed systems gotcha** — great question because it tests whether you understand that scaling horizontally breaks assumptions that hold on a single instance.

**Root cause (the answer they're looking for):**
`@Scheduled` in Spring runs **per JVM instance**. If you scale the app to multiple pods/instances, **every instance** runs its own copy of the scheduled job independently — there's no built-in coordination between instances.

**How I'd explain the fix options:**

**a) Use a distributed lock** so only one instance executes the job at a time:
- **ShedLock** — most common, purpose-built for this. Simple annotation-based:
```java
@Scheduled(cron = "0 0 * * * *")
@SchedulerLock(name = "myScheduledTask", lockAtMostFor = "10m")
public void runJob() { ... }
```
- Or a manual lock using Redis (`SET NX`) or a DB-based lock table.

**b) Move scheduling out of the application entirely**
- Use an external scheduler like **Quartz with a clustered JobStore** (DB-backed, coordinates across nodes natively), or an orchestration-level scheduler (Kubernetes CronJob, AWS EventBridge/Lambda) that triggers the job exactly once regardless of how many app instances exist.

**c) Designate a leader**
- Leader-election pattern (e.g., via Kubernetes, Zookeeper, or Spring Cloud) where only the elected leader instance runs scheduled tasks.

**How I'd phrase it:** *"This happens because `@Scheduled` is JVM-local, not cluster-aware — scaling to multiple instances means multiple independent triggers. I'd use ShedLock for a quick fix, or move to a clustered Quartz/external scheduler for a more robust solution."*

---

## 6. Works locally but fails in production

This is an open-ended "how do you debug environment differences" question. I'd structure it as a checklist rather than one root cause, since it signals thoroughness.

**a) Configuration differences**
- Different `application.properties`/`application-prod.yml` values — DB URLs, timeouts, feature flags, active Spring profiles (`@Profile`, `spring.profiles.active`).
- Environment variables or secrets missing/misconfigured in production.

**b) Environment/infrastructure differences**
- Different Java version between local and prod (e.g., local on Java 17, prod still on Java 11 image).
- Different OS — path separators, case-sensitive file systems (works on Windows/Mac, fails on Linux prod).
- Missing environment variables, or different timezone/locale settings causing date/formatting bugs.

**c) Data differences**
- Local DB has small/clean test data; production has scale, nulls, edge cases, or legacy bad data that trigger bugs never seen locally.
- Local DB and prod DB schema drift (someone forgot to run a migration).

**d) Network/security differences**
- Firewall rules, security groups blocking a port/service in prod that's open locally.
- Production might be behind a proxy/load balancer changing headers, timeouts, or SSL termination behavior.
- Production has stricter CORS or authentication configs.

**e) Concurrency/scale-related bugs**
- Local testing is usually single-user, single-threaded in practice. Production has real concurrent traffic that exposes race conditions, thread-safety issues, or connection pool limits never hit locally.

**f) Dependency/version mismatches**
- Different library versions if the build wasn't reproducible (no lock file, `SNAPSHOT` dependencies resolving differently at build time).

**How I'd phrase it:** *"I'd treat 'works locally, fails in prod' as an environment-diff problem first — profiles, config, Java version, and data shape — and only look for a genuine code bug if all of those check out. I'd also check prod logs/stack traces directly rather than guessing, since the actual exception usually points straight at the mismatch."*

---

## 7. Kafka consumers are healthy but message lag keeps increasing

"Healthy" consumers (no crashes, no errors) with growing lag means **throughput mismatch** — consumers aren't processing fast enough relative to production rate, not that something is broken. I'd investigate:

**a) Consumption rate vs. production rate**
- Producers are pushing messages faster than consumers can process them. Check messages-in-rate vs. messages-consumed-rate on the topic.

**b) Not enough partitions/consumers**
- Consumer group parallelism is capped by partition count — if you have 4 partitions but only 2 active consumer instances, you're underutilized; but even maxed out, if partitions < needed throughput, you physically can't scale consumption further without repartitioning.

**c) Slow per-message processing**
- Check what each consumer does per message — a slow downstream call (DB write, external API, synchronous processing) inside the consumer loop directly limits throughput. This is the most common real-world cause.

**d) Consumer configuration issues**
- `max.poll.records` too high combined with slow processing can cause consumers to exceed `max.poll.interval.ms`, triggering rebalances that pause consumption further (a vicious cycle — looks "healthy" per-instance but the group keeps rebalancing).
- `fetch.min.bytes` / `fetch.max.wait.ms` tuning affecting batch efficiency.

**e) GC pauses or resource contention** on the consumer side that isn't causing outright failure but is slowing processing (thread dumps would show this).

**f) Skewed partitions**
- Uneven key distribution means some partitions get much more traffic than others — a few consumers become bottlenecks while others sit idle.

**How I'd phrase it:** *"Since the consumers aren't failing, I'd focus on throughput — is this a processing-speed problem per message, a partition/parallelism ceiling, or a sudden spike in producer volume? I'd check consumer lag per partition (not just aggregate) to see if it's evenly distributed or a hot-partition problem."*

---

## 8. Retry logic starts creating duplicate transactions during failures

This is a classic **idempotency** question — they want to hear that word and see you understand *why* retries are dangerous without it.

**Root cause:**
Retries assume the original request "failed," but in distributed systems, a failure can mean:
- The request never reached the server (safe to retry), **or**
- The request succeeded on the server, but the **response** was lost/timed out before reaching the client (retry now duplicates a successful operation).

The client can't always tell these apart — so blind retries can re-execute a transaction that already completed.

**How I'd explain the fix:**

**a) Idempotency keys**
- Client generates a unique key per logical operation (e.g., UUID), sends it with the request. Server checks if that key was already processed and returns the cached result instead of re-executing:
```java
if (idempotencyRepository.existsById(requestId)) {
    return idempotencyRepository.getResult(requestId);
}
// process and store result keyed by requestId
```

**b) Idempotent operation design**
- Design operations to be naturally idempotent where possible — e.g., "set balance to X" instead of "add X to balance," or use unique constraints in the DB to reject duplicate inserts.

**c) At-least-once + deduplication instead of exactly-once illusion**
- Accept that retries mean at-least-once delivery, and put the responsibility on deduplication (unique constraint, idempotency table) rather than trying to prevent retries altogether.

**d) Outbox pattern (for distributed transactions)**
- If the transaction involves publishing an event/message alongside a DB write, use the transactional outbox pattern to avoid a scenario where the DB commits but the message fails (or vice versa), which is a common source of "duplicate-looking" side effects during retries.

**e) Careful retry configuration**
- Only retry on genuinely safe/idempotent failure types (timeouts, 5xx) — not retry blindly on all exceptions. Add exponential backoff to reduce duplicate collision windows.

**How I'd phrase it:** *"The core issue is that retries can't distinguish 'never happened' from 'happened but I didn't get the response' — so the fix isn't better retry logic, it's making the underlying operation idempotent, usually via an idempotency key checked before executing the transaction."*

---

## 9. One slow database query starts affecting the entire application / multiple services

This tests understanding of **cascading failures / resource contention in shared infrastructure** — a very real production scenario.

**How the slowness cascades:**

**a) Connection pool starvation**
- The slow query holds a DB connection for longer. If enough requests hit that slow query concurrently, the connection pool gets exhausted — now *unrelated* queries/endpoints that need a connection also start failing/queuing, even though their own queries are fast.

**b) Thread pool exhaustion**
- Similarly, app server threads (e.g., Tomcat threads) handling those slow requests stay occupied longer. If enough threads are stuck waiting on the slow query, the thread pool fills up and the app can't accept new requests at all — affecting completely unrelated endpoints.

**c) Shared database resource contention**
- The slow query might be holding row/table locks, blocking other queries (even on other tables, if it's a long transaction holding a broader lock or blocking replication/binlog processing).
- High CPU/I/O usage on the DB server from the slow query can degrade performance for every other query hitting that same DB instance, so multiple services sharing that DB all degrade simultaneously.

**d) Cascading timeouts across services**
- If Service A calls Service B which has the slow query, Service A's requests start timing out too, and if Service A is called by Service C, the slowness propagates upstream — a classic cascading failure across a microservice chain.

**How I'd explain the fix/prevention:**
- **Immediate:** identify and fix/kill the slow query (missing index, bad query plan — check `EXPLAIN ANALYZE`).
- **Isolation:** use separate connection pools per critical path (bulkhead pattern) so one slow path can't starve the whole pool.
- **Timeouts:** set aggressive query and request timeouts so slow calls fail fast instead of holding resources indefinitely.
- **Circuit breakers** (Resilience4j/Hystrix-style) between services so a slow downstream dependency doesn't cascade into total failure upstream.
- **Read replicas** for heavy read queries so they don't compete with critical write/transactional queries on the primary DB.

**How I'd phrase it:** *"This is a classic case of shared-resource contention — connection pools and thread pools are finite, so one slow query anywhere can starve capacity for everything else. I'd apply the bulkhead pattern to isolate pools, set proper timeouts, and use circuit breakers so failures don't cascade across services."*

---

Great trio — cache invalidation, "invisible" failures, and memory leaks are all top-tier senior-level topics. Let's go:

## 10. Cache improves performance initially, but users later receive stale data

This is the classic **cache invalidation problem** — "there are only two hard things in computer science." I'd structure my answer around *why* staleness happens and how to fix it.

**Why it happens:**

**a) No/weak invalidation strategy**
- Data in the underlying DB changes, but the cache isn't updated or evicted — the cache just keeps serving the old value until TTL expiry (if any).

**b) TTL too long or missing entirely**
- If TTL is set too generously (or not set at all), stale data sits in cache far longer than acceptable for the use case.

**c) Cache not invalidated on write path**
- A common bug: writes go directly to the DB without also updating/evicting the corresponding cache entry (cache and DB fall out of sync).

**d) Multiple app instances with local (in-memory) caches**
- If using something like Caffeine/local `HashMap` cache per instance instead of a shared cache (Redis), one instance updates its local cache while others still serve stale data — no coordination across nodes.

**e) Race condition between write and cache update**
- Classic race: Thread A reads DB (old value) → Thread B writes new value + invalidates cache → Thread A now writes the old value back into cache — cache ends up stale even though invalidation logic exists.

**How I'd explain the fixes:**

- **Cache-aside with explicit invalidation:** on every write, evict/update the cache key immediately after the DB write succeeds.
- **Write-through cache:** every write goes through the cache layer, which updates both cache and DB atomically as one path, avoiding drift.
- **Reasonable TTL as a safety net**, even with explicit invalidation, so stale data can't live forever if an invalidation is ever missed.
- **Versioned/tagged cache keys** — e.g., include a version number or `updated_at` timestamp in the cache key so old versions naturally stop being served.
- **Use a distributed cache (Redis)** instead of per-instance local caching when running multiple app instances, so invalidation is consistent cluster-wide.
- **Event-driven invalidation** — publish a "data changed" event (e.g., via Kafka) that all instances subscribe to and evict their local caches accordingly, if local caching is required for latency reasons.

**How I'd phrase it:** *"I'd first check whether we even have an explicit invalidation step on the write path, or if we're relying purely on TTL. Then I'd check if this is a distributed caching problem — multiple instances with local caches out of sync — versus a single shared cache with a race condition between write and invalidate."*

---

## 11. APIs randomly return 500 errors / timeouts even though logs/infra look healthy

This is designed to test whether you look **beyond your own app** — the phrase "logs/infra look healthy" is a strong hint the problem is external, intermittent, or not being logged at all.

**Where I'd look:**

**a) Is it actually random, or does it correlate with something?**
- Check timestamps of failures against traffic spikes, deployment times, specific endpoints, specific downstream services, or specific instances (one bad pod vs. all).

**b) Downstream dependency flakiness**
- A third-party API or another microservice intermittently failing/timing out, but the failure is being swallowed or logged at a level you're not checking (e.g., swallowed exception, or only logged as a generic "500" without the root cause).

**c) Load balancer / infra layer issues**
- Health checks passing (app "looks" healthy) but a specific instance is in a bad state — e.g., stuck threads, one pod overloaded while others are fine, so failures look "random" in aggregate but are concentrated on one node.
- LB timeout shorter than app's actual response time under load — LB kills the connection and returns 5xx even though the app would've eventually responded.

**d) Connection pool / resource exhaustion happening intermittently**
- Brief spikes in concurrent requests exhaust the DB connection pool or thread pool for a few seconds, causing failures, then it recovers — by the time you check metrics/dashboards (often sampled per minute), the spike is invisible.

**e) Insufficient logging/observability**
- The 500s might be genuinely happening for a real reason, but exception details are being lost — e.g., a generic global exception handler returning 500 without logging the actual stack trace, or logs not being correlated with a request ID/trace ID across services.

**f) GC pauses**
- A "stop-the-world" GC pause of a few hundred ms to a few seconds can cause a request to time out entirely while CPU/memory *averages* still look fine on a dashboard.

**g) Transient network issues**
- DNS resolution hiccups, connection resets, or packet loss between services — especially in cloud/containerized environments — that don't show up in application-level logs at all.

**How I'd phrase it:** *"'Looks healthy' usually means we're not measuring the right thing — I'd add distributed tracing (correlation IDs) to actually catch the failing request path, check per-instance metrics instead of aggregate, and specifically look at GC logs and connection pool metrics at the exact failure timestamps rather than trusting dashboard averages."*

---

## 12. JVM memory usage slowly increases every day — how would you investigate?

This is a **memory leak diagnosis** question, and interviewers want a specific, tool-driven process, not just "check for memory leaks."

**Step-by-step approach I'd describe:**

**Step 1 — Confirm it's a real leak, not just normal GC behavior**
- Check if memory grows and then drops after GC (normal sawtooth pattern — healthy) vs. grows and never comes back down even after full GC (real leak). Tools: `jstat -gcutil <pid>` over time, or GC logs.

**Step 2 — Take heap dumps at different points in time**
```bash
jmap -dump:live,format=b,file=heap1.hprof <pid>
```
- Take one early, one after several hours/days of the growth, and compare.

**Step 3 — Analyze heap dumps**
- Use **Eclipse MAT (Memory Analyzer Tool)** or **VisualVM** to:
  - Find the dominant object types consuming heap
  - Use "Leak Suspects" report in MAT, which often points directly at the culprit
  - Look at retained size and GC roots — what's holding a reference to these objects and preventing GC from collecting them

**Step 4 — Common root causes I'd specifically check for:**
- **Static collections** (e.g., a `static Map` used as a cache) that keep growing and are never evicted
- **Unbounded caches** without size limits or TTL
- **Listener/callback registration without deregistration** (e.g., event listeners added but never removed)
- **ThreadLocal misuse** — values not cleared, especially in thread-pooled environments (common in web apps) where threads are reused
- **Unclosed resources** — streams, connections not released, holding onto memory indirectly
- **Classloader leaks** — common in app-server redeployment scenarios (less relevant for Spring Boot standalone JARs, but worth mentioning if relevant)
- **Growing session data** if using in-memory HTTP sessions without expiry

**Step 5 — Correlate with usage pattern**
- Does the growth rate correlate with traffic volume, or is it constant regardless of load? Constant/flat-rate-regardless-of-traffic growth often points to a scheduled job or background thread rather than request handling.

**Step 6 — Confirm the fix**
- After identifying and fixing the suspected leak (e.g., adding eviction to a cache, clearing a `ThreadLocal`), re-run with the same heap dump comparison process to confirm the growth pattern is resolved before calling it done.

**How I'd phrase it:** *"I'd treat it as a heap dump comparison problem — take dumps at two points in time, diff them using MAT's leak suspects report, and follow the GC roots. In my experience, the usual suspects are unbounded static caches, ThreadLocals not being cleared in a thread pool, or listeners that never get deregistered."*

---

Good — this trio ties together thread starvation, health-check blind spots, and resilience patterns. Let's finish strong:

## 13. Thread pools become exhausted while CPU stays low

This is the "threads are waiting, not working" pattern again, but they want the specific mechanics here.

**Why this happens (core concept):**
Low CPU + exhausted thread pool means threads are **blocked waiting on I/O**, not doing computation. Threads sitting idle-but-occupied don't consume CPU, but they do consume pool capacity.

**Common causes I'd check:**

**a) Blocking calls to slow downstream dependencies**
- Synchronous calls to a slow external API, slow DB query, or slow downstream microservice — the thread just sits there waiting for a response, holding a pool slot the whole time.

**b) Lock contention / synchronized blocks**
- Threads queued up waiting to acquire a lock (`synchronized`, `ReentrantLock`) held by another thread for too long — visible via thread dump as `BLOCKED` state.

**c) No timeouts configured**
- If downstream calls (`RestTemplate`, `WebClient`, JDBC) have no timeout set, a single hung dependency can hold threads indefinitely, one request at a time, until the whole pool is consumed.

**d) Thread pool sized too small for actual concurrency needs**
- Default Tomcat thread pool (200) or a custom `ExecutorService` might just be undersized for current traffic + latency combination — even without a bug, math doesn't work out (`threads needed ≈ throughput × avg latency`).

**e) Deadlock or livelock** in a subset of threads, slowly consuming the pool as more requests pile up behind the stuck ones.

**How I'd investigate:**
- Take a **thread dump** (`jstack <pid>`) during the exhaustion — look at thread states: `BLOCKED`, `WAITING`, `TIMED_WAITING`, and what they're stuck on (stack trace shows exact line — usually a lock, a socket read, or a DB call).
- Check active vs. queued thread counts (Actuator `/actuator/metrics/tomcat.threads.busy` or executor metrics).

**Fixes:**
- Set explicit timeouts on all external calls.
- Use separate thread pools for different types of work (bulkhead pattern) so a slow dependency doesn't starve the whole app.
- Consider async/non-blocking I/O (WebFlux) for high-latency-dependency-heavy workloads.
- Right-size the pool based on Little's Law (`threads = target throughput × avg latency`).

**How I'd phrase it:** *"CPU stays low because the threads aren't computing, they're blocked — so I'd immediately take a thread dump to see what state most threads are stuck in and what they're waiting on, rather than looking at CPU/memory dashboards which won't show this."*

---

## 14. Health checks pass, but users continue facing failures

This tests whether you understand that **health checks often check the wrong thing** — a very real, very common production gap.

**Why this happens:**

**a) Health check is too shallow**
- Most default health checks (e.g., Spring Boot Actuator `/health`) just check "is the app up and responding" — they don't check if the app can actually **do its job** (e.g., reach the DB, reach a critical downstream dependency, has healthy connection pool).

**b) Health check doesn't cover the actual failing dependency**
- If the app depends on DB + Redis + an external payment API, but health check only pings the DB, a Redis or payment API outage won't be reflected — health check stays green while real requests fail.

**c) Health check runs on a different code path than real traffic**
- The `/health` endpoint might succeed instantly (simple ping) while the actual business logic path hits a slow/broken dependency that the health check never exercises.

**d) Load balancer health check interval too infrequent / too lenient**
- LB might mark the instance healthy based on a stale check from 30 seconds ago, while the instance degraded in the meantime.

**e) Partial degradation, not full failure**
- App is "up" but some subset of functionality is broken (e.g., one specific downstream integration fails), which a generic liveness check won't catch — only specific business-logic checks would.

**Fixes I'd suggest:**
- Differentiate **liveness** (is the process running) from **readiness** (can it actually serve traffic) probes — Kubernetes supports both.
- Build **custom health indicators** that check real dependencies:
```java
@Component
public class DbHealthIndicator implements HealthIndicator {
    public Health health() {
        // actually check DB connectivity/pool state
    }
}
```
- Add **synthetic transactions** — periodically run an actual representative business operation, not just a ping, to validate true end-to-end health.
- Combine with real-time error rate monitoring (not just health check status) — if error rate spikes even with green health checks, alert on that too.

**How I'd phrase it:** *"Health checks passing just means the process is alive, not that it's actually functional — I'd add readiness checks tied to real dependencies (DB, cache, critical downstream APIs) and pair health checks with real error-rate/latency monitoring, since that's what actually reflects user experience."*

---

## 15. One external service becomes slow, all APIs impacted — how to prevent cascading failure?

This is the capstone resilience-patterns question — I'd name the patterns explicitly since that's exactly what's expected.

**Why it cascades (brief, since they likely already get this from earlier questions):**
Threads/connections calling the slow external service get held longer → pool exhausts → unrelated requests that don't even use that service get starved too, since they're competing for the same shared thread/connection pool.

**Prevention patterns I'd walk through:**

**a) Timeouts (first line of defense)**
- Every external call must have an explicit, aggressive timeout — never rely on defaults (which are sometimes infinite). Fail fast rather than hold resources indefinitely.

**b) Circuit Breaker pattern (Resilience4j)**
- After a threshold of failures/slow responses, the circuit "opens" and stops calling the failing service entirely for a cooldown period, immediately failing fast instead of piling up more blocked threads:
```java
@CircuitBreaker(name = "paymentService", fallbackMethod = "fallback")
public String callPaymentService() { ... }

public String fallback(Exception e) {
    return "Service temporarily unavailable";
}
```

**c) Bulkhead pattern**
- Isolate thread pools/connection pools per downstream dependency, so a slow payment API can only exhaust its *own* dedicated pool, not the pool shared by all other endpoints.

**d) Fallback / graceful degradation**
- Define a sensible fallback response (cached data, default value, degraded feature) instead of a hard failure when the dependency is down — keep the rest of the app functional.

**e) Rate limiting / load shedding**
- Limit how many concurrent calls go to the struggling service, shedding excess load rather than queuing everything and making it worse.

**f) Async/non-blocking calls**
- Where possible, make calls to external services asynchronous so a slow response doesn't tie up a request-handling thread at all.

**g) Retry with backoff — carefully**
- Retries can help with transient issues but must have backoff + limited attempts, or they'll worsen load on an already-struggling service (thundering herd).

**How I'd phrase it:** *"I'd apply the standard resilience trio — timeouts, circuit breakers, and bulkheads — using something like Resilience4j: timeouts so we fail fast, circuit breakers so we stop hammering a failing service, and bulkheads so that failure is contained to just that dependency's slice of resources instead of taking down the whole app."*

---

That's a complete, cohesive set of 15 — nicely, they all reinforce a handful of core themes an interviewer is testing for at 5 YOE: **thread/connection pool mechanics, resilience patterns (timeout/circuit-breaker/bulkhead), observability beyond basic metrics, and understanding of distributed-systems failure modes**.

One suggestion: if you have 1-2 real incidents from your own experience that map to any of these (even loosely), practice weaving them in briefly — interviewers at this level often follow up with "have you actually seen this happen?" and a genuine example (even a small one) will carry much more weight than reciting the pattern name. Want to run through a mock verbal answer for any of these, or do you have more questions to go?
A tip for all three answers: interviewers love hearing you mention **specific tools** (Actuator, thread dumps via `jstack`, Micrometer, HikariCP metrics, APM tools) — it signals real hands-on debugging experience, not textbook knowledge.

---

## 16. Autoscaling creates more pods, but response time still increases

This tests whether you understand that **autoscaling only fixes compute-bound bottlenecks** — if the bottleneck is elsewhere, adding pods does nothing or even makes it worse.

**Why this happens:**

**a) Bottleneck is a shared downstream resource, not the app tier**
- If the real constraint is the database (connection limit, CPU, disk I/O) or a shared cache, adding more app pods just means **more pods competing for the same limited DB connections** — this can actually make things worse (more contention, more connection pool exhaustion at the DB level).

**b) Database connection limits**
- Each new pod opens its own connection pool. If DB `max_connections` is fixed (say 200) and you scale from 5 pods × 20 connections to 20 pods × 20 connections, you can blow past the DB's own limit — causing new failures instead of relief.

**c) External dependency is the actual bottleneck**
- If a third-party API or a downstream microservice is slow/rate-limited, scaling your own pods doesn't help — you're still bottlenecked by that dependency's capacity (and you might even get rate-limited harder now with more concurrent callers).

**d) Autoscaling metric is wrong**
- If autoscaling is based on CPU/memory, but the actual bottleneck is I/O wait or thread pool exhaustion (as discussed earlier), scaling triggers late or not at all — CPU looks fine so HPA doesn't scale, even though the app is struggling.

**e) Sticky sessions / uneven load distribution**
- If load balancing isn't distributing traffic evenly across new pods (e.g., sticky sessions, poor LB algorithm), new pods sit idle while old pods remain overloaded.

**f) Shared cache or message queue bottleneck**
- More pods hitting the same Redis instance or Kafka broker can shift the bottleneck there instead of resolving it.

**How I'd investigate:**
- Check where time is actually being spent per request (APM/tracing) — is it DB, external call, or actual app compute? Scaling app pods only helps the last category.
- Check DB connection pool utilization across all pods combined vs. DB's max limit.

**How I'd phrase it:** *"Autoscaling only helps if the app tier itself is the bottleneck. I'd check whether we're actually compute-bound, or if we're just adding more competitors for the same limited DB connections or a rate-limited external dependency — in which case more pods won't help, and might even make it worse."*

---

## 17. Deployment increases 4xx errors, or increases latency without traffic increase

Two related but distinct symptoms — I'd address both since the question bundles them.

**4xx errors increasing after deployment:**
4xx means **client-side/request problems**, not server crashes — so I'd look at what changed in the **contract or validation logic**:

- **API contract change** — a field renamed/removed/made required, breaking existing clients who haven't updated (backward-incompatible change).
- **Validation logic changed** — stricter input validation added that now rejects previously-valid requests.
- **Auth/authorization changes** — token format changed, a new permission check added, expired credentials not refreshed, or a new security filter misconfigured (blocking legitimate requests as 401/403).
- **Routing/API gateway config change** — path change, versioning issue, or a new rate-limiting rule too aggressively rejecting requests (429).
- **Client not yet updated** — if it's a coordinated API + client deployment and only one side shipped, mismatched expectations cause 4xx spikes until both are aligned.

**Latency increasing without traffic increase:**
Since traffic is constant, the regression is purely from **what shipped in the deployment**, not load. I'd check:

- **New N+1 query or inefficient DB call** introduced in this release.
- **A new synchronous external call** added in the request path that wasn't there before.
- **Changed caching behavior** — cache key changed, TTL reduced, or a cache layer accidentally removed/bypassed.
- **New logging/tracing overhead** — verbose logging or synchronous logging to a slow sink added in the release.
- **JVM warm-up effect** — if pods were freshly restarted, JIT hasn't optimized hot paths yet (should stabilize after a few minutes — worth ruling out before deeper investigation).
- **New dependency version** — a library upgrade (ORM, JSON serializer, HTTP client) with different default behavior/performance characteristics.
- **Configuration change** — connection pool size reduced, thread pool size reduced, timeout values changed in this deploy.

**How I'd approach both:** Compare this deployment's diff directly against the previous one — code changes, config changes, dependency version bumps — since "same traffic, different behavior" almost always maps directly to something in the release itself. I'd also check if this is a canary/rolling deployment, and whether the issue is isolated to new-version pods only (confirms it's the code) vs. across all pods (points to shared infra/config).

**How I'd phrase it:** *"Since traffic is unchanged, I wouldn't look at scaling or capacity — I'd diff this release against the last one for contract changes, validation changes, or auth changes for the 4xx case, and for latency, I'd check for new blocking calls, changed caching, or a dependency version bump."*

---

## 18. Logs exist everywhere, but tracing one request across services is still difficult

This is a **distributed tracing / observability maturity** question — they want you to name the actual solution, not just describe the pain.

**Why this happens:**

**a) No correlation ID propagated across services**
- Each service logs independently with its own request ID (or no ID at all), so there's no common thread to `grep` across all of them for a single user request.

**b) Logs are unstructured and scattered**
- Plain text logs across dozens of services/pods with no centralized aggregation — you'd have to manually SSH/check each service's logs and try to align timestamps, which doesn't scale and is error-prone.

**c) Async/message-queue boundaries break context**
- If a request goes through Kafka or an async job queue, the correlation context often gets dropped unless explicitly propagated through message headers.

**d) No distributed tracing infrastructure in place**
- Without a tracing system, there's no way to see the full call graph (which services were called, in what order, how long each took) for a single request.

**How I'd fix it:**

**a) Correlation/trace ID propagation**
- Generate a unique trace ID at the entry point (API gateway or first service) and propagate it through every downstream call — HTTP headers for sync calls, message headers for Kafka/queues.
- Use **MDC (Mapped Diagnostic Context)** in Spring/SLF4J so every log line automatically includes the trace ID without manual work:
```java
MDC.put("traceId", traceId);
```

**b) Distributed tracing tools**
- Adopt **Spring Cloud Sleuth + Zipkin**, or better, **OpenTelemetry** (the modern standard) integrated with a backend like Jaeger, Zipkin, or a commercial APM (Datadog, New Relic, Dynatrace) — these auto-instrument HTTP calls, DB calls, and messaging, giving a visual trace of the entire request path with per-hop latency.

**c) Centralized log aggregation**
- Ship all logs to a centralized system (ELK stack — Elasticsearch/Logstash/Kibana, or Loki/Grafana) so you can search by trace ID across all services in one place instead of per-service log files.

**d) Structured logging**
- Log in JSON format with consistent fields (traceId, service name, timestamp, level) rather than free-text, making aggregation and searching reliable.

**How I'd phrase it:** *"The fix is propagating a single trace ID through every hop — sync and async — using MDC for logs and OpenTelemetry/Sleuth for actual distributed tracing, then centralizing everything in something like ELK or Grafana so I can pull the entire request's journey with one trace ID instead of manually correlating timestamps across a dozen log files."*

---

More good ones — circuit breaker limitations, exactly-once processing, and architecture trade-offs. Here goes:

## 22. Circuit breakers configured, but cascading failures still happen

This tests whether you understand that a circuit breaker is **one layer of defense, not a complete solution** — interviewers ask this specifically to see if you know its limitations.

**Why cascading failures can still happen despite circuit breakers:**

**a) Circuit breaker configured on the wrong boundary**
- If it's only wrapping the direct downstream call but the actual resource contention is happening at a shared layer underneath (e.g., a shared thread pool, shared DB connection pool, or shared cache), the circuit breaker doesn't isolate that shared resource — bulkhead is missing.

**b) Threshold/timeout misconfigured**
- If the failure threshold is too high or the timeout is too long, the circuit stays closed too long while requests keep piling up and exhausting resources before it finally trips.

**c) No bulkhead — shared thread pool across dependencies**
- Circuit breaker prevents *calling* the failing service, but if all dependencies share one thread pool, threads already in flight to the failing service (before the circuit opened) can still exhaust the pool, affecting calls to *other*, healthy dependencies too.

**d) Fallback itself is expensive or blocking**
- If the fallback method does something slow (e.g., another DB call, another external call) instead of returning something cheap/cached, the "protected" path is still slow.

**e) Circuit breaker only applied at one layer**
- If Service A → B → C, and only A→B has a circuit breaker but B→C doesn't, a slowdown in C can still cascade through B before A's circuit breaker to B even notices anything's wrong.

**f) Retry logic combined with circuit breaker incorrectly**
- If retries are layered outside/around the circuit breaker without coordination, failed calls might retry aggressively right as the circuit is trying to stay open, effectively causing a thundering herd against a recovering service (this also affects the "half-open" state testing).

**How I'd fix it:**
- Pair circuit breakers with **bulkheads** so failure in one dependency can't starve resources for others.
- Tune thresholds/timeouts based on actual latency SLAs, not defaults.
- Apply circuit breakers **at every hop** in the call chain, not just the entry point.
- Keep fallback methods cheap and non-blocking (cached response, static default).
- Coordinate retry and circuit breaker configuration so retries respect the breaker's open/half-open state instead of fighting it.

**How I'd phrase it:** *"A circuit breaker alone only stops one type of failure — repeated calls to something already failing. It doesn't isolate shared resources like thread pools, so I'd pair it with a bulkhead, make sure breakers exist at every hop in the chain, not just the first one, and double check the fallback itself isn't doing something slow or blocking."*

---

## 23. Same Kafka event processed twice — avoiding duplicate processing

This is the practical, Kafka-specific version of the idempotency question — they want concrete mechanics here, not just the word "idempotent."

**Why it happens (brief context):**
Kafka guarantees **at-least-once delivery** by default — if a consumer processes a message but crashes/fails before committing the offset, or if a rebalance happens at the wrong moment, the same message gets redelivered and reprocessed after recovery.

**How I'd prevent duplicate processing — the concrete mechanisms:**

**a) Idempotent consumer logic (most direct fix)**
- Track processed message IDs (or a natural key from the payload) in a dedup store — a DB table or Redis set — and check before processing:
```java
if (processedEventRepository.existsById(eventId)) {
    return; // already processed, skip
}
processEvent(event);
processedEventRepository.save(eventId);
```
- For correctness, this check-and-save often needs to happen **within the same transaction** as the actual business write, so a crash between "process" and "mark as processed" doesn't cause a gap.

**b) Design the operation to be naturally idempotent**
- E.g., use `INSERT ... ON CONFLICT DO NOTHING` (upsert) instead of a plain insert, or "set balance to X" instead of "increment balance by X" — so reprocessing the same event doesn't change the outcome even without explicit dedup tracking.

**c) Use the message's natural unique key + DB unique constraint**
- If the event has a natural unique identifier (e.g., `orderId` + `eventType`), add a unique constraint in the DB so a duplicate insert simply fails/no-ops instead of double-processing.

**d) Manual offset commit, committed only after successful processing**
- Use manual ack (`enable.auto.commit=false`) and commit the offset only after the business logic + dedup record are successfully persisted — this narrows (though doesn't fully eliminate) the window where reprocessing can occur.

**e) Kafka's exactly-once semantics (EOS), where applicable**
- For producer→Kafka→consumer pipelines that stay entirely within Kafka (e.g., Kafka Streams), Kafka supports transactional/exactly-once semantics via idempotent producers + transactional consumers. Worth mentioning, but I'd note it doesn't cover the common case where the "processing" involves an external side effect like a DB write or an API call outside Kafka's transaction boundary — that's still where idempotency logic at the app level is needed.

**How I'd phrase it:** *"At-least-once delivery means duplicates are expected, not a bug — so I'd make the consumer idempotent, typically by tracking a processed-event ID in the same transaction as the business write, or by designing the write itself to be naturally idempotent, like an upsert instead of an insert. I'd also make sure offsets are committed manually, after processing succeeds, not before."*

---

## 24. When would you choose a monolith over microservices?

This is a **judgment/maturity** question — interviewers specifically want to see that you don't treat microservices as a default "best practice," since over-engineering with microservices too early is a very common real-world mistake.

**When I'd choose a monolith:**

**a) Small team / early-stage product**
- If the team is small (say, under 8-10 engineers) and the product/domain boundaries aren't well understood yet, a monolith is much faster to develop, test, and deploy — microservices add coordination overhead (network calls, service discovery, distributed transactions, observability tooling) that isn't justified yet.

**b) Domain boundaries aren't clear yet**
- Splitting into services requires knowing where the seams are. If the business domain is still evolving, a monolith with **clean internal modular boundaries** (modular monolith) is easier to refactor later than prematurely-split microservices with wrong boundaries, which are painful to fix (a bad service boundary means chatty, coupled network calls instead of a simple in-process refactor).

**c) Low/predictable scale requirements**
- If different parts of the system don't have wildly different scaling needs, there's no operational benefit to scaling services independently — the complexity of microservices isn't paying for itself.

**d) Limited DevOps/infra maturity**
- Microservices need strong platform investment: CI/CD per service, service mesh or API gateway, centralized logging/tracing, container orchestration. Without that maturity in place, microservices often create more operational pain than they solve.

**e) Simpler operational/transactional requirements**
- If the system needs strong consistency/ACID transactions across what would be different services, a monolith with a single DB avoids the complexity of distributed transactions, sagas, or eventual consistency patterns.

**f) Latency-sensitive systems**
- In-process function calls in a monolith are much faster than network calls between microservices — for latency-critical paths, avoiding network hops can matter.

**When microservices genuinely make sense (brief contrast, since a good answer shows both sides):**
- Large teams needing independent deployability and ownership boundaries
- Components with very different scaling profiles (e.g., an image-processing service vs. a lightweight API)
- Need for polyglot tech stacks per domain
- Mature domain boundaries that are stable and well-understood

**How I'd phrase it:** *"I'd default to a monolith — ideally a well-modularized one — for a small team or an evolving domain, since microservices add real operational cost: network calls, distributed transactions, more infra to maintain. I'd only move to microservices once specific pain points show up — like needing independent scaling or independent deployment for different teams — rather than splitting upfront based on best-practice assumptions. A modular monolith is also much easier to split later into services once boundaries are clear, than to merge or re-draw incorrect service boundaries."*

---
