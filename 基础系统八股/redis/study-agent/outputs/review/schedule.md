# Review Schedule

Use this file for short, targeted spaced-review prompts.

## Template

```markdown
## YYYY-MM-DD

- Topic: <topic>
- Review timing: <same day / next day / 3 days / 1 week>
- Prompt: <one short recall, boundary, transfer, or diagnosis question>
- Target: <schema or weak boundary being tested>
- Result: Pending
- Next review: TBD
```

## Default Intervals

- New weak concept: same day.
- Missed again: next day.
- Correct after repair: 3 days.
- Stable: 1 week.

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 如果面试官问「为什么用 Redis 做 MySQL 缓存，而不是只访问 MySQL 或用 Memcached？」请用性能、并发、能力边界三点回答。
- Target: 用能力边界定位 Redis
- Result: Correct recall on 2026-07-09; needs near-transfer check before Stable.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 统计一个页面的 PV 和 UV 有什么区别？如果只需要百万级 UV 的近似值，为什么 HyperLogLog 比 Set 更省内存？
- Target: HyperLogLog 的基数统计前置概念
- Result: Correct PV/UV repair, HyperLogLog-vs-Set memory boundary, and member-list boundary on 2026-07-09.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 用一句话分别解释 listpack 和跳表：哪一个更像紧凑小数组，哪一个更像带多层索引的有序链表？
- Target: listpack vs skiplist prerequisite
- Result: Correct on 2026-07-09; learner linked listpack to small compact storage and skiplist to large ordered ZSet with faster lookup/insert.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: Hash、Set、ZSet 在小数据和大数据时分别倾向使用什么底层结构？请说出小数据省内存、大数据提效率的取舍。
- Target: Redis compact encoding vs scalable structure
- Result: Correct on 2026-07-09; learner completed String-SDS, List-quicklist, Hash-listpack/hash table, Set-intset/hash table, ZSet-listpack/skiplist table.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: Redis 常说的单线程具体指哪条链路？请举一个由后台线程处理的耗时任务。
- Target: Redis single-thread boundary
- Result: Correct on 2026-07-09; learner identified request receive/parse/execute/respond as main-thread chain and named close file, AOF fsync, lazyfree/UNLINK as background work.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: Redis 6.0 的 I/O 多线程并发处理的是什么？命令执行是否由多个线程并发执行？
- Target: Redis 6.0 I/O-thread vs command-execution boundary
- Result: Correct after repair on 2026-07-09; learner stated I/O threads concurrently process socket reads/writes while command execution remains on the main thread.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: 3 days
- Prompt: 用一条因果链回答 Redis 为什么快，必须包含内存读写、高效数据结构、单线程主链路、I/O 多路复用、后台线程、Redis 6.0 I/O 多线程边界。
- Target: Redis speed causal chain
- Result: Correct integrated answer on 2026-07-09.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: AOF 和 RDB 各自记录什么？哪个恢复更快，哪个通常丢数据更少？
- Target: AOF vs RDB persistence tradeoff
- Result: Correct on 2026-07-09; learner identified AOF as write-command log, RDB as memory snapshot, RDB as faster recovery, AOF as lower data-loss risk.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: AOF everysec 最坏大约会丢多久的数据？为什么性能通常比 always 更好？
- Target: AOF appendfsync everysec tradeoff
- Result: Correct on 2026-07-09; learner identified about 1 second data-loss window and fewer synchronous fsync calls than always.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: 3 days
- Prompt: 执行 bgsave 生成 RDB 时，Redis 主线程还能继续写吗？COW 如何让子进程看到 fork 时刻的数据？
- Target: RDB bgsave and COW boundary
- Result: Correct on 2026-07-09; learner stated main thread can continue writes and COW keeps child process on snapshot-time data.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: 3 days
- Prompt: 混合持久化生成的新 AOF 文件前半部分和后半部分分别是什么格式？它分别借用了 RDB 和 AOF 的哪个优点？
- Target: Hybrid persistence file structure
- Result: Correct on 2026-07-09; learner stated front half is RDB, back half is AOF, using RDB fast recovery and AOF lower data-loss risk.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 主从复制、哨兵模式、Redis Cluster 分别主要解决什么问题？请用副本、故障转移、水平扩展对应说明。
- Target: Redis high-availability layer mapping
- Result: Correct on 2026-07-09; learner mapped master-replica to replicas/read scaling, Sentinel to failover, Cluster to slot-based horizontal scaling.
- Next review: 2026-07-12

