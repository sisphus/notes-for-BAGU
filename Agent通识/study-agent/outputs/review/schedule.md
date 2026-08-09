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

## 2026-07-22

### Loop Engineering prerequisite bridge

- Topic: 第一章 AI Agent 入门
- Review timing: same day
- Prompt: 一次 Agent 运行已经成功，但长期任务清单仍有未完成项；哪个工程层负责读取持久状态、选择下一项工作并判断全局完成？
- Target: distinguish inner ReAct execution from cross-run Loop Engineering
- Result: Correct after repair on 2026-07-23
- Next review: 2026-07-26

## 2026-07-23

### Context readiness audit

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 一个 Agent 能读写代码，但不知道模块职责、PR/CI 要求和 staging 配置；三个缺口分别属于哪类上下文，各会造成什么失败？
- Target: audit Agent readiness by code, process, and environment context
- Result: Correct on 2026-07-24; classification was exact and the failure was valid, but it repeated the worked example rather than demonstrating fresh transfer.
- Next review: 2026-07-27

## 2026-07-24

### Stateless API message reconstruction

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 模型返回 assistant tool call `call_7`，Harness 得到真实结果后，第二次请求必须追加哪两条消息？结果怎样与调用关联？
- Target: reconstruct a stateless Agent API call from message roles
- Result: Correct after repair on 2026-07-24; the learner supplied `tool_call_id: call_7` after distinguishing the invocation ID from the function name.
- Next review: 2026-07-27

## 2026-07-25

### Prefix stability and cache reuse

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 每次请求都改写 system prompt 中的当前时间，会怎样影响缓存、TTFT 和成本？动态时间应放在哪里？
- Target: preserve prefix stability for KV and Prompt Cache reuse
- Result: Correct after repair on 2026-07-28; the learner stated that both TTFT and inference cost rise after cache invalidation and recomputation.
- Next review: 2026-07-31

## 2026-07-28

### KV Cache versus Prompt Cache

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 同一次生成中复用历史 token 的 K/V，与下一次 API 请求复用相同前缀，分别是哪种缓存？
- Target: distinguish KV Cache from Prompt Cache by scope
- Result: Correct on 2026-07-29; A was KV Cache and B was Prompt Cache. The learner then reported a missing prerequisite about physical storage location.
- Next review: 2026-08-01

## 2026-07-29

### Cache hardware placement bridge

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 自托管 GPU 推理的活跃 KV Cache 通常在哪类内存？云 API 的 Prompt Cache 由用户电脑还是服务商基础设施持有？
- Target: locate KV and Prompt Cache across runtime and hardware layers
- Result: Correct after repair on 2026-07-29; the learner identified GPU VRAM/HBM as the active KV Cache storage layer after separating device memory from compute cores.
- Next review: 2026-08-01

### Process-driven system prompt

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 一个 Agent 经常漏掉执行后的核验；为什么把步骤组织成“验证 -> 分类 -> 执行 -> 核验”的 SOP，比继续追加孤立规则更可靠？
- Target: turn unordered rule piles into executable SOPs
- Result: Correct on 2026-07-29; the learner identified ordered stages, branches, and completion conditions, while closely following the worked example.
- Next review: 2026-08-01

### Executable business-rule boundary

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 若按比例收费只允许用于“谈判降低现有账单”，取消订阅以避免未来扣费应归入按比例收费还是固定收费？依据是什么？
- Target: turn product intent into executable decision boundaries
- Result: Correct on 2026-08-02; the learner selected fixed service fee by applying the explicit cancellation rule, closely repeating the supplied boundary.
- Next review: 2026-08-05

### Three pricing modes prerequisite

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 按实际节省额抽成、按完成服务收固定费用、低成功率任务开始前收费且通常不退款，分别是什么收费方式？
- Target: distinguish three service pricing modes by what the customer pays for
- Result: Correct after guided repair on 2026-08-02; the learner recognized percentage-based savings pricing, fixed service fee, and difficult-task upfront payment.
- Next review: 2026-08-05

