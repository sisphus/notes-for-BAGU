# Review Schedule

## 2026-07-09

- Topic: MySQL 架构总览 - Server 层 vs InnoDB 层。
  - Reason: 新在线章节《面试官：MySQL 的架构是怎么样的？》已开始；第一步需要稳定“Server 层处理 SQL/执行计划，InnoDB 层负责页、B+Tree、缓存和日志”的边界。
  - Next review: 2026-07-12.
  - Prompt: `select * from user where id = 7` 进入 MySQL 后，Server 层先做哪两三件事？真正沿 B+Tree 找数据页/记录的是哪一层？binlog 和 redo log 分别在哪一层？
  - Status: Passed in MCQ review on 2026-07-09. User answered the Server/InnoDB boundary correctly and stated this architecture-layering point is fully mastered; deprioritize in near-term MCQ rotation.

- Topic: MySQL Buffer Pool - 页级缓存单位。
  - Reason: 新章节 `buffer_pool.md` 已开始；第一步需要稳定 Buffer Pool 缓存的是页，不是单条记录，以及 B+Tree 先定位页、再在页内定位行。
  - Next review: 2026-07-12.
  - Prompt: 为什么查询 `id=10` 只返回一行，InnoDB 仍可能把整个 16KB 页加载到 Buffer Pool？请同时说明索引定位页和页内定位记录的关系。
  - Status: First check passed. User correctly stated that InnoDB interacts with disk by pages, B+Tree locates the page containing the target record, and after the page enters Buffer Pool InnoDB uses in-page structures to locate the row.

- Topic: MySQL Buffer Pool - 控制块和三条链表。
  - Reason: 需要稳定 Free/Flush/LRU 不是互斥页类型，而是通过控制块管理缓存页的三种链表视角，尤其脏页同时在 LRU 和 Flush 中。
  - Next review: 2026-07-12.
  - Prompt: Free List、Flush List、LRU List 分别解决什么管理问题？为什么脏页会同时在 LRU List 和 Flush List？
  - Status: First check passed. User correctly mapped Free List to free page allocation, Flush List to dirty-page flushing, and LRU List to used-page hot/cold eviction; precision retained that dirty pages are in both LRU and Flush.

- Topic: MySQL Buffer Pool - 改良 LRU 与 Buffer Pool 污染。
  - Reason: 需要稳定结果集大小不是关键，扫描页数量才是污染风险；`innodb_old_blocks_time` 是为了防止一次性扫描页立刻挤进 young 区域淘汰热点页。
  - Next review: 2026-07-12.
  - Prompt: 为什么一个全表扫描最终只返回 3 行，仍可能造成 Buffer Pool 污染？`innodb_old_blocks_time` 为什么能降低这个风险？请注意说明“后续访问超过阈值才进 young”。
  - Status: First check passed. User correctly identified many scanned pages, one-time accesses, young-region hot-page eviction, and the old-region time threshold; precision retained that passive waiting alone is not enough, a later access after the threshold promotes the page.

- Topic: MySQL Buffer Pool - 脏页延迟落盘与 WAL。
  - Reason: 需要稳定 update 先改 Buffer Pool 脏页不是偷懒，而是为了避免每次同步随机写数据页；redo log + WAL 负责保护脏页未刷盘时的 crash-safe。
  - Next review: 2026-07-12.
  - Prompt: 为什么 update 不直接每次同步刷数据页？如果脏页未刷盘时宕机有什么风险？redo log + WAL 如何兜底？
  - Status: First check passed after narrow repair. User correctly explained dirty-page loss risk, redo log + WAL recovery, and then repaired the missing performance reason: synchronous random data-page writes are expensive, so InnoDB first modifies memory, protects it with redo log, and later flushes dirty pages in batches.

- Topic: MySQL how_update - 组提交 flush/sync/commit。
  - Reason: 需要稳定组提交不是取消刷盘，而是把多个事务组织成队列并在 sync 阶段合并 binlog fsync，降低双 1 提交的 I/O 成本。
  - Next review: 2026-07-12.
  - Prompt: 组提交的 flush、sync、commit 三个阶段分别做什么？哪个阶段把多个事务的 binlog 合并成一次 fsync？为什么不能说 flush 阶段已经持久化到磁盘？
  - Status: First check passed. User correctly placed binlog write-without-fsync in flush, merged binlog fsync in sync, and InnoDB commit/redo commit marking in commit.

## 2026-07-08

- Topic: MySQL how_update - 三类日志的安全目标边界。
  - Reason: 新章节 `how_update.md` 已开始；第一步需要稳定 undo log、redo log、binlog 三者不是同一种“恢复日志”，而是分别服务回滚/MVCC、crash-safe、备份恢复/主从复制。
  - Next review: 2026-07-11.
  - Prompt: 一条 `update` 里，事务没提交中途失败、事务已提交但脏页未刷盘时宕机、从库/备份要知道变更历史，这三种风险分别靠什么日志解决？每个日志属于哪一层？
  - Status: First check passed. User correctly mapped rollback to undo log, committed dirty-page crash recovery to redo log + WAL, and backup/replication history to Server-layer binlog.

- Topic: MySQL how_update - undo log 回滚动作与 MVCC 版本链。
  - Reason: 需要稳定 undo log 的双重角色：反向操作用于回滚，版本链用于快照读找旧版本，避免只把 undo log 背成“回滚日志”。
  - Next review: 2026-07-11.
  - Prompt: `name` 从 `'jay'` 改成 `'xiaolin'` 后，undo log 对回滚和普通快照读各有什么用？请说出 Read View、`roll_pointer`、undo log 版本链三者的关系。
  - Status: First check passed after narrow repair. User stated rollback uses reverse-operation information and repaired MVCC chain as Read View deems current version invisible, then follows `roll_pointer` to the undo log version chain for an older version.

- Topic: MySQL how_update - Buffer Pool 脏页与 WAL。
  - Reason: 需要稳定 redo log 的必要性：Buffer Pool 让 update 先改内存脏页，脏页延迟落盘提升性能，但必须用 redo log 保护崩溃恢复。
  - Next review: 2026-07-11.
  - Prompt: 为什么 update 不直接同步写数据页到磁盘，而是先改 Buffer Pool 里的页并标记脏页？这样有什么风险？redo log 和 WAL 怎么消除这个风险？
  - Status: First check passed. User explained random I/O cost, dirty-page loss risk, redo log physical page modification record, and WAL as log first then data page later.

- Topic: MySQL how_update - redo log buffer 和刷盘参数 0/1/2。
  - Reason: 需要稳定 redo log buffer、OS Page Cache、磁盘三层边界，并能解释 `innodb_flush_log_at_trx_commit` 的安全性/性能取舍。
  - Next review: 2026-07-11.
  - Prompt: `innodb_flush_log_at_trx_commit = 0/1/2` 时，事务提交后的 redo log 分别停在哪一层？MySQL 进程崩溃、OS 崩溃或断电时，0 和 2 的风险为什么不同？
  - Status: First check passed. User mapped 0/1/2 to redo log buffer, disk, and OS Page Cache, and identified 1 as safest and 0 as fastest; precision note retained that 2 is write to Page Cache, not fsync to disk.

