# 分布式系统系统学习计划

> 目标：在 6 周内建立机制完整、能够进行工程选型并经过实践验证的分布式系统知识体系。
>
> 建议投入：每周 10～13 小时，总计约 65～75 小时。
>
> 学习方式：机制螺旋。始终围绕同一个电商下单系统，从远程调用逐步推进到共识、协调、锁、事务和服务治理。

---

## 1. 学完以后应该具备什么能力

完成这份计划不等于“把 27 个 Markdown 文件读完”。真正的完成标准是能够做到以下五件事：

1. **讲得出**：不看笔记，用自己的话说明一个机制解决什么问题。
2. **画得出**：画出正常路径、关键状态以及消息流转顺序。
3. **推得动**：给定宕机、超时、网络分区或重复请求，推演系统接下来会发生什么。
4. **选得出**：面对具体约束，能比较方案并说明为什么选择其中一个。
5. **验得出**：通过一个小实验验证机制，而不是只相信文章结论。

对每个核心概念，都必须回答下面五个问题：

1. 它解决什么问题？不用它会发生什么？
2. 它依赖哪些前提和故障假设？
3. 正常路径如何运行？
4. 异常路径在哪里？它不能保证什么？
5. 它牺牲了什么？适合和不适合哪些场景？

如果只能背定义，或者只能描述正常流程，该知识点仍然处于“未掌握”状态。

---

## 2. 当前目录材料分析

当前材料共 27 个 Markdown 文件，约 7240 行。它是一套偏 Java 后端、工程实践和面试复习的专题文章，不是一套完整的分布式系统教科书。

### 2.1 材料的优点

- 覆盖 RPC、网关、配置中心、分布式 ID、锁、事务、ZooKeeper 等常见后端主题。
- 大多数文章会讨论方案对比和异常边界，不只是给出名词定义。
- Raft、Paxos、ZAB、Gossip、一致性哈希等协议已经形成独立专题。
- Redis 锁、Fencing Token、TCC、Saga、消息事务等内容能够直接连接工程问题。

### 2.2 材料的不足

下面这些基础在当前目录中没有独立成章，需要在学习过程中作为“桥接课”补齐：

- 超时为什么不能证明远端没有执行；
- deadline、重试、退避、抖动和重试放大的关系；
- 幂等键、去重表、状态机和 exactly-once 幻觉；
- 物理时钟、逻辑顺序和“没有瞬时全局视图”；
- 故障检测为什么只能基于怀疑，而不是得到完美证明；
- 数据复制、数据分片和一致性模型之间的区别；
- 安全性与活性、共识与事务、协调与传播之间的边界。

这些桥接内容不能跳过，否则后面的 CAP、Raft、锁和事务容易学成孤立术语。

### 2.3 文件分级

#### A. 导航和诊断材料

这些文件不作为正式章节逐行精读：

- [分布式系统总导航](distributed-system/README.md)
- [协议专题导航](distributed-system/protocol/README.md)
- [RPC 专题导航](distributed-system/rpc/README.md)
- [ZooKeeper 专题导航](distributed-system/distributed-process-coordination/zookeeper/README.md)
- [分布式系统面试题](distributed-system/distributed-system-interview-questions.md)

四个 `README.md` 用来查找文章；面试题文件只在学习前做基线诊断，并在最后一周做综合验收。

#### B. 必须精读的主线材料

- [分布式系统入门](distributed-system/distributed-system-intro.md)
- [RPC 入门](distributed-system/rpc/rpc-intro.md)
- [CAP 与 BASE](distributed-system/protocol/cap-and-base-theorem.md)
- [分布式协调](distributed-system/protocol/centralized-and-decentralized.md)
- [拜占庭将军问题](distributed-system/protocol/byzantine-generals-problem.md)
- [Gossip](distributed-system/protocol/gossip-protocol.md)
- [Raft](distributed-system/protocol/raft-algorithm.md)
- [Paxos](distributed-system/protocol/paxos-algorithm.md)
- [ZAB](distributed-system/protocol/zab.md)
- [一致性哈希](distributed-system/protocol/consistent-hashing.md)
- [分布式 ID](distributed-system/distributed-id.md)
- [分布式锁入门](distributed-system/distributed-lock.md)
- [分布式锁实现](distributed-system/distributed-lock-implementations.md)
- [分布式事务](distributed-system/distributed-transaction.md)
- [配置中心](distributed-system/distributed-configuration-center.md)

#### C. 工程深化材料

- [Dubbo](distributed-system/rpc/dubbo.md)
- [API 网关](distributed-system/api-gateway.md)
- [Spring Cloud Gateway](distributed-system/spring-cloud-gateway-questions.md)
- [ZooKeeper 入门](distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.md)
- [ZooKeeper 实战](distributed-system/distributed-process-coordination/zookeeper/zookeeper-in-action.md)

#### D. 选择性阅读材料

