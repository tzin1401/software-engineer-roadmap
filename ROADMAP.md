# Lộ trình 15 tuần — Software Engineer từ nền tảng

**Nguyên tắc sắp xếp: học cái nền trước, cái dựa trên nó sau.** Mỗi bài có cột **Cần biết trước**: các bài đó nằm phía trên nên khi học tới bạn đã có đủ kiến thức để hiểu.
Thứ tự lớn: mạng → nền tảng máy tính → database → auth → security → system design → thiết kế phần mềm → ngôn ngữ & framework → công cụ kỹ thuật.
Chỉ được sang bài tiếp theo khi trả lời **đúng 100%** câu hỏi ôn của bài hiện tại.
**Track DSA đang TẠM HOÃN**; giữ sẵn thư mục `00-dsa/` để học sau.

Ký hiệu nguồn:
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

## GIAI ĐOẠN 1 — Nền tảng (Tuần 1–5)

### Tuần 1 — `01-web-fundamentals/` (Mạng & Web)
Mọi thứ phía sau (database qua mạng, auth qua HTTP, system design) đều chạy trên mạng, nên học đầu tiên.
**Đối chiếu:** MDN Web Docs, Cloudflare Learning Center, High Performance Browser Networking, 🗺️ backend (internet, HTTP, APIs).

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 1.1 | `01-how-internet-works` | Packet, IP, router, OSI/TCP-IP, TCP vs UDP, port | — |
| 1.2 | `02-dns` | DNS resolve thế nào, record A/AAAA/CNAME/MX/NS/TXT, TTL, caching, anycast | 1.1 |
| 1.3 | `03-http-basics` | Request/response, method, status code, header, keep-alive, HTTP caching (Cache-Control, ETag), cookie là gì, HTTP/1.1 vs 2 vs 3 | 1.1, 1.2 |
| 1.4 | `04-cryptography-basics` | Hash, HMAC, mã hoá đối xứng vs bất đối xứng, chữ ký số, certificate & CA | — |
| 1.5 | `05-https-tls` | TLS 1.2 vs 1.3 handshake (1-RTT, 0-RTT), mTLS | 1.1, 1.3, 1.4 |
| 1.6 | `06-rest-api` | REST, thiết kế endpoint, idempotency, pagination, versioning, OpenAPI; so sánh GraphQL, gRPC | 1.3 |

### Tuần 2 — `02-computing-foundations/` (Nền tảng máy tính)
Cần cho: locking trong database, ORM, event loop của Node, async/await, concurrency của Java/C#.
**Đối chiếu:** Operating Systems: Three Easy Pieces (phần concurrency), 🗺️ computer-science, 🗺️ software-design-architecture (OOP).

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 2.1 | `01-process-thread-concurrency` | Process vs thread, race condition, mutex/semaphore, deadlock, sync vs async, blocking vs non-blocking I/O | 1.1 |
| 2.2 | `02-memory-model` | Stack vs heap, value vs reference type, pass-by-value, garbage collection | — |
| 2.3 | `03-oop` | Class/object, 4 tính chất OOP, interface vs abstract class, composition vs inheritance | 2.2 |

### Tuần 2–3 — `03-database/`
**Đối chiếu:** CMU 15-445 (Intro to Database Systems), Use The Index, Luke, Designing Data-Intensive Applications (ch. 2–3, 7), 🗺️ backend/sql/postgresql-dba.

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 3.1 | `01-sql-fundamentals` | SELECT, JOIN, GROUP BY, subquery, window function, normalization (1NF–3NF) | — |
| 3.2 | `02-index` | B-Tree vs hash index, clustered vs non-clustered, composite, covering index, khi nào index không được dùng | 3.1 |
| 3.3 | `03-transaction-acid` | Transaction, ACID, WAL | 3.1 |
| 3.4 | `04-isolation-locking` | Dirty/non-repeatable/phantom read, 4 isolation level, MVCC, row/gap lock, deadlock, optimistic vs pessimistic locking | 2.1, 3.3 |
| 3.5 | `05-query-performance` | EXPLAIN/execution plan, slow query, pagination offset vs cursor, connection pool | 1.1, 3.2 |
| 3.6 | `06-orm-n-plus-1` | ORM, N+1 query và cách xử lý, migrations | 2.3, 3.5 |
| 3.7 | `07-nosql` | Document, key-value, wide column, graph; SQL vs NoSQL | 3.1–3.3 |