- Topic: MySQL how_update - redo log 与 binlog 的恢复边界。
  - Reason: 需要稳定“redo log 不是历史恢复账本，binlog 才是归档/复制日志”这个边界，避免把 crash-safe 和误删恢复混成一类恢复。
  - Next review: 2026-07-11.
  - Prompt: 误删整个库为什么不能用 redo log 恢复，而要用 binlog？请同时说明 redo log 的循环写/crash-safe 和 binlog 的追加写/全量历史。
  - Status: First check passed with precision note. User explained binlog as Server-layer append log preserving full change history for backup recovery, replication, and point-in-time replay; precision retained that redo log is circular InnoDB physical log for crash-safe, not a history ledger.

- Topic: MySQL how_update - binlog STATEMENT vs ROW。
  - Reason: 需要稳定 binlog 格式边界：STATEMENT 重放 SQL，ROW 重放主库最终行结果，避免把二者都理解成“记录更新”。
  - Next review: 2026-07-11.
  - Prompt: `update user set token = uuid() where id = 1` 在 STATEMENT 和 ROW binlog 下分别记录什么？为什么 ROW 能避免非确定性函数导致的主从不一致？它的代价是什么？
  - Status: First check passed. User explained nondeterministic functions can produce different results when SQL is replayed under STATEMENT; ROW records the primary's final row value and improves consistency, at the cost of larger logs for many-row updates.

- Topic: MySQL how_update - 主从复制三阶段。
  - Reason: 需要稳定 binlog 复制链路：主库写 binlog，本地提交；从库 I/O 线程拉取到 relay log；从库回放线程执行 relay log 中的变更。
  - Next review: 2026-07-11.
  - Prompt: 请用三步说明 MySQL 主从复制：主库先做什么？从库 I/O 线程连接谁、把什么写到哪里？从库回放线程再做什么？默认异步复制为什么可能在主库宕机时丢最新数据？
  - Status: First check passed. User correctly chained primary transaction/binlog/local commit, replica I/O thread pulling binlog into relay log, and replica replay thread applying relay log changes.

- Topic: MySQL how_update - binlog cache 与 sync_binlog write/fsync 边界。
  - Reason: 需要稳定 binlog cache、OS Page Cache、磁盘三层，以及 `sync_binlog=0/1/N` 的安全性/性能取舍。
  - Next review: 2026-07-11.
  - Prompt: `sync_binlog=0/1/N` 时事务提交后分别如何 write/fsync？binlog 的 write 和 fsync 分别到哪一层，为什么 write 不等于已经落盘？
  - Status: First check passed after narrow repair. User mapped 0/1/N correctly and repaired write as Page Cache only, fsync as disk persistence.

- Topic: MySQL how_update - 一条 UPDATE 的完整执行主链。
  - Reason: 需要把 undo log、Buffer Pool 脏页、redo log、binlog cache 串成一个有序机制，避免只会孤立背三类日志。
  - Next review: 2026-07-11.
  - Prompt: `UPDATE t_user SET name='xiaolin' WHERE id=1` 中，执行器找到记录后，InnoDB 改数据页前先写什么？改完 Buffer Pool 里的页后写什么、页变成什么？Server 层之后把什么写入哪里？
  - Status: First check passed. User correctly ordered undo log before data-page update, dirty Buffer Pool page plus redo log after update, and Server-layer binlog into binlog cache.

- Topic: MySQL how_update - 两阶段提交的 XID 崩溃判断。
  - Reason: 需要稳定 redo log prepare 状态下不能直接提交或回滚，而要用 XID 去 binlog 查，避免混淆 redo log 和 binlog 半成功时的恢复策略。
  - Next review: 2026-07-11.
  - Prompt: redo log 已 prepare 并刷盘但 binlog 没有对应 XID 时，重启后怎么处理？如果 binlog 已经有对应 XID，但 redo log 还没写 commit 标识就崩溃，重启后怎么处理？为什么？
  - Status: Passed again in MCQ review on 2026-07-09. User selected commit when redo is prepare and binlog already has the same XID, preserving redo/binlog consistency.

- Topic: MySQL show_lock - 未命中主键 update 的 Gap Lock 范围。
  - Reason: 新章节 `show_lock.md` 已开始；第一步要稳定通过 `data_locks` 读出 `update id=25` 未命中时的 `(20,30)` Gap Lock，避免把 `LOCK_TYPE=RECORD` 误判为 Record Lock。
  - Next review: 2026-07-11.
  - Prompt: `update t_student set score=100 where id=25` 返回 `Rows matched: 0`，为什么仍然会加锁？`LOCK_TYPE=RECORD`、`LOCK_MODE=X, GAP`、`LOCK_DATA=30` 分别怎么帮助你推导出 `(20,30)` Gap Lock？
  - Status: First check passed. User explained current-read write locking, target insert position, `LOCK_MODE=X,GAP`, and deriving `(20,30)` from `LOCK_DATA=30` plus previous primary-key record.

- Topic: MySQL show_lock - Gap Lock 兼容与插入意向锁冲突。
  - Reason: Time 2/3 的核心边界是同范围 Gap Lock 之间兼容，但 insert 的插入意向锁与对方 Gap Lock 冲突。
  - Next review: 2026-07-11.
  - Prompt: 事务 A/B 都持有 `(20,30)` 的 X 型 Gap Lock 时，为什么两把 Gap Lock 不互相阻塞？为什么 A 插入 `id=25` 时会被 B 的 Gap Lock 阻塞？
  - Status: First check passed. User distinguished Gap Lock compatibility from insertion-intention-vs-Gap conflict; precision added that Gap Lock is purely inhibitive.

- Topic: MySQL show_lock - 等待图与循环等待。
  - Reason: Need to connect Time 3 and Time 4 into a deadlock wait graph rather than treating them as isolated insert blocks.
  - Next review: 2026-07-11.
  - Prompt: A 插入 `id=25` 时等谁释放什么锁？B 插入 `id=26` 时等谁释放什么锁？为什么这形成循环等待？
  - Status: First check passed. User described A waiting for B's Gap Lock and B waiting for A's Gap Lock, forming circular wait.

- Topic: MySQL show_lock - 本章综合口述。
  - Reason: `show_lock.md` 的三个核心边界已首轮通过：未命中 update 的 Gap Lock、Gap Lock 兼容 vs 插入意向锁冲突、A/B 循环等待。需要综合口述避免碎片化。
  - Next review: 2026-07-11.
  - Prompt: 用 5-7 句话口述本章：未命中主键 update 为什么仍加 `(20,30)` Gap Lock，如何读 `data_locks` 判断锁种和范围，为什么 Gap Lock 兼容，为什么 insert 插入意向锁被对方 Gap Lock 阻塞，最终为什么形成死锁？
  - Status: Awaiting integrated oral answer.