## 2026-07-09

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: Redis 主从复制是同步还是异步？主库写完后是否等待所有从库执行完再返回？这能否保证强一致？
- Target: Redis async replication boundary
- Result: Correct on 2026-07-09; learner stated master returns after local execution and strong consistency is not guaranteed.
- Next review: 2026-07-12

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 脑裂导致数据丢失的后半段是什么？请说出哨兵选新主后，旧主网络恢复时为什么会丢掉隔离期间写入。
- Target: Redis brain split data-loss causal chain
- Result: Correct after repair on 2026-07-10; learner stated old master is demoted to replica, then full-syncs from new master, clearing/overwriting isolated writes.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: 3 days
- Prompt: `min-slaves-to-write` 和 `min-slaves-max-lag` 限制主库在什么复制健康条件下才能继续写？为什么这能减少脑裂数据丢失？
- Target: Redis brain split write-limiting prevention
- Result: Correct on 2026-07-10; learner stated master must have enough low-lag replicas before accepting writes, reducing isolated old-master writes during split brain.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: Redis 过期删除和内存淘汰分别由什么触发？
- Target: Expiration deletion vs memory eviction trigger
- Result: Correct on 2026-07-10; learner stated expiration handles TTL keys and eviction handles memory reaching maxmemory.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 惰性删除和定期删除分别是什么？为什么 Redis 要把它们配合使用？
- Target: Lazy deletion plus periodic deletion
- Result: Correct on 2026-07-10; learner stated lazy deletion checks on access, periodic deletion samples TTL keys, and the combination balances CPU with memory recovery.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 内存淘汰策略里，volatile-* 和 allkeys-* 的淘汰范围分别是什么？没有 TTL 的 key 会不会被 volatile-lru 淘汰？
- Target: Volatile vs allkeys eviction scope
- Result: Correct on 2026-07-10; learner stated volatile only evicts TTL keys, allkeys evicts from all keys, and volatile-lru will not evict a key without TTL.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: 3 days
- Prompt: LRU 和 LFU 分别按什么标准淘汰 key？为什么 LFU 更能缓解偶发访问造成的缓存污染？
- Target: LRU vs LFU eviction boundary
- Result: Correct on 2026-07-10; learner distinguished recency from frequency and explained one-off recent access is protected by LRU but still low-frequency under LFU.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 多 key 同时失效、单个热点 key 失效、数据根本不存在分别对应缓存雪崩、击穿、穿透中的哪一个？
- Target: Cache avalanche/breakdown/penetration classification
- Result: Correct on 2026-07-10; learner mapped multi-key expiry to avalanche, single hot-key expiry to breakdown, and nonexistent data to penetration.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: 3 days
- Prompt: 分别给缓存雪崩、缓存击穿、缓存穿透说一个典型解决方案。
- Target: Cache avalanche/breakdown/penetration mitigation mapping
- Result: Correct on 2026-07-10; learner mapped random TTL to avalanche, mutex rebuild to breakdown, and Bloom filter to penetration.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 为什么 Cache Aside 推荐先更新数据库再删除缓存？它为什么仍不是强一致方案，又为什么更适合读多写少？
- Target: Cache Aside concurrency and applicability boundaries
- Result: Stable on 2026-07-10; learner explained both stale-value timelines, the read-heavy boundary, and correctly chose a distributed lock when preventing concurrent cache overwrite matters more than write throughput.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: Cache Aside、Read/Write Through、Write Back 分别由谁访问数据库？数据库更新是同步还是异步？
- Target: Cache update strategy ownership and persistence timing
- Result: Stable on 2026-07-10; learner identified cache-component ownership in Read Through, distinguished synchronous Write Through from asynchronous Write Back, and rejected Write Back for payment balances because acknowledged dirty data can be lost before persistence.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 用 ZSet 实现订单超时取消时，score 和 member 分别保存什么？消费者如何查询到期任务？
- Target: ZSet delayed queue mapping and consume flow
- Result: Stable on 2026-07-10; learner mapped execution timestamp to score, task ID/content to member, and correctly selected all tasks with score no later than the current time.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 大 key 是 key 名很长还是 value 很大？它会造成哪些阻塞、流量或倾斜风险？
- Target: Big key definition and risk chain
- Result: Stable on 2026-07-10; learner completed definition, four risks, discovery-method selection, synchronous `DEL` blocking, `HSCAN + HDEL` batching, and `UNLINK` mapping-removal/background-free reasoning.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: Pipeline 为什么能提高多命令交互性能？它是否会让单条命令执行更快，是否提供事务原子性？
- Target: Pipeline network-RTT and non-transaction boundary
- Result: Stable on 2026-07-10; learner explained reduced network waits and correctly rejected automatic rollback because Pipeline has no transaction constraint.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: `DISCARD` 为什么不是事务回滚？`EXEC` 后一条命令运行时报错时，其他正确命令会怎样？
- Target: Redis transaction queue-discard vs runtime rollback boundary
- Result: Stable on 2026-07-10; learner distinguished queue discard from rollback and correctly kept a successful `SET` after a later runtime error.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 为什么分布式锁要用 `SET key unique_value NX PX ttl` 一次完成，而不能把抢锁和设置过期时间拆开？
- Target: Atomic distributed-lock acquisition with TTL
- Result: Stable on 2026-07-10; learner covered local-vs-distributed scope, atomic `SET NX PX`, unique token, Lua compare-and-delete, TTL renewal, asynchronous failover risk, and Redlock majority/time conditions.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: AOF 重写期间，普通 AOF 缓冲区和 AOF 重写缓冲区分别保护哪个文件，为什么两个都要写？
- Target: AOF rewrite dual-buffer ownership
- Result: Stable on 2026-07-10; learner stated that the ordinary AOF buffer keeps the old file valid while the rewrite buffer appends rewrite-period increments to the new file.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: Redlock 为什么使用多个彼此独立的 Redis 主节点？5 个节点时至少要拿到几个节点的锁？
- Target: Redlock multiple authorities and majority prerequisite
- Result: Correct after prerequisite repair on 2026-07-10; learner rejected 2/5 because it is not a majority.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: 为什么多个服务实例各自的本地 mutex 不能互相约束？Redis 分布式锁把共同锁状态放在哪里？
- Target: Local lock vs distributed lock scope
- Result: Correct after prerequisite repair on 2026-07-10; learner stated that separate instances cannot see who acquired another instance's local lock.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见面试题
- Review timing: same day
- Prompt: `UNLINK` 为什么能让 key 立即不可访问，同时把实际内存释放留给后台线程？
- Target: key-value mapping removal vs value-memory reclamation
- Result: Correct after prerequisite repair on 2026-07-10; learner separated mapping removal from background memory reclamation.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: `123`、短字符串 `hello`、长字符串通常对应哪种 String 编码？为什么修改 `embstr` 时会先转为 `raw`？
- Target: 用值形态与修改路径判断 Redis String 编码
- Result: Correct on 2026-07-10; learner mapped the three encodings and explained the modification-triggered conversion to `raw`.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: 消费者从 List 取出消息后、处理完成前宕机，为什么普通 `RPOP` 会丢消息？`BRPOPLPUSH` 如何利用备份 List 修复这个窗口？
- Target: 用队列保障清单评估 List 消息队列
- Result: Correct on 2026-07-10; learner explained the removal-before-processing window and the atomic transfer to a backup List until successful processing.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: 一个对象通常整体读取，但其中一个字段更新很频繁；整体对象和高频字段应如何在 String + JSON 与 Hash 之间选择？
- Target: 用更新粒度选择 Hash 或 String JSON
- Result: Correct after repair on 2026-07-10; learner mapped the whole-read object to String + JSON and the independently updated hot field to Hash.
- Next review: 2026-07-13

