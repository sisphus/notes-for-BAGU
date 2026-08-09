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

## 2026-07-23

- Topic: 第一章 AI Agent 入门——Loop Engineering
- Question: 本轮修复一个 Bug 且测试通过，但持久任务清单还有四项；Loop Engineering 接下来还需要决定哪两件事？
- User answer: 读取持久任务清单，发现或选择下一项工作。
- Correct reasoning: 先根据持久状态判断长期目标是否完成；若未完成，再选择下一项工作并启动下一轮。局部任务通过验证不等于全局完成。
- Error type: boundary confusion
- Fix strategy: 只修“局部完成 vs 全局完成”边界，并用一个二选一完成门问题复测。
- Repair result: Correct after repair on 2026-07-23; the learner stated that Loop Engineering cannot finish while the global goal remains incomplete.

## 2026-07-24

- Topic: 第二章 上下文工程——无状态 API 工具调用
- Question: 模型返回 `assistant` 工具调用 `call_7`，Harness 得到结果后，第二次请求要追加哪两条消息，结果怎样与调用关联？
- User answer: “assistant 和 tool；结果是 assistant：我请求了 call_7；tool：get_weather 的结果。”
- Correct reasoning: 保留原始 `assistant.tool_calls` 后，`tool` 消息必须携带 `tool_call_id: "call_7"`；关联依据是调用 ID，不是工具名称或消息相邻位置。
- Error type: boundary confusion
- Fix strategy: 只追问 tool 消息中的关联字段和值，不重复讲四种角色。
- Repair attempt 1: “必须写 tool_call_id；get_weather：tool_call_id。”
- Repair result: Incorrect; the learner recognized the field name but still used the function name rather than copying the invocation ID `call_7`.
- Repair attempt 2: `tool_call_id: call_7`
- Final repair result: Correct on 2026-07-24; schedule a fresh delayed transfer rather than marking the schema stable immediately.

## 2026-07-25

- Topic: 第二章 上下文工程——前缀稳定性与缓存复用
- Question: 每次请求都改写 system prompt 中的当前时间，会怎样影响缓存、TTFT 和成本？动态时间应放在哪里？
- User answer: “旧前缀发生变化，后续缓存从变化位置开始失效并重新计算；当前时间在真正需要时调用时间工具获取。”
- Correct reasoning: 动态 system prompt 使前缀缓存失效并触发重复 prefill；首 token 延迟上升，重复计算和计费使成本上升。时间应追加到轨迹末尾或按需通过工具获取。
- Error type: boundary confusion
- Fix strategy: 保留已经正确的失效机制与时间工具，只补“重算 → TTFT 上升、成本上升”两个结果。
- Repair answer: “首 token 延迟：上升；推理成本：上升。”
- Repair result: Correct on 2026-07-28; schedule a fresh transfer for 2026-07-31.