- Topic: MySQL deadlock - 幂等校验里的 next-key lock 与插入意向锁循环等待。
  - Reason: 新章节 `deadlock.md` 已开始；核心 schema 是把 `select ... for update` 查不存在尾部订单号、尾部 next-key lock 共存、insert 获取插入意向锁、互相等待串成死锁等待图。
  - Next review: 2026-07-11.
  - Prompt: 两个事务分别 `select ... where order_no=1007/1008 for update`，当前最大 `order_no=1006`，为什么查询阶段都能成功，而后续各自 insert 时会形成 A 等 B、B 等 A？
  - Status: First check passed. User explained Gap Lock compatibility, supremum pseudo-record boundary, insertion intention lock conflict, and A-waits-B/B-waits-A cycle.

- Topic: MySQL deadlock - insert 隐式锁与冲突时显式化。
  - Reason: 文章后半段进入 insert 加锁机制。需要稳定“普通 insert 正常情况用隐式锁保护，遇到 gap lock 或唯一键冲突时才生成显式锁结构”这个 schema。
  - Next review: 2026-07-11.
  - Prompt: 事务 A 插入唯一二级索引 `order_no=1006` 成功但未提交时，为什么一开始可能没有显式锁结构？事务 B 再插入同值时，为什么 A 的隐式锁会转成显式 X 记录锁，B 会等待？
  - Status: First check passed. User explained trx_id-based implicit protection, conversion to explicit X record lock, B's S next-key request, and X/S conflict.

- Topic: MySQL deadlock - 主键冲突 vs 唯一二级索引冲突。
  - Reason: 文章区分 insert 失败后加锁类型：重复主键加 S 型记录锁，重复唯一二级索引值加 S 型 next-key lock。该边界容易被“都是唯一约束冲突”混淆。
  - Next review: 2026-07-11.
  - Prompt: 插入重复主键 `id=5` 失败，和插入重复唯一二级索引 `order_no=1001` 失败，分别会对已有记录加什么 S 型锁？为什么不能都说成 S 型记录锁？
  - Status: First check passed. User mapped duplicate primary key to S Record Lock, duplicate unique secondary index to S Next-Key Lock, and separated lock mode from lock range.

- Topic: MySQL deadlock - 事后解除 vs 业务预防。
  - Reason: 文章最后区分数据库层死锁发生后的解除策略，以及订单号唯一索引这种业务预防手段。
  - Next review: 2026-07-11.
  - Prompt: `innodb_lock_wait_timeout` 和 `innodb_deadlock_detect=on` 分别怎么打破循环等待？为什么给 `order_no` 建唯一索引是业务预防，而不是死锁发生后的处理？
  - Status: First check passed. User distinguished timeout rollback, active wait-graph cycle detection with rollback, and unique-index business prevention.

- Topic: MySQL deadlock - 本章综合口述。
  - Reason: `deadlock.md` 的分步 schema 已全部首轮通过；需要用一次综合口述把等待图、插入意向锁、隐式锁、唯一冲突和处理策略串起来，避免碎片化记忆。
  - Next review: 2026-07-11.
  - Prompt: 用 6-8 句话口述订单幂等校验死锁：查询阶段为什么共存，插入阶段为什么互等，insert 隐式锁何时显式化，主键/唯一二级索引冲突分别加什么 S 锁，数据库层和业务层分别怎么处理/预防？
  - Status: Awaiting integrated oral answer.

- Topic: MySQL lock_phantom - 删除导致的幻读与 Record/GAP 分工。
  - Reason: 新章节 `lock_phantom.md` 已开始；学习者已通过“结果集变少也算幻读”的定义边界，也能说明删除已命中的 `id=2` 被阻塞对应 next-key lock 的 Record 部分；还能说明无 `age` 索引时会沿主键索引扫描并加很多 next-key lock，近似锁全表；有 `age` 索引时，二级索引范围锁防止新增匹配行，主键记录锁防止已有匹配行被删改；已知道 `data_locks` 要看 `LOCK_MODE` 判断具体锁种。后续需补全 `LOCK_MODE` 映射。
  - Next review: 2026-07-11.
  - Prompt: `select * from t_user where age > 20 for update` 已经返回 6 行时，为什么另一个事务删除其中一行会被阻塞？这和 Gap Lock 防插入分别对应 next-key lock 的哪一部分？
  - Status: Result-set-change, Record deletion side, no-index scan-path, age-index lock split, and data_locks field-boundary checks passed; awaiting LOCK_MODE mapping.

- Topic: MySQL UPDATE 无有效索引 - 执行计划放大锁范围。
  - Reason: 新章节 `update_index.md` 的四轮检查通过；学习者能说明无索引导致全表扫描，扫描到的索引项被 next-key lock 覆盖，效果接近锁全表；也能说明有索引但实际仍全表扫描时风险还在；还能区分 `sql_safe_updates` 是基础防线，不能替代 `EXPLAIN`，并说明 `force index` 的使用边界。需要延迟复习时补上“不是直接表锁”和“锁通常持有到事务结束”。
  - Next review: 2026-07-11.
  - Prompt: `update user set name='x' where age=20` 中，如果 `age` 没有索引，或 `age` 有索引但 `EXPLAIN` 显示 `type=ALL,key=NULL`，为什么都可能阻塞大量写操作？`sql_safe_updates` 能挡住什么、挡不住什么？什么时候才考虑 `force index(idx_age)`？请同时说明它是不是直接加了表锁，以及锁什么时候释放。
  - Status: Four checks passed with precision note; awaiting integrated oral answer.

## 2026-07-07

- Topic: MySQL 行级锁加锁规则 - 索引扫描路径与 next-key 退化。
  - Reason: 新章节 `how_to_lock.md` 已开始；核心 schema 是先判断 SQL 走哪棵索引和扫描到哪些边界，再按避免幻读决定 next-key lock 是否退化为 Record Lock 或 Gap Lock。
  - Next review: 2026-07-10.
  - Prompt: RR 下主键已有 `id=1` 和 `id=5`，`select * from user where id=2 for update` 且记录不存在。为什么锁的是 `(1,5)` Gap Lock，而不是锁住 `id=5` 的 Next-Key Lock？
  - Status: First check passed. User identified Gap Lock on `(1,5)` and the purpose as preventing insertion of `id=2`; precision repaired that the lock is on the primary-index right boundary but not on the `id=5` record itself.