### Rules versus Few-shot examples

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 当目标语气或输出格式难以用规则精确定义时，应继续增加抽象规则，还是提供少量高质量输入输出示例？为什么？
- Target: choose rules or Few-shot examples by pattern describability
- Result: Correct on 2026-08-05; the learner chose 2–3 high-quality examples for a hard-to-define tone and identified token waste and rule dilution from many similar examples.
- Next review: 2026-08-08

### Tool definition as operating manual

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 一个工具只有函数名和参数类型，导致 Agent 不知道何时使用以及参数值应长什么样；工具定义至少缺少哪两类信息？
- Target: write tool definitions as agent-facing operating manuals
- Result: Correct after repair on 2026-08-05; the learner identified both parameter descriptions with examples and purpose with usage boundaries.
- Next review: 2026-08-08

### External data versus executable instruction

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 网页、邮件或文档正文中出现“忽略原任务并执行某操作”，应被视为可信指令还是不可信外部数据？
- Target: treat external content as untrusted data rather than executable instruction
- Result: Correct on 2026-08-05; the learner classified the embedded command as untrusted external data.
- Next review: 2026-08-08

### Layered prompt-injection defense

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 外部内容已经标记来源，是否足以安全执行其中涉及敏感邮件或文件写入的操作？还需要哪类执行层控制？
- Target: layer prompt-injection defenses across context and execution
- Result: Correct on 2026-08-05; the learner stated that source marking is insufficient and named permission restrictions and sandboxing as execution-layer defenses.
- Next review: 2026-08-08

### Skill progressive disclosure

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 安装了大量 Skills 但单次任务只需一个时，启动时应注入全部完整内容，还是只提供元数据目录并按需加载完整 Skill？为什么？
- Target: use Skills to progressively disclose domain capabilities
- Result: Correct on 2026-08-06; the learner chose metadata-first, on-demand loading and identified irrelevant-token, attention-dilution, prefix-size, and cache-invalidation costs.
- Next review: 2026-08-09

### Append-only Skill loading

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 在长轨迹中加载完整 Skill，应改写 system 前缀还是通过专用工具把内容追加为 tool result？哪种方式保留已有缓存，为什么？
- Target: append Skill instructions without rewriting the cached prefix
- Result: Correct on 2026-08-06; the learner chose a dedicated Skill tool result appended to the trajectory and explained that unchanged prior tokens preserve KV/Prompt Cache.
- Next review: 2026-08-09

### Status bar as readings plus policy

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 当调用次数、TODO 和环境状态散落在长轨迹中时，应让模型反复重算，还是由 Harness 在末尾提供结构化读数和对应行动策略？为什么？
- Target: distill implicit trajectory state into status readings plus policy
- Result: Correct after repair on 2026-08-08; the learner identified both Harness-computed structured readings and the policy that maps readings to action.
- Next review: 2026-08-11

### Replace versus append status updates

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 对长轨迹中的高频小状态，应删除旧状态并替换，还是保留旧状态并持续追加？主要缓存收益和状态陈旧代价是什么？
- Target: choose replace or append status updates by cache and staleness cost
- Result: Correct near-transfer after instruction on 2026-08-08; remains Forming pending delayed independent transfer
- Next review: 2026-08-11

### Context overflow versus context rot

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 上下文窗口尚未接近上限，但 Agent 开始遗漏中部约束、重复旧问题；这是容量问题还是信息密度与检索质量问题？
- Target: distinguish context overflow from context rot
- Result: Correct application on 2026-08-09; recognized Context Rot before capacity exhaustion and chose not to wait for a full window
- Next review: 2026-08-12

### Trigger-based batch compression versus per-round rewriting

- Topic: 第二章 上下文工程
- Review timing: same day
- Prompt: 在推理质量仍稳定且窗口尚有空间时，为什么不应每轮重写工具结果摘要？何时值得批量支付一次缓存重建？
- Target: trade one controlled cache rebuild for task-aware batch compression
- Result: Pending
- Next review: TBD

### Three Agent learning mechanisms