### Tuần 3–4 — `04-authentication-authorization/`
**Đối chiếu:** OWASP Cheat Sheet Series (Authentication, Session Management, JWT), RFC 9700 (OAuth 2.0 Security BCP), OAuth 2.1 draft, Auth0 docs, 🗺️ backend (authentication).

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 4.1 | `01-authn-vs-authz` | Authentication vs Authorization; basic auth, API key, token-based; MFA, passkeys (WebAuthn) | 1.3, 1.4 |
| 4.2 | `02-cookie-session` | Cookie (HttpOnly, Secure, SameSite), session-based auth, session store | 1.3, 4.1 |
| 4.3 | `03-jwt` | Cấu trúc JWT, ký/verify (HS256 vs RS256), stateless, ưu nhược điểm, lưu token ở đâu | 1.4, 4.2 |
| 4.4 | `04-refresh-token` | Access vs refresh token, rotation, revoke, sender-constrained token | 4.3 |
| 4.5 | `05-oauth2-oidc` | OAuth2 flows, Authorization Code + PKCE, OAuth 2.1, OpenID Connect, SSO, SAML | 4.3, 4.4 |
| 4.6 | `06-rbac-abac` | Phân quyền theo role/attribute | 4.1 |

### Tuần 4–5 — `05-web-security/`
Học sau auth vì nhiều lỗ hổng (CSRF, XSS đánh cắp token) tấn công chính cơ chế đăng nhập.
**Đối chiếu:** OWASP Top 10:2025, PortSwigger Web Security Academy, OWASP Cheat Sheet Series.

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 5.1 | `01-same-origin-cors` | Origin, same-origin policy, CORS, preflight, credentials | 1.3, 4.2 |
| 5.2 | `02-xss` | Stored/Reflected/DOM XSS, CSP, security headers (HSTS…) | 4.2, 4.3, 5.1 |
| 5.3 | `03-csrf` | CSRF, CSRF token, SameSite; vì sao CORS không chống được CSRF | 4.2, 5.1 |
| 5.4 | `04-injection-ssrf` | SQL injection, prepared statement, command injection, SSRF | 3.1 |
| 5.5 | `05-password-storage` | Hash vs encrypt, salt, bcrypt/scrypt/argon2 | 1.4 |
| 5.6 | `06-secrets-supply-chain` | Quản lý secret, least privilege, mã hoá in transit & at rest 📘, dependency scanning, lockfile, SBOM | 1.5 |
| 5.7 | `07-owasp-top10-2025` | Tổng hợp: map các bài trên vào OWASP Top 10:2025 (A01 Broken Access Control … A03 Software Supply Chain Failures, A10 Mishandling of Exceptional Conditions) | 5.1–5.6 |

---

## GIAI ĐOẠN 2 — System Design (Tuần 5–8)

### `06-system-design/`
Thứ tự: khối xây dựng đơn lẻ → dữ liệu phân tán → trade-off lý thuyết (CAP chỉ hiểu được sau khi biết replication) → hệ thống nhiều service → luyện đề.
**Đối chiếu:** 📘 System Design Primer, 🎯 Hello Interview System Design in a Hurry, 🗺️ system-design, ByteByteGo / System Design Interview (Alex Xu), Designing Data-Intensive Applications.

**A. Tư duy & các khối xây dựng**
| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 6.1 | `01-core-tradeoffs` | 📘 Performance vs scalability, latency vs throughput | 1.1 |
| 6.2 | `02-back-of-envelope` | 📘🎯 Powers of two, latency numbers, ước lượng QPS, storage, bandwidth | 6.1 |
| 6.3 | `03-scaling` | Vertical vs horizontal, stateless service; 🎯 scaling reads vs writes | 4.2, 6.1 |
| 6.4 | `04-load-balancer-reverse-proxy` | 📘 L4/L7, thuật toán, health check, reverse proxy; 🎯 API gateway | 1.1, 1.3, 6.3 |
| 6.5 | `05-caching` | 📘 Các tầng cache, cache-aside/write-through/write-behind/refresh-ahead, Redis vs Memcached, eviction, invalidation, cache stampede | 1.3, 3.5, 6.3 |
| 6.6 | `06-cdn` | 📘 Push vs pull CDN, edge cache | 1.2, 6.5 |