- Topic: MySQL 唯一索引等值命中 - 记录锁退化。
  - Reason: 进入 `how_to_lock.md` 的第二个小 schema：唯一索引等值命中时，唯一约束已经阻止重复值插入，因此只需 Record Lock 防止已有记录被改删。
  - Next review: 2026-07-10.
  - Prompt: 主键已有 `id=5`，`select * from user where id=5 for update` 为什么退化成 Record Lock，而不是锁 `(1,5]` 的 Next-Key Lock？
  - Status: First check passed. User identified Record Lock on primary key `id=5`, blocking update/delete of that record, and explained that locking `(1,5)` is unnecessary because inserting `id=2/3/4` does not change `where id=5`.

- Topic: MySQL 唯一索引范围查询 - `>` 和 `>=` 下界。
  - Reason: 进入唯一索引范围查询；需要区分第一条扫描记录是否是等值命中边界。`id > 15` 的第一条结果不是等值命中，`id >= 15` 且 `15` 存在时第一步可退化为记录锁。
  - Next review: 2026-07-10.
  - Prompt: 主键已有 `id=15` 和 `id=20`，为什么 `id >= 15 for update` 在 `id=15` 上可以是 Record Lock，而 `id > 15 for update` 扫到 `id=20` 时要锁 `(15,20]`？
  - Status: First comparison passed. User explained `id >= 15` as an equality-hit boundary and explained `id > 15` needs `(15,20]` to prevent inserting `id=16/17/18/19` and avoid phantom rows.

- Topic: MySQL 唯一索引范围查询 - supremum 末尾区间。
  - Reason: After locking `(15,20]` for `id > 15`, range scan continues to the supremum pseudo-record to protect the open interval after the current maximum record.
  - Next review: 2026-07-10.
  - Prompt: 主键最大值是 `id=20`，`select ... where id > 15 for update` 为什么除了 `(15,20]`，还要锁 `(20,+∞]`？
  - Status: First check passed. User said it blocks inserts where `id > 20`; precision repaired that those inserted rows would also satisfy `id > 15` and become phantom rows.

- Topic: MySQL 唯一索引范围查询 - `<` 终止边界。
  - Reason: Entering upper-bound cases. For `id < 5` when `id=5` exists, the boundary record is the first non-matching record, so it is not necessary to lock the `id=5` record itself; the gap `(1,5)` is enough to prevent phantom inserts.
  - Next review: 2026-07-10.
  - Prompt: 主键已有 `id=1` 和 `id=5`，`id < 5 for update` 扫到 `id=5` 停止时，为什么边界锁是 `(1,5)` Gap Lock，而不是 `(1,5]` Next-Key Lock？
  - Status: First check passed. User explained that `id=5` itself does not need to be locked and the important inserts to block are `id=2/3/4`.

- Topic: MySQL 唯一索引范围查询 - `<=` 包含端点。
  - Reason: Contrast with `< 5`. For `id <= 5` when `id=5` exists, the right endpoint is part of the result set, so InnoDB must protect both the gap and the `id=5` record.
  - Next review: 2026-07-10.
  - Prompt: 主键已有 `id=1` 和 `id=5`，`id <= 5 for update` 为什么在 `id=5` 上不能退化成 `(1,5)` Gap Lock，而要保留 `(1,5]` Next-Key Lock？
  - Status: First check passed. User identified that `(1,5]` blocks inserts `id=2/3/4` through the gap part and update/delete of `id=5` through the record part.

- Topic: MySQL 非唯一索引等值未命中 - 二级索引间隙锁。
  - Reason: Moving from unique-index range rules into non-unique secondary-index equality. When `age=25` is absent, the right boundary is found in the secondary index, and no primary-key record lock is needed because no matching row exists.
  - Next review: 2026-07-10.
  - Prompt: 二级索引 `age` 中相邻值是 `22` 和 `39`，`select ... where age=25 for update` 未命中时，为什么锁的是 `index_age` 上的 `(22,39)` Gap Lock，且不需要主键索引记录锁？
  - Status: First check passed with precision note. User explained that `age=25` does not exist, so Record Lock cannot be applied and `(22,39)` Gap Lock is used; precision repaired that the lock is on secondary index `index_age` and no primary-key record lock is needed because no matching row exists.

- Topic: MySQL 非唯一索引等值命中 - 扫到第一个不匹配项。
  - Reason: For `age=22` on a non-unique secondary index, locking the matched secondary-index record alone is not enough; InnoDB must continue to the first non-matching secondary-index record to block same-value phantom inserts, and must also lock the matched primary-key row.
  - Next review: 2026-07-10.
  - Prompt: `age=22` 命中 `(age=22,id=10)`，下一条不匹配是 `(age=39,id=20)`。为什么需要二级索引 `(21,22]`、主键 `id=10` Record Lock、以及二级索引 `(22,39)` Gap Lock？
  - Status: Passed again in MCQ review on 2026-07-09. User selected the complete lock set: secondary `(21,22]` Next-Key Lock, primary `id=10` Record Lock, and secondary `(22,39)` Gap Lock.

- Topic: MySQL 二级索引边界值插入 - 二级索引值加主键值。
  - Reason: Non-unique secondary-index locks need boundary analysis using `(secondary key, primary key)`, not just the secondary key value.
  - Next review: 2026-07-10.
  - Prompt: 已有二级索引边界 `(age=39,id=20)` 上有 Gap Lock。为什么插入 `(age=39,id=3)` 可能被阻塞，而插入 `(age=39,id=21)` 可能不被这把锁阻塞？
  - Status: First check passed. User explained `(age=39,id=3)` falls before the locked boundary `(age=39,id=20)`, while `(age=39,id=21)` falls after it, so the next-record boundary differs.

- Topic: MySQL 非唯一索引范围查询 - 二级 Next-Key 不退化。
  - Reason: Moving to non-unique secondary-index range queries. Unlike unique-index range equality boundaries, non-unique range scans keep next-key locks on secondary-index records and also lock matched primary-key rows.
  - Next review: 2026-07-10.
  - Prompt: `age >= 22 for update` 为什么 `age=22` 这个二级索引边界不能像唯一索引 `id>=15` 那样退化成 Record Lock，而要保留 `(21,22]` Next-Key Lock？
  - Status: First check passed with precision note. User said `(21,22]` prevents new same-value/boundary-near inserts; precision added that the root reason is `age` is non-unique, so `age=22` is not guaranteed to be a single row.

- Topic: MySQL 无索引锁定语句 - 近似锁全表。
  - Reason: Final chapter risk point: if locking reads or writes do not use an effective index, InnoDB scans many records and applies next-key locks broadly.
  - Next review: 2026-07-10.
  - Prompt: 为什么 `update/delete/select ... for update` 没走索引时，不只是慢，还可能接近锁全表？
  - Status: First check passed. User explained that without an effective index InnoDB may full-scan, and because the statement is locking, scanned index records get next-key locks, broadening blocking to many records and gaps.

