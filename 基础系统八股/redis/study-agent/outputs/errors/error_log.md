# Error Log

Record real learner mistakes so they can become review assets.

## Template

```markdown
## YYYY-MM-DD

- Topic: <topic>
- Question: <question asked>
- User answer: <learner answer>
- Correct reasoning: <short correct reasoning>
- Error type: <missing prerequisite / concept misunderstanding / procedure confusion / boundary confusion / overloaded working memory / surface-level memorization / transfer failure>
- Fix strategy: <targeted repair>
```

## 2026-07-10

- Topic: Redis 常见面试题
- Question: 脑裂场景里，为什么旧主库网络恢复后会丢失它隔离期间接收的写入？请用「旧主仍接收写入 -> 哨兵选新主 -> 旧主降级并全量同步新主」这条链回答。
- User answer: 旧主库在和从库断联时，仍然能接收客户端写入，但这些写入没有同步到从库。
- Correct reasoning: 旧主隔离期间继续接收写入且无法同步给从库；哨兵会选出新主；网络恢复后旧主被降级为从库并向新主做全量同步，旧主本地隔离期间的写入会被新主数据覆盖。
- Error type: Boundary confusion
- Fix strategy: 用三段链路修复：旧主写入未同步 -> 哨兵选新主 -> 旧主降级并全量同步新主导致旧写入被清掉。

## 2026-07-10

- Topic: Redis 常见面试题
- Question: 请补全脑裂后半段：旧主隔离期间写入没有同步给从库；哨兵选出新主；网络恢复后旧主会被 ______，然后向新主做 ______，所以旧主隔离期间的写入会被 ______。
- User answer: 旧主隔离期间写入没有同步给从库；哨兵选出新主；网络恢复后旧主会被清空，然后向新主做同步，所以旧主隔离期间的写入会被清掉。
- Correct reasoning: 网络恢复后旧主先被降级为从库；然后作为从库向新主做全量同步；全量同步过程会清空/覆盖旧主本地数据，因此隔离期间写入丢失。
- Error type: Procedure confusion
- Fix strategy: 强调顺序：降级为从库 -> 全量同步 -> 清空/覆盖旧主本地隔离写入。

## 2026-07-10

- Topic: Redis 常见数据类型、命令与应用场景
- Question: 用户资料通常整体读取，但 `login_count` 更新频繁；整体资料和 `login_count` 分别应如何在 String + JSON 与 Hash 之间选择？
- User answer: Hash，因为适合部分字段频繁变化的对象。
- Correct reasoning: 整体资料以整体读取为主，适合 String + JSON；只有频繁独立更新的 `login_count` 适合拆到 Hash。两者可以混合使用，而不是为整个对象只选一种类型。
- Error type: Boundary confusion
- Fix strategy: 用一个二空映射固定边界：整体读取 -> String + JSON；字段级频繁更新 -> Hash。

## 2026-07-11

- Topic: Redis 常见数据类型、命令与应用场景
- Question: `uid:1={5,6,7,8,9}`、`uid:2={7,8,9,10,11}`，要找 uid:1 有而 uid:2 没有的成员，应执行哪条 Redis 命令，结果是什么？
- User answer: `uid:1 - uid:2`，结果为 5、6。
- Correct reasoning: 集合表达式和结果正确，但还需要映射为完整 Redis 命令：`SDIFF uid:1 uid:2`，返回 5、6。
- Error type: Procedure confusion
- Fix strategy: 只补命令模板：`A - B -> SDIFF A B`。

## 2026-07-11

- Topic: Redis 常见数据类型、命令与应用场景
- Question: 消息 A 被 group1 的 consumer1 读取后，同组 consumer2 能否把它当作新消息读取？group2 能否读取？为什么？
- User answer: 同组不能；group2 能。
- Correct reasoning: 判断正确；原因是同一组内消费者共享该组的消费进度并分担新消息，而不同消费组各自维护独立消费进度。
- Error type: Surface-level memorization
- Fix strategy: 用一句因果模板修复：组内共享消费进度；组间各自维护独立消费进度。

## 2026-07-11

- Topic: Redis 数据结构
- Question: 对 `HSET person name xiaolin age 18`，`person` key 和 value 分别是什么对象，value 对象的 `encoding` 表示什么？
- User answer: key 是 Hash 类型，value 是 String 类型，`encoding` 表示对象当前采用 String 表示。
- Correct reasoning: Redis key 统一是 String 对象；`HSET` 创建或修改的 value 是 Hash 对象；`type` 表示 Hash 这一逻辑类型，`encoding` 表示它当前采用的底层数据结构/内部表示。
- Error type: Concept misunderstanding
- Fix strategy: 用三空映射固定层次：key -> String object；value -> Hash object；encoding -> internal representation。
