# System Design: 80-Day Plan (2 hrs/day, ~160 hrs)

**Goal:** interview-ready and architect-level depth at work. **Starting level:** basics.
**Hands-on:** every concept gets built as a small project in a public GitHub repo, `system-design-lab`, with commits most days.

## Daily routine (2 hrs)

**Regular day** (no build scheduled)
| Block | Time | What |
|---|---|---|
| Learn | 45 min | Read / watch the day's topic |
| Apply | 45 min | Design or draw on paper, **no notes**, then check against the source |
| Review | 30 min | Anki flashcards on yesterday + 3 days ago; explain today's topic out loud in 2 min |

**Build day** (marked 🔨 in the tables)
| Block | Time | What |
|---|---|---|
| Learn | 30 min | Same topic, trimmed to the essentials |
| Sketch | 15 min | The day's Apply task, as a quick sketch only |
| Build | 45 min | Work on the day's project; **end with a commit and push** |
| Review | 30 min | Flashcards + 2-minute out-loud explanation |

**Mock days (every 7th day)** stay pure: 45 min timed mock, 45 min critique, 30 min flashcards. No building.
If you miss a day, do not stack up. Skip the next regular day's Apply block and carry on.

## Hands-on build track

### Repo: `system-design-lab` (public GitHub)
- **Language: Python** (matches your day-to-day stack). Go is a good alternative for the LSM store and Raft if you want the extra practice.
- One folder per project, with:
  - `README.md`: problem, design diagram, tradeoffs, how to run, benchmark or test results
  - `docker-compose.yml` where it needs Redis, Postgres, or Kafka
  - tests, including failure tests (kill a node, drop messages, send concurrent requests)
- Root `README.md` indexes all projects with one line each.
- Commit conventions: small commits, `feat(lsm): add compaction`; push at the end of every build session.
- Tag each finished project, for example `p3-lsm-done`.
- Add a GitHub Actions workflow that runs tests per project.
- **Public repo rule:** use only generic domains and synthetic data. Do **not** commit anything from InReport or Innovaccer (code, schemas, configs, or data). The Day 74 design stays in your private notes, not the repo.

### Projects and their days
| # | Project | Build days | What you learn |
|---|---|---|---|
| P1 | URL shortener (FastAPI + Postgres, base62 IDs) | 3, 5, 6 | API design, schema, indexing |
| P2 | Caching layer (cache-aside, LRU from scratch, stampede protection) | 10, 11 | Eviction, invalidation, hot keys |
| P3 | LSM-tree key-value store (WAL, memtable, SSTables, compaction) | 8, 9, 12 | Storage engines, serialization |
| P4 | Replicated KV (leader/follower, async replication, failover) | 15, 16, 17 | Replication, lag anomalies, quorums |
| P5 | Sharded KV cluster with consistent hashing | 18, 19 | Partitioning, rebalancing |
| P6 | Rate limiter (token bucket, sliding window, Redis + Lua) | 20 | Atomicity, distributed counters |
| P7 | Snowflake-style ID generator | 27 | Ordering, clocks |
| P8 | Raft (leader election + log replication, simulated network, fault tests) | 30, 31, 32 | Consensus |
| P9 | Order saga + outbox (Kafka, idempotent consumers) | 33, 38 | Distributed transactions, delivery semantics |
| P10 | Mini Kafka (append-only log, partitions, consumer groups, offsets) | 36, 37 | Logs, ordering, replay |
| P11 | Streaming click counter (windows, late data) | 39, 40, 41 | Stream processing, CDC/outbox |
| P12 | Mini search engine (inverted index, BM25, Bloom filter, typeahead trie) | 43, 46, 47 | Search internals, probabilistic structures, geohash |
| P13 | Chat server (WebSockets, Redis pub/sub, presence, ordering) | 48 | Real-time transport |
| P14 | Resilience toolkit (retry + jitter, circuit breaker, bulkhead) with chaos tests | 51, 52 | Failure handling, failover drill on P4 |
| P15 | Observability stack (Prometheus, Grafana, tracing) added to P1/P9/P13, then deployed on `kind` Kubernetes | 53, 54, 55 | Metrics, tracing, JWT auth and tenant isolation, k8s rollout |
| P16 | Case-study mini builds (see Phase 4 table): feed fan-out simulator, chunked file sync, crawler, payment ledger, seat-hold booking, CRDT text, job scheduler, ETL | 57, 60, 61, 65, 67, 68, 72, 73 | Core mechanism of each case study |
| P17 | Capstone: polish, benchmark, and document the best 3 projects; architecture diagrams | 78-80 | Communication |