- Topic: MySQL 插入意向锁 - 等待请求结构。
  - Reason: User correctly said the insert blocks and that insertion intention lock is row-level, but did not understand why the waiting lock structure is useful and felt it may not matter.
  - Next review: 2026-07-10.
  - Prompt: 为什么不能只说“insert 被 gap lock 阻塞”就结束？插入意向锁作为等待锁结构记录了“谁在等”和“等哪个点/间隙”中的哪些信息？
  - Status: Passed immediate repair; delayed recall scheduled.

- Topic: MySQL 行级锁 - Record/GAP/Next-Key 边界。
  - Reason: User correctly explained that `Record Lock(id=5)` protects the existing `id=5` row, `Gap Lock(3,5)` blocks inserts such as `id=4` without locking endpoints, and `Next-Key Lock(3,5]` covers both the gap and the right endpoint record.
  - Next review: 2026-07-10.
  - Prompt: 已有索引记录 `id=3` 和 `id=5` 时，`Record Lock(id=5)`、`Gap Lock(3,5)`、`Next-Key Lock(3,5]` 分别阻止什么？哪个包含右端点记录？
  - Status: First row-lock boundary check passed.

- Topic: MySQL AUTO-INC 锁 - 并发和复制一致性。
  - Reason: User correctly explained that `innodb_autoinc_lock_mode = 2` releases the lightweight auto-increment lock after applying for values, improving concurrency, but statement binlog may diverge because replicas sequentially replay SQL while row binlog applies the primary's actual row results.
  - Next review: 2026-07-10.
  - Prompt: 为什么 `innodb_autoinc_lock_mode = 2` 并发性能最好？为什么它在 statement binlog 下有主从自增 id 不一致风险，而配合 row binlog 可以接受？
  - Status: First AUTO-INC consistency check passed.

- Topic: MySQL binlog statement vs row 前置概念。
  - Reason: User explicitly reported a missing prerequisite around why statement binlog cannot reproduce primary-side concurrent insert timing, while row binlog can preserve actual auto-increment values.
  - Next review: 2026-07-10.
  - Prompt: statement binlog 和 row binlog 分别重放“SQL 语句”还是“主库实际行结果”？为什么这会影响 AUTO-INC 并发插入的主从一致性？
  - Status: Repaired once; delayed recall scheduled.

- Topic: MySQL 意向锁 - 表锁和行锁桥接。
  - Reason: User correctly explained that without intention locks, acquiring an exclusive table lock may require checking many records, while with intention locks MySQL can inspect table-level intention locks to know rows may already be locked.
  - Next review: 2026-07-10.
  - Prompt: 意向锁为什么能让申请表锁时不用逐行检查已有行锁？它和插入意向锁是不是同一种锁？
  - Status: First intention-lock purpose check passed.

- Topic: MySQL MDL - DDL 阻塞链。
  - Reason: User correctly explained that a long transaction's MDL read lock blocks an `alter table` MDL write lock, and once the write lock is waiting, later reads can also queue because MDL write locks have higher priority.
  - Next review: 2026-07-10.
  - Prompt: 事务 A `begin; select ...` 后不提交，线程 C 执行 `alter table`，为什么 C 等待后，后续新的 `select` 也可能被堵住？
  - Status: First MDL blocking-chain check passed.

- Topic: MySQL 全局锁 - single-transaction 备份边界。
  - Reason: User correctly explained that `mysqldump --single-transaction` opens an RR transaction and uses a Read View; InnoDB can rely on MVCC while MyISAM lacks transaction/MVCC snapshot support.
  - Next review: 2026-07-10.
  - Prompt: 为什么 InnoDB 可以用 `mysqldump --single-transaction` 在备份期间继续写入，而 MyISAM 这类引擎仍更依赖全局锁？
  - Status: First transaction-snapshot boundary check passed.

- Topic: MySQL 全局锁 - 全库逻辑备份一致性。
  - Reason: User first gave a partial answer, then repaired it to the precise cross-table inconsistency: user balance is not deducted while product stock is reduced.
  - Next review: 2026-07-10.
  - Prompt: 全库逻辑备份时，先备份用户表，期间发生一次下单，再备份商品表。恢复后用户余额和商品库存分别可能是什么状态？为什么这说明备份需要一致性切面？
  - Status: First check passed after repair; review cross-table snapshot wording.

- Topic: MySQL 锁分类 - 加锁范围。
  - Reason: User correctly distinguished global, table-level, and row-level locks by affected scope, and connected row locks with record/range-level next-key analysis.
  - Next review: 2026-07-10.
  - Prompt: MySQL 按全局锁、表级锁、行级锁分类时，分类维度是什么？这个维度优先回答“保护谁/阻塞谁”，还是“什么时候释放”？
  - Status: First scope-classification check passed; needs explicit wording.

## 2026-07-06

- Topic: MySQL 特殊幻读 - 锁的时间点。
  - Reason: User correctly explained that next-key lock only blocks later inserts and cannot block transactions that already completed before the current read.
  - Next review: 2026-07-09.
  - Prompt: RR 下先普通查 `id > 100`，别人插入 `id=200` 并提交后，再 `select ... for update` 为什么能看到 4 行？next-key lock 的时间边界是什么？
  - Status: First lock-timing check passed.

- Topic: MySQL 特殊幻读 - 当前读和自写可见。
  - Reason: User correctly explained that the later `update` is a current read and can read the latest committed row, then update it; precision anchor needed that the row becomes the current transaction's own modification, so later ordinary reads can see it.
  - Next review: 2026-07-09.
  - Prompt: RR 下事务 A 先普通查 `id=5` 为空，事务 B 插入并提交后，A 又 `update id=5`，为什么 A 后续普通读能看到这行？请不要说 Read View 刷新。
  - Status: First check passed with precision anchor.

- Topic: MySQL 幻读定义边界。
  - Reason: User correctly identified that 5 rows changing to 4 rows still counts as phantom read because the result set changed.
  - Next review: 2026-07-09.
  - Prompt: 同一事务内，同一条 `where id > 100` 查询第一次返回 5 行、第二次返回 4 行，为什么也算幻读？它和不可重复读的边界是什么？
  - Status: First result-set boundary check passed.

- Topic: MySQL MVCC 快照读 vs 当前读。
  - Reason: User correctly explained that ordinary `select` is snapshot read using the existing Read View, while current read needs next-key lock to block newly inserted rows in the locked range.
  - Next review: 2026-07-09.
  - Prompt: 可重复读下，普通 `select` 和 `select ... for update` 分别靠什么机制避免幻读？为什么当前读需要 next-key lock？
  - Status: First snapshot/current-read boundary check passed.

