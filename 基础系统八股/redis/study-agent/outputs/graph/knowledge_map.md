# Knowledge Map

Use Mermaid graphs only. Keep the global map compact and connected.

```mermaid
graph TD
    Learning[Learning] --> Schema[Reusable schemas]
    Schema --> MentalModel[Mental models]
    MentalModel --> Transfer[Transfer tasks]
    Transfer --> Review[Review prompts]
    Schema --> RedisTypes[Redis 数据类型选择]
    RedisTypes --> DataShape[业务数据形状]
    RedisTypes --> Operations[必需操作]
    RedisTypes --> Boundaries[性能与可靠性边界]
    RedisTypes -->|depends on| RedisObjects[Redis 对象层]
    Encodings[底层数据结构] -->|is used in| RedisObjects
    Encodings -->|transfers to| Tradeoffs[时间空间与最坏时延]
    Persistence[Redis 持久化 schema] -->|is a type of| Schema
    AOF[AOF 日志与重写] -->|is a type of| Persistence
    AOF -->|depends on| IO[write page cache 与 fsync]
    AOF -->|depends on| Process[fork COW 与双缓冲]
```

## Rules

- Add a node only when it helps future learning.
- Edges should mean depends on, builds on, is a type of, is part of, is used in, or transfers to.
- Avoid disconnected or decorative nodes.


<!-- study-agent-visual-contract:v1 -->
## Visual Contract

- This file is the global cross-topic and cross-chapter knowledge map.
- Use Mermaid for this global map.
- Keep chapter maps and concept-local diagrams canonical inside their notes; do not duplicate them here.
<!-- /study-agent-visual-contract:v1 -->