**B. Dữ liệu phân tán**
| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 6.7 | `07-database-scaling` | 📘 Replication, federation, sharding, denormalization, SQL tuning | 3.4, 3.5, 6.3 |
| 6.8 | `08-consistent-hashing` | 📘🎯 Hash ring, virtual node | 1.4 (hash), 6.5, 6.7 |
| 6.9 | `09-cap-consistency` | 📘 CAP (CP vs AP), weak/eventual/strong consistency; 🔍 PACELC | 6.7 |
| 6.10 | `10-availability-patterns` | 📘 Failover active-passive/active-active, số "nines", availability nối tiếp vs song song | 6.4, 6.7, 6.9 |
| 6.11 | `11-data-modeling-search-blob` | 🎯 Data modeling, Elasticsearch (inverted index), object storage (S3), presigned URL | 3.2, 3.7, 6.7 |

**C. Bất đồng bộ & điều phối**
| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 6.12 | `12-async-message-queue` | 📘 Message queue vs task queue, back pressure, backoff; Kafka, RabbitMQ, delivery semantics; 🗺️ queue-based load leveling; 🎯 stream processing, long-running tasks | 2.1, 6.3 |
| 6.13 | `13-rate-limiting` | Token bucket, leaky bucket, sliding window (📘 Stripe) | 6.4, 6.5 |
| 6.14 | `14-distributed-coordination` | 🎯 Contention: distributed lock, optimistic concurrency; leader election, ZooKeeper, Raft (khái niệm) | 2.1, 3.4, 6.9 |
| 6.15 | `15-id-and-probabilistic-structures` | 📘 Snowflake 🔍 UUID, Bloom filter, HyperLogLog, Count-Min Sketch | 1.4 (hash), 6.7 |

**D. Hệ thống nhiều service**
| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 6.16 | `16-microservices` | 📘 Microservices, service discovery; 🗺️ BFF, sidecar, service mesh, strangler fig | 1.6, 6.4, 6.12 |
| 6.17 | `17-distributed-transactions` | 🔍🎯 2PC, Saga, outbox pattern, event sourcing/CQRS | 3.3, 6.12, 6.16 |
| 6.18 | `18-realtime-communication` | 🔍🎯 Polling, long polling, WebSocket, SSE; fan-out qua pub/sub | 1.3, 6.4, 6.12 |
| 6.19 | `19-reliability-observability` | Circuit breaker, retry (retry storm), timeout, bulkhead, idempotency key, graceful degradation; log/metric/trace (📘 Dapper) | 1.6, 6.16 |

**E. Phỏng vấn & luyện đề**
| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 6.20 | `20-interview-framework` | 📘 Khung 4 bước + 🎯 delivery framework: requirements → core entities → API → high-level → deep dives | 6.1–6.19 |
| 6.21 | `21-real-world-architectures` | 📘 GFS, Bigtable, Dynamo, Cassandra, Kafka, MapReduce, ZooKeeper | 6.7–6.14 |
| 6.22 | `22-design-practice` | 📘 Pastebin/Bit.ly, Twitter timeline, web crawler, key-value store…; 🎯 chat app, rate limiter, Dropbox, ticket booking, news feed | 6.20 |

---

## GIAI ĐOẠN 3 — Thiết kế phần mềm & ngôn ngữ (Tuần 8–15)

### Tuần 8–9 — `07-software-design/`
Học trước Spring/.NET/NestJS vì cả ba xây trên Dependency Injection và design patterns.
**Đối chiếu:** refactoring.guru, 🎯 Low-Level Design in a Hurry, 📘 OOD questions, 🗺️ software-design-architecture, Martin Fowler (testing).

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 7.1 | `01-solid` | SOLID, DRY, KISS, YAGNI | 2.3 |
| 7.2 | `02-design-patterns` | Singleton, Factory, Builder; Adapter, Decorator, Facade; Strategy, Observer, State; Dependency Injection | 7.1 |
| 7.3 | `03-testing` | Testing pyramid, unit/integration/e2e, mock/stub, TDD | 7.2 (DI để mock) |
| 7.4 | `04-ood-practice` | 🎯 Khung trả lời LLD; 📘 LRU cache, hash map, parking lot, chat server; 🎯 elevator, ticket booking | 7.2 |