- Topic: MySQL MVCC RC vs RR Read View 创建时机。
  - Reason: User correctly explained that RC creates a new Read View for the next read, so a committed version from transaction A may become visible, while RR keeps using the old Read View and ordinary reads may still see the old version.
  - Next review: 2026-07-09.
  - Prompt: 为什么事务 A 提交后，RC 下一次普通读可能看到新值，而 RR 普通读仍可能看到旧值？
  - Status: First timing check passed.

- Topic: MySQL MVCC Read View 可见性判断。
  - Reason: User correctly judged that `trx_id=51` is not visible when it falls in `[min_trx_id=51, max_trx_id=53)` and is present in `m_ids=[51,52]`, then said to follow `roll_pointer` to an undo-log old version.
  - Next review: 2026-07-09.
  - Prompt: 给定 `m_ids=[51,52]`, `min_trx_id=51`, `max_trx_id=53`，为什么 `trx_id=51` 不可见？下一步找哪里？
  - Status: Passed again in MCQ review on 2026-07-09. User selected the correct rule: `trx_id=51` is not visible because it falls in `[min_trx_id=51, max_trx_id=53)` and is present in `m_ids=[51,52]`.

- Topic: MySQL MVCC Read View 字段边界。
  - Reason: User correctly explained that `m_ids` is the active transaction id list and `max_trx_id` is the next transaction id to be assigned when the Read View is created.
  - Next review: 2026-07-09.
  - Prompt: Read View 里的 `max_trx_id` 为什么不是 `m_ids` 里的最大事务 id？
  - Status: Field boundary first check passed.

- Topic: MySQL MVCC 版本链隐藏字段。
  - Reason: User repaired the `trx_id` boundary and can state that `trx_id` records the transaction id that generated or last modified the record version; `roll_pointer` follows undo log to older versions.
  - Next review: 2026-07-09.
  - Prompt: 在 MVCC 里，`trx_id` 和 `roll_pointer` 分别回答“谁改的”和“旧版本在哪里”这两个问题，具体怎么回答？
  - Status: First check passed after precision repair.

- Topic: MySQL COUNT 综合口述边界。
  - Reason: User's integrated answer correctly said `count(*)` and `count(1)` are similar and that different transactions may see different row counts, but phrased InnoDB as unable to guarantee accuracy and omitted approximate/counter-table optimization.
  - Next review: 2026-07-07.
  - Prompt: 把“InnoDB 不能保证准确”改成更准确的一句话，并补上大表 count 的近似值/额外计数表两种优化。
  - Status: MVCC accuracy sentence repaired; still needs full integrated answer with large-count optimization choices.

- Topic: MySQL 大表 COUNT 优化取舍。
  - Reason: User correctly distinguished approximate count for a search result page from precise count maintenance for an order backend.
  - Next review: 2026-07-09.
  - Prompt: 大表 `count(*)` 慢时，为什么不能只把它改成 `count(1)`？搜索结果数量和订单总数分别适合什么方案？
  - Status: First check passed; review precision vs approximation boundary.

- Topic: MySQL InnoDB COUNT 与 MVCC 可见性。
  - Reason: User correctly explained that InnoDB supports transactions and different transaction views can see different row counts, so one global exact row count is not suitable.
  - Next review: 2026-07-09.
  - Prompt: 为什么 InnoDB 不能像 MyISAM 那样只靠一个全局 `row_count` 回答所有无条件 `count(*)`？
  - Status: First check passed; review with read-view wording.

- Topic: MySQL COUNT 的扫描对象选择。
  - Reason: User correctly explained that InnoDB may scan the smaller secondary index for `count(*)` because it reads fewer pages and saves I/O compared with scanning the clustered index.
  - Next review: 2026-07-09.
  - Prompt: 为什么 InnoDB 做 `count(*)` 时可能选择 `key_len` 更小的二级索引，而不是扫描聚簇索引？
  - Status: First check passed; review before MVCC row-count explanation.

- Topic: MySQL COUNT 的非 NULL 表达式语义。
  - Reason: User correctly explained that `count(name)` counts only rows where `name` is not `NULL`, while `count(1)` counts rows regardless of whether `name` is `NULL`.
  - Next review: 2026-07-09.
  - Prompt: `count(name)` 和 `count(1)` 在遇到 `name = NULL` 的行时有什么区别？
  - Status: First check passed; review before comparing COUNT performance.

## 2026-07-05

- Topic: MySQL LIKE 前缀匹配与索引失效。
  - Reason: User correctly explained that `like '林%'` has a fixed left prefix and can locate a range, while `like '%林'` matches suffixes such as `陈林/张林/周林` that are not necessarily contiguous in the full-string order and have no clear scan start.
  - Next review: 2026-07-09.
  - Prompt: 为什么 `name like '林%'` 通常可以走索引，而 `name like '%林'` 通常不能快速定位？
  - Status: First check passed; review the fixed-start-point explanation.

- Topic: MySQL 索引列上的函数和表达式。
  - Reason: User correctly explained that `id + 1 = 10` cannot directly use the raw `id` index because the index stores original `id` values, while `id = 10 - 1` keeps the indexed column in original form after constant folding.
  - Next review: 2026-07-09.
  - Prompt: 为什么 `where id + 1 = 10` 通常不能走 `id` 索引，而 `where id = 10 - 1` 可以？
  - Status: First check passed; review the distinction between calculation on indexed column and calculation on constant side.

- Topic: MySQL 隐式类型转换与索引失效。
  - Reason: User correctly explained that MySQL converts strings to numbers; `phone = 1300000001` can put `CAST` on the indexed `phone` column, while `id = '1'` converts the constant and leaves the indexed `id` column unchanged.
  - Next review: 2026-07-09.
  - Prompt: 为什么 `varchar phone = 1300000001` 可能索引失效，而 `int id = '1'` 通常还能走索引？
  - Status: First check passed; review which side receives CAST.

- Topic: MySQL 联合索引最左匹配。
  - Reason: User correctly explained that `(a,b,c)` sorts first by `a`, then by `b` inside equal `a`; therefore `where b=2` lacks a global start point, while `where a=1 and b=2` can use local order inside the fixed `a=1` group.
  - Next review: 2026-07-10.
  - Prompt: 为什么 `(a,b,c)` 能支持 `a=1 and b=2`，但通常不能直接支持 `b=2` 高效定位？
  - Status: First check passed; review global order vs local order.

- Topic: MySQL 索引下推与定位边界。
  - Reason: User correctly explained that in `(a,b,c)`, `where a=1 and c=3` cannot fully position by `c` because `b` is missing, while index condition pushdown reduces back-to-table lookups.
  - Next review: 2026-07-10.
  - Prompt: 为什么 `a=1 and c=3` 不能完整使用 `(a,b,c)` 三列定位？索引下推减少的是扫描边界还是回表次数？
  - Status: First check passed; review boundary positioning vs pushed filtering.

