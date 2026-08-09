# Linux 软中断 Graph

```mermaid
graph TD
    Device[硬件设备事件] -- "is used in" --> HardIRQ[硬件中断通知]
    HardIRQ -- "is part of" --> TopHalf[上半部快速处理]
    TopHalf -- "builds on" --> SoftIRQ[触发软中断]
    SoftIRQ -- "is part of" --> BottomHalf[下半部延迟处理]
    Ksoftirqd[ksoftirqd 每 CPU 线程] -- "is used in" --> SoftIRQ
    DMA[DMA 写入内存] -- "is used in" --> NetReceive[网卡收包]
    NetReceive -- "transfers to" --> HardIRQ
    BottomHalf -- "is used in" --> Protocol[协议栈处理]
    Proc[/proc/softirqs] -- "is used in" --> Rate[类型与 CPU 增速]
    Top[top 的 si] -- "is used in" --> Diagnose[软中断诊断]
    Rate -- "is used in" --> Diagnose
    Sar[sar -n DEV] -- "is used in" --> Diagnose
    Tcpdump[tcpdump] -- "is used in" --> Diagnose
```

## Edge semantics

- 硬件事件通过硬中断通知内核；硬中断路径包含快速上半部。
- 上半部安排后续 softirq；下半部承担延后处理。
- `ksoftirqd` 可用于执行积压的 per-CPU softirq，但不等于 softirq 的唯一执行上下文。
- `top`、`/proc/softirqs`、`sar`、`tcpdump` 组成从 CPU 到包来源的诊断链。