### Tuần 9–10 — `08-java-core/`
**Đối chiếu:** dev.java (Oracle Learn), Baeldung, 🗺️ java, JDK 25 release notes.

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 8.1 | `01-jvm-jdk-jre` | JVM, JDK, JRE, bytecode, JIT, class loading | — |
| 8.2 | `02-java-memory-gc` | Heap/stack/metaspace, Java Memory Model, G1, ZGC | 2.2, 8.1 |
| 8.3 | `03-core-classes-collections` | String immutability & pool, equals/hashCode, List/Set/Map/Queue, HashMap bên trong | 2.3, 8.2 |
| 8.4 | `04-generics-exceptions` | Generics, type erasure, checked vs unchecked, try-with-resources | 8.3 |
| 8.5 | `05-streams-lambda` | Functional interface, lambda, Stream API, Optional | 8.4 |
| 8.6 | `06-java-concurrency` | Thread, ExecutorService, synchronized, volatile, Lock, ConcurrentHashMap, CompletableFuture | 2.1, 8.3 |
| 8.7 | `07-modern-java` | Java 25 LTS: records, sealed classes, pattern matching, switch expression, virtual threads, structured concurrency, scoped values | 8.5, 8.6 |

### Tuần 10–11 — `09-spring-boot/`
**Đối chiếu:** Spring docs & Spring Academy, Baeldung, 🗺️ spring-boot, Spring Boot 4 / Framework 7 release notes.

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 9.1 | `01-ioc-di` | IoC container, Bean, scope, lifecycle | 7.2, 8.4 |
| 9.2 | `02-auto-configuration` | Auto-config, starter (modular trong Boot 4), profile, configuration properties, Maven/Gradle | 9.1 |
| 9.3 | `03-spring-mvc-rest` | DispatcherServlet, request lifecycle, validation, exception handling, API versioning | 1.6, 9.2 |
| 9.4 | `04-spring-data-jpa` | JPA/Hibernate, entity lifecycle, relationships, lazy/eager, @Transactional (propagation, isolation) | 3.4, 3.6, 9.3 |
| 9.5 | `05-spring-security` | Filter chain, authentication/authorization, JWT, OAuth2 resource server | 4.3–4.5, 9.3 |
| 9.6 | `06-aop-testing` | AOP, proxy (vì sao self-invocation mất @Transactional), @SpringBootTest, MockMvc, Testcontainers | 7.3, 9.4 |
| 9.7 | `07-production-spring` | Actuator, Micrometer/OpenTelemetry, RestClient/HTTP interface, retry có sẵn (Framework 7), Spring Cloud | 6.19, 9.6 |

### Tuần 11–12 — `10-javascript-core/`
**Đối chiếu:** javascript.info, MDN JavaScript Guide, You Don't Know JS, 🗺️ javascript, 🗺️ typescript.

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 10.1 | `01-types-coercion` | Kiểu dữ liệu, == vs ===, truthy/falsy, ES6+ (destructuring, spread/rest, Map/Set) | 2.2 |
| 10.2 | `02-scope-closure-hoisting` | var/let/const, lexical scope, closure, hoisting, TDZ | 10.1 |
| 10.3 | `03-this-prototype` | this, call/apply/bind, prototype chain, class | 2.3, 10.2 |
| 10.4 | `04-event-loop` | Call stack, task queue, microtask queue | 2.1, 10.2 |
| 10.5 | `05-async` | Callback, Promise (all/allSettled/race/any), async/await, xử lý lỗi, iterator/generator | 10.4 |
| 10.6 | `06-memory-modules` | Garbage collection, memory leak, CommonJS vs ES Module | 2.2, 10.2 |
| 10.7 | `07-typescript-basics` | Type, interface, union, generics, utility types | 10.3 |

