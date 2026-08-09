# Current Session

- Topic: Redis AOF 持久化
- Source files: `materials/redis/storage/aof.md`
- Note file: `outputs/notes/redis_aof_notes.md`
- Knowledge map: `outputs/graph/knowledge_map.md`
- Current schema: 在可靠性与性能之间选择 `appendfsync` 策略
- Learner level: Level 3 on AOF basics from prior Redis interview practice; this dedicated chapter should use causal-chain and transfer checks rather than definition-only recall.
- Pending question: 一个业务允许宕机时最多丢失大约 1 秒的 AOF 日志，但不能接受每条写命令都同步等待磁盘。应选择 `always`、`everysec` 还是 `no`？请用可靠性和性能各说明一句理由。
- Last answer: Correct. The learner identified page cache as non-durable memory state and explained that a whole-machine power loss before operating-system writeback can lose the AOF write.
- Next action: Wait for the learner to choose `everysec` and justify both its approximately one-second loss window and its lower synchronous-fsync cost than `always`.
- Updated at: 2026-08-08

## Notes

The learner explicitly named `aof.md`, so the unfinished `data_struct.md` synthesis checkpoint was replaced rather than resumed. Its durable note and progress remain available in the earlier chapter artifacts.

The full local source was read in its real order before teaching. A complete source-grounded note was created before the first micro-lesson, with the chapter map kept inline and the cross-topic map in `outputs/graph/knowledge_map.md`. Existing evidence shows the learner already knows the broad AOF/RDB distinction, `everysec` tradeoff, and dual-buffer ownership, so this chapter uses causal diagnosis and concentrates on `write` versus `fsync`, state-based rewrite, fork/COW, and the full set of latency points.

Progress sync 2026-08-08: The learner correctly explained both risks of execute-before-log ordering. That boundary is stable. Teaching advances to the user-space buffer, kernel page cache, and durable-flush boundary; a concept-local Mermaid model was added beside Schema 2.

Progress sync 2026-08-08: The learner correctly distinguished `write()` reaching page cache from durable flush and applied the whole-machine power-loss boundary. That boundary is stable. Teaching advances to choosing among `always`, `everysec`, and `no` from an explicit reliability/performance requirement.

Roadmap:

1. 用执行顺序解释 AOF 为什么只记录成功写命令，以及两个风险窗口。
2. 区分 AOF buffer、page cache、`write()` 与 `fsync()`，再选择三种写回策略。
3. 用“当前状态替代完整历史”解释 AOF 重写和新文件策略。
4. 用页表、`fork` 和 COW 解释后台重写的一致视图与资源成本。
5. 用双缓冲所有权和“全量 + 增量”解释最终文件切换，并总结三个主进程延迟点。