- [ZooKeeper 进阶](distributed-system/distributed-process-coordination/zookeeper/zookeeper-plus.md)：与 CAP、2PC、Paxos 和 ZAB 重复较多。先学专门文章，再阅读其中的数据模型、会话、Watcher 和应用场景部分。
- [分布式 ID 设计案例](distributed-system/distributed-id-design.md)：用来做案例分析，不需要把其中每一种业务编码规则当成标准答案。

### 2.4 版本敏感内容

以下文章包含明显的版本背景：

- `dubbo.md` 主要基于 Dubbo 2；
- `zookeeper-in-action.md` 使用 ZooKeeper 3.5.8、Curator 4.2.0 示例；
- Spring Cloud Gateway、Apollo、Nacos、Redis、Redisson 的默认行为会随版本变化；
- 部分文章包含经验阈值、性能数字或产品比较。

学习时应遵守下面的原则：

- 机制、状态变化和故障边界需要掌握；
- 版本号、默认参数、命令和依赖坐标在实际实验前重新核对；
- 不把某个版本的性能数字或默认阈值背成普遍规律；
- 工程实验优先使用当前稳定且相互兼容的版本。

---

## 3. 总体学习路径

```text
为什么要分布式
    ↓
远程调用与失败歧义
    ↓
网络分区、CAP、BASE 与故障模型
    ↓
决策 vs 传播：Leader、Quorum、Lease、Fencing、Gossip
    ↓
共识：Raft → Paxos → ZAB
    ↓
具体协调服务：ZooKeeper
    ↓
数据映射与标识：一致性哈希、分布式 ID
    ↓
共享资源正确性：原子更新、幂等、分布式锁
    ↓
跨服务一致性：XA、TCC、AT、Saga、Outbox、事务消息
    ↓
完整服务体系：网关、RPC、注册发现、配置中心
    ↓
电商下单系统综合设计与故障答辩
```

这个顺序刻意没有照文件夹排列。每一层都会制造下一层要解决的问题。

---

## 4. 每次学习的固定流程

一次标准学习单元为 90～120 分钟：

1. **闭卷回忆，10 分钟**
   - 不看笔记，说出上次学到的机制和仍然不确定的地方。
2. **带问题阅读，25～35 分钟**
   - 只围绕本次核心问题阅读指定部分。
3. **重建机制，20 分钟**
   - 关闭原文，自己画正常流程或状态转换。
4. **故障推演，20 分钟**
   - 至少注入两个故障：超时、宕机、网络分区、重复消息或进程暂停。
5. **口头解释，10 分钟**
   - 用“问题 → 机制 → 异常 → 代价 → 场景”讲一遍。
6. **记录状态，5 分钟**
   - 标记为“未掌握 / 能复述 / 能推演 / 能迁移”，并记录下一次复习时间。

### 复习节奏

每个核心知识点在以下时间闭卷复习：

- 第一次学习后的第 1 天；
- 第 3 天；
- 第 7 天；
- 进入最终综合项目前再复习一次。

如果第 7 天不能完成故障推演，该知识点退回“能复述”，不能标记完成。

---

## 5. 第 0 阶段：启动诊断

预计用时：1.5～2 小时。

### 任务 0.1：建立知识边界

快速浏览：

- [总导航](distributed-system/README.md)
- [分布式系统面试题](distributed-system/distributed-system-interview-questions.md)

不要查资料，尝试回答：

1. 集群、分布式系统和微服务分别是什么？
2. 为什么超时不能直接等同于失败？
3. CAP 为什么不是任意三选二？
4. Gossip 和 Raft 是否都属于共识算法？
5. 分布式锁和分布式事务分别保证什么？
6. ZooKeeper 的临时节点为什么可以参与实现锁？
7. TCC、Saga 和消息事务分别适合什么场景？

### 任务 0.2：记录基线

每道题只标记：

- `0`：不知道；
- `1`：听过但讲不清；
- `2`：能说正常流程；
- `3`：能分析故障和方案取舍。

这不是考试成绩，而是第 6 周对比学习效果的基线。

### 启动闸门

能说明当前材料的四条主线即可开始第 1 周：

- 通信调用；
- 一致性与共识；
- 数据正确性；
- 服务治理与协调组件。

---

## 6. 第 1 周：远程调用与失败歧义

预计用时：10～12 小时。

本周核心问题：**一次本地方法调用变成网络调用后，语义发生了什么变化？**

### 单元 1：为什么要拆成多节点

精读 [分布式系统入门](distributed-system/distributed-system-intro.md)：

- 什么是分布式系统；
- 为什么需要分布式系统；
- 从单体电商到分布式电商；
- 典型特征和核心难点。

输出：

- 一张“单体下单 vs 分布式下单”对照表；
- 一条完整调用链：`用户 → 网关 → 订单 → 库存 → 支付 → 数据库/MQ`；
- 标出网络边界、独立故障边界和数据所有者。

