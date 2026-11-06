# Lộ trình 15 tuần — Software Engineer từ nền tảng

Học theo thứ tự từ trên xuống. Nền tảng backend và system design học trước (tuần 1–7), core ngôn ngữ và runtime học sau (tuần 8–14), thực hành kỹ thuật ở tuần 15.
**Track DSA đang TẠM HOÃN** (người học chọn bỏ qua lúc này); giữ sẵn thư mục `00-dsa/` để học sau.
Chỉ được sang bài tiếp theo khi trả lời **đúng 100%** câu hỏi ôn của bài hiện tại.

Ký hiệu nguồn của từng mục:
- 📘 [System Design Primer](https://github.com/donnemartin/system-design-primer)
- 🗺️ [roadmap.sh](https://roadmap.sh) (backend, system-design, java, spring-boot, javascript, nodejs, aspnet-core)
- 🎯 Hello Interview (system design & low-level design in a hurry)
- 🔍 danh sách câu phỏng vấn 2026 hoặc tài liệu chính thức của phiên bản mới

Mỗi phần có dòng **Đối chiếu**: các khoá học, lộ trình chuẩn dùng để kiểm tra bài học đúng và đủ khi soạn.

---

## TRACK SONG SONG — `00-dsa/` (Data Structures & Algorithms) — ⏸️ TẠM HOÃN
Học theo pattern, không học thuộc lời giải. Mỗi pattern: bài lý thuyết + 5–10 bài LeetCode. Track này đi độc lập với lộ trình chính, cũng phải pass quiz mới sang pattern sau.
**Đối chiếu:** NeetCode 150 / Blind 75, 🗺️ datastructures-and-algorithms, LeetCode "14 patterns".

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| D.1 | `01-big-o` | Time/space complexity, phân tích độ phức tạp |
| D.2 | `02-arrays-hashing` | Array, hash map/set, prefix sum |
| D.3 | `03-two-pointers` | Two pointers |
| D.4 | `04-sliding-window` | Fixed & dynamic window |
| D.5 | `05-stack-queue` | Stack, monotonic stack, queue/deque |
| D.6 | `06-binary-search` | Binary search, search on answer |
| D.7 | `07-linked-list` | Fast/slow pointer, reverse, merge |
| D.8 | `08-trees` | DFS/BFS, BST, traversal |
| D.9 | `09-heap-trie` | Heap/priority queue, top-K, trie |
| D.10 | `10-backtracking` | Subset, permutation, combination |
| D.11 | `11-graphs` | BFS/DFS trên graph, topological sort, union-find, Dijkstra |
| D.12 | `12-dynamic-programming` | 1D/2D DP |
| D.13 | `13-greedy-intervals` | Greedy, merge intervals |

---

## GIAI ĐOẠN 1 — Backend Fundamentals & System Design (Tuần 1–7)

### Tuần 1 — `01-web-fundamentals/` (Web & Networking cơ bản)
**Đối chiếu:** MDN Web Docs, Cloudflare Learning Center, High Performance Browser Networking, 🗺️ backend (internet, HTTP, APIs).

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 1.1 | `01-how-internet-works` | TCP/IP, mô hình OSI/TCP-IP, TCP vs UDP, port |
| 1.2 | `02-dns` | DNS resolve thế nào, record A/AAAA/CNAME/MX/NS/TXT, TTL, DNS caching, anycast |
| 1.3 | `03-http-basics` | Request/response, method, status code, header, keep-alive, HTTP caching (Cache-Control, ETag), HTTP/1.1 vs 2 (multiplexing) vs 3 |
| 1.4 | `04-https-tls` | Mã hoá đối xứng/bất đối xứng, certificate & CA, TLS 1.2 vs 1.3 handshake (1-RTT, 0-RTT), mTLS |
| 1.5 | `05-rest-api` | REST, thiết kế endpoint, idempotency, pagination, versioning, OpenAPI; so sánh GraphQL, gRPC, SOAP |
| 1.6 | `06-cors` | Same-origin policy, CORS, preflight |

### Tuần 2 — `02-database/`
**Đối chiếu:** CMU 15-445 (Intro to Database Systems), Use The Index, Luke, Designing Data-Intensive Applications (ch. 2–3, 7), 🗺️ backend/sql/postgresql-dba.

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 2.1 | `01-sql-fundamentals` | SELECT, JOIN, GROUP BY, subquery, window function, normalization (1NF–3NF) |
| 2.2 | `02-index` | B-Tree vs hash index, clustered vs non-clustered, composite index, covering index, khi nào index không được dùng |
| 2.3 | `03-transaction-acid` | Transaction, ACID, WAL |
| 2.4 | `04-isolation-locking` | Dirty/non-repeatable/phantom read, 4 isolation level, MVCC, lock (row/gap), deadlock, optimistic vs pessimistic locking |
| 2.5 | `05-nosql` | Document, key-value, wide column, graph; SQL vs NoSQL |
| 2.6 | `06-orm-n-plus-1` | ORM, N+1 query và cách xử lý, migrations |
| 2.7 | `07-query-performance` | EXPLAIN/execution plan, slow query, pagination offset vs cursor, connection pool |

### Tuần 3 — `03-authentication-authorization/`
**Đối chiếu:** OWASP Cheat Sheet Series (Authentication, Session Management, JWT), RFC 9700 (OAuth 2.0 Security BCP), OAuth 2.1 draft, Auth0 docs, 🗺️ backend (authentication).

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 3.1 | `01-authn-vs-authz` | Authentication vs Authorization; basic auth, API key, token-based; MFA, passkeys (WebAuthn) |
| 3.2 | `02-cookie-session` | Cookie (HttpOnly, Secure, SameSite), session-based auth, session store |
| 3.3 | `03-jwt` | Cấu trúc JWT, ký/verify (HS256 vs RS256), stateless, ưu nhược điểm, lưu token ở đâu |
| 3.4 | `04-refresh-token` | Access token vs refresh token, rotation, revoke, sender-constrained token |
| 3.5 | `05-oauth2-oidc` | OAuth2 flows, Authorization Code + PKCE, OAuth 2.1 (bỏ implicit), OpenID Connect, SSO, SAML |
| 3.6 | `06-rbac-abac` | Phân quyền theo role/attribute |

### Tuần 4 — `04-web-security/`
**Đối chiếu:** OWASP Top 10:2025, PortSwigger Web Security Academy, OWASP Cheat Sheet Series.

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 4.1 | `01-owasp-top10-2025` | OWASP Top 10:2025 (A01 Broken Access Control … A03 Software Supply Chain Failures, A10 Mishandling of Exceptional Conditions) |
| 4.2 | `02-xss` | Stored/Reflected/DOM XSS, CSP, security headers (HSTS…) |
| 4.3 | `03-csrf` | CSRF, CSRF token, SameSite |
| 4.4 | `04-injection-ssrf` | SQL injection, prepared statement, command injection, SSRF |
| 4.5 | `05-password-hashing` | Hash vs encrypt, salt, bcrypt/scrypt/argon2 |
| 4.6 | `06-secrets-supply-chain` | Quản lý secret, least privilege, mã hoá in transit & at rest 📘, supply chain: dependency scanning, lockfile, SBOM |

### Tuần 5–7 — `05-system-design/` (Xử lý traffic)
**Đối chiếu:** 📘 System Design Primer, 🎯 Hello Interview System Design in a Hurry, 🗺️ system-design, ByteByteGo / System Design Interview (Alex Xu), Designing Data-Intensive Applications.

**Nền tảng: mọi thứ đều là trade-off**
| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 5.1 | `01-core-tradeoffs` | 📘 Performance vs scalability, latency vs throughput |
| 5.2 | `02-cap-consistency` | 📘 CAP theorem (CP vs AP), weak/eventual/strong consistency; 🔍 PACELC |
| 5.3 | `03-availability-patterns` | 📘 Failover active-passive/active-active, replication, số "nines", availability nối tiếp vs song song |
| 5.4 | `04-back-of-envelope` | 📘🎯 Powers of two, latency numbers, "numbers to know"; ước lượng QPS, storage, bandwidth |

**Các khối xây dựng**
| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 5.5 | `05-scaling` | Vertical vs horizontal scaling, stateless service; 🎯 scaling reads vs scaling writes |
| 5.6 | `06-load-balancer-reverse-proxy` | 📘 Load balancer L4/L7, thuật toán, health check, reverse proxy, LB vs reverse proxy; 🎯 API gateway |
| 5.7 | `07-caching` | 📘 Các tầng cache, cache-aside/write-through/write-behind/refresh-ahead, Redis vs Memcached, eviction, invalidation, cache stampede |
| 5.8 | `08-cdn` | 📘 Push CDN vs pull CDN, edge cache |
| 5.9 | `09-rate-limiting` | Token bucket, leaky bucket, sliding window (📘 Stripe rate limiter) |
| 5.10 | `10-async-message-queue` | 📘 Message queue vs task queue, back pressure, exponential backoff; Kafka, RabbitMQ, delivery semantics; 🗺️ queue-based load leveling, competing consumers; 🎯 stream processing (Flink), long-running tasks |
| 5.11 | `11-database-scaling` | 📘 Replication, federation, sharding, denormalization, SQL tuning |
| 5.12 | `12-consistent-hashing` | 📘🎯 Hash ring, virtual node, dùng trong sharding/cache |
| 5.13 | `13-data-modeling-search-blob` | 🎯 Data modeling cho system design, Elasticsearch (inverted index), object storage (S3), presigned URL (🗺️ valet key), xử lý file lớn |
| 5.14 | `14-distributed-coordination` | 🎯 Dealing with contention: distributed lock, optimistic concurrency; leader election, ZooKeeper, Raft (khái niệm) |
| 5.15 | `15-microservices` | 📘 Microservices, service discovery; 🗺️ BFF, sidecar, service mesh, strangler fig, anti-corruption layer |
| 5.16 | `16-realtime-communication` | 🔍🎯 Polling, long polling, WebSocket, SSE; fan-out qua pub/sub ở quy mô lớn |
| 5.17 | `17-distributed-transactions` | 🔍🎯 Multi-step processes: 2PC, Saga (choreography/orchestration), outbox pattern, event sourcing/CQRS |
| 5.18 | `18-id-and-probabilistic-structures` | 📘 Snowflake ID 🔍 UUID, Bloom filter, HyperLogLog, Count-Min Sketch (top-K) |
| 5.19 | `19-reliability-observability` | Circuit breaker, retry (retry storm), timeout, bulkhead, idempotency key, graceful degradation; observability: log/metric/trace (📘 Dapper) |

**Phỏng vấn & luyện đề**
| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 5.20 | `20-interview-framework` | 📘 Khung 4 bước của Primer + 🎯 delivery framework: requirements → core entities → API → high-level design → deep dives |
| 5.21 | `21-real-world-architectures` | 📘 Đọc rút nguyên lý: GFS, Bigtable, Dynamo, Cassandra, Kafka, MapReduce, ZooKeeper |
| 5.22 | `22-design-practice` | 📘 Pastebin/Bit.ly, Twitter timeline, web crawler, key-value store, AWS triệu user…; 🎯 chat app, rate limiter, Dropbox, ticket booking, news feed |

---

## GIAI ĐOẠN 2 — Core ngôn ngữ & Runtime (Tuần 8–14)

### Tuần 8 — `06-programming-foundations/` (Nền chung cho cả 3 ngôn ngữ)
**Đối chiếu:** refactoring.guru (design patterns), 🎯 Low-Level Design in a Hurry, 📘 OOD questions, 🗺️ software-design-architecture.

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 6.1 | `01-oop` | 4 tính chất OOP, interface vs abstract class, composition vs inheritance |
| 6.2 | `02-solid` | SOLID, DRY, KISS, YAGNI |
| 6.3 | `03-design-patterns` | Creational: Singleton, Factory, Builder; Structural: Adapter, Decorator, Facade; Behavioral: Strategy, Observer, State; Dependency Injection |
| 6.4 | `04-memory-model` | Stack vs heap, value vs reference type, pass-by-value, garbage collection |
| 6.5 | `05-concurrency-basics` | Process vs thread, race condition, mutex/semaphore, deadlock, sync vs async |
| 6.6 | `06-ood-practice` | 🎯 Khung trả lời LLD; 📘 LRU cache, hash map, parking lot, chat server; 🎯 elevator, movie ticket booking |

### Tuần 9 — `07-java-core/`
**Đối chiếu:** dev.java (Oracle Learn), Baeldung, 🗺️ java, JDK 25 release notes.

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 7.1 | `01-jvm-jdk-jre` | JVM, JDK, JRE, bytecode, JIT, class loading |
| 7.2 | `02-java-memory-gc` | Heap/stack/metaspace, Java Memory Model, các loại GC (G1, ZGC) |
| 7.3 | `03-core-classes-collections` | String immutability & pool, equals/hashCode, List/Set/Map/Queue, HashMap hoạt động thế nào, ArrayList vs LinkedList |
| 7.4 | `04-generics-exceptions` | Generics, type erasure, checked vs unchecked exception, try-with-resources |
| 7.5 | `05-streams-lambda` | Functional interface, lambda, Stream API, Optional |
| 7.6 | `06-java-concurrency` | Thread, ExecutorService, synchronized, volatile, Lock, ConcurrentHashMap, CompletableFuture |
| 7.7 | `07-modern-java` | Java 25 LTS: records, sealed classes, pattern matching, switch expression, virtual threads, structured concurrency, scoped values |

### Tuần 10 — `08-spring-boot/`
**Đối chiếu:** Spring official docs & Spring Academy, Baeldung, 🗺️ spring-boot, Spring Boot 4 / Spring Framework 7 release notes.

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 8.1 | `01-ioc-di` | IoC container, Bean, scope, lifecycle |
| 8.2 | `02-auto-configuration` | Auto-config, starter (modular trong Boot 4), profile, configuration properties, Maven/Gradle |
| 8.3 | `03-spring-mvc-rest` | DispatcherServlet, request lifecycle, validation, exception handling, API versioning (Framework 7) |
| 8.4 | `04-spring-data-jpa` | JPA/Hibernate, entity lifecycle, relationships, lazy/eager, @Transactional (propagation, isolation) |
| 8.5 | `05-spring-security` | Filter chain, authentication/authorization, JWT, OAuth2 resource server |
| 8.6 | `06-aop-testing` | AOP, proxy (vì sao self-invocation mất @Transactional), @SpringBootTest, MockMvc, Testcontainers |
| 8.7 | `07-production-spring` | Actuator, Micrometer/OpenTelemetry, HTTP interface client (RestClient), retry/resilience có sẵn trong Framework 7, Spring Cloud (Gateway, OpenFeign, Circuit Breaker) |

### Tuần 11 — `09-javascript-core/`
**Đối chiếu:** javascript.info, MDN JavaScript Guide, You Don't Know JS, 🗺️ javascript, 🗺️ typescript.

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 9.1 | `01-types-coercion` | Kiểu dữ liệu, == vs ===, truthy/falsy, ES6+ (destructuring, spread/rest, Map/Set/WeakMap) |
| 9.2 | `02-scope-closure-hoisting` | var/let/const, lexical scope, closure, hoisting, TDZ |
| 9.3 | `03-this-prototype` | this, call/apply/bind, prototype chain, class |
| 9.4 | `04-event-loop` | Call stack, task queue, microtask queue |
| 9.5 | `05-async` | Callback, Promise (all/allSettled/race/any), async/await, xử lý lỗi, iterator/generator |
| 9.6 | `06-memory-modules` | Garbage collection, memory leak, CommonJS vs ES Module |
| 9.7 | `07-typescript-basics` | Type, interface, union, generics, utility types |

### Tuần 12 — `10-nodejs/`
**Đối chiếu:** Node.js official docs (Learn), 🗺️ nodejs, Node.js Best Practices (goldbergyoni).

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 10.1 | `01-node-architecture` | V8, libuv, thread pool (mặc định 4), non-blocking I/O, Node vs browser |
| 10.2 | `02-node-event-loop` | Các phase của event loop, process.nextTick vs setImmediate vs Promise |
| 10.3 | `03-modules-npm` | CommonJS vs ESM trong Node, npm, package.json, semver, lockfile |
| 10.4 | `04-streams-buffer` | 4 loại stream, Buffer, backpressure, pipeline |
| 10.5 | `05-express-nestjs` | Middleware, routing, NestJS module/DI |
| 10.6 | `06-node-scaling` | Cluster, worker threads, child process, PM2 |
| 10.7 | `07-node-production` | Error handling (uncaught exception, unhandled rejection), graceful shutdown, logging, env config, security (helmet), testing (node:test/Jest/Vitest), debug memory leak |

### Tuần 13 — `11-csharp-core/`
**Đối chiếu:** Microsoft Learn (C# docs), C# in Depth, 🗺️ aspnet-core (phần C#), .NET 10 / C# 14 release notes.

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 11.1 | `01-clr-dotnet` | CLR, IL, JIT, .NET vs .NET Framework, .NET 10 LTS |
| 11.2 | `02-types-memory` | Value vs reference type, struct vs class vs record, boxing, GC (generations, workstation vs server), IDisposable/using |
| 11.3 | `03-linq-delegates` | Delegate, event, lambda, LINQ, deferred execution, IEnumerable vs IQueryable |
| 11.4 | `04-async-await` | Task, async/await, ConfigureAwait, thread pool starvation |
| 11.5 | `05-modern-csharp` | Pattern matching, nullable reference types, init/required, Span<T>, C# 14 (field-backed property, extension members) |

### Tuần 14 — `12-dotnet/`
**Đối chiếu:** Microsoft Learn (ASP.NET Core, EF Core), 🗺️ aspnet-core.

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 12.1 | `01-aspnet-core-pipeline` | Middleware pipeline & thứ tự middleware, hosting, Kestrel |
| 12.2 | `02-dependency-injection` | DI lifetime: transient/scoped/singleton, captive dependency |
| 12.3 | `03-web-api` | Controller vs Minimal API, model binding, validation, filter |
| 12.4 | `04-ef-core` | EF Core, migration, change tracking, lazy/eager/explicit loading, N+1, concurrency |
| 12.5 | `05-auth-in-dotnet` | Authentication/Authorization trong ASP.NET Core, JWT bearer, policy |
| 12.6 | `06-production-dotnet` | Configuration & Options pattern, logging (Serilog), BackgroundService, caching, health checks, testing (xUnit, WebApplicationFactory) |

---

## GIAI ĐOẠN 3 — Thực hành kỹ thuật (Tuần 15)

### Tuần 15 — `13-engineering-practices/`
**Đối chiếu:** 🗺️ backend (testing, CI/CD, containerization), 🗺️ docker, 🗺️ git-github, The Twelve-Factor App.

| # | Thư mục | Nội dung chính |
|---|---------|----------------|
| 13.1 | `01-git` | Branch, merge vs rebase, conflict, Git flow vs trunk-based |
| 13.2 | `02-testing` | Testing pyramid, unit/integration/e2e, mock/stub, TDD, test coverage |
| 13.3 | `03-docker-containers` | Container vs VM, Dockerfile, image layer, docker compose, Kubernetes cơ bản (pod, deployment, service) |
| 13.4 | `04-ci-cd-12factor` | CI/CD pipeline, deployment strategies (blue-green, canary, rolling), 12-factor app |

---

## Cách học mỗi bài
1. Prompt: **"tôi học xong `<bài cũ>` rồi, học tới `<bài mới>`"** (bài đầu tiên: "bắt đầu học 1.1"). DSA: "học DSA D.x".
2. Claude kiểm tra bài cũ đã pass quiz 100% chưa → nghiên cứu (đối chiếu các nguồn ở dòng **Đối chiếu** của phần đó) và sinh `lesson.md`.
3. Học xong, prompt "cho tôi câu hỏi ôn" → Claude sinh `quiz.md`.
4. Trả lời (trong chat hoặc ghi vào `answers.md`) → Claude chấm, ghi kết quả vào `grading.md` và `PROGRESS.md`.
5. Sai câu nào → Claude giải thích và cho câu hỏi làm lại. Đúng hết mới được qua bài.
