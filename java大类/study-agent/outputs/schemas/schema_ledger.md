# Schema Ledger

Track reusable schemas, not every topic or detail.

| Schema name | Trigger situation | Compressed concepts | What the learner can do after acquiring it | Common failure signal | Status |
| --- | --- | --- | --- | --- | --- |
| 从 Java 源码追踪到平台机器码 | 解释跨平台、编译与解释并存，或区分 JDK/JRE/JVM/JIT/AOT | JDK 工具、字节码、平台 JVM、解释器、热点 JIT、AOT 取舍 | 从 `.java` 追踪到平台机器行为，并指出每层是否平台相关 | 把字节码当机器码，或说 JVM 本身跨平台 | Forming |
| 按责任边界选择过程步骤或对象协作 | 需要比较 POP/OOP，或决定状态和行为应如何组织 | 过程分解、状态所有权、不变式、变化边界、性能判断边界 | 根据任务的状态与演化方式选择组织范式，并给出机制理由 | 只背“OOP 易维护”，或用范式名称直接推断性能 | Forming |
| 按求值顺序追踪表达式 | 遇到 `++/--`、移位或混合表达式，需要预测表达式值和变量终值 | 前缀/后缀求值、先取值还是先写回、移位补位与有效距离 | 把表达式产出值和变量写回值分开逐步追踪 | 只靠口诀猜最终值，或混淆 `>>` 与 `>>>` | Forming |
| 按调用方责任分类异常 | 设计方法异常契约，或判断失败应捕获、声明、修复还是交给系统终止 | `Throwable` 层次、checked/unchecked 编译规则、`Error` 边界、调用方处理责任 | 根据调用方是否必须决策来选择异常契约，并区分类加载的预期查找失败与运行环境失败 | 把 checked 等同于可恢复，或把 unchecked 一律等同于程序 bug | Forming |

## Status Values

- Not started: named but not taught yet.
- Forming: introduced and practiced, but not stable.
- Stable: learner can explain and apply it in near-transfer tasks.
- Needs review: learner missed it, confused its boundary, or failed to transfer it.
