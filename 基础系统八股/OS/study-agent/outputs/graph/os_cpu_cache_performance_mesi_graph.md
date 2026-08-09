# OS CPU Cache Performance and MESI Graph

```mermaid
graph TD
    Perf[程序性能优化] -- "depends on" --> Hierarchy[多级 Cache]
    Hierarchy -- "depends on" --> Gap[CPU-内存延迟差]
    Line[Cache Line] -- "is part of" --> Hierarchy
    Locality[空间局部性] -- "is used in" --> Perf
    Layout[连续布局与小步长] -- "is used in" --> Locality
    Mapping[Tag-Index-Offset] -- "is used in" --> Hierarchy
    Layout -- "is used in" --> Perf
    Branch[分支预测] -- "is used in" --> Perf
    Affinity[CPU 亲和性] -- "is used in" --> Perf
    WriteBack[写回与 Dirty] -- "depends on" --> Line
    Coherence[多核缓存一致性] -- "builds on" --> WriteBack
    Coherence -- "depends on" --> Propagation[写传播]
    Coherence -- "depends on" --> Serialization[同一行写入串行化]
    Snooping[总线嗅探] -- "is used in" --> Propagation
    MESI[MESI 状态机] -- "is used in" --> Coherence
```

## Edge semantics

- `程序性能优化 -> 多级 Cache`, `多级 Cache -> CPU-内存延迟差`: depends on.
- `Cache Line -> 多级 Cache`: is part of.
- `空间局部性 -> 程序性能优化`, `连续布局与小步长 -> 空间局部性`: is used in.
- `Tag-Index-Offset -> 多级 Cache`: is used in.
- `连续布局与小步长 / 分支预测 / CPU 亲和性 -> 程序性能优化`: is used in.
- `写回与 Dirty -> Cache Line`: depends on.
- `多核缓存一致性 -> 写回与 Dirty`: builds on.
- `多核缓存一致性 -> 写传播 / 同一行写入串行化`: depends on.
- `总线嗅探 -> 写传播`, `MESI -> 多核缓存一致性`: is used in.