验收问题：为什么“多部署几台机器”不自动等于分布式系统设计已经完成？

### 单元 2：RPC 正常调用链

精读 [RPC 入门](distributed-system/rpc/rpc-intro.md)：

- RPC 是调用模型，不是某个固定协议；
- Client、Client Stub、网络、Server Stub、Server；
- 动态代理、序列化、寻址和反序列化；
- Dubbo、gRPC、Thrift 的定位。

输出：闭卷画出一次 RPC 请求和响应，至少包含：

`代理 → 请求对象 → 序列化 → 连接 → 服务端分发 → 本地执行 → 响应`。

### 单元 3：桥接课——超时不等于失败

建立三个结果：

1. 明确成功：收到成功响应；
2. 明确失败：请求在执行前被确定拒绝；
3. 结果未知：请求可能没到，也可能已执行但响应丢失。

必须能分析：

- 请求丢失；
- 服务端执行前宕机；
- 服务端执行完成后响应丢失；
- 客户端超时后重试；
- 原请求和重试请求同时到达。

输出：一张“客户端观察结果 vs 服务端真实状态”矩阵。

### 单元 4：桥接课——重试和幂等

掌握以下边界：

- timeout 是等待上限；
- deadline 是整条调用链的时间预算；
- retry 会把一次逻辑请求变成多次物理执行；
- backoff 和 jitter 用于控制重试同步与放大；
- 幂等键、唯一约束、状态机和去重表用于控制重复副作用。

验收问题：为什么“重试三次”不是一个完整的容错方案？

### 单元 5：Dubbo 架构第一次学习

选择性精读 [Dubbo](distributed-system/rpc/dubbo.md)：

- Dubbo 基础；
- Provider、Consumer、Registry、Monitor；
- Invoker；
- Dubbo 工作原理。

暂时跳过 SPI 源码和各负载均衡实现细节，第 6 周再回看。

输出：解释注册中心宕机后，已经运行的消费者为什么可能仍能调用已有 Provider，以及这种本地缓存有什么风险。

### 单元 6：实践——制造一次“结果未知”

使用熟悉的语言实现两个最小服务：调用方 A 和被调用方 B。

实验步骤：

1. B 收到请求后写入一条业务记录；
2. B 故意延迟响应，A 的超时时间短于 B 的处理时间；
3. A 超时并重试；
4. 观察 B 是否产生重复记录；
5. 加入幂等键和唯一约束；
6. 再次执行，确认重复物理请求只产生一个业务结果。

实验报告必须记录：

- 客户端日志；
- 服务端日志；
- 实际业务记录数量；
- 加入幂等机制前后的区别。

### 第 1 周闸门

不看资料完成以下任务：

1. 画出 RPC 完整调用链；
2. 解释为什么远程调用不能真正等同于本地调用；
3. 推演“服务端已扣库存但响应丢失”的后果；
4. 给出安全重试所需的最小条件；
5. 区分注册中心、服务提供者和调用链监控的职责。

任一项只能背结论时，不进入第 2 周。

---

## 7. 第 2 周：CAP、故障模型与分布式协调

预计用时：11～13 小时。

本周核心问题：**节点只能看到局部状态、网络可能分区时，系统如何作出安全决定？**

### 单元 1：CAP 的严格语义

精读 [CAP 与 BASE](distributed-system/protocol/cap-and-base-theorem.md) 的 CAP 部分。

必须掌握：

- CAP 的 C 是线性一致性，不是 ACID 的 C；
- CAP 的 A 是非故障节点必须响应的严格定义，不等于日常 SLA；
- P 是网络可能分区的模型条件；
- 真正的取舍发生在分区期间；
- 为什么不能把系统永久贴成简单的 CP/AP 标签；
- PACELC 补充了无分区时延迟与一致性的取舍。

输出：用同一个库存副本案例分别设计“分区时拒绝写”和“分区时继续写”。

### 单元 2：BASE 与三个“一致性”

继续精读 BASE 部分，明确区分：

| 语境 | 一致性在问什么 |
|---|---|
| ACID | 事务前后是否满足业务不变量 |
| CAP | 多副本操作是否满足线性一致性 |
| BASE/最终一致 | 中间态或副本是否最终收敛 |

验收问题：为什么“银行转账需要一致性”这句话信息不足？必须进一步问哪些问题？

### 单元 3：决策与传播

精读 [分布式协调](distributed-system/protocol/centralized-and-decentralized.md)：

- 决策问题 vs 传播问题；
- Leader、Primary 和 Quorum；
- 多数派交集为什么必要但不充分；
- 脑裂；
- Term/Epoch；
- Lease；
- Fencing Token；
- 定时任务的至少一次执行与业务幂等。

输出：将 `Leader、Quorum、Lease、Fencing Token、幂等键` 分别写成一句“能保证什么”和一句“不能保证什么”。

### 单元 4：故障模型、安全性与活性

