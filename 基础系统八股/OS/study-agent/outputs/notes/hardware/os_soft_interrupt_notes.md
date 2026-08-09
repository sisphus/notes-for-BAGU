# Linux 软中断：上下半部、观测与故障定位

### 1. Topic Overview

- What this is about: 本文从“硬件事件如何异步通知 CPU”出发，解释 Linux 为什么把中断处理拆成上半部与下半部，并给出从 `top`、`/proc/softirqs`、`sar` 到 `tcpdump` 的软中断排查路径。
- Why it matters: 中断处理既要及时响应硬件，又不能长期占住 CPU 或阻塞后续中断。理解拆分机制后，才能把“CPU 的 `si` 很高”继续定位到某种软中断、某块网卡和具体流量来源。
- Difficulty level: 初级到中等。难点不在命令本身，而在区分“谁触发、先做什么、后做什么”，以及区分累计次数与变化速率。
- Prerequisites: 用户态与内核态的基本概念；CPU 正在执行任务时可能被异步事件打断；网卡可通过 DMA 把数据写入内存。
- Source of truth: `materials/os/1_hardware/soft_interrupt.md`。已逐段核对正文以及 `/proc/softirqs`、`top si`、`watch` 配图。
- Source order: 中断的异步通知（9-29）→ 上半部/下半部拆分（33-61）→ 软中断类型与每 CPU 计数（65-90）→ CPU 过高定位链（94-115）→ 总结（119-132）。
- Precision boundary: 原文把下半部概括为“以内核线程方式运行”，适合建立第一层直觉，但不能扩展成“所有软中断只在 `ksoftirqd/N` 中运行”。普通 Linux 中，待处理 softirq 也可在硬中断退出等路径被执行；工作持续过久或积压时，per-CPU `ksoftirqd/N` 会接手。PREEMPT_RT 的线程化行为又不同。参见 Linux 内核文档的 [softirq 执行说明](https://www.kernel.org/doc/html/v4.14/kernel-hacking/hacking.html#software-interrupt-context-softirqs-and-tasklets) 与 [中断退出路径](https://docs.kernel.org/core-api/entry.html)。

### 2. Core Concepts

#### Schema 1: 把中断看成“异步通知”，而不是持续轮询

- Definition: 硬件设备在事件到来时向 CPU 发出中断请求；操作系统暂停当前执行流，转入相应的内核中断处理程序。CPU 不必一直主动询问设备“完成了吗”。
- Intuition: 等外卖时继续做自己的事，骑手到达后用电话通知；电话铃声对应异步中断，而一直盯着配送页面对应低效轮询。
- Example: 网卡先通过 DMA 把收到的数据写进内存，再用硬件中断通知内核“新数据已经到达”。
- Common mistakes: 把中断理解成 CPU 定期主动查询；认为设备中断发生后，应用程序会直接处理硬件；忽略当前执行流会被打断。

#### Schema 2: 用“上半部快确认、下半部后处理”缩短硬中断路径

- Definition: Linux 将一次中断相关工作拆成两阶段。上半部由硬件中断触发，优先完成硬件紧密、时间敏感且必须尽快做完的工作；随后由内核触发下半部，异步完成更耗时的剩余工作。本文把上半部对应为硬中断，把下半部对应为软中断。
- Intuition: 接到骑手电话后先确认“我马上下来”并尽快挂断；下楼取餐和后续交流稍后完成。关键不是少做工作，而是把占用紧急通道的时间压短。
- Example: 网卡收到包后，硬中断上半部快速确认事件、暂时抑制频繁网卡中断并触发 `NET_RX`；下半部再从内存取包、经过协议栈逐层处理，最终交给应用程序。
- Common mistakes: 认为上半部处理完了整个网络包；认为软中断一定由硬件直接触发；把“延迟执行”误解成“不重要，可以无限拖延”；把 softirq 绝对等同于 `ksoftirqd` 线程。
- Precision boundary: “上半部 = 硬中断、下半部 = 软中断”是本文的教学主线。Linux 还存在 threaded IRQ、tasklet、workqueue、NAPI 等相关机制；回答本文问题时先抓住拆分目的，不把所有下半部机制混成同一种实现。
- Masking boundary: 原文用“处理中断时临时关闭中断，其他中断可能丢失”说明处理程序必须短。更严谨地说，屏蔽会延迟后续响应；具体事件会被挂起、合并还是丢失，取决于中断控制器和设备语义，不能把“屏蔽期间所有中断必然丢失”当成通则。

#### Schema 3: 把 `/proc/softirqs` 读成“类型 × CPU 的累计计数矩阵”

- Definition: `/proc/softirqs` 的行表示 softirq 类型，列表示 CPU；单元格是系统运行以来该 CPU 处理该类 softirq 的累计次数。`/proc/interrupts` 用于查看硬中断统计。
- Intuition: 水表总读数本身大并不说明现在漏水；单位时间内增长得多快才反映当前负载。
- Example: `NET_RX` 表示网络接收，`NET_TX` 表示网络发送，`TIMER` 表示定时器，`SCHED` 表示调度，`RCU` 表示 RCU 相关工作。用 `watch -d cat /proc/softirqs` 观察两次采样之间哪些格子快速增加。
- Common mistakes: 只看累计值大小就断言当前异常；只看类型总量而忽略 CPU 分布；看到某 CPU 计数不均就立即断言故障。RSS/RPS、IRQ affinity 和工作负载本身都可能造成合理倾斜，分布应作为线索而非单独结论。

#### Schema 4: 用“CPU → softirq 类型 → 网卡 → 数据包来源”逐层缩小故障范围

- Definition: 软中断 CPU 过高时，先确认 CPU 时间是否主要消耗在 softirq，再根据计数增速找到类型；若是网络接收，再定位网卡流量和包来源，最后决定处理动作。
- Intuition: 先确认哪栋楼报警，再确认哪一层、哪一个房间、是什么烟源，而不是一看到报警就直接换整栋楼。
- Example:

  1. `top` 后按 `1`，观察各 CPU 的 `si`，并留意 `ksoftirqd/N`。
  2. `watch -d cat /proc/softirqs`，找出增长最快的类型和 CPU。
  3. 若 `NET_RX` 快速增长，用 `sar -n DEV` 找到收包速率高的网卡。
  4. 用 `tcpdump` 抓包，分析源地址、协议、端口和流量形态。
  5. 非法流量可考虑防火墙/限流；正常高流量则继续评估负载分散、网卡/CPU 能力与扩容。
- Common mistakes: 把 `top si` 高直接等同于网络攻击；只看某个瞬时进程 CPU；跳过 softirq 类型和网卡定位就抓全量包；未控制 `tcpdump` 的接口、过滤条件和抓取时长而制造额外负载。

### 3. Deep Understanding

#### Mechanism chain: 为什么要拆上下半部

`设备事件异步到达 -> 硬件中断打断当前任务 -> 紧急路径若做太久，会增加当前任务停顿并延迟后续中断响应 -> 上半部只做必须立即完成的短工作 -> 标记/触发后续工作 -> 下半部完成协议处理等复杂工作 -> 紧急通道更快释放。`

这里要同时保留三个边界：

- “快”描述上半部的设计目标，不表示它什么都不做；它必须完成硬件确认和安排后续处理所需的最小工作。
- “延迟”描述把复杂工作移出最紧急路径，不表示下半部可以任意晚执行。
- `ksoftirqd/N` 是过载时非常重要的可调度执行载体，但 softirq 与该线程不是一一同义关系。

#### Diagnostic chain: 为什么关注变化速率

`top 的 si 高 -> softirq 确实占用 CPU -> /proc/softirqs 两次采样求增量 -> 找到增长最快的类型/CPU -> 若是 NET_RX，再用 sar 找网卡 -> tcpdump 找包源 -> 根据流量是否合法和容量是否充足采取动作。`

累计计数满足近似关系：

`某类 softirq 的当前速率 ≈ (第二次累计值 - 第一次累计值) / 采样间隔`

这比“哪个累计值最大”更接近当前问题，因为机器运行越久，历史累计值自然越大。

### 4. Minimal Working Example

一台 4 核 Web 服务器出现响应延迟：

1. `top` 显示 CPU2 的 `si = 35%`，`ksoftirqd/2` 活跃，先确认开销与 softirq 相关。
2. 间隔 2 秒读取 `/proc/softirqs`：CPU2 的 `NET_RX` 从 `8,000,000` 增至 `8,400,000`，其他类型变化很小。
3. 变化速率约为 `(8,400,000 - 8,000,000) / 2 = 200,000 次/秒`。这一步定位到网络接收方向，但还不能断言流量非法。
4. `sar -n DEV 1` 显示 `eth0` 收包率显著高于其他网卡。
5. 对 `eth0` 做受控 `tcpdump`，再判断是正常业务突增、小包风暴还是异常来源。

完整因果链：

`eth0 大量收包 -> 网卡/DMA 写入内存并触发硬中断 -> 上半部快速安排 NET_RX -> 网络协议处理形成大量 softirq 工作 -> softirq 预算被持续消耗/积压 -> ksoftirqd/2 活跃 -> top 中 si 升高。`

### 5. Knowledge Graph

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

### 6. Self-Test Questions

#### Recall

1. Linux 为什么不把一次中断的全部工作都放在上半部完成？
2. `/proc/softirqs` 的行、列和数值分别表示什么？
3. 为什么判断当前异常时要看计数变化速率，而不是只看累计值？

#### Application / Transfer

4. `top` 显示 `si` 高，但 `/proc/softirqs` 中增长最快的是 `TIMER` 而不是 `NET_RX`。为什么不能直接沿用网卡排查链？下一步应围绕什么线索展开？
5. 某 8 核服务器只有 CPU0 的 `NET_RX` 快速增长。请给出两个可能方向，并说明为什么“分布不均”只是线索而不是最终结论。

#### Explain Like I Am 5

6. 用“接电话先说一句再挂断，下楼后完成剩余事情”解释上半部和下半部。

### 7. Weak Point Detection

- Likely failure pattern 1: 只会背“硬中断/软中断”，说不出拆分所降低的两类风险：正常任务停顿与后续中断响应延迟。
- Likely failure pattern 2: 把上半部/下半部误解成两个硬件部件，或认为软中断由硬件直接触发。
- Likely failure pattern 3: 把 softirq 与 `ksoftirqd/N` 完全画等号。
- Likely failure pattern 4: 看到 `/proc/softirqs` 的大数就判断当前高负载，忘记它是累计计数。
- Likely failure pattern 5: `top si` 高后直接猜攻击或扩容，没有按“类型 → 网卡 → 包来源”逐层取证。