Builds on Days 64, 69, 71, and 75 reuse earlier projects: extend P12's geohash for the Uber design, P15 for metrics, P4/P5 for the cache cluster, and P11 for ad-click aggregation.

## Resources
- **DDIA**: *Designing Data-Intensive Applications* (Kleppmann). Backbone of Phases 1-3.
- **Xu**: *System Design Interview* Vol 1 and 2 (Alex Xu). Case studies.
- **MIT 6.824**: free lectures on YouTube, and the Raft labs for P8.
- **Papers**: Dynamo, GFS, MapReduce, Bigtable, Spanner, Kafka, Raft.
- **System Design Primer** (GitHub, donnemartin) for quick reference and flashcards.
- Engineering blogs: Uber, Netflix, Discord, Stripe, Meta, Cloudflare.

## Case-study template (use from Day 1, one page each)
1. Requirements (functional / non-functional) and scope
2. Estimation (QPS, storage, bandwidth)
3. API
4. Data model and storage choice
5. High-level design
6. Deep dive on 1-2 hard parts
7. Bottlenecks, failure modes, tradeoffs ("I chose X over Y because ..., at the cost of ...")

---

## Phase 1: Fundamentals (Days 1-21)

### Week 1: Approach, networking, APIs, first databases
| Day | Learn | Apply / Sketch | 🔨 Build |
|---|---|---|---|
| ☐ 1 | The design framework; latency numbers; powers of 2; back-of-envelope estimation | Estimate Twitter storage and QPS | Create the `system-design-lab` repo with README index, `.gitignore`, and CI skeleton |
| ☐ 2 | Networking: TCP vs UDP, DNS, HTTP/1.1/2/3, TLS | Trace everything that happens when you type a URL | |
| ☐ 3 | API design: REST, gRPC, GraphQL, pagination, idempotency, versioning | Write the API for a URL shortener | 🔨 **P1**: FastAPI skeleton, `POST /shorten`, `GET /{code}`, tests |
| ☐ 4 | Scaling basics: vertical vs horizontal, stateless services, L4/L7 load balancers, LB algorithms | Scale a single-server app from 1K to 1M users, step by step | |
| ☐ 5 | Relational DBs: ACID, normalization, indexes (DDIA ch1-2) | Schema design for an e-commerce store | 🔨 **P1**: Postgres storage, base62 ID generation, unique index |
| ☐ 6 | NoSQL: key-value, document, wide-column, graph; SQL vs NoSQL | Pick a DB for 5 given scenarios and defend each | 🔨 **P1**: add Docker Compose, load test, write the README with design and tradeoffs |
| ☐ 7 | **Review + Mock: URL shortener** | | |

### Week 2: Storage and caching
| Day | Learn | Apply / Sketch | 🔨 Build |
|---|---|---|---|
| ☐ 8 | Storage engines: B-tree vs LSM-tree, WAL, SSTables (DDIA ch3) | Compare write/read paths of both on paper | 🔨 **P3**: memtable + write-ahead log, crash recovery test |
| ☐ 9 | Indexing deep dive: composite, covering, secondary, inverted; query plans | Run `explain` on a slow query in a local sample DB (not work data) | 🔨 **P3**: flush memtable to SSTables, read path with sparse index |
| ☐ 10 | Caching 1: cache-aside, write-through, write-back, eviction (LRU/LFU) | Add a cache layer to the Day 5 e-commerce design | 🔨 **P2**: LRU cache from scratch, plus cache-aside in front of P1 |
| ☐ 11 | Caching 2: invalidation, stampede, hot keys, TTLs, CDN | Design cache invalidation for a product page | 🔨 **P2**: stampede protection (single-flight), hit-rate benchmark |
| ☐ 12 | Encoding and schema evolution: JSON, Protobuf, Avro (DDIA ch4) | Evolve an API schema without breaking old clients | 🔨 **P3**: binary SSTable encoding with Protobuf/msgpack, compaction |
| ☐ 13 | Object/blob storage, file systems, S3 concepts, pre-signed URLs | Design a file upload service | |
| ☐ 14 | **Review + Mock: Pastebin** | | |

