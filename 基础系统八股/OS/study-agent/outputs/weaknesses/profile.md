# Weakness Profile

Record repeated mistakes or important gaps. Do not record every small hesitation.

## Template

```markdown
## Topic: <topic>

- Weakness: <specific weakness>
- Evidence: <what the learner said or did>
- Error type: <missing prerequisite / concept misunderstanding / procedure confusion / boundary confusion / overloaded working memory / surface-level memorization / transfer failure>
- Fix strategy: <how future tutoring should repair it>
- Status: Active
```

## Topic: OS - RR 时间片轮转队列执行

- Weakness: 在整合多轮 RR 时间线时重复遗漏当前队首的一次执行，并把相继完成压缩成同时完成。
- Evidence: 初次完整序列写成“B A”，经两次单步队列修复后，在当前队列 A→B、剩余 3/1 秒的整合题中仍写成“B→A”。
- Error type: procedure confusion
- Fix strategy: 每轮固定写四列：当前队列、取出的队首、运行后剩余时间、回队尾或完成移出；能独立重建一条完整时间线后再讨论时间片取舍。
- Status: Active

## Topic: OS - Direct I/O 与断电持久化

- Weakness: 在局部比较题中能说出 `O_DIRECT` 不等于断电持久化，但进入完整日志写入时间线后又把“绕过 Page Cache”迁移成“写返回即持久化”。
- Evidence: 局部题回答程序 B 绕过 Page Cache 且不能仅凭写返回断言持久化；final transfer 随后回答“`O_DIRECT` 能，因为直接写到磁盘持久化了”。
- Error type: transfer failure
- Fix strategy: 后续所有 Direct I/O 题都先画两轴：缓存路径轴看 Page Cache，持久化轴看 `O_SYNC/fdatasync/fsync` 与确认点；两轴都填写后才下结论。
- Repair result: 2026-07-18 已正确完成两轴填空并否定 `O_DIRECT` 对同步语义的替代；等待 2026-07-21 再迁移后关闭弱点。
- Status: Active pending review

## Topic: OS - 事件循环的同步阻塞边界

- Weakness: 看到事件循环“可以使用非阻塞设计”时，会忽略题设已经指定的同步阻塞调用；在 ET 读取循环中也尚未把“探测空缓冲区的额外一次阻塞 read”映射为同一个执行流停顿问题。
- Evidence: 首次在“事件循环同步等待数据库 5 秒”时回答仍能继续；在明确对比 A 同步等待与 B 转交 worker 后，第二次仍回答 A、B 都能继续；2026-07-19 又明确表示不理解 ET 为什么必须使用非阻塞 fd。
- Error type: missing prerequisite
- Fix strategy: 先固定 `一个线程只有一条当前执行流` 与 `同步阻塞使该线程停在调用点`，再判断是否存在 worker/异步执行者。后续题先回答“谁在等待、谁在执行”，最后才判断事件循环能否推进。
- Repair result: 2026-07-19 已正确回答数据库同步阻塞期间没有线程执行其余 handler，正确迁移到 CPU 长任务，并进一步正确运行 ET 第三次 `read` 的阻塞/非阻塞分支；等待 2026-07-22 复查后关闭弱点。
- Status: Active pending review
