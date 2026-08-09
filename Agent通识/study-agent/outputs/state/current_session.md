# Current Session

- Topic: 第二章 上下文工程
- Source: `materials/book/chapter2.md`
- Note file: `outputs/notes/ai_agent_chapter2_context_engineering_notes.md`
- Roadmap: 信息供给审计 -> API 消息循环 -> 静态前缀与轨迹 -> KV/Prompt Cache -> Prompt 与注入防御 -> Skills -> 状态栏 -> 压缩与隔离
- Current schema: 用任务收益换取一次受控的缓存重建
- Learner level: Level 1 on compression and cache tradeoffs
- Pending question: 一个 Agent 只用了 70% 的窗口，推理仍然准确，但它每得到一个新工具结果就立即重写旧结果的摘要。应继续逐轮压缩，还是等待腐化信号或容量阈值后批量压缩？后一种方式主要避免了什么缓存代价？
- Last answer: “Context Rot，要马上处理。” Correct application: the learner recognized quality degradation before capacity exhaustion and did not wait for the window to fill. Boundary added: responding immediately does not require full compression on every round.
- Next action: Evaluate whether the learner chooses trigger-based batch compression and identifies repeated cache invalidation after each replacement point.
- Updated at: 2026-08-09

## Notes

The learner correctly distinguished Context Rot from overflow. Continue with the deliberate tradeoff between information-density gains and cache rebuilding caused by compression.