精读 [拜占庭将军问题](distributed-system/protocol/byzantine-generals-problem.md)：

- Crash、Omission/Timing、Byzantine 三类故障；
- Agreement、Validity、Termination；
- Safety 和 Liveness；
- CFT 的 `2f + 1` 与经典 BFT 的 `3f + 1`；
- 为什么签名和法定人数解决不同问题；
- 为什么 Raft、Paxos、ZAB 不防恶意节点。

不需要背诵 OM(m) 的递归细节，但要理解 `3m + 1` 所属的具体模型。

### 单元 5：Gossip 与最终传播

精读 [Gossip](distributed-system/protocol/gossip-protocol.md)：

- Gossip 是传播协议，不是共识协议；
- 反熵和谣言传播；
- Push、Pull、Push-Pull；
- 概率收敛、冗余消息和不一致窗口；
- Redis Cluster、Cassandra、Consul 中分别传播什么。

输出：比较 `Leader + 日志复制` 与 `Gossip`：谁作决定、谁传播状态、何时收敛、能否提供严格互斥。

### 单元 6：综合设计——三个实例的定时任务

设计一个每天凌晨执行的对账任务，要求：

- 三个实例都可以触发；
- 不能因为一个实例宕机而永久漏任务；
- 重复调度不能造成重复结算；
- 长时间暂停的旧执行者不能覆盖新执行者结果。

方案中必须出现：

- 任务唯一 ID；
- 领取或选主机制；
- Lease 或超时回收；
- 业务幂等；
- 必要时使用 Fencing Token；
- 执行状态和重试策略。

### 第 2 周闸门

必须能够完成：

1. 反驳“CAP 就是三选二”；
2. 区分 CAP-C、ACID-C 和最终一致性；
3. 解释多数派为什么不能单独保证安全；
4. 解释 Lease 为什么不能证明旧客户端已经停止；
5. 说明 Gossip 和共识的根本区别；
6. 区分安全性和活性。

---

## 8. 第 3 周：Raft、Paxos、ZAB 与 ZooKeeper

预计用时：13～15 小时。本周是理论和实践最重的一周。

本周核心问题：**一组非拜占庭节点如何对日志顺序达成一致，并在 Leader 故障后安全恢复？**

### 单元 1：Raft 角色、任期与选举

精读 [Raft](distributed-system/protocol/raft-algorithm.md) 的背景、基础概念和 Leader 选举。

必须掌握：

- Follower、Candidate、Leader；
- Term 的作用；
- election timeout 与随机化；
- 同一任期每个节点最多投一票；
- 日志新旧如何限制候选人资格；
- 多数派一侧为什么可以继续选主。

实践：用五张纸模拟五个节点，手工执行一次正常选举、一次分票和一次少数派隔离。

### 单元 2：Raft 日志复制

继续精读日志复制部分：

- `index、term、command`；
- `commitIndex` 与 `lastApplied`；
- `prevLogIndex` 与 `prevLogTerm`；
- 日志匹配属性；
- `nextIndex` 回退和冲突修复；
- Follower 如何获得提交点。

输出：给出一组有冲突的 Leader/Follower 日志，逐步写出 AppendEntries 如何找到共同前缀并修复日志。

### 单元 3：Raft 安全性与异常推演

重点掌握：

- Leader Completeness；
- 为什么候选人的日志必须足够新；
- 为什么不能仅凭“旧任期日志已复制到多数派”直接提交；
- 当前任期日志的提交规则；
- Leader 崩溃、Follower 崩溃、网络分区；
- Term inflation 与 Pre-Vote。

至少手推以下场景：

1. Leader 写入自己后立刻宕机；
2. 日志到达一个 Follower 后 Leader 宕机；
3. 日志到达多数派但客户端没收到响应；
4. 旧 Leader 与多数派隔离后继续接收客户端请求；
5. 分区恢复后两边日志冲突。

### 单元 4：先懂 Raft，再学 Paxos

精读 [Paxos](distributed-system/protocol/paxos-algorithm.md)：

- Proposer、Acceptor、Learner；
- Prepare/Promise；
- Accept/Accepted；
- 已接受值为什么必须被后续提案继承；
- 活锁；
- Multi-Paxos 的稳定 Leader 和跳过 Phase 1；
- 日志槽位和空洞。

输出一张对比表：

| 维度 | Basic Paxos | Multi-Paxos | Raft |
|---|---|---|---|
| 决定对象 |  |  |  |
| Leader 是否必要 |  |  |  |
| 日志是否允许空洞 |  |  |  |
| 活性如何恢复 |  |  |  |
| 工程实现难点 |  |  |  |

### 单元 5：ZooKeeper 数据模型与 ZAB

先精读 [ZooKeeper 入门](distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.md)：

- ZNode 数据模型；
- 持久、临时、顺序节点；
- Stat 和 version；
- Watcher；
- Session；
- Leader、Follower、Observer。