## 2026-07-10

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: `A={5,6,7,8,9}`、`B={7,8,9,10,11}`，要找 A 有而 B 没有的成员，应使用什么命令，结果是什么？
- Target: 用目标集合关系选择 Set 运算方向
- Result: Correct after repair on 2026-07-11; learner supplied `SDIFF uid:1 uid:2` with the correct argument order.
- Next review: 2026-07-14

## 2026-07-11

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: 为什么不应直接在 Redis 主库对两个百万级 Set 执行 `SINTER`？可以把聚合放到哪两个位置？
- Target: 用集合规模评估 Set 聚合阻塞风险
- Result: Correct on 2026-07-11; learner linked large aggregation to main-thread blocking and named replica-side and client-side computation.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: 所有 member 的 score 都是 0 时，要查询 member 的 `[132, 133)` 字典序区间，应使用哪个 ZSet 命令？为什么同分是重要前提？
- Target: 区分 ZSet 的 score 范围与 member 字典序范围
- Result: Correct on 2026-07-11; learner selected `ZRANGEBYLEX` and explained the equal-score prerequisite through ZSet's score-first ordering.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: 统计连续 3 天签到人数时，为什么每天必须用相同 userId 对应相同 offset？需要依次执行哪两个 BitMap 命令？
- Target: 用稳定 offset 对齐多个 BitMap
- Result: Correct on 2026-07-11; learner explained positional alignment and supplied `BITOP AND -> BITCOUNT`.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: 请补全 GEO 的实现链：二维经纬度经过什么编码成为 ZSet 的什么字段，并利用哪种能力搜索附近位置？
- Target: 用 GeoHash 到 ZSet score 的映射理解 GEO
- Result: Correct on 2026-07-11; learner completed `coordinates -> GeoHash -> ZSet score -> ordered-range search`.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: 一条新消息被 group1 的一个消费者读取后，同组其他消费者还能否把它当作新消息读取？group2 能否读取同一条消息？
- Target: 用消费进度归属区分 Stream 组内与组间分发
- Result: Correct after repair on 2026-07-11; learner explained shared progress within one group and independent progress across groups.
- Next review: 2026-07-14