- Topic: MySQL OR 条件与 index merge。
  - Reason: User correctly explained that when `age` has no index in `id=1 or age=18`, the `age=18` branch may force a full scan; when both sides have indexes, MySQL may scan both indexes and merge the results.
  - Next review: 2026-07-10.
  - Prompt: 为什么 OR 里只有一边有索引通常不够？`index merge` 做的是联合索引定位，还是多个索引结果集合并？
  - Status: First check passed; review OR branch coverage and index merge.

- Topic: MySQL 单表 2000W 经验值。
  - Reason: User correctly explained that when row size grows from 1KB to 5KB, each leaf data page can hold fewer rows, so the same row count needs more leaf pages and reaches the B+Tree height threshold earlier.
  - Next review: 2026-07-08.
  - Prompt: 为什么 1KB 行可以估出 2000W 量级，而 5KB 行会把三层 B+Tree 下的建议阈值压低到约 500W？请用 `y` 解释。
  - Status: First check passed; review precision that the threshold drops under the same height target, not that every 5KB-row table immediately becomes taller.

- Topic: MySQL 大表慢查询诊断。
  - Reason: User correctly shifted the cause from row count itself to missing effective indexes, excessive page reads, and high I/O cost.
  - Next review: 2026-07-08.
  - Prompt: 一个 2500W 行表主键点查很快，但无索引范围查询很慢，为什么不能只说“因为超过 2000W 所以慢”？
  - Status: First diagnostic check passed; next review should connect query path with Buffer Pool hit/miss.

- Topic: MySQL Buffer Pool 与工作集边界。
  - Reason: User correctly explained that when needed pages are in Buffer Pool, InnoDB reads from memory quickly, while loading the same pages from disk costs much more due to disk I/O.
  - Next review: 2026-07-08.
  - Prompt: 同样一次 B+Tree 查找访问 3 个页，为什么 Buffer Pool 命中和磁盘读取的性能差别很大？
  - Status: First check passed; connect this to why row count alone cannot determine query latency.

- Topic: Binary and balanced trees for disk indexes.
  - Reason: User correctly explained that balanced binary trees still become relatively tall on large datasets and that each extra level can require another disk page read.
  - Next review: 2026-07-08.
  - Prompt: Why are AVL and red-black trees still less suitable than high-fanout B+Trees for large disk indexes despite being `O(log n)`?
  - Status: First check passed; needs delayed recall.

- Topic: B Tree vs B+Tree page efficiency.
  - Reason: User correctly explained that B+Tree non-leaf nodes store only index data, not actual row data, so they can hold more index entries.
  - Next review: 2026-07-08.
  - Prompt: Why can B+Tree non-leaf pages usually hold more index entries than B Tree nodes, and how does that reduce disk I/O?
  - Status: First check passed; needs delayed recall.

- Topic: B+Tree insert/delete stability.
  - Reason: User correctly explained that B+Tree deletes usually affect leaf records and often avoid large non-leaf changes, while B Tree deletes can involve real records in non-leaf nodes and trigger more complex restructuring.
  - Next review: 2026-07-08.
  - Prompt: Why can B+Tree deletion often disturb upper levels less than B Tree deletion?
  - Status: First check passed; needs delayed recall.

- Topic: B+Tree range query advantage.
  - Reason: User correctly explained that B+Tree leaf nodes are linked, so range queries can locate the start and scan along linked leaves instead of repeatedly traversing from the root.
  - Next review: 2026-07-08.
  - Prompt: For `where create_time between '2026-12-01' and '2026-12-12'`, why can B+Tree scan linked leaves after finding the start?
  - Status: First check passed; needs delayed recall.

## 2026-06-26

- Topic: Index structure evaluation lens.
  - Reason: User correctly identified that disk I/O count is costly and range queries matter when choosing a MySQL index structure.
  - Next review: 2026-06-29.
  - Prompt: Why is `O(log n)` alone not enough to decide whether a data structure is suitable for MySQL indexes?
  - Status: First check passed; review range-query leaf-scan precision.

- Topic: Page as I/O and local search unit.
  - Reason: User correctly explained that InnoDB reads a whole data page instead of only one row because page-based I/O reduces repeated disk access and lets InnoDB search locally in memory.
  - Next review: 2026-06-29.
  - Prompt: If InnoDB wants one row, why does it read a whole 16KB page instead of only that row?
  - Status: First check passed; needs delayed recall.

- Topic: Page directory as in-page index.
  - Reason: User correctly explained that a page-directory slot points to each group's last record and can be binary-searched to locate the target group.
  - Next review: 2026-06-29.
  - Prompt: In an InnoDB data page, what does a page-directory slot point to, and why does that help avoid scanning the whole page?
  - Status: First check passed; needs delayed recall.

- Topic: B+Tree node page vs leaf data boundary.
  - Reason: User correctly identified that non-leaf directory entries route to the next page, but said data pages only exist at leaf nodes.
  - Next review: 2026-06-27.
  - Prompt: Are InnoDB B+Tree root and intermediate nodes also data pages, or are only leaf nodes data pages? What is special about leaf pages?
  - Status: Repair passed; needs delayed recall.

- Topic: Clustered vs secondary leaf-page content.
  - Reason: User correctly explained that clustered-index leaves store full user records, secondary-index leaves store primary-key values, and secondary-index `select *` may need back-to-table lookup.
  - Next review: 2026-06-29.
  - Prompt: What do clustered-index and secondary-index leaf pages store, and why can `select *` through a secondary index require back-to-table lookup?
  - Status: First check passed; needs delayed recall.

- Topic: Page-oriented B+Tree integrated retrieval.
  - Reason: User correctly chained a primary-key lookup as root page -> non-leaf page -> leaf data page -> page-directory slot -> target slot/group -> concrete row record.
  - Next review: 2026-06-29.
  - Prompt: For `where id = 6`, describe the path from B+Tree root page to the final row record, including page directory lookup.
  - Status: Integrated check passed; needs delayed recall.

## 2026-05-31

- Topic: Variable-length field length list.
  - Reason: Earlier answer described length metadata as helping "search data"; repaired to field-boundary parsing.
  - Next review: 2026-06-01.
  - Prompt: Why does InnoDB need length metadata for `VARCHAR` fields?
  - Status: Needs short recall.

- Topic: NULL value list bit vs byte.
  - Reason: Earlier answer said one fewer NULL means one fewer bit; repaired to whole-byte allocation.
  - Next review: 2026-06-01.
  - Prompt: Why might reducing nullable columns from 3 to 2 not reduce physical row size?
  - Status: Needs short recall.

- Topic: InnoDB hidden fields.
  - Reason: User reported missing prerequisite knowledge for `row_id`, `trx_id`, and `roll_pointer`; basic schema repaired.
  - Next review: When starting MVCC.
  - Prompt: Match `row_id`, `trx_id`, and `roll_pointer` to their jobs.
  - Status: Review with transaction materials.