### Week 3: Scaling data
| Day | Learn | Apply / Sketch | 🔨 Build |
|---|---|---|---|
| ☐ 15 | Replication 1: leader-follower, sync vs async, failover (DDIA ch5) | Walk through a leader crash step by step | 🔨 **P4**: leader + follower nodes, async log shipping |
| ☐ 16 | Replication 2: multi-leader, leaderless, quorums (W+R>N), read repair | Compute quorum settings for 3 availability/consistency goals | 🔨 **P4**: configurable quorum reads/writes, read repair |
| ☐ 17 | Replication lag: read-your-writes, monotonic reads, conflict resolution | Spot the anomaly in 5 given scenarios | 🔨 **P4**: failover on leader kill, test that demonstrates replication-lag anomalies |
| ☐ 18 | Partitioning 1: range vs hash, hot spots (DDIA ch6) | Choose a partition key for a chat app and a logs system | 🔨 **P5**: router + N shards, hash partitioning |
| ☐ 19 | Partitioning 2: consistent hashing, virtual nodes, rebalancing, secondary indexes | Implement consistent hashing in 30 lines | 🔨 **P5**: consistent hashing ring with virtual nodes, add/remove node with key movement stats |
| ☐ 20 | Rate limiting: token bucket, leaky bucket, sliding window; distributed limiter with Redis | Design the limiter's data model and race-condition handling | 🔨 **P6**: token bucket and sliding window, Redis Lua script, concurrency test |
| ☐ 21 | **Review + Mock: Rate limiter** (compare against your P6 build) | | |

## Phase 2: Distributed systems theory (Days 22-42)

### Week 4: Transactions and consistency
| Day | Learn | Apply / Sketch | 🔨 Build |
|---|---|---|---|
| ☐ 22 | Transactions and isolation levels (DDIA ch7) | Map each level to the anomalies it prevents | |
| ☐ 23 | Dirty read, lost update, write skew, phantoms; MVCC; serializable snapshot isolation | Design a booking flow that avoids write skew | |
| ☐ 24 | Locking, optimistic concurrency, distributed locks, fencing tokens | Critique a Redis-based lock design | |
| ☐ 25 | Distributed trouble: unreliable networks, clocks, pauses (DDIA ch8) | List failure modes of a 3-service call chain | |
| ☐ 26 | Consistency models: linearizable, sequential, causal, eventual; CAP and PACELC | Classify Postgres, Cassandra, DynamoDB, Mongo on CAP/PACELC | |
| ☐ 27 | Ordering: Lamport and vector clocks; ID generation (UUID, Snowflake, ULID) | Design a unique ID generator | 🔨 **P7**: Snowflake-style generator service, clock-skew handling test |
| ☐ 28 | **Review + Mock: Distributed KV store** (compare against P4/P5) | | |

Optional on Days 22-24: write a small script that reproduces lost update and write skew on Postgres at different isolation levels; commit it under `p-extras/isolation-demos`.

### Week 5: Consensus and distributed transactions
| Day | Learn | Apply / Sketch | 🔨 Build |
|---|---|---|---|
| ☐ 29 | Consensus intro: leader election, Paxos idea (DDIA ch9) | Explain why consensus is hard in 2 min, out loud | |
| ☐ 30 | Raft part 1: raft.github.io visualization and the Raft paper | Trace a leader election with a network partition | 🔨 **P8**: simulated network, node state machine, leader election |
| ☐ 31 | Raft part 2: log replication, safety; etcd/ZooKeeper use cases | Design service discovery on top of etcd | 🔨 **P8**: log replication, commit index |
| ☐ 32 | Distributed transactions: 2PC, 3PC, XA, their failure modes | Draw the 2PC failure cases | 🔨 **P8**: fault tests (partition, leader crash, message loss), README with state diagram |
| ☐ 33 | Sagas, outbox pattern, idempotency keys, "exactly-once" | Design the order/payment flow as a saga | 🔨 **P9**: order, payment, and inventory services; saga orchestrator |
| ☐ 34 | Failure detection, gossip; **Dynamo paper** | Summarize Dynamo's design choices and tradeoffs on one page | |
| ☐ 35 | **Review + Mock: Distributed lock service** | | |

