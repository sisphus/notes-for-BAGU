# Weakness Profile

Record repeated mistakes or important gaps. Do not record every small hesitation.

## Template

```markdown
## Topic: <topic>

- Weakness: <specific weakness>
- Evidence: <what the learner said or did>
- Error type: <missing prerequisite / concept misunderstanding / procedure confusion / boundary confusion / overloaded working memory / surface-level memorization / transfer failure>
- Fix strategy: <how future tutoring should repair it>
- Status: Repaired; learner corrected the demotion/full-sync order and explained min-slaves write limiting.
```

## Topic: Redis 常见面试题

- Weakness: HyperLogLog 的前置概念「基数统计」不清楚。
- Evidence: 在 Redis 类型选型题中，A-ZSet、B-BitMap、D-Hash 正确，但对「统计百万级页面 UV」明确表示缺失前置知识；随后能正确判断 [u1, u2, u1, u3] 的 PV=4、UV=3，并能说明 Set 要存对象、内存开销大。
- Error type: Missing prerequisite
- Fix strategy: 先区分「总访问次数 PV」和「去重人数/去重用户数 UV」，再说明 HyperLogLog 是用小内存估算唯一元素数量，不保存完整用户集合。
- Status: Repaired for type selection; keep scheduled review for retention.

## Topic: Redis 常见面试题

- Weakness: Redis 底层编码中的 listpack 和跳表缺少直觉前置。
- Evidence: 在学习「小数据紧凑编码，大数据切换专用结构」时，明确询问 listpack 是什么、跳表是什么；随后能判断 3 个元素的 ZSet 适合 listpack，100 万个元素的 ZSet 更适合跳表，并说明省内存与查找/插入效率理由。
- Error type: Missing prerequisite
- Fix strategy: 用「连续紧凑小数组」解释 listpack，用「多层索引链表」解释跳表，再回到省内存和查找效率取舍。
- Status: Repaired for current encoding schema; keep scheduled review for retention.

## Topic: Redis 常见面试题

- Weakness: Redis 脑裂数据丢失链路只说到旧主写入未同步，缺少旧主恢复后被降级并全量同步新主的覆盖动作。
- Evidence: 回答脑裂数据丢失原因时，只答出「旧主库在和从库断联时仍能接收客户端写入，但没有同步到从库」；修复题中又把「清空」放在「降级为从库」之前，顺序仍不精确。
- Error type: Boundary confusion
- Fix strategy: 强制用「旧主仍接收写入 -> 哨兵选新主 -> 旧主降级为从库 -> 向新主全量同步 -> 隔离期间写入被覆盖」五段链复述。
- Status: Active

## Topic: Redis 常见面试题

- Weakness: 不清楚异步删除中「解除 key 的关联」与「释放 value 内存」是两个步骤。
- Evidence: 学习 `UNLINK` 时明确表示 missing prerequisite，并询问什么叫 key 的关联。
- Error type: Missing prerequisite
- Fix strategy: 先用 Redis 字典中的 `key -> value 对象` 映射解释可访问性，再对比 `DEL` 同步释放和 `UNLINK` 后台释放。
- Status: Repaired on 2026-07-10; learner explained that `UNLINK` removes the key association first and the background thread gradually reclaims the value memory.

## Topic: Redis 常见面试题

- Weakness: 缺少普通锁到分布式锁的作用域前置，不清楚两者的关系。
- Evidence: 进入 Redis 分布式锁命令设计时明确表示 missing prerequisite，并询问什么是分布式锁及其与普通锁的关系。
- Error type: Missing prerequisite
- Fix strategy: 先用「单进程共享内存 mutex」对比「多进程不共享本地内存，需要 Redis 保存共同锁状态」，再回到 `SET NX PX`。
- Status: Repaired on 2026-07-10; learner explained that separate instances cannot see which other instance acquired its local lock.

## Topic: Redis 常见面试题

- Weakness: 缺少 Redlock 的整体前置，不清楚它为什么使用多个独立 Redis 主节点。
- Evidence: 在进入 Redlock 多数派和有效时间计算时明确表示 missing prerequisite，并询问什么是 Redlock。
- Error type: Missing prerequisite
- Fix strategy: 先对比「一个 Redis 作为唯一锁裁判」和「多个独立 Redis 节点共同投票」，再引入多数派与耗时小于 TTL 两个条件。
- Status: Repaired on 2026-07-10; learner rejected 2/5 successful nodes because they do not form a majority.