- Topic: Storage engine, page, and B+Tree bridge.
  - Reason: User reported missing prerequisite schema before learning MySQL index interview questions.
  - Next review: 2026-06-01.
  - Prompt: In one sentence each, explain what the storage engine, page, and B+Tree do in an indexed lookup.
  - Status: Passed immediate fill-in; needs delayed recall.

- Topic: Index classification dimensions.
  - Reason: User correctly labeled a unique key as secondary index but could not explain the four classification dimensions conceptually.
  - Next review: 2026-06-01.
  - Prompt: Classify `UNIQUE KEY uk_email(email)` using the four questions: structure, physical storage, field constraint, field count.
  - Status: Passed immediate classification; needs delayed recall.

- Topic: Covering index.
  - Reason: User correctly explained that `select age where name='Tom'` can avoid back-to-table lookup when using `idx_name_age(name, age)`.
  - Next review: 2026-06-04.
  - Prompt: With `KEY idx_name_age(name, age)`, compare whether `select age where name='Tom'` and `select * where name='Tom'` need back-to-table lookup.
  - Status: New schema; needs delayed recall.

- Topic: B+Tree vs Hash index.
  - Reason: User initially named Hash space cost, then correctly identified equality lookup as the suitable Hash case after the order-vs-no-order repair.
  - Next review: 2026-06-05.
  - Prompt: Why can B+Tree support `between` and `order by`, while Hash is mainly suitable for equality lookup?
  - Status: Immediate repair passed; needs delayed recall.

- Topic: B+Tree vs binary tree.
  - Reason: User correctly explained B+Tree's high fanout, low height, and page-level I/O advantage.
  - Next review: 2026-06-05.
  - Prompt: Why does "one page can hold many keys" make B+Tree more suitable than binary trees for disk-based indexes?
  - Status: First check passed; needs delayed recall.

- Topic: B+Tree vs B Tree range scan.
  - Reason: User correctly explained that ordered linked leaf nodes allow a range query to locate the start and scan forward.
  - Next review: 2026-06-10.
  - Prompt: Why do B+Tree leaf-node links help `between` range queries?
  - Status: First check passed; needs delayed recall.

- Topic: Composite index leftmost prefix.
  - Reason: User correctly explained that `a` is globally ordered while `b` is ordered only inside the same `a` range, so `where b=2` cannot use `(a,b,c)` to position efficiently.
  - Next review: 2026-06-10.
  - Prompt: For `KEY idx_a_b_c(a,b,c)`, why can `where a=1 and b=2` use the index better than `where b=2`?
  - Status: First check passed; needs delayed recall.

- Topic: Composite index range boundary.
  - Reason: User correctly explained that `a > 1` forms the index scan range while `b = 2` usually filters during the scan.
  - Next review: 2026-06-10.
  - Prompt: With `KEY idx_a_b(a,b)`, why does `where a > 1 and b = 2` mainly use `a` for boundary positioning?
  - Status: First check passed; needs delayed recall.

- Topic: Composite index inclusive boundary.
  - Reason: User correctly explained that `a >= 1` includes the `a=1` group where `b` is ordered, so `b=2` can help determine the starting boundary.
  - Next review: 2026-06-27.
  - Prompt: Why can `b=2` help position the start for `where a >= 1 and b = 2`, but not in the same way for `where a > 1 and b = 2`?
  - Status: First check passed; needs delayed recall.

- Topic: Composite index BETWEEN boundary.
  - Reason: User correctly explained that the `a=2` starting boundary group has ordered `b`, so `b=2` can help locate the starting boundary for `BETWEEN`.
  - Next review: 2026-06-27.
  - Prompt: With `KEY idx_a_b(a,b)`, why can `b=2` help for `where a between 2 and 8 and b=2`?
  - Status: First check passed; needs delayed recall.

- Topic: Prefix LIKE index range.
  - Reason: User correctly explained that leading-wildcard LIKE has no fixed starting point, so it is hard to use the ordered index for positioning.
  - Next review: 2026-06-27.
  - Prompt: Why can `name like 'j%'` use an index range, while `name like '%j'` usually cannot?
  - Status: First check passed; needs delayed recall.

- Topic: Index condition pushdown.
  - Reason: User correctly explained that ICP reduces back-to-table lookups by checking `b=2` inside the secondary index before fetching full rows.
  - Next review: 2026-06-27.
  - Prompt: For `where a > 1 and b = 2` on `(a,b)`, why does ICP reduce back-to-table lookups rather than the original index scan range?
  - Status: First check passed; needs delayed recall.

- Topic: Index selectivity.
  - Reason: User correctly chose `(user_id, status)` over `(status, user_id)` from selectivity because `user_id` filters far more rows.
  - Next review: 2026-06-27.
  - Prompt: From selectivity alone, why is a high-cardinality column usually a better leading column in a composite index?
  - Status: First check passed; needs delayed recall.

- Topic: Composite index for filtering and ordering.
  - Reason: User correctly explained that `(status, create_time)` can both filter `status=1` and keep results naturally ordered by `create_time`.
  - Next review: 2026-06-27.
  - Prompt: Why can `(status, create_time)` reduce filesort for `where status=1 order by create_time`?
  - Status: First check passed; needs delayed recall.

- Topic: Index maintenance cost.
  - Reason: User understood that frequent updates make indexing expensive, but should connect the cost specifically to maintaining B+Tree order.
  - Next review: 2026-06-27.
  - Prompt: Why does updating an indexed column cost more than updating a non-indexed column?
  - Status: First answer partially correct; needs precision review.

- Topic: Index invalidated by expression.
  - Reason: User correctly explained that calculating on the indexed column prevents direct positioning on the ordered key.
  - Next review: 2026-06-27.
  - Prompt: Why is `where age = 19` easier to use with `idx_age(age)` than `where age + 1 = 20`?
  - Status: First check passed; needs delayed recall.

- Topic: EXPLAIN index verification.
  - Reason: User correctly identified `key` as the actual index used, distinct from `possible_keys`.
  - Next review: 2026-06-27.
  - Prompt: In EXPLAIN, why is `key` stronger evidence of actual index usage than `possible_keys`?
  - Status: First check passed; needs delayed recall.

- Topic: EXPLAIN access type.
  - Reason: User correctly explained that `type: ALL` with `key: NULL` means a full table scan without an actual chosen index.
  - Next review: 2026-06-27.
  - Prompt: What do `type: ALL` and `key: NULL` together tell you about a query plan?
  - Status: First check passed; needs delayed recall.

- Topic: EXPLAIN Extra sort.
  - Reason: User correctly explained that `Using filesort` means MySQL cannot directly use index order for sorting and needs an extra sort step.
  - Next review: 2026-06-27.
  - Prompt: What does `Extra: Using filesort` usually tell you about an `ORDER BY` query?
  - Status: First check passed; needs delayed recall.