### Week 6: Messaging and streaming
| Day | Learn | Apply / Sketch | 🔨 Build |
|---|---|---|---|
| ☐ 36 | Queues vs logs, pub/sub, RabbitMQ vs Kafka (DDIA ch11) | Choose queue vs log for 4 scenarios | 🔨 **P10**: append-only log, segment files, offsets |
| ☐ 37 | Kafka internals: partitions, consumer groups, offsets, ISR, retention; Kafka paper | Size a Kafka topic for 100K msg/s | 🔨 **P10**: partitions, consumer groups, replay, retention |
| ☐ 38 | Delivery semantics, ordering, DLQs, retries, backpressure | Design retry + DLQ policy for a payment event consumer | 🔨 **P9**: outbox table + relay to real Kafka (Docker), idempotent consumer, DLQ |
| ☐ 39 | Stream processing: windows, event time vs processing time, watermarks | Design a real-time "clicks per minute" counter | 🔨 **P11**: event producer, tumbling windows |
| ☐ 40 | Batch: MapReduce and Spark (DDIA ch10); GFS paper | Write word-count and a join in MapReduce terms | 🔨 **P11**: late-data handling with watermarks, sliding windows |
| ☐ 41 | CDC, event sourcing, CQRS, derived data (DDIA ch12) | Redesign the Day 5 e-commerce with CQRS | 🔨 **P11**: event-sourced read model rebuilt from the log |
| ☐ 42 | **Review + Mock: Notification system** | | |

## Phase 3: Specialized systems and architecture (Days 43-56)

### Week 7: Search, stores, infra
| Day | Learn | Apply / Sketch | 🔨 Build |
|---|---|---|---|
| ☐ 43 | Search: Lucene/Elasticsearch internals, inverted index, shards, relevance | Explain how a query on an Elasticsearch cluster executes | 🔨 **P12**: tokenizer, inverted index, BM25 ranking |
| ☐ 44 | Bigtable paper; wide-column and Cassandra data modeling | Model a time-series use case in Cassandra | |
| ☐ 45 | Spanner, TrueTime, NewSQL (CockroachDB) | Explain how Spanner gets external consistency | |
| ☐ 46 | Redis deep dive; Bloom filters, HyperLogLog, count-min sketch | Pick a probabilistic structure for 3 problems | 🔨 **P12**: Bloom filter (also usable in P3 reads), HyperLogLog |
| ☐ 47 | Geospatial (geohash, quadtree, S2), time-series DBs, graph DBs | Design "find nearby X" | 🔨 **P12**: geohash index and nearby search |
| ☐ 48 | Proxies, API gateway, service mesh, WebSockets vs SSE vs long polling | Choose a real-time transport for 3 products | 🔨 **P13**: WebSocket chat server, Redis pub/sub across 2 instances |
| ☐ 49 | **Review + Mock: Search autocomplete** (compare against P12) | | |

### Week 8: Architecture and reliability
| Day | Learn | Apply / Sketch | 🔨 Build |
|---|---|---|---|
| ☐ 50 | Microservices vs monolith, service boundaries, service discovery | Split a monolith: draw the boundaries and justify them | |
| ☐ 51 | Resilience: timeouts, retries with jitter, circuit breakers, bulkheads, load shedding | Add resilience to the Day 38 consumer design | 🔨 **P14**: retry with jitter, circuit breaker, bulkhead |
| ☐ 52 | SLI/SLO/SLA, multi-region, active-active, DR, RPO/RTO | Define SLOs and a DR plan for a payments API | 🔨 **P14**: chaos tests, plus a failover drill on P4 with measured recovery time |
| ☐ 53 | Observability: logs, metrics, traces, alerting; read 2 public postmortems | Write your own postmortem for a hypothetical outage | 🔨 **P15**: Prometheus metrics and Grafana dashboards for P1/P9 |
| ☐ 54 | Security: authN/authZ, OAuth2/JWT, encryption, multi-tenancy isolation | Threat-model a multi-tenant API | 🔨 **P15**: JWT auth and per-tenant isolation in P1 |
| ☐ 55 | Deployment: containers, Kubernetes basics, CI/CD, canary/blue-green, feature flags | Plan a zero-downtime rollout with rollback | 🔨 **P15**: deploy to a local `kind` cluster, rolling update and rollback |
| ☐ 56 | **Review + Mock: Chat system (WhatsApp)** (compare against P13) | | |

## Phase 4: Case studies (Days 57-77)
For each: use the Learn block to read a reference solution plus a related blog or paper. Do the sketch as a **timed solo design first** (read the reference *after* attempting). On 🔨 days, trim the Learn block to 30 min and the design to 15 min.

### Week 9: Case studies I
| Day | Design | Hard parts to focus on | 🔨 Build |
|---|---|---|---|
| ☐ 57 | News feed / Twitter timeline | Fan-out on write vs read, celebrity problem | 🔨 **P16**: fan-out simulator, benchmark both strategies |
| ☐ 58 | Instagram / photo sharing | Media pipeline, CDN, feed ranking | |
| ☐ 59 | YouTube / Netflix | Transcoding, adaptive bitrate, CDN placement | |
| ☐ 60 | Google Drive / Dropbox | Chunking, dedup, sync conflicts | 🔨 **P16**: content-defined chunking, dedup store, sync diff |
| ☐ 61 | Web crawler | Politeness, dedup, frontier design | 🔨 **P16**: polite crawler against a local test site, URL frontier, dedup |
| ☐ 62 | Email / large-scale notification delivery | Throttling, retries, provider failover | |
| ☐ 63 | **Review + Mock: Twitter (full, timed)** | | |