## 2026-07-11

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: 消费者读取消息后、确认前宕机，消息记录在哪里，如何查询？`XACK` 会改变什么，是否删除 Stream 主体中的消息？
- Target: 区分 Stream 消息存储与消费确认状态
- Result: Correct on 2026-07-11; learner identified Pending List, `XPENDING`, and the boundary that `XACK` removes pending state without deleting the Stream message.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: same day
- Prompt: 为什么 Pending List 与 `XACK` 不能保证 Redis Stream 端到端绝不丢消息？指出两个中间件丢失窗口和一个堆积风险。
- Target: 用三段不丢与堆积介质评估 Redis Stream
- Result: Correct on 2026-07-11; learner identified AOF `everysec`, asynchronous-replication failover, and in-memory backlog/OOM risk.
- Next review: 2026-07-18

## 2026-07-18

- Topic: Redis 常见数据类型、命令与应用场景
- Review timing: 1 week
- Prompt: 为内容平台的对象缓存、独立计数、点赞去重、排行榜、连续签到、附近车辆和可靠审核队列选择 Redis 类型；最后说明什么条件下审核队列应改用专业 MQ。
- Target: command.md integrated type-selection and boundary transfer
- Result: Pending
- Next review: TBD

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: `redisObject` 的 `type`、`encoding`、`ptr` 分别说明什么？请用一个 Hash value 举例。
- Target: 用对象层与编码层读懂 Redis 键值存储
- Result: Correct after repair on 2026-07-11; learner mapped key -> String object, value -> Hash object, and encoding -> underlying data structure/internal representation.
- Next review: 2026-07-14

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: SDS 的 `len` 和 `alloc` 分别解决 C 字符串的什么问题？为什么 SDS 能保存内含 `\0` 的二进制数据？
- Target: 用 len 与 alloc 理解 SDS 能力
- Result: Stable on 2026-07-11; learner used `len` as the binary-data boundary and applied `alloc - len` plus expand-before-write to prevent overflow.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 扩容后所需 `newlen` 小于 1 MB 和达到 1 MB 时，SDS 分别怎样预分配？为什么大数据不继续翻倍？
- Target: 用阈值式预分配折中 SDS 时间与空间
- Result: Stable on 2026-07-11; learner correctly calculated the doubling and plus-1-MB branches.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: SDS 为什么设计多种 sdshdr 头部类型？`__attribute__((packed))` 又另外减少了什么空间？
- Target: 用分级头部与 packed 压缩 SDS 元数据
- Result: Stable on 2026-07-11 after refinement; learner identified oversized-field waste and understood tight packing, with alignment padding named explicitly in feedback.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 为什么取双向链表的 head、tail、len 是 O(1)，而按位置找中间节点是 O(N)？
- Target: 用链表元数据区分定位与遍历成本
- Result: Stable after completion on 2026-07-11; learner named the direct `tail` pointer and sequential `next/prev` traversal.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 为什么链表的 `void*` 与回调函数很通用，但它存储大量小值并顺序扫描时通常不如连续数组？
- Target: 用内存布局评估链表的通用性代价
- Result: Stable after completion on 2026-07-11; learner supplied both scattered-node cache cost and per-node header overhead.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: ziplist 的 `zltail`、`zllen`、entry `prevlen`分别记录什么？哪个字段支持从后向前遍历？
- Target: 用 ziplist 头信息与 entry 字段导航连续内存
- Result: Stable on 2026-07-11; learner used `prevlen` for reverse navigation, chose `zltail` for tail positioning, and explained O(N) middle traversal.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 为什么在一串 250-253 字节的 ziplist entry 前插入 `>=254` 字节的 entry，会使后续 `prevlen` 从 1 字节扩为 5 字节并连锁传播？
- Target: 用 254 字节阈值推演 ziplist 连锁更新
- Result: Stable on 2026-07-11; learner derived e1 253 -> 257, e2 propagation, and repeated reallocation/data-movement cost.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 两个 key 落到同一哈希桶时，`dictEntry.next` 如何保留它们？为什么链过长会让查找向 O(N) 恶化？
- Target: 用冲突链解释哈希表的平均 O(1) 边界
- Result: Stable on 2026-07-11; learner explained `next` chaining and long-chain linear traversal.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: dict 为什么准备 ht[0] 和 ht[1]？请按「分配新表 -> 迁移 -> 释放/转正」说出 rehash 过程。
- Target: 用双表状态切换解释 rehash
- Result: Stable on 2026-07-11; learner gave the two-table state chain and one-shot migration blocking risk.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 渐进 rehash 期间查找为什么要先 ht[0] 后 ht[1]？为什么新 key 只写 ht[1]？
- Target: 用请求分摊解释渐进式 rehash
- Result: Stable on 2026-07-11; learner explained two-table lookup and ht[1]-only insertion until ht[0] empties.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 负载因子为 2 和 5 且都正在 bgsave 时，按文章规则哪个会 rehash？
- Target: 用负载因子判断 rehash 时机
- Result: Stable on 2026-07-11; learner deferred load factor 2 during bgsave and forced rehash at load factor 5.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: int16 intset 加入 65535 时，为什么整个 contents 都要升级为 int32，而不是只让新元素用 int32？
- Target: 用统一宽度解释 intset 整体升级
- Result: Stable on 2026-07-11; learner connected uniform encoding to fixed-stride contiguous-array addressing.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: intset 从 int16 升级到 int32 后删除那个大数，会降级吗？延迟升级之前节省了什么？
- Target: 用延迟升级解释 intset 的内存收益与不降级边界
- Result: Stable on 2026-07-11; learner stated no downgrade and the pre-upgrade memory benefit.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: ZSet 中 dict 和 skiplist 分别主要支持哪类查询？为什么写入要更新两者？
- Target: 用双索引匹配 ZSet 单点与范围查询
- Result: Stable on 2026-07-11; learner mapped dict to ZSCORE, skiplist to score ranges, and explained dual-write consistency.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 跳表从最高层搜索时，什么情况向右走，什么情况下沉一层？
- Target: 用高层向右与越界下沉搜索跳表
- Result: Stable on 2026-07-11; learner handled both score overshoot and equal-score member lexicographic overshoot.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 跳表搜索目标节点时，为什么沿路径累加每次跳跃的 span 就能得到排名？
- Target: 用 span 累加计算跳表排名
- Result: Stable on 2026-07-11; learner summed spans to rank and separated span from navigation.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: Redis 跳表为什么不在每次插删后强制相邻层 2:1，而用 25% 概率逐层晋升？
- Target: 用随机层高构造跳表稀疏索引
- Result: Stable on 2026-07-11; learner linked probabilistic sparsity to average O(logN) and avoidance of global rebalancing.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 跳表相比平衡树，为什么更方便做 ZRANGE 范围遍历和频繁插删？
- Target: 用操作形态比较跳表与平衡树
- Result: Stable on 2026-07-11; learner explained L0 range traversal and tree rotation/rebalancing costs.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: quicklist 的每个链表节点为什么保存一小段 ziplist，而不是单个元素或全部元素？
- Target: 用分段紧凑布局理解 quicklist 折中
- Result: Stable on 2026-07-11; learner explained header amortization and bounded contiguous-memory/cascade impact.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: listpack 的 entry 为什么使用当前项 `len` 而不使用后一项的 `prevlen`？这如何消除连锁更新？
- Target: 用去除前项长度依赖解释 listpack
- Result: Stable on 2026-07-11; learner explained both no-cascade dependency removal and reverse traversal via prior entry-len decoding.
- Next review: 2026-07-18

## 2026-07-11

- Topic: Redis 数据结构
- Review timing: same day
- Prompt: 用「链表 -> ziplist -> quicklist -> listpack」说明 Redis 如何在节点开销、连续内存、连锁更新之间演进。
- Target: 用时间空间与最坏时延解释结构演进
- Result: Pending
- Next review: TBD