再精读 [ZAB](distributed-system/protocol/zab.md)：

- ZXID 与 Epoch；
- 消息广播；
- 多数派 ACK；
- Leader 选举；
- DIFF、TRUNC、SNAP；
- 已提交事务保留、未提交事务截断；
- ZAB 与 Raft 的相同点和不同点；
- ZooKeeper 本地读可能陈旧。

最后选择性阅读 [ZooKeeper 进阶](distributed-system/distributed-process-coordination/zookeeper/zookeeper-plus.md) 中的数据模型、会话、Watcher 和应用场景。跳过已经学过的 2PC、3PC、Paxos 和 ZAB 重复解释。

### 单元 6：ZooKeeper 三节点实践

以 [ZooKeeper 实战](distributed-system/distributed-process-coordination/zookeeper/zookeeper-in-action.md) 为操作线索，但实验前核对当前版本命令和 Curator 依赖。

实践目标：

1. 启动三节点 ZooKeeper ensemble；
2. 确认 Leader 和 Follower；
3. 创建持久节点、临时节点和临时顺序节点；
4. 注册 Watcher，修改节点并观察一次性通知；
5. 断开客户端但在 session timeout 内恢复，观察临时节点；
6. 让 session 真正过期，确认临时节点删除；
7. 停止 Leader，观察重新选举和写入恢复；
8. 再停止一个投票节点，确认失去 Quorum 后写入停止；
9. 恢复节点，观察数据同步。

实验报告必须把每个观察结果关联到：`Session、Quorum、Epoch/ZXID、Watcher、ZAB` 中至少一个机制。

### 第 3 周闸门

必须能够：

1. 手推一次 Raft 选举和日志冲突修复；
2. 解释已提交日志为什么不会被新 Leader 覆盖；
3. 区分 Basic Paxos、Multi-Paxos、Raft 和 ZAB；
4. 解释 ZooKeeper 临时节点和 Session 的真实关系；
5. 解释为什么三节点集群能容忍一个投票节点故障，却不能容忍两个；
6. 说明 ZooKeeper 的写顺序保证为什么不自动等于所有读都线性一致。

---

## 9. 第 4 周：数据映射与分布式 ID

预计用时：10～12 小时。

本周核心问题：**数据应该放到哪个节点，以及多个节点如何生成不冲突的标识？**

### 单元 1：从取模哈希到哈希环

精读 [一致性哈希](distributed-system/protocol/consistent-hashing.md)：

- `hash(key) % N`；
- 节点数变化为什么导致大规模重映射；
- 哈希环；
- 节点增加和删除；
- 数据倾斜；
- 虚拟节点和权重。

注意边界：一致性哈希解决映射稳定性，不负责副本一致性、故障确认或数据复制。

### 单元 2：一致性哈希模拟实验

实现一个最小模拟器：

1. 生成 100000 个 key；
2. 将 key 分布到 3 个真实节点；
3. 增加第 4 个节点；
4. 计算迁移 key 的比例；
5. 分别测试无虚拟节点和每节点 100 个虚拟节点；
6. 比较各节点负载的最大值、最小值和标准差；
7. 删除一个节点并观察接管压力。

验收标准：能解释虚拟节点为什么同时改善均衡性和故障接管分散度。

### 单元 3：分布式 ID 的需求与有状态方案

精读 [分布式 ID](distributed-system/distributed-id.md) 的前半部分：

- 全局唯一、高性能、高可用；
- 严格递增、趋势递增和无序的区别；
- 数据库自增；
- 数据库号段；
- Redis INCR；
- MongoDB ObjectId；
- 唯一约束为什么仍然重要。

重点推演：

- 发号器主从切换；
- 号段申请后实例宕机；
- Redis 主从异步复制导致 ID 回退；
- 单个计数 key 成为热点。

### 单元 4：无状态或弱状态 ID 方案

继续精读：

- UUID v4 与 v7；
- Snowflake 的时间戳、Worker ID 和 sequence；

- 时钟回拨；
- Worker ID 冲突；
- 单毫秒序列耗尽；
- Leaf、Tinyid、UidGenerator 等框架的核心差异。

不要背框架宣传数字。必须根据位分配自己计算：

- 能使用多少年；
- 支持多少 Worker；
- 每毫秒最多生成多少 ID。

### 单元 5：业务 ID 案例分析

阅读 [分布式 ID 设计案例](distributed-system/distributed-id-design.md)：

- 订单号；
- 优惠券和兑换码；
- TraceId/SpanId；
- 短网址。

对每个案例都问：

- 真正需要的是唯一性、不可猜测性、可排序性，还是业务可读性？
- 是否会泄露时间、地区、机器或业务量？
- ID 是否应该承担业务信息？
- 是否需要额外唯一约束和核销状态？

### 单元 6：ID 选型实践

为下面四个场景分别选方案：

1. MySQL 订单表主键；
2. 对外展示的订单号；
3. TraceId；
4. 一次性兑换码。