### Week 10: Case studies II
| Day | Design | Hard parts to focus on | 🔨 Build |
|---|---|---|---|
| ☐ 64 | Uber / ride sharing | Geo index, matching, location updates at scale | 🔨 Extend P12 geohash into a driver-matching simulator |
| ☐ 65 | Payment system | Idempotency, ledger, reconciliation | 🔨 **P16**: double-entry ledger with idempotency keys, concurrency tests |
| ☐ 66 | Distributed message queue (design Kafka) | Storage layout, replication, consumer coordination | |
| ☐ 67 | Ticketmaster / hotel booking | Concurrency, holds, flash-sale traffic | 🔨 **P16**: seat-hold booking with expiry, load test with concurrent clients |
| ☐ 68 | Google Docs (collaborative editing) | OT vs CRDT | 🔨 **P16**: simple CRDT text (RGA) with merge tests |
| ☐ 69 | Metrics / logging platform | Ingestion, aggregation, retention tiers | 🔨 Reuse P15: add a downsampling and retention job |
| ☐ 70 | **Review + Mock: Uber (full, timed)** | | |

### Week 11: Case studies III and real-world work
| Day | Design | Hard parts to focus on | 🔨 Build |
|---|---|---|---|
| ☐ 71 | Distributed cache (Redis cluster) | Sharding, failover, hot keys | 🔨 Combine P4 + P5: replicated, sharded cache cluster with failover test |
| ☐ 72 | Job scheduler / workflow engine (Airflow-style) | Exactly-once-ish execution, DAGs, retries | 🔨 **P16**: DAG scheduler with retries and worker leases |
| ☐ 73 | Data pipeline / ETL / warehouse | Backfills, schema drift, lineage, idempotent loads | 🔨 **P16**: idempotent ETL with backfill and schema-drift test, synthetic data |
| ☐ 74 | **Your own system:** take the SDR export; design it for 100x scale | Batched ES queries, config-driven processing, multi-tenancy; write bottlenecks and a migration plan | Private notes only. **Nothing from this goes in the public repo.** |
| ☐ 75 | Ad click aggregation / leaderboard | Streaming aggregates, exactly-once counting, late data | 🔨 Extend P11: dedup + leaderboard with Redis sorted sets |
| ☐ 76 | Communication: write a short design doc/ADR from Day 74; practice explaining tradeoffs to a non-expert | Design-doc structure, alternatives considered, risks | Write an ADR for a P-project choice and publish it in the repo's `docs/` |
| ☐ 77 | **Review + Mock: your choice, with a stranger or with Claude** | | |

## Phase 5: Integration (Days 78-80)
| Day | Task |
|---|---|
| ☐ 78 | Two timed mocks (45 min each) on topics you've not yet been tested on. Log mistakes. 🔨 Polish P3 and P8: tests green, README with diagram and benchmarks. |
| ☐ 79 | **Capstone:** design a system end-to-end of your choice, write the full doc, and defend it in a mock Q&A. 🔨 Polish P4/P5 and P9 the same way. |
| ☐ 80 | Final review: one-page cheat sheet (numbers, patterns, tradeoffs), weak-area drill, retrospective, next-steps list. 🔨 Finish the root README, pin the repo on your GitHub profile, and tag `v1.0`. |

---

## Checkpoints
| After | You should be able to |
|---|---|
| Day 21 | Estimate capacity, pick a DB, explain caching, replication, and partitioning, with P1-P6 on GitHub |
| Day 42 | Explain consistency models, transactions, consensus, and Kafka internals without notes, with P7-P11 on GitHub |
| Day 56 | Design a service with resilience, observability, and security built in, with P12-P15 on GitHub |
| Day 77 | Complete a case study in 45 min, naming at least 3 tradeoffs and 2 failure modes, with the case-study builds on GitHub |

## Tracking
- Tick the box when a day is done.
- Keep an **Anki deck**: one card per concept, tradeoff, or number.
- Keep a **mistakes log**: after every mock, write what you missed and re-test it a week later.
- Check **GitHub** at each checkpoint: every project has a README, passing tests, and a tag.
