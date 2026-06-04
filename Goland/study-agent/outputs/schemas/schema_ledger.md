# Schema Ledger

## Go 后端能力分层 schema

- Trigger situation: 不知道 Go 后端面试该先学什么，或答题卡住时需要定位薄弱层。
- Compressed concepts: 语法层、Idiomatic Go 层、并发层、后端工程层、系统设计层、项目表达层。
- What the learner can do after acquiring it: 能把一个 Go 后端问题归类到对应能力层，并决定下一步该补语法、风格、并发、工程、设计还是表达。
- Common failure signal: 把复习路线当成普通目录；跳过基础直接背 GMP、GC、Redis、系统设计等结论。
- Status: Forming. First check correct: learner identified interface/code-style uncertainty as Idiomatic Go layer.

## Go 程序组织 schema

- Trigger situation: 看到一个 Go 项目入口、包结构、import、init 或 import cycle 问题时。
- Compressed concepts: package、导出标识符、import、init、main、internal、初始化顺序。
- What the learner can do after acquiring it: 能判断一个 Go 文件属于哪个包、哪些名字能被外部访问、程序初始化大致按什么顺序发生。
- Common failure signal: 把 package 当成文件夹名字死记；不理解大写导出规则；误用 init 做复杂业务逻辑；不理解为什么禁止循环导入。
- Status: Forming. Checks correct: learner explained exported identifiers by uppercase initial and package-private identifiers by lowercase initial; learner correctly ordered config -> db -> main initialization.