每个答案必须包含：需求、方案、唯一性来源、故障模式、安全风险和兜底约束。

### 第 4 周闸门

必须能够：

1. 计算节点变化后的 key 迁移；
2. 解释虚拟节点不能解决副本一致性；
3. 对比数据库号段、Redis、Snowflake 和 UUID v7；
4. 推演 Snowflake 时钟回拨和 Worker ID 冲突；
5. 区分内部主键、对外业务号和兑换码的不同需求。

---

## 10. 第 5 周：分布式锁与分布式事务

预计用时：12～14 小时。

本周核心问题：**多节点竞争资源或共同完成一次业务写入时，如何保护业务不变量？**

### 单元 1：先判断是否真的需要锁

精读 [分布式锁入门](distributed-system/distributed-lock.md)：

- 本地锁为什么不能保护跨进程资源；
- 临界区和锁粒度；
- 互斥、过期、安全释放、可重入、续约；
- 分布式锁与分布式事务的区别。

每个场景先依次评估：

1. 数据库条件更新；
2. 唯一约束；
3. 乐观锁/CAS；
4. 幂等键；
5. 队列串行化；
6. 最后才是分布式锁。

验收问题：秒杀防超卖为什么不一定需要分布式锁？

### 单元 2：Redis 锁的正确状态转换

精读 [分布式锁实现](distributed-system/distributed-lock-implementations.md) 的 Redis 部分：

- `SET key value NX EX/PX`；
- owner token；
- Lua 比较后删除；
- TTL；
- Redisson Watch Dog；
- 可重入边界。

输出：画出客户端 A 获取锁、续约、释放锁的状态图，并标出客户端崩溃和续约失败分支。

### 单元 3：Redis 集群故障与 Fencing Token

继续精读：

- 主从异步复制和故障转移；
- 锁记录丢失；
- Redlock 的条件和争议；
- 效率型锁 vs 正确性型锁；
- Lease 失效后的旧客户端；
- Fencing Token 的资源端校验。

必须明确：owner token 防误删，Fencing Token 防迟到写，两者不是同一个机制。

### 单元 4：ZooKeeper 锁

阅读同一文章的 ZooKeeper 部分：

- 临时顺序节点；
- 只监听前驱节点；
- 公平排队；
- Session 失效；
- Curator `InterProcessMutex`；
- ZooKeeper 锁为什么仍然需要考虑旧客户端迟到写。

输出对比：Redis、ZooKeeper、etcd 分别适合什么类型的锁，失败时会出现什么风险。

### 单元 5：锁失效实践

设计两个客户端 A、B 和一个共享资源：

1. A 获取一个短 TTL 的 Redis 锁；
2. A 在临界区内暂停，直到锁过期；
3. B 获取新锁并写入资源；
4. A 恢复后继续写；
5. 观察单纯 Redis 锁为什么不能阻止迟到写；
6. 给两次锁获取分配递增 token；
7. 让资源端只接受更大 token；
8. 确认 A 的旧 token 写入被拒绝。

实验结论必须区分：互斥服务的状态与最终资源的状态。

### 单元 6：从本地事务到 XA/2PC/3PC

精读 [分布式事务](distributed-system/distributed-transaction.md) 的前半部分：

- ACID；
- 本地事务边界；
- 跨库/跨服务后为什么失去单库事务；
- AP、RM、TM；
- 2PC Prepare/Commit；
- XA；
- 同步阻塞、协调者故障和不确定状态；
- 3PC 试图改善什么，以及为什么没有成为互联网业务主流方案。

输出：画出 TM 在 Prepare 后宕机时，各 RM 所知道和不知道的状态。

### 单元 7：柔性事务方案

继续精读：

- TCC：Try/Confirm/Cancel；
- 幂等、空回滚、悬挂；
- Seata AT 和 undo log；
- Saga 正向/反向恢复；
- 本地消息表/Transactional Outbox；
- RocketMQ 事务消息；
- 最大努力通知；
- 补偿失败、重试和人工处理。

为同一个订单业务比较：

| 方案 | 是否预留资源 | 是否长时间持锁 | 业务侵入 | 是否有隔离 | 失败恢复方式 |
|---|---|---|---|---|---|
| XA/2PC |  |  |  |  |  |
| TCC |  |  |  |  |  |
| AT |  |  |  |  |  |
| Saga |  |  |  |  |  |
| Outbox + MQ |  |  |  |  |  |

### 单元 8：订单一致性设计

场景：创建订单、扣减库存、使用优惠券、发出订单创建事件。

必须给出：

- 每个服务拥有的数据；
- 哪一步使用本地事务；
- 是否需要资源预留；
- 选择 TCC、Saga、AT、XA 或 Outbox 的理由；
- 幂等键；
- 消费去重；
- 补偿动作；
- 补偿失败后的告警和人工处理；
- 对账任务。