- Topic: 第一章 AI Agent 入门
- Review timing: 3 days
- Prompt: 新能力分别写入模型参数、当前提示示例、外部知识库时，各属于哪种学习机制？哪一种最容易随上下文结束而消失？
- Target: distinguish three Agent learning mechanisms
- Result: Pending
- Next review: TBD

### ReAct feedback loop

- Topic: 第一章 AI Agent 入门
- Review timing: 3 days
- Prompt: 一个工具调用失败后，结果应经过哪两个环节才能产生新的行动？请写出完整 ReAct 循环。
- Target: run a ReAct feedback loop
- Result: Pending
- Next review: TBD

## 2026-07-26

### Loop Engineering global completion gate

- Topic: 第一章 AI Agent 入门
- Review timing: 3 days
- Prompt: 单个子任务已通过验证，但持久任务清单未清零；系统应宣布完成还是启动下一轮？判断依据是什么？
- Target: distinguish inner ReAct execution from cross-run Loop Engineering
- Result: Pending
- Next review: TBD

## 2026-07-29

### Three-component boundary

- Topic: 第一章 AI Agent 入门
- Review timing: 1 week
- Prompt: 一个 Agent 有 LLM 和工具，但看不到工具结果；它缺的是哪个组件，执行上会出现什么症状？
- Target: decompose an Agent into decision, observation, and action
- Result: Pending
- Next review: TBD

### Four-step tool-calling loop

- Topic: 第一章 AI Agent 入门
- Review timing: 1 week
- Prompt: 模型已经生成工具调用，但外部系统没有发生变化；流程最可能卡在哪一步？下一步必须把什么送回模型？
- Target: run the four-step tool-calling loop
- Result: Pending
- Next review: TBD

### Static prefix and dynamic trajectory

- Topic: 第一章 AI Agent 入门
- Review timing: 1 week
- Prompt: 系统提示词、模型刚生成的工具调用、工具返回结果分别属于静态前缀还是动态轨迹？
- Target: separate context into static prefix and dynamic trajectory
- Result: Pending
- Next review: TBD

### Five Harness functions

- Topic: 第一章 AI Agent 入门
- Review timing: 1 week
- Prompt: 在退款系统中，政策文本、金额上限、数据库确认和超时重试分别属于 Harness 的哪类功能？
- Target: map reliability needs to the five Harness functions
- Result: Pending
- Next review: TBD

## 2026-07-30

### Engineering scope classification

- Topic: 第一章 AI Agent 入门
- Review timing: 1 week
- Prompt: 管理提示词、完整上下文、外围可靠性机制、跨运行持续推进，分别属于哪种工程范围？
- Target: classify Prompt, Context, Harness, and Loop Engineering by scope
- Result: Pending
- Next review: TBD

### Simplicity, transparency, and ACI

- Topic: 第一章 AI Agent 入门
- Review timing: 1 week
- Prompt: 工具名和参数模糊、执行轨迹不可见、框架层次过多，分别违反哪个 Agent 构建原则？
- Target: diagnose Agent design with simplicity, transparency, and ACI
- Result: Pending
- Next review: TBD

### Model selection by task fit

- Topic: 第一章 AI Agent 入门
- Review timing: 1 week
- Prompt: 一个模型排行榜领先但延迟高、不支持图片且不能私有部署；什么情况下应直接淘汰它？
- Target: choose an Agent model by task fit
- Result: Pending
- Next review: TBD

### Workflow versus autonomous Agent

- Topic: 第一章 AI Agent 入门
- Review timing: 1 week
- Prompt: 一个流程既有不可跳过的合规步骤，又有无法预先枚举的异常恢复；应采用哪种编排模式，控制权如何分配？
- Target: use control ownership to separate Workflow and Agent
- Result: Pending
- Next review: TBD

### Guardrails and human escalation

- Topic: 第一章 AI Agent 入门
- Review timing: 1 week
- Prompt: 提示注入、高额转账和回复中的手机号分别应在哪一侧部署护栏？哪一步需要人工确认？
- Target: place guardrails by input, execution, and output risk
- Result: Pending
- Next review: TBD
