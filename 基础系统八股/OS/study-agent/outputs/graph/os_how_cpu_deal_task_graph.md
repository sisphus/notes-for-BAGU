# CPU 如何执行任务 Graph

```mermaid
graph TD
    CacheLine[Cache Line 搬运粒度] -- "is used in" --> TaskExec[CPU 执行任务]
    Scheduler[Linux 调度器] -- "is used in" --> TaskExec
    Locality[空间局部性] -- "builds on" --> CacheLine
    Coherence[多核缓存一致性] -- "depends on" --> CacheLine
    MESI[MESI 状态机] -- "is used in" --> Coherence
    FalseSharing[伪共享] -- "builds on" --> Coherence
    FalseSharing -- "transfers to" --> Isolation[对齐-填充-分片]
    Scheduler -- "depends on" --> TaskStruct[task_struct 任务]
    RunQueue[每 CPU 运行队列 rq] -- "is part of" --> Scheduler
    SchedClass[调度类] -- "is part of" --> RunQueue
    Realtime[Deadline-Realtime 策略] -- "is a type of" --> SchedClass
    Fair[Fair 普通任务] -- "is a type of" --> SchedClass
    Fair -- "depends on" --> Vruntime[经典 CFS vruntime]
    Vruntime -- "depends on" --> NiceWeight[nice 到 weight]
```

## Edge semantics

- `Cache Line / Linux 调度器 -> CPU 执行任务`: is used in.
- `空间局部性 -> Cache Line`: builds on.
- `多核缓存一致性 -> Cache Line`: depends on.
- `MESI -> 多核缓存一致性`: is used in.
- `伪共享 -> 多核缓存一致性`: builds on.
- `伪共享 -> 对齐-填充-分片`: transfers to a mitigation decision.
- `Linux 调度器 -> task_struct`: depends on.
- `每 CPU 运行队列 -> Linux 调度器`, `调度类 -> 运行队列`: is part of.
- `Deadline-Realtime / Fair -> 调度类`: is a type of.
- `Fair -> vruntime -> nice/weight`: depends on.
