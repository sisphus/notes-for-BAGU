# Redis Interview Knowledge Graph

```mermaid
graph TD
    DataTypes[数据类型选择] -->|is part of| RedisPosition[Redis定位]
    DataTypes -->|depends on| Encodings[底层编码结构]
    ThreadModel[线程与事件循环] -->|is part of| RedisPosition
    ThreadModel -->|is used in| Performance[高性能解释]
    RedisPosition -->|is used in| CacheUse[MySQL缓存]
    Persistence[持久化选择] -->|is part of| RedisPosition
    AOF[AOF日志] -->|is part of| Persistence
    RDB[RDB快照] -->|is part of| Persistence
    Hybrid[混合持久化] -->|depends on| AOF
    Hybrid -->|depends on| RDB
    Availability[高可用设计] -->|is part of| RedisPosition
    ExpireEvict[过期与淘汰] -->|is used in| CacheUse
    CacheFailures[缓存故障诊断] -->|depends on| CacheUse
    CacheConsistency[缓存一致性] -->|depends on| CacheUse
    PracticalOps[实战问题] -->|depends on| ThreadModel
```

## Edge Semantics

- `depends on`: target concept is a prerequisite.
- `builds on`: source extends the target into a higher-level mechanism.
- `is part of`: source concept belongs inside the target concept.
- `is used in`: source concept is applied inside the target scenario.