### 第 5 周闸门

必须能够：

1. 判断一个问题是否真的需要分布式锁；
2. 解释 `SET NX PX`、owner token、Lua 解锁和 Watch Dog 各自解决什么；
3. 推演锁过期后的双执行者问题；
4. 解释 Fencing Token 为什么必须由资源端校验；
5. 区分分布式锁与分布式事务；
6. 对比 XA、TCC、AT、Saga 和 Outbox；
7. 解释 TCC 的幂等、空回滚和悬挂；
8. 为订单业务设计异常恢复路径。

---

## 11. 第 6 周：网关、配置中心与综合系统设计

预计用时：11～13 小时。

本周核心问题：**如何把前五周的机制组合成一个可运行、可治理、可恢复的服务体系？**

### 单元 1：API 网关的职责和边界

精读 [API 网关](distributed-system/api-gateway.md)：

- 统一入口；
- 路由和过滤；
- 鉴权、限流、熔断、灰度和协议转换；
- 网关自身的高可用；
- 网关、Nginx、BFF 和业务服务的边界；
- 控制面与数据面；
- Spring Cloud Gateway、Kong、APISIX 等选型维度。

输出：列出哪些逻辑适合放网关，哪些必须留在业务服务。

### 单元 2：Spring Cloud Gateway

阅读 [Spring Cloud Gateway](distributed-system/spring-cloud-gateway-questions.md)：

- Route；
- Predicate；
- GatewayFilter 与 GlobalFilter；
- 动态路由；
- 限流；
- 全局异常处理。

重点理解请求流经组件的顺序，不背具体版本 API。实践前再核对所用 Spring Cloud 版本。

### 单元 3：配置中心

精读 [配置中心](distributed-system/distributed-configuration-center.md)：

- 配置中心、注册中心、K8s ConfigMap 和 Service Mesh 配置的边界；
- Push、Pull 和长轮询；
- Apollo 的通知与拉取；
- Nacos 的变更通知与客户端拉取；
- 配置版本、灰度、回滚和审计；
- 客户端内存缓存、本地快照和默认值；
- 关键配置的 fail-open/fail-closed 选择；
- 配置刷新不等于所有对象自动重建。

故障推演：配置中心完全不可用时，新实例和已有实例分别如何表现？

### 单元 4：Dubbo 第二次学习

回到 [Dubbo](distributed-system/rpc/dubbo.md)，重点阅读：

- SPI；
- 微内核；
- Registry；
- Cluster/Router/LoadBalance；
- Random、LeastActive、ConsistentHash、RoundRobin；
- 序列化方案。

这次要把 Dubbo 放进完整链路：

`网关 → Consumer → 注册发现 → 负载均衡 → Invoker → Provider → 监控`。

验收问题：一致性哈希负载均衡能否保证后端状态一致？为什么？

### 单元 5：最终综合项目——分布式下单系统

设计一个包含以下组件的系统：

- API Gateway；
- 订单、库存、支付、优惠券服务；
- RPC 或 HTTP 内部调用；
- 注册发现和配置中心；
- MySQL；
- Redis；
- 消息队列；
- ZooKeeper 或 etcd，仅在确有协调需求时使用。

设计文档必须包含：

1. **架构图**：标明客户端、网关、服务、存储和控制面。
2. **数据所有权**：每个服务拥有哪些表或状态。
3. **请求链路**：正常下单的完整消息流。
4. **超时预算**：入口 deadline 如何向下游传递。
5. **重试策略**：哪些请求能重试，依赖什么幂等机制。
6. **ID 方案**：内部主键、订单号、TraceId 分别如何生成。
7. **库存正确性**：条件更新、唯一约束、锁或队列如何选择。
8. **事务方案**：为什么选择 TCC、Saga、AT、XA 或 Outbox。
9. **消息语义**：重复消费、乱序、死信和对账如何处理。
10. **配置容灾**：配置中心不可用时如何启动和运行。
11. **协调组件边界**：哪些状态需要共识，哪些只需传播。
12. **可观测性**：TraceId、日志、指标和告警如何关联。

### 单元 6：故障矩阵

最终设计必须逐个回答以下故障：

1. 网关实例宕机；
2. RPC 请求未到服务端；
3. 服务端完成操作但响应丢失；
4. 重试导致重复请求；
5. 注册中心不可用；
6. 配置中心不可用；
7. Redis 主节点在复制锁之前宕机；
8. 锁持有者发生长时间暂停；
9. ZooKeeper Leader 切换；
10. 失去 ZooKeeper Quorum；
11. 消息重复投递；
12. 消息消费成功但 ACK 丢失；
13. TCC Confirm/Cancel 重复调用；
14. Saga 补偿失败；
15. Snowflake 时钟回拨或 Worker ID 冲突；
16. 数据分片节点扩容。

每个故障都要写清：