### Tuần 12–13 — `11-nodejs/`
**Đối chiếu:** Node.js docs (Learn), 🗺️ nodejs, Node.js Best Practices (goldbergyoni).

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 11.1 | `01-node-architecture` | V8, libuv, thread pool (mặc định 4), non-blocking I/O, Node vs browser | 2.1, 10.4 |
| 11.2 | `02-node-event-loop` | Các phase của event loop, process.nextTick vs setImmediate vs Promise | 10.5, 11.1 |
| 11.3 | `03-modules-npm` | CommonJS vs ESM trong Node, npm, package.json, semver, lockfile | 10.6 |
| 11.4 | `04-streams-buffer` | 4 loại stream, Buffer, backpressure, pipeline | 11.2 |
| 11.5 | `05-express-nestjs` | Middleware, routing, NestJS module/DI | 1.6, 7.2, 11.3 |
| 11.6 | `06-node-scaling` | Cluster, worker threads, child process, PM2 | 6.3, 11.1 |
| 11.7 | `07-node-production` | Uncaught exception/unhandled rejection, graceful shutdown, logging, env config, helmet, testing (node:test/Jest/Vitest), debug memory leak | 7.3, 11.5 |

### Tuần 13–14 — `12-csharp-core/`
**Đối chiếu:** Microsoft Learn (C#), C# in Depth, 🗺️ aspnet-core (phần C#), .NET 10 / C# 14 release notes.

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 12.1 | `01-clr-dotnet` | CLR, IL, JIT, .NET vs .NET Framework, .NET 10 LTS (so sánh với JVM ở 8.1) | 8.1 |
| 12.2 | `02-types-memory` | Value vs reference, struct vs class vs record, boxing, GC generations, IDisposable/using | 2.2, 12.1 |
| 12.3 | `03-linq-delegates` | Delegate, event, lambda, LINQ, deferred execution, IEnumerable vs IQueryable | 8.5, 12.2 |
| 12.4 | `04-async-await` | Task, async/await, ConfigureAwait, thread pool starvation | 2.1, 10.5 |
| 12.5 | `05-modern-csharp` | Pattern matching, nullable reference types, init/required, Span<T>, C# 14 | 12.3 |

### Tuần 14–15 — `13-dotnet/`
**Đối chiếu:** Microsoft Learn (ASP.NET Core, EF Core), 🗺️ aspnet-core.

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 13.1 | `01-aspnet-core-pipeline` | Middleware pipeline & thứ tự, hosting, Kestrel | 1.3, 12.4 |
| 13.2 | `02-dependency-injection` | Transient/scoped/singleton, captive dependency | 7.2, 13.1 |
| 13.3 | `03-web-api` | Controller vs Minimal API, model binding, validation, filter | 1.6, 13.2 |
| 13.4 | `04-ef-core` | Migration, change tracking, lazy/eager/explicit loading, N+1, concurrency | 3.4, 3.6, 12.3 |
| 13.5 | `05-auth-in-dotnet` | Authentication/Authorization, JWT bearer, policy | 4.3–4.6, 13.1 |
| 13.6 | `06-production-dotnet` | Options pattern, Serilog, BackgroundService, caching, health checks, xUnit, WebApplicationFactory | 7.3, 13.3 |

### Tuần 15 — `14-engineering-practices/`
**Đối chiếu:** 🗺️ backend (CI/CD, containerization), 🗺️ docker, 🗺️ git-github, The Twelve-Factor App.

| # | Thư mục | Nội dung chính | Cần biết trước |
|---|---------|----------------|----------------|
| 14.1 | `01-git` | Branch, merge vs rebase, conflict, Git flow vs trunk-based | — |
| 14.2 | `02-docker-containers` | Container vs VM, Dockerfile, image layer, compose, Kubernetes cơ bản | 1.1, 2.1 |
| 14.3 | `03-ci-cd-12factor` | CI/CD pipeline, blue-green/canary/rolling, 12-factor app | 7.3, 14.2 |

---

## Cách học mỗi bài
1. Prompt: **"tôi học xong `<bài cũ>` rồi, học tới `<bài mới>`"** (bài đầu tiên: "bắt đầu học 1.1").
2. Claude kiểm tra bài cũ đã pass quiz 100% chưa → nghiên cứu (đối chiếu dòng **Đối chiếu**) → sinh `lesson.md` theo khung giảng trong `CLAUDE.md`.
3. Học xong, prompt "cho tôi câu hỏi ôn" → Claude sinh `quiz.md` (có cả vài câu ôn lại các bài trước).
4. Trả lời (trong chat hoặc `answers.md`) → Claude chấm, ghi `grading.md` và `PROGRESS.md`.
5. Sai câu nào → Claude giải thích lại và cho câu hỏi làm lại. Đúng hết mới được qua bài.
