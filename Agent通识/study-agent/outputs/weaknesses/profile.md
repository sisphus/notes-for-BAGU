# Weakness Profile

Record repeated mistakes or important gaps. Do not record every small hesitation.

## Template

```markdown
## Topic: <topic>

- Weakness: <specific weakness>
- Evidence: <what the learner said or did>
- Error type: <missing prerequisite / concept misunderstanding / procedure confusion / boundary confusion / overloaded working memory / surface-level memorization / transfer failure>
- Fix strategy: <how future tutoring should repair it>
- Status: Resolved on 2026-07-23 after the learner distinguished local task success from the global completion gate.
```

## Topic: 第一章 AI Agent 入门

- Weakness: Loop Engineering 的“本轮完成 vs 长期目标完成”边界尚未建立。
- Evidence: 学习者先明确回复“missing prerequisite，请解释一下 loop engineering”；桥接后能说出读取持久任务清单和选择下一项工作，但遗漏了全局完成判定。
- Error type: boundary confusion
- Fix strategy: 用同一个 Coding Agent 场景对比“单个 Bug 测试通过”和“持久任务清单清零且总体验收通过”，要求先判断全局完成，再决定结束或启动下一轮。
- Status: Active

## Topic: 第二章 上下文工程

- Weakness: 混淆工具名称与单次工具调用 ID，无法稳定写出 `tool_call_id` 的值。
- Evidence: 首次回答只说 tool 是 `get_weather` 的结果，修复时虽识别出 `tool_call_id` 字段，仍把 `get_weather` 当成关联值，而没有复制调用请求的 `id = call_7`。
- Error type: boundary confusion
- Fix strategy: 固定使用 `function.name = 做什么` 与 `tool_call.id = 这一次调用是谁` 的对照，只要求补全 `tool_call_id: ____`，稳定后再回到完整消息列表。
- Status: Improving; exact field-value boundary was correct after repair on 2026-07-24, with fresh transfer scheduled for 2026-07-27.

- Weakness: 缺少 KV/Prompt Cache 从逻辑机制映射到推理运行时与物理内存层级的前置模型。
- Evidence: 学习者能正确区分同次生成的 KV Cache 与跨请求的 Prompt Cache，并已判断自托管侧属于“本机 GPU”、云端 Prompt Cache 属于“服务商基础设施”；经窄化修复后进一步明确活跃 K/V 张量位于 GPU 显存（VRAM/HBM），而非计算核心。
- Error type: missing prerequisite
- Fix strategy: 使用 `推理运行时 -> 设备内存或主机内存` 与 `API 客户端 -> 服务商缓存基础设施` 两条路径，先固定“缓存由谁管理、通常落在哪层内存”，再回到缓存架构约束。
- Status: Resolved after repair on 2026-07-29; delayed independent review is scheduled for 2026-08-01.

- Weakness: 尚未建立本章账单 Agent 三种收费方式的基础分类。
- Evidence: 在判断取消 Netflix 应采用哪种收费方式时，学习者明确表示“我对收费方式不懂”；桥接后依次正确识别“按省钱提成”“固定服务费”和“困难任务预收款”。
- Error type: missing prerequisite
- Fix strategy: 先按“用户为什么付钱”区分按省钱提成、固定服务费和困难任务预收款；每次只识别一个具体场景，再恢复取消订阅的业务边界题。
- Status: Resolved after guided repair on 2026-08-02; delayed independent review is scheduled for 2026-08-05.
