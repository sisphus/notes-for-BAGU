# Schema Ledger

Track reusable schemas, not every topic or detail.

| Schema name | Trigger situation | Compressed concepts | What the learner can do after acquiring it | Common failure signal | Status |
| --- | --- | --- | --- | --- | --- |
| 用能力边界定位 Redis | 被问「什么是 Redis / 为什么用 Redis / Redis vs Memcached」 | 内存数据库、缓存/队列/锁、数据类型、持久化、集群、Memcached 对比 | 先说 Redis 解决的系统问题，再说能力边界和典型场景 | 只说 Redis 很快，或把 Redis 说成纯缓存 | Forming |
| 按数据形状选择 Redis 类型 | 需要把业务需求映射到 String/Hash/List/Set/ZSet/Bitmap/HLL/GEO/Stream | 数据形状、操作需求、类型能力、典型场景 | 为排行榜、签到、UV、购物车、队列等场景选型并说明理由 | 对 HyperLogLog/基数统计缺少前置概念，或只背类型名称不会落到场景 | Stable |
| 用底层结构解释 Redis 类型边界 | 被追问数据类型如何实现、为什么快、什么时候变重 | SDS、quicklist、listpack、hash table、intset、skiplist、版本差异 | 解释类型背后的结构和小规模/大规模编码切换 | 把 Redis 7.0 仍说成 ziplist，或只说 ZSet 等于跳表 | Stable |
| 解释 Redis 快的主链路 | 被问 Redis 单线程为什么快、Redis 6 多线程是什么 | 内存、数据结构、事件循环、I/O 多路复用、后台线程、I/O 线程 | 给出完整性能因果链并说明单线程边界 | 认为 Redis 完全单线程或命令多线程执行 | Stable |
| 解释 AOF 日志、写回策略与重写 | 被问 AOF 如何恢复数据、appendfsync 怎么选、AOF 文件过大怎么办 | 先执行后写日志、AOF buffer、page cache、fsync、always/everysec/no、bgrewriteaof、AOF 重写缓冲区 | 解释 AOF 的丢失窗口、写入成本、恢复重放成本，以及重写如何不阻塞主进程且不丢增量命令 | 把 write 当成落盘，或忘记 AOF 重写缓冲区 | Stable |
| 解释 RDB 快照、bgsave 与 COW | 被问 RDB 如何生成、save/bgsave 区别、bgsave 期间能否写 | 全量快照、save 阻塞、bgsave 子进程、fork、COW、快照频率 | 解释 RDB 恢复快但可能丢快照间隔内数据，以及 COW 如何保持快照一致 | 以为 bgsave 不能写，或不知道 COW 的旧页/新页边界 | Stable |
| 用混合持久化折中 AOF 和 RDB | 被问为什么 Redis 4.0 引入混合持久化、混合 AOF 文件长什么样 | AOF 重写、RDB 全量、AOF 增量、恢复速度、兼容性 | 说明混合文件前半段 RDB、后半段 AOF，解释它如何兼顾恢复速度和较少丢数据 | 以为混合持久化是两个独立文件，或只说优点不说可读性/兼容性代价 | Stable |
| 用高可用层次回答 Redis 集群 | 被问主从、哨兵、Cluster、哈希槽 | 异步复制、故障转移、16384 slots、CRC16、分片 | 区分副本、自动切换和水平扩展各自解决的问题 | 认为主从同步强一致，或混淆哨兵和 Cluster | Stable |
| 用写入限制解释脑裂数据丢失 | 被问 Redis 脑裂为什么丢数据以及怎么缓解 | 网络分区、旧主写入、哨兵新主、全量同步、min-slaves 配置 | 复述脑裂数据丢失链路并解释写入限制的作用 | 只说旧主隔离期间收写入，不说明旧主恢复后降级并全量同步新主导致本地写入被覆盖 | Stable |
| 区分过期删除和内存淘汰 | 被问 TTL key 怎么删、内存满了删谁、LRU/LFU | expires dict、lazy/periodic deletion、maxmemory、volatile/allkeys、LRU/LFU | 根据触发条件区分删除策略和淘汰策略 | 把 TTL 过期和 maxmemory 淘汰混为一谈 | Stable |
| 用访问模式诊断缓存三大故障 | 被问雪崩、击穿、穿透区别和方案 | 多 key 同时失效、单热点 key 失效、不存在数据、锁、空值、布隆过滤器 | 快速分类缓存故障并给出对应缓解方案 | 把击穿和穿透混淆 | Stable |
| 用 Cache Aside 解释缓存一致性 | 被问缓存和数据库如何保证一致性 | 读回填、先更新 DB 再删缓存、两类并发时序、读多写少边界、锁与短 TTL 取舍 | 画出读写并发时序，说明常用策略、残余风险和适用负载 | 只背结论，不能解释旧值回填，或在写多场景忽略频繁失效造成的命中率下降 | Stable |
| 用责任归属和落库时机区分缓存更新策略 | 被问 Cache Aside、Read/Write Through、Write Back 有什么区别 | 应用或缓存组件负责 DB 交互、同步或异步落库、脏数据、性能与可靠性 | 根据责任归属和落库时机识别三种策略并说明 Redis 的适用边界 | 把 Read Through 说成应用回源，或混淆 Write Through 同步落库与 Write Back 异步落库 | Stable |
| 用 ZSet 的时间分数实现延迟队列 | 被问订单超时取消、延迟任务如何用 Redis 实现 | ZSet、执行时间 score、任务 member、按时间范围查询、轮询消费 | 设计最小延迟队列并说明生产与消费流程 | 把执行时间放进 member，或忽略 ZSet 按 score 排序和范围查询能力 | Stable |
| 用发现-风险-删除方式处理大 key | 被问大 key 怎么发现和删除 | value 大小、四类风险、bigkeys、SCAN、MEMORY USAGE、分批删除、UNLINK、lazyfree | 安全定位和处理大 key，避免阻塞主线程 | 把 key 名长当成大 key，建议直接 DEL，或在主节点高峰扫描 | Stable |
| 用网络往返解释 Pipeline 的收益与边界 | 被问 Pipeline 有什么用、与事务有什么区别 | 客户端批处理、网络 RTT、统一发送和读取响应、批次大小、非原子性 | 解释 Pipeline 为什么提速，并说明它不是服务端事务 | 认为 Pipeline 让单条命令执行更快，或保证整批命令原子执行 | Stable |
| 区分放弃命令队列与事务回滚 | 被问 Redis 事务是否支持回滚、DISCARD 有什么用 | MULTI、QUEUED、EXEC、DISCARD、运行时错误、部分成功 | 区分执行前清空队列与执行后撤销数据，并解释 Redis 事务的错误边界 | 把 DISCARD 当成回滚，或认为一条运行时错误会撤销其他成功命令 | Stable |
| 用原子命令和唯一值设计分布式锁 | 被问 Redis 分布式锁和 Redlock | SET NX PX unique_value、Lua 解锁、TTL、续约、异步复制风险、多数派 | 设计单节点锁并说明可靠性边界和 Redlock 条件 | SETNX 后单独 EXPIRE，或解锁直接 DEL | Stable |
| 用值形态与修改路径判断 Redis String 编码 | 被问 String 的 `int/embstr/raw` 如何选择、SDS 为什么优于 C 字符串 | 整数值、SDS、短字符串连续分配、长字符串分离分配、修改时编码转换、版本阈值 | 根据值形态判断常见编码，并解释 `embstr` 修改时为什么先转成 `raw` | 认为 String 只能存文本、死记统一长度阈值，或认为 `embstr` 可原地修改 | Stable |
| 用队列保障清单评估 List 消息队列 | 被问 List 能否做消息队列、如何保序去重和避免处理中的消息丢失 | LPUSH/RPOP、BRPOP、全局消息 ID、BRPOPLPUSH、备份 List、消费组缺失 | 按保序、阻塞读取、去重、处理可靠性四项分析 List 队列，并说明其消费组边界 | 只说 List 是 FIFO，或认为 RPOP 后消费者宕机仍能从原队列重读消息 | Stable |
| 用更新粒度选择 Hash 或 String JSON | 被问对象缓存应使用 String + JSON 还是 Hash | 整体读写、字段级更新、序列化、HSET/HGET、对象 ID 到字段值映射 | 根据读取和更新粒度拆分整体对象与高频变化字段 | 看到对象就一律用 Hash，或为了更新一个字段反复读写整个 JSON | Stable |
| 用目标集合关系选择 Set 运算方向 | 被问共同关注、合并成员或差异推荐应该使用哪个 Set 命令 | SINTER 交集、SUNION 并集、SDIFF 左集合减右集合、方向性 | 把业务关系翻译成集合表达式，并给出方向正确的 Redis 命令 | 能写出集合差方向和结果，但没有映射到 `SDIFF` 命令；或把“推荐 A 有而 B 没有”写成 `SDIFF B A` | Stable |
| 用集合规模评估 Set 聚合阻塞风险 | 准备对大 Set 执行交集、并集或差集 | SINTER/SUNION/SDIFF、元素规模、主线程执行、从库聚合、客户端聚合 | 在执行集合运算前评估阻塞风险，并选择主库之外的计算位置 | 只看命令语义，不评估集合规模，直接在主库对百万级 Set 聚合 | Stable |
| 区分 ZSet 的 score 范围与 member 字典序范围 | 被问 ZSet 应按分数区间还是按成员字典序查询 | ZRANGEBYSCORE、ZRANGEBYLEX、score 排序、member 字典序、同分前提、开闭区间 | 根据排序维度选择范围命令，并解释字典序查询为什么要求相关成员同分 | 用 `ZRANGEBYLEX` 查询不同 score 的全局字典序，或把 score 范围写成 member 范围 | Stable |
| 用稳定 offset 对齐多个 BitMap | 被问如何统计连续多天签到、共同在线或多个二值条件同时成立 | userId 到 offset 的稳定映射、每日 Bitmap、BITOP AND、目标 Bitmap、BITCOUNT | 设计跨多个 BitMap 的对位聚合，并说明为什么同一对象必须始终落在同一 offset | 每天重新编号用户，或做 AND 后忘记对结果执行 BITCOUNT | Stable |
| 用 GeoHash 到 ZSet score 的映射理解 GEO | 被问 GEO 如何实现附近的人、为什么底层使用 ZSet | 二维经纬度、区域划分、GeoHash 编码、一维 score、ZSet 有序范围查找 | 解释 GEO 如何把二维位置转换为可排序值，并据此完成附近查询 | 认为 GEO 有独立底层容器，或把原始经纬度直接当一个普通 score | Stable |
| 用消费进度归属区分 Stream 组内与组间分发 | 被问 Stream 同一消息能否被多个消费者或多个消费组读取 | XGROUP、XREADGROUP、`>`、组内共享进度、组间独立进度、负载分担 | 判断一条消息在同组和不同组中的可见性，并解释消费组的负载分担语义 | 能判断同组不能、不同组可以，却不能解释组内共享进度与组间独立进度 | Stable |
| 区分 Stream 消息存储与消费确认状态 | 被问消费者宕机后未处理消息在哪里、XPENDING/XACK 做什么 | Pending Entries List、已投递未确认、XPENDING、XACK、Stream 主体、XDEL | 追踪消息从投递到确认的状态，并说明确认与删除消息是两件事 | 认为 XACK 会从 Stream 主体删除消息，或不知道未确认消息可由 XPENDING 查询 | Stable |
| 用三段不丢与堆积介质评估 Redis Stream | 被问 Redis 能否当消息队列、与 Kafka/RabbitMQ 有什么差距 | 生产者重试、中间件持久化、消费者 ACK、AOF everysec、异步复制、内存堆积、裁剪、专业 MQ | 从生产者/中间件/消费者三段和堆积介质判断 Redis Stream 是否适用 | 因为有 XACK 就声称绝不丢消息，或只谈消费可靠性而忽略故障切换和内存积压 | Stable |
| 用对象层与编码层读懂 Redis 键值存储 | 被问 Redis key-value 如何存储、数据类型与底层结构有何区别 | redisDb、dict/dictht、dictEntry、redisObject type/encoding/ptr、对象与底层表示 | 沿存储链定位 key/value 对象，并区分逻辑类型与内部编码 | 把 String/Hash 与 SDS/hash table 放在同一层，或把全局 dict 与 Hash value 混淆 | Forming |
| 用 len 与 alloc 理解 SDS 能力 | 被问 Redis 为什么不直接用 C 字符串、SDS 为什么二进制安全 | len、alloc、buf、O(1) 求长度、二进制安全、扩容前容量检查 | 用字段级因果链解释 SDS 的长度、二进制和缓冲区安全能力 | 只说 SDS 更快，或因尾部仍有 `\0` 就认为不能保存内含 `\0` 的数据 | Stable |
| 用阈值式预分配折中 SDS 时间与空间 | SDS 追加数据需要扩容、被问为什么要多分配未使用空间 | newlen、1 MB 阈值、小数据翻倍、大数据加 1 MB、内存分配次数 | 根据字符串规模计算预分配，并解释为何不总是翻倍 | 把阈值误用于旧 len，或认为所有 SDS 都永远翻倍扩容 | Stable |
| 用分级头部与 packed 压缩 SDS 元数据 | 被问 SDS 为什么有多种 sdshdr、`packed` 解决什么问题 | sdshdr5/8/16/32/64、len/alloc 字段宽度、结构体对齐、padding、packed | 区分「字段本身过宽」与「编译器对齐填充」两种浪费 | 只说有多种 SDS 类型，却不知道它们按容量选择；或把 packed 误认为压缩 buf 数据 | Stable |
| 用链表元数据区分定位与遍历成本 | 被问 Redis 双向链表哪些操作是 O(1)、为什么中间查找仍慢 | listNode prev/next/value、list head/tail/len、已知节点、顺序遍历 | 根据是否存在直接指针/元数据，判断 O(1) 或 O(N) | 因为取 head/tail/len 是 O(1)，就误以为按位置查中间节点也是 O(1) | Stable |
| 用内存布局评估链表的通用性代价 | 需要在链表与连续容器之间取舍，或被问 Redis 为什么后来引入紧凑结构 | void* value、dup/free/match 回调、prev/next 节点头、非连续内存、CPU cache locality | 说明链表可保存通用值，同时评估节点头和缓存局部性成本 | 只看插删灵活，忽略每节点额外指针和分散内存对顺序扫描的影响 | Stable |
| 用 ziplist 头信息与 entry 字段导航连续内存 | 被问压缩列表如何定位尾节点、如何反向遍历、为什么中间查找是 O(N) | zlbytes、zltail、zllen、zlend、prevlen、encoding、data、连续内存 | 根据表头偏移与 entry 长度信息解释首尾定位和双向遍历 | 把 zllen 误当尾偏移，或不知道 prevlen 记录前一节点长度 | Stable |
| 用 254 字节阈值推演 ziplist 连锁更新 | ziplist 在头部插入大 entry、被问为什么小元素也可能触发多次重分配 | prevlen、<254 用 1 字节、>=254 用 5 字节、250-253 字节临界 entry、多米诺扩容 | 从新前驱长度变化逐步推出后续 entry 的结构扩容链 | 只说插入大数据导致整表扩容，但说不出 `prevlen` 1 -> 5 与 254 阈值 | Stable |
| 用冲突链解释哈希表的平均 O(1) 边界 | 两个 key 落入同一哈希桶、被问 Redis 如何解决哈希冲突 | dictht.table、哈希桶、dictEntry key/value/next、链式哈希、链长 | 追踪同桶 entry 的 `next` 链，并评估长链对查找复杂度的影响 | 认为链式哈希消除了冲突成本，或同桶新 key 会覆盖旧 key | Stable |
| 用双表状态切换解释 rehash | 哈希表负载增大需要扩容、被问 dict 为什么有 ht[2] | ht[0]、ht[1]、分配新表、数据迁移、释放旧表、新表转正、一次性迁移阻塞 | 按状态转换复述 rehash，并识别大表全量迁移的延迟风险 | 只说扩容为原来两倍，却说不出新旧表的交接过程 | Stable |
| 用请求分摊解释渐进式 rehash | 大哈希表需扩容但不能长时阻塞、迁移期间执行查找或插入 | 按桶迁移、每次请求分摊、查两表、新 key 只入 ht[1]、ht[0] 只减不增 | 推导迁移期间的读写路径，并解释为何最终能收敛到新表 | 把渐进 rehash 说成后台线程全量搬迁，或只查一张表/继续向 ht[0] 插入 | Stable |
| 用负载因子判断 rehash 时机 | 哈希表 used 增长、同时可能正在 bgsave 或 bgrewriteaof | load factor=used/size、>=1 条件触发、RDB/AOF 后台任务、>=5 强制触发 | 结合负载与后台任务状态判断是否 rehash | 只记住负载因子 >=1，忽略后台任务例外和 >=5 强制边界 | Stable |
| 用统一宽度解释 intset 整体升级 | 小规模纯整数 Set 加入超出当前编码范围的数 | encoding、length、contents[]、INT16/INT32/INT64、连续数组、整体转换、有序性 | 根据新元素宽度推导扩容与全体转换过程 | 认为 contents 中可同时混存 int16 和 int32，或只升级新元素 | Stable |
| 用延迟升级解释 intset 的内存收益与不降级边界 | 需要评估 intset 为什么不一开始就用 int64，或删除大元素后的编码状态 | 按需 INT16/32/64、小值节省空间、只升级不降级 | 说明小值阶段的空间收益，并预测删除大值后仍保持高宽度 | 认为删掉唯一大数后会自动从 int32/int64 降级 | Stable |
| 用双索引匹配 ZSet 单点与范围查询 | 被问 ZSet 为什么不只用跳表、ZSCORE 与 ZRANGEBYSCORE 分别如何实现 | dict member->score、skiplist score 有序、单点 O(1) 平均、范围起点 O(logN) 平均、双写一致性 | 根据查询形态选择索引，并说明写入双更新的必要性 | 把 ZSet 等同于只有跳表，或忽略 dict/skiplist 数据不一致的后果 | Stable |
| 用高层向右与越界下沉搜索跳表 | 在跳表中按 score/member 定位节点或范围起点 | 多层有序链表、header 最高层、level.forward、score 优先、member 同分比较、下沉 | 模拟跳表搜索路径，判断何时向右、何时下降 | 在当前层的下一节点已越过目标时仍向右，或从底层开始线性查找 | Stable |
| 用 span 累加计算跳表排名 | 被问 ZRANK 如何利用跳表、level.span 有什么作用 | level.forward、level.span、底层距离、搜索路径累加、排位 | 沿查找路径累加 span 得到目标排名，并区分导航指针与排名元数据 | 把 span 当成决定向右/下沉的条件，或认为它只记物理字节距离 | Stable |
| 用随机层高构造跳表稀疏索引 | 插入或删除跳表节点、被问为什么不严格维持层间比例 | 严格 2:1 的维护成本、逐层晋升概率 25%、高层节点概率递减、版本相关最大层高 | 解释随机层高如何以低维护成本形成稀疏高层索引 | 认为 Redis 每次插入后都会重新平衡到严格 2:1，或认为各层节点一样多 | Stable |
| 用操作形态比较跳表与平衡树 | 被问 ZSet 为什么用跳表而不是 AVL/红黑树 | 指针期望开销、底层链表范围遍历、局部指针插删、树旋转/平衡、span 扩展 ZRANK | 从内存、范围操作和实现维护三方面说明 Redis 的选择 | 只答查找都是 O(logN)，不会比较范围遍历和更新维护成本 | Stable |
| 用分段紧凑布局理解 quicklist 折中 | 被问 Redis 3.2 List 为什么从链表/ziplist 演进为 quicklist | 双向链表、quicklistNode、节点内 ziplist、节点大小/元素数限制、节点头摊薄、连锁更新影响范围 | 从每元素指针开销和单一大 ziplist 风险两面解释分段紧凑布局 | 只背 quicklist=链表+ziplist，说不出每块大小为何是关键 | Stable |
| 用去除前项长度依赖解释 listpack | 被问 listpack 如何替代 ziplist、为什么没有连锁更新 | 连续内存、变长编码、entry encoding/data/len、当前项长度、无 prevlen、反向解码 backlen | 从字段依赖图解释新增/扩大 entry 为什么不传播到后续 entry | 认为 listpack 没有长度字段，或没有 prevlen 就不能反向遍历 | Stable |
| 用时间空间与最坏时延解释结构演进 | 需要综合回答 Redis 为什么从链表/ziplist 演进到 quicklist/listpack | 链表指针头与 cache locality、ziplist 紧凑性与连锁更新、quicklist 分段限界、listpack 去除前项长度依赖 | 用「保留什么优点、修复什么边界」复述数据结构演进 | 只背版本和映射表，无法说出空间节省与最坏更新成本的因果链 | Forming |

## Status Values

- Not started: named but not taught yet.
- Forming: introduced and practiced, but not stable.
- Stable: learner can explain and apply it in near-transfer tasks.
- Needs review: learner missed it, confused its boundary, or failed to transfer it.