- 客户端看到什么；
- 服务端真实状态可能是什么；
- 哪个机制发现或缓解问题；
- 系统是否保证安全；
- 系统是否仍有活性；
- 是否需要重试、补偿、对账或人工处理。

### 单元 7：最终口头答辩

再次使用 [分布式系统面试题](distributed-system/distributed-system-interview-questions.md)，但不按文章答案背诵。

闭卷回答以下 15 题：

1. 为什么远程调用不等于本地调用？
2. CAP 为什么不是任意三选二？
3. CAP-C、ACID-C 和最终一致性分别是什么？
4. 多数派为什么必要但不充分？
5. Gossip 为什么不是共识？
6. Raft 如何防止已提交日志被覆盖？
7. Paxos、Raft 和 ZAB 的关系与差异是什么？
8. ZooKeeper 的 Session、临时节点和 Watcher 如何协作？
9. 一致性哈希解决什么，不解决什么？
10. Snowflake 的唯一性来自哪里，怎样失效？
11. Redis 锁为什么可能出现两个执行者？
12. owner token 与 Fencing Token 有什么区别？
13. 分布式锁和分布式事务有什么区别？
14. TCC、Saga 和 Outbox 如何选？
15. 网关、RPC、注册中心和配置中心的职责如何划分？

每题按以下结构回答：

`问题背景 → 核心机制 → 正常路径 → 故障路径 → 代价与选型`。

### 第 6 周闸门

只有同时满足以下条件，才算完成本轮学习：

- 第 0 阶段的七个诊断问题全部达到 `3`；
- 15 道口头题都能脱离原文回答；
- 最终架构图中没有职责混淆；
- 16 个故障场景都能推演；
- 至少完成 RPC 结果未知、ZooKeeper Quorum、一致性哈希、锁失效/Fencing 四个实验；
- 对锁、事务和 ID 的选型能够给出替代方案及拒绝理由；
- 一周后仍能完成最终综合答辩。

---

## 12. 最终验收评分表

每个主题按 0～3 分评分：

| 分数 | 标准 |
|---|---|
| 0 | 不知道或存在根本性误解 |
| 1 | 能识别名词，但依赖原文复述 |
| 2 | 能解释正常机制和常见故障 |
| 3 | 能迁移到新场景、比较方案并设计验证实验 |

核心主题：

| 主题 | 第 0 阶段 | 第 3 周 | 第 6 周 | 一周后复测 |
|---|---:|---:|---:|---:|
| 远程调用与失败歧义 |  |  |  |  |
| 重试与幂等 |  |  |  |  |
| CAP、BASE、PACELC |  |  |  |  |
| 故障模型、安全性、活性 |  |  |  |  |
| Leader、Quorum、Lease、Fencing |  |  |  |  |
| Gossip |  |  |  |  |
| Raft |  |  |  |  |
| Paxos、ZAB |  |  |  |  |
| ZooKeeper |  |  |  |  |
| 一致性哈希 |  |  |  |  |
| 分布式 ID |  |  |  |  |
| 分布式锁 |  |  |  |  |
| 分布式事务 |  |  |  |  |
| RPC 与服务治理 |  |  |  |  |
| 网关与配置中心 |  |  |  |  |

完成要求：所有主题最终达到 `3`，并通过一周后的复测。一次答对但一周后无法重建机制，不算稳定掌握。

---

## 13. 常见学习误区

1. **把文件读完当成学会**：阅读完成只代表输入结束，不代表能够重建机制。
2. **从 Paxos 开始硬啃**：先用 Raft 建立复制状态机、任期和日志安全，再学 Paxos。
3. **把 CAP 当产品标签**：必须分析具体对象、具体操作和分区时行为。
4. **把 Gossip 当共识**：传播观察结果不等于决定全局唯一结果。
5. **把多数派当完整协议**：还需要任期、投票限制、日志规则和提交规则。
6. **把超时当失败**：超时往往意味着结果未知。
7. **把重试当可靠性**：没有幂等和退避的重试会制造重复副作用和雪崩。
8. **把锁当事务**：互斥不能保证跨资源提交原子性。
9. **认为有 TTL 就不会双执行**：旧客户端可能在锁过期后继续运行。
10. **认为 ZooKeeper 锁天然绝对安全**：Session 失效后旧客户端仍可能迟到写，需要资源端保护。
11. **追求 ID 连续**：大多数业务只需要唯一，连续性可能降低可用性并泄露业务量。
12. **背中间件默认值**：版本参数会变，机制和边界才是长期知识。

---

## 14. 实际执行方式

后续学习按下面的方式进行：

1. 每次只开始一个学习单元；
2. 先讲一个机制模型；
3. 立即做一个短问题检查；
4. 只修补回答中缺失的边界；
5. 通过后再进入下一个单元；
6. 每周最后完成阶段闸门；
7. 最终综合问题没有完成前，不把章节标记为结束。

开始学习时，从下面这句话进入：

> 开始第 0 阶段：启动诊断。
