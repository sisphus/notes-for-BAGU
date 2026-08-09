# Error Log

Record real learner mistakes so they can become review assets.

## Template

```markdown
## YYYY-MM-DD

- Topic: <topic>
- Question: <question asked>
- User answer: <learner answer>
- Correct reasoning: <short correct reasoning>
- Error type: <missing prerequisite / concept misunderstanding / procedure confusion / boundary confusion / overloaded working memory / surface-level memorization / transfer failure>
- Fix strategy: <targeted repair>
```

## 2026-07-11

- Topic: OS - CPU 是如何执行程序的
- Question: 在图灵机执行 `1 + 2` 的例子里，控制单元看到 `+` 后为什么不把它当普通数据存入状态？
- User answer: “因为不是数据是运算单元。”
- Correct reasoning: `+` 是运算符指令，它表示要执行的操作；控制单元识别它后，通知运算单元取出状态中的 `1` 和 `2` 计算。`+` 本身不是运算单元。
- Error type: boundary confusion
- Fix strategy: 用“控制单元识别运算符指令 -> 通知运算单元”的两空补全题修复角边界。

## 2026-07-11

- Topic: OS - CPU 是如何执行程序的
- Question: 64 位 CPU 搭配 32 位地址总线，按字节寻址时理论寻址空间是多少，由哪个位宽决定？
- User answer: “它的理论寻址空间是 4 bytes；地址总线的位宽。”
- Correct reasoning: 32 位地址总线能表示 `2^32` 个地址；按字节寻址时就是 `2^32 bytes = 4 GB`。`32 bit = 4 bytes` 描述的是 32 个二进制位本身的大小，不是 32 位地址能覆盖的总容量。
- Error type: boundary confusion
- Fix strategy: 固定三步换算：`n 位 -> 2^n 个地址 -> 按字节寻址得 2^n bytes`，再换算为 GB。

## 2026-07-11

- Topic: OS - CPU 是如何执行程序的
- Question: `if-else` 在机器层面属于哪类指令，核心修改哪个寄存器？
- User answer: “跳转指令；PC、IR。”
- Correct reasoning: 跳转指令修改 PC，使 PC 指向新的下一条指令地址。IR 只保存当前正在执行的跳转指令，不保存跳转目标。
- Error type: transfer failure
- Fix strategy: 用“IR = 当前指令，PC = 下一条指令地址”的对比，让学习者单选跳转目标应写入的寄存器。

## 2026-07-12

- Topic: OS - 存储器层次结构
- Question: 按文章中的常见组织，哪一级 Cache 会分成指令缓存和数据缓存，哪一级通常由多个 CPU 核心共享？
- User answer: “L1 L2 分成指令缓存和数据缓存。L3 通常由多个 CPU 核心共享。”
- Correct reasoning: 本文明确说明 L1 通常拆成指令缓存和数据缓存；L2 通常每核私有，但本文没有把它描述为指令/数据分离；L3 通常由多个核心共享。
- Error type: boundary confusion
- Fix strategy: 用单空题固定“指令/数据分离 = L1”，再用一次完整查找路径检验 L1/L2/L3 的角色。

## 2026-07-12

- Topic: OS - 存储器层次结构
- Question: 数据不在寄存器、L1、L2，但在 L3 时，写出查找顺序并说明是否还需访问内存。
- User answer: “L1 -> L2 -> L3。”
- Correct reasoning: 查找顺序正确；在 L3 命中后即可取得数据，不需要继续访问更慢的内存。只有 L3 也未命中时才访问内存。
- Error type: procedure confusion
- Fix strategy: 用“命中即停止，未命中才向下一层”的单一判断题修复漏答。

## 2026-07-12

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Question: 一个计算密集线程的热点工作集已在核心 0 的 L1/L2 中，但核心 1 当前空闲。是否应该一律绑在核心 0？各说一个收益和代价。
- User answer: “是”，随后正确指出绑核能保留私有 L1/L2 热度，但会限制负载均衡，使线程在原核心繁忙时无法迁移到空闲核心。
- Correct reasoning: 不能得出“一律绑核”的绝对结论。只有当缓存复用收益大于失去调度灵活性和负载均衡能力的代价，并由测量证实时，绑核才可能值得。
- Error type: boundary confusion
- Fix strategy: 用“缓存复用收益 > 调度与负载均衡代价”条件式补全题，修复绝对结论与取舍分析之间的矛盾。

## 2026-07-12

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Question: A 写 i=100、B 写 i=200，C 看到 `100 -> 200`、D 看到 `200 -> 100`。写传播和写入串行化是否分别做到，为什么仍不一致？
- User answer: 写传播做到，写入串行化没有做到，因为“D 观察到的顺序不对”。
- Correct reasoning: 写传播已做到，串行化失败的原因是 C、D 对同一位置的写入顺序没有达成一致。协议不预先规定必须是 `100 -> 200`；若所有核心都同意 `200 -> 100`，也满足写入串行化。
- Error type: boundary confusion
- Fix strategy: 用“所有观察者都看到 `200 -> 100`”的反例，区分“共同顺序”与“预先指定的正确顺序”。

## 2026-07-12

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Question: 分别判断永久使用同一地址旧副本、普通 `i++` 丢失更新、跨地址 flag/data 观察顺序异常属于 coherence、atomicity 还是 memory ordering。
- User answer: 依次回答 atomicity、coherence、memory ordering。
- Correct reasoning: 永久使用同一地址的旧 Cache 副本违反 coherence；普通 `i++` 的读-改-写被交错导致丢失更新，违反 atomicity；跨地址可见顺序异常属于 memory ordering。
- Error type: boundary confusion
- Fix strategy: 固定诊断触发词：同一地址旧副本 -> coherence；复合读改写被插入 -> atomicity；不同地址观察顺序 -> memory ordering。

## 2026-07-12

- Topic: OS - Linux 软中断
- Question: 上半部执行太久，会对当前正常进程和随后到来的其他中断分别造成什么影响？
- User answer: “会让当前进程阻塞，并让随后到来的其他中断延迟响应。”
- Correct reasoning: 其他中断会延迟响应；原进程只是被硬中断打断、暂停执行并失去一段 CPU 时间，不代表它因等待某个事件或资源而必然进入调度意义上的阻塞态。
- Error type: boundary confusion
- Fix strategy: 用“被中断/暂停执行”对比“等待事件而阻塞”，让学习者判断硬中断是否必然改变原进程的调度状态。

## 2026-07-12

- Topic: OS - Linux 软中断
- Question: CPU0/CPU1 的 NET_RX 累计值在 2 秒内分别从 1,000,000/800,000 增至 1,040,000/802,000；判断谁更活跃并计算速率。
- User answer: “CPU0 的 NET_RX 更活跃；1,040,000，802,000。”
- Correct reasoning: CPU0 更活跃的判断正确；速率必须先用第二次累计值减第一次累计值得到增量，再除以 2 秒。第二次快照本身仍是历史累计总数，不是当前每秒速率。
- Error type: procedure confusion
- Fix strategy: 固定三步：`第二次累计值 - 第一次累计值 -> 得到区间增量 -> 除以采样秒数`，先只计算 CPU0。

## 2026-07-13

- Topic: OS - malloc 动态内存分配
- Question: `malloc(64)` 成功能否说明“执行了 malloc 系统调用，并立即获得 64B 物理内存”？指出两个层次错误。
- User answer: 正确说明分配器可先从内存池取块，只有空间不足时才可能通过系统调用向内核扩容；未回答是否已立即获得物理内存。
- Correct reasoning: `malloc` 是用户态分配器接口，可直接复用内存池；它成功时返回虚拟地址，尚未访问的页不一定已驻留物理内存，首次访问时才可能触发可恢复缺页并建立映射。
- Error type: boundary confusion
- Fix strategy: 保留已掌握的“内存池 vs 系统调用”，只用一道“分配后从未访问”的判断题修复“虚拟地址 vs 驻留物理页”。

## 2026-07-13

- Topic: OS - malloc 动态内存分配
- Question: 为什么反复申请/释放 `4KB` 小块更适合复用 `brk` 内存池，而偶尔申请并快速释放的 `8MB` 大块更适合独立 `mmap`？
- User answer: 正确选择了两种策略，说明小块批量获取、快速复用且重复成本低，大块独立且容易归还 OS；未明确点出系统调用/重新首触缺页与 heap 碎片/高水位这两组因果。
- Correct reasoning: 频繁独立 `mmap/unmap` 会重复进入内核，重新分配后首次访问还可能再触发缺页；`brk` 内存池复用可减少这些 CPU 成本。但大块若留在连续 heap，可能抬高高水位，并在频繁分配/释放中形成难以复用的空洞；独立 `mmap` 便于整块解映射归还 OS。
- Error type: boundary confusion
- Fix strategy: 保留正确的策略选择，用一道双空因果链题固定 `mmap -> 系统调用/首触` 和 `brk -> 碎片/高水位` 两个精确边界。

## 2026-07-13

- Topic: OS - Linux 内存回收
- Question: 首次写入遇到无空闲物理页时，补全缺页处理、后台回收、直接回收和 OOM 的顺序，并指出哪一步阻塞申请进程。
- User answer: 正确写出“缺页异常 → kswapd 后台回收 → direct reclaim → OOM Killer”的升级顺序；未回答哪一步会阻塞申请进程。`OM Killer` 按拼写笔误处理。
- Correct reasoning: `kswapd` 是与申请进程异步执行的后台回收；`direct reclaim` 发生在申请路径中，申请进程同步参与并等待回收，因此会被阻塞。
- Error type: boundary confusion
- Fix strategy: 保留已经正确的升级顺序，只用一道二选一因果题修复“异步后台回收 vs 同步直接回收”的阻塞边界。

## 2026-07-14

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Question: 应用请求 `4KB`、内核预读 `16KB`；分别说明预读成功的收益、预读失效，以及未使用预读页怎样伤害传统 LRU。
- User answer: 正确指出后续命中 Page Cache 能减少磁盘 I/O，也正确识别另外三个未使用页属于预读失效、额外读取和缓存白做；未回答它们如何导致热点页淘汰与命中率下降。
- Correct reasoning: 传统 LRU 会把刚加载的预读页放到链表头部。即使它们之后不被访问，也会占据前排；缓存满时，链表尾部可能是真正的热点页，却会被优先淘汰。热点再次访问时发生未命中并产生磁盘 I/O，因此整体命中率下降。
- Error type: boundary confusion
- Fix strategy: 保留已经掌握的“收益 vs 失效”，只追问 `未使用预读页进头部 -> 热点页从尾部淘汰 -> 命中率下降` 这条因果链。

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Question: 高并发服务器必须利用 8 核且隔离单线程阻塞时，在 N:1 与 1:1 中选哪个，并承担什么主要成本？
- User answer: “N:1；内核需要维护更多 TCB 和上下文。”
- Correct reasoning: 应选 1:1。每个用户线程对应独立内核线程，内核能把多个线程调度到多个核心，且一个线程阻塞不妨碍其他内核线程；代价正是内核要维护更多 TCB/上下文，线程创建、终止和切换开销更大。
- Error type: procedure confusion
- Fix strategy: 固定选型顺序：先把“多核并行/阻塞隔离”翻译成“需要多个内核可见可调度实体”，再由映射关系选择 1:1；暂不重复追问已经答对的成本。

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Question: 非抢占式 FCFS 中，A(t=0, 8s)、B(t=1, 2s)、C(t=2, 1s) 的执行顺序及 B、C 的等待时间是什么？
- User answer: “ABC，B、C 分别在就绪队列等待 8、10。”
- Correct reasoning: 顺序 A-B-C 正确；A 在 t=8 结束，所以 B 于 t=8 开始，等待 `8-1=7` 秒；B 在 t=10 结束，所以 C 于 t=10 开始，等待 `10-2=8` 秒。8 和 10 是开始时刻，不是从各自到达后经过的时长。
- Error type: procedure confusion
- Fix strategy: 固定 `等待时间 = 开始运行时刻 - 到达时刻`，保留已经正确的 FCFS 顺序，只做一次减法修复题。

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Question: RR 中 A/B 同时就绪、所需时间 5/3 秒、时间片 2 秒、初始队列 A→B；写出直到完成的 CPU 进程序列。
- User answer: “B A。”
- Correct reasoning: 初始队首 A 必须先运行。A 运行 2 秒后剩 3 秒，未完成所以回队尾，队列变为 B→A；之后继续按同一规则逐时间片轮转，未完成进程会多次出现，而不是每个进程只出现一次。
- Error type: procedure confusion
- Fix strategy: 暂停完整序列题，先练一次 `取队首 -> 扣除一个时间片 -> 未完成回队尾`，确认队列变化后再逐轮展开。

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Question: 已执行 A→B；当前队列 A→B、A/B 剩余 3/1 秒、时间片 2 秒，写出直到完成的剩余 RR 序列。
- User answer: “B→A，都剩余1秒，然后都完成了，因为时间片2s。”
- Correct reasoning: 当前队首 A 必须先运行 2 秒，A 剩 1 秒并回队尾，队列变为 B→A；B 再运行 1 秒完成并移出；最后 A 运行 1 秒完成。因此剩余序列是 A→B→A，不是 B→A，两个进程也不是同时完成。
- Error type: procedure confusion
- Fix strategy: 用三空时间线强制每轮先读取当前队首，再扣除实际运行时间，最后明确“回队尾”或“完成移出”。

## 2026-07-16

- Topic: OS - 多线程互斥与同步：公平读者—写者
- Question: W 已到达并取得入口 `flag`、随后阻塞在 `wDataMutex`；R2/R3 后到并阻塞在 `flag`。已有读者全部退出后谁先获得数据锁？
- User answer: 正确指出读者优先下后来读者可以加入且 W 可能饥饿，也正确指出公平方案中 R2/R3 阻塞在 `flag`；但认为旧读者退出后由 R2/R3 获得 `wDataMutex`。
- Correct reasoning: W 已经越过入口并持有 `flag`，正在 `wDataMutex` 的等待队列中；R2/R3 尚未越过入口，不能参与数据锁竞争。最后一个旧读者 V 数据锁后，应先唤醒/推进 W；W 写完并 V(flag) 后，后来读者才可继续登记进入。
- Error type: boundary confusion
- Fix strategy: 保留已经正确的饥饿判断和阻塞位置，只追踪两级闸门：`flag` 决定谁已获准进入，`wDataMutex` 决定谁访问数据。

## 2026-07-16

- Topic: OS - 死锁四个必要条件
- Question: 将两线程两锁现场分别映射到互斥、持有并等待、不可剥夺和环路等待。
- User answer: 互斥、不可剥夺和环路等待正确；“持有并等待”写成“A 等 B、B 又等 A”，与环路等待的描述重复。
- Correct reasoning: 持有并等待是单个线程层面的状态——A 请求 `mutex_B` 时仍持有 `mutex_A`，B 请求 `mutex_A` 时仍持有 `mutex_B`；环路等待是多个线程之间的整体关系——A 等 B 持有的锁，同时 B 等 A 持有的锁，依赖首尾闭合。
- Error type: boundary confusion
- Fix strategy: 保留其余三个正确映射，只追问每个线程“等待新锁时仍持有哪把旧锁”，再用一句话对比单线程属性与跨线程闭环。

## 2026-07-16

- Topic: OS - pstack 死锁排查证据边界
- Question: 多次稳定 `pstack` 显示 A、B 都停在 `pthread_mutex_lock` 时，能确认什么、不能确认什么，还缺少哪些关键证据？
- User answer: 正确回答不能直接确认，并指出已观察到 A、B 栈不变且停在 `pthread_mutex_lock`；将缺失证据概括为“进一步观察细节”。
- Correct reasoning: `pstack` 给出长期不变的锁等待位置，只能筛出死锁嫌疑。确认闭环还需查清每个线程具体等待的 mutex 对象，以及该 mutex 当前的 owner，再把关系拼成 `thread -> waited mutex -> owner`。
- Error type: boundary confusion
- Fix strategy: 保留已经掌握的“线索不等于证明”，只用两个填空固定 `waited mutex object` 与 `owner`。

## 2026-07-17

- Topic: OS - 单进程线程上限的地址预算
- Question: 在原文 32 位 Linux 模型中，用户空间约 `3G`、每线程预留约 `10M` 时，只从地址预算能创建多少线程？为什么它不是 Linux 的固定上限？
- User answer: 正确说明在特定模型和栈配置下，地址空间约允许创建 300 个线程；未回答为什么该值会随计算输入和其他系统瓶颈变化。
- Correct reasoning: `约 300` 只来自文中特定的 32 位用户空间与每线程预留之比；OS/位数、用户空间布局或栈大小改变，地址估算就会改变，而且 `threads-max`、`pid_max`、`max_map_count` 与实际机器资源还可能在地址空间之前成为瓶颈。
- Error type: boundary confusion
- Fix strategy: 保留正确的计算，只追问两类变化源：地址公式的输入，以及地址空间之外的更小系统预算。

## 2026-07-17

- Topic: OS - 单进程线程上限全章综合迁移
- Question: 地址预算支持 100 万、`threads-max` 余量 6 万、`pid_max` 余量 5 万、VMA 余量支持 3 万时，哪个预算先限制线程数？只把线程栈从 `8M` 降到 `1M` 是否会提高最终上限？
- User answer: 正确判断约 3 万线程时先撞到 VMA 余量；错误认为减小线程栈会提升 VMA 数量余量。
- Correct reasoning: 减小线程栈改变的是每线程虚拟地址映射的大小，从而扩大地址空间预算；`vm.max_map_count` 限制的是单进程 VMA 数量，参数值并不会因栈变小而自动增加。题设中 VMA 仍是约 3 万线程对应的最小预算，因此只减小栈大小不会提高最终上限。
- Error type: boundary confusion
- Fix strategy: 保留正确的 `min=3万` 判断，只对比“每个映射多大”与“最多允许多少个映射”，再重新判断最小预算是否发生变化。

## 2026-07-17

- Topic: OS - 线程故障类型与影响范围
- Question: Java 工作线程抛出未捕获 `NullPointerException`，以及 C 工作线程非法访存触发默认 `SIGSEGV` 时，分别终止谁、由哪一层处置？
- User answer: 正确判断 Java 场景通常终止当前线程、C 场景终止整个进程，也正确说明 C 场景由 CPU 同步内存异常进入 Linux `SIGSEGV` 默认动作；将 Java 线程的未捕获异常称为“线程正常退出”。
- Correct reasoning: `NullPointerException` 是由 JVM 支撑的 Java 语言级异常机制处理；未被捕获时，当前 Java 线程沿未捕获异常路径异常终止，不是线程函数像 `return` 一样正常完成。JVM 进程通常仍可继续。
- Error type: boundary confusion
- Fix strategy: 保留影响范围和 C 故障链，只追问“处理层 + 正常返回/异常终止”两个字段，不重问已经掌握的 B 场景。

## 2026-07-17

- Topic: OS - 捕捉信号与修复同步内存故障
- Question: 原文忽略 `raise(SIGSEGV)` 后仍能打印，能否推出真实空指针解引用也可安全忽略；若处理器不修复地址和 PC，返回后发生什么？
- User answer: 正确回答“不能”，但认为忽略后会继续执行下一条语句，把软件主动产生信号后的控制流误套到真实同步非法访存。
- Correct reasoning: `raise(SIGSEGV)` 本身没有一条失败后等待重试的内存访问指令，若信号被允许忽略，调用返回后可继续；真实空指针解引用保存的 PC 指向原故障指令，处理器若不修复地址或改写 PC，返回后通常再次执行该指令并再次触发 `SIGSEGV`，不能据此安全继续。
- Error type: boundary confusion
- Fix strategy: 保留已掌握的“不能外推”，只追问处理器返回后的第一条 CPU 指令及其结果。

## 2026-07-17

- Topic: OS - CPU 调度发生点与抢占边界
- Question: A 因等待磁盘从运行态进入等待态；B 因时间片耗尽从运行态回到就绪态。两种情况是否都会触发 CPU 重新选择运行者？哪一种属于抢占，为什么？
- User answer: “否；B 属于抢占；因为时间片耗尽，当前进程仍然可以运行，但 CPU 被强制收回。”
- Correct reasoning: B 的判断和理由正确。A 进入等待态后虽然不是被抢占，但它已经不能继续运行并自然释放 CPU，操作系统仍必须从就绪队列选择下一个运行者。因此 A、B 都会发生调度，只有 B 属于抢占。
- Error type: boundary confusion
- Fix strategy: 保留“B 仍可运行却被强制收回 CPU”这一已掌握边界，只追问 A 释放 CPU 后是否还需要重新选人，以固定“发生调度不等于发生抢占”。

## 2026-07-17

- Topic: OS - SCAN 与 C-SCAN 移动路径
- Question: 磁道范围 0-199，磁头在 53，请求为 14、37、65、67、98，初始方向向大磁道。写出 SCAN 和 C-SCAN 在所有请求完成前的移动路径，包含到达的物理端点；C-SCAN 的 199->0 途中是否服务请求？
- User answer: “SCAN：65 67 98 37 14；C-SCAN：65 67 98 14 37；否。”
- Correct reasoning: 请求服务顺序和“不在 C-SCAN 复位途中服务”都正确，但完整磁头移动路径还必须包含非请求端点。SCAN 为 `65->67->98->199->37->14`；C-SCAN 为 `65->67->98->199->0->14->37`，其中 `199->0` 只是复位。
- Error type: procedure confusion
- Fix strategy: 不重复请求服务顺序，只让学习者在 `98` 与后续请求之间补入 SCAN 的 `199`，以及 C-SCAN 的 `199->0`。

## 2026-07-17

- Topic: OS - 三类调度算法综合选型
- Question: CPU 混合未知长度任务、低成本 LRU 近似、机械磁盘单向均匀且避免物理端点三个场景分别选什么算法？
- User answer: “CPU：MLFQ；页面：LFU；磁盘：C-LOOK。”
- Correct reasoning: CPU 的 MLFQ 与磁盘的 C-LOOK 正确。页面需求强调“接近 LRU”且降低精确链表维护成本，应选 Clock：访问位提供近期访问的粗粒度证据，环形扫描给访问位为 1 的页第二次机会。LFU 统计累计访问频率，优化证据轴不同，并可能受久远历史污染。
- Error type: concept misunderstanding
- Fix strategy: 保留已经正确的 MLFQ 与 C-LOOK，只对比 Clock 的访问位/最近性近似和 LFU 的频率计数，重新选择页面策略。

## 2026-07-17

- Topic: OS - 文件系统名字、dentry、inode 与数据块
- Question: `/docs/a.txt` 和 `/backup/a-link` 是同一个文件的两个硬链接。它们应有几个文件名、几个 dentry、几个 inode、几份文件数据？
- User answer: “两个文件名，一个 dentry，一个 inode，一份文件数据。”
- Correct reasoning: 两个硬链接有两个独立名字，因此各自有目录记录，并可各自形成一个 dentry；这两个 dentry 随后共同指向同一个 inode，最终共享同一份文件数据。正确计数是两个文件名、两个 dentry、一个 inode、一份数据。
- Error type: boundary confusion
- Fix strategy: 保留已经掌握的文件名、inode 和数据份数，只追问“每个名字是否需要自己的名字映射缓存”，不重复整个三层模型。
- Repair result: Correct on 2026-07-17: explicitly drew two distinct dentry paths converging on inode 42 and the same data.

## 2026-07-18

- Topic: OS - FAT 表规模估算
- Question: 200GB 磁盘、1KB 数据块、每个 FAT 表项 4B 时，表项数和 FAT 内存占用是多少？
- User answer: “表项数=200GB/1KB=200000；FAT 大小=200000*4B=800kB”，并正确说明每块一个表项以及大磁盘、小块会增大内存占用。
- Correct reasoning: 按原文十进制量级，`1GB = 1,000,000KB`，所以 `200GB = 200,000,000KB`；除以 `1KB/block` 得 2 亿个表项；乘 `4B` 得 8 亿字节，约 800MB。
- Error type: procedure confusion
- Fix strategy: 保留 FAT 机制与趋势判断，只让学习者补齐 `GB -> KB -> entries -> B -> MB` 单位链，不重问结构原理。
- Repair result: Correct on 2026-07-18: recomputed 200,000,000KB, 200 million entries, and about 800MB.

## 2026-07-18

- Topic: OS - SwapCached 与普通文件后备边界
- Question: 一个匿名页 swap out 后又 swap in；判断进入 SwapCached 的条件，以及它是否变成普通文件内容。
- User answer: “Swap 中的副本仍然保留；它属于某个普通文件。”
- Correct reasoning: 第一部分正确：SwapCached 依赖 Swap 槽中的副本仍保留。第二部分错误：该页仍源自匿名内存，只是拥有 Swap 槽作为磁盘后备；它没有因此变成带普通文件名、inode 和文件数据块身份的文件内容。原文把它纳入广义 Page Cache 是后备关系上的类比。
- Error type: boundary confusion
- Fix strategy: 保留“Swap 副本仍在”这一正确条件，只用两个后备对象作对比：SwapCached 对应 Swap 槽，普通文件页对应命名文件；再追问前者是否拥有普通文件身份。
- Repair result: Correct on 2026-07-18: distinguished the Swap slot from ordinary file data and explicitly denied ordinary filename/inode identity to the SwapCached anonymous page.

## 2026-07-18

- Topic: OS - Direct I/O 与断电持久化的综合迁移
- Question: 在日志追加写的完整时间线中，判断仅改为 `O_DIRECT`、不增加同步语义，能否解决断电持久性问题。
- User answer: “O_DIRECT：能，因为直接写到磁盘持久化了。”
- Correct reasoning: `O_DIRECT` 选择绕过 Page Cache 的数据路径，但不单独提供应用确认点所需的同步、设备刷新与写入顺序保证；`O_DIRECT write` 返回不能替代 `O_SYNC`、`fdatasync/fsync` 或上层恢复协议。题设只要求数据和文件大小时，确认前 `fdatasync(fd)` 是最小匹配；`fsync(fd)` 也安全但同步范围更强。
- Error type: transfer failure
- Fix strategy: 固定两轴填空：`O_DIRECT -> 是否绕过 Page Cache`；`同步语义 -> 是否等待持久化边界`。只重问这一轴，不重复已经正确的数据位置与崩溃时间线。
- Repair result: Correct on 2026-07-18: explicitly assigned `O_DIRECT` to the Page Cache path axis, assigned `fdatasync/fsync/O_SYNC` to the persistence-wait axis, and rejected `O_DIRECT` as a replacement for synchronization.

## 2026-07-18

- Topic: OS - 存储 I/O 三层软件栈
- Question: 将文件系统形成请求、通用块层组织请求、设备驱动完成物理 I/O 三个阶段按因果顺序排列。
- User answer: “C. 文件系统把应用的 read 转换为所需的存储请求；C. 文件系统把应用的 read 转换为所需的存储请求；B. 设备驱动操作控制器完成物理 I/O。”
- Correct reasoning: 正确顺序是 `C -> A -> B`。文件系统先把文件语义转换成存储请求；通用块层在中间排队、合并或调度块请求；设备驱动最后操作控制器完成物理 I/O。
- Error type: procedure confusion
- Fix strategy: 保留已经正确的 C 起点和 B 终点，只追问两者之间缺失的通用块层动作，不重讲三层职责。
- Repair result: Correct on 2026-07-18: identified the missing middle step as “the generic block layer organizes requests” and then reran the complete sequence as `C -> A -> B`.

## 2026-07-18

- Topic: OS - `mmap + write` 的映射与复制边界
- Question: `mmap + write` 相比 `read + write` 消失和保留的 CPU 拷贝分别是哪一条？
- User answer: “消失的 CPU 拷贝：内核态 -> 用户态；仍保留的 CPU 拷贝：Page Cache -> socket 缓冲区。”
- Correct reasoning: “内核态 -> 用户态”描述的是执行权限/控制流边界，不是 payload 数据路径的两个对象。`mmap` 消除的是 `Page Cache（内核读缓冲区） -> 独立用户缓冲区` 的 CPU 数据复制；用户虚拟地址改为映射同一批文件缓存页。`Page Cache -> socket 缓冲区` 的 CPU 复制仍保留。
- Error type: boundary confusion
- Fix strategy: 保留所有正确次数和已正确的保留路径，只要求用 `具体缓冲区 -> 具体缓冲区` 重写消失的那一条边。
- Repair result: Correct on 2026-07-18: rewrote the removed copy as `Page Cache -> tmp_buf`, restoring the data-object boundary without changing the already-correct counts.

## 2026-07-19

- Topic: OS - I/O 多路复用的事件循环阻塞边界
- Question: `20` 个 Socket 已就绪；单个事件循环线程处理第一个 Socket 时同步等待数据库 `5` 秒。这期间能否继续处理其余 `19` 个？
- User answer: “能；因为事件循环处理可以没必要阻塞从而把精力用在其他等待的socket。”
- Correct reasoning: “可以设计成非阻塞”不等于题设当前路径没有阻塞。单个事件循环线程一旦在同步数据库调用中等待，就不能同时执行其余事件处理器；其他 `19` 个就绪项必须等待该调用返回。只有使用异步数据库接口，或先把阻塞任务交给工作线程/进程并立即返回事件循环，事件循环才能继续推进其他 Socket。
- Error type: boundary confusion
- Fix strategy: 固定 A/B 条件表：`事件循环内同步等待 -> 事件循环停住`；`异步提交或转交工作线程 -> 事件循环可继续`。保留已经正确的 Socket/线程/就绪项数量，不重问这些数字。

## 2026-07-19

- Topic: OS - 单事件循环线程的同步阻塞（重复错误）
- Question: A 在事件循环线程里同步等待数据库 5 秒；B 把数据库任务交给工作线程后立即返回事件循环。A、B 各能否继续处理其余 19 个就绪 Socket？
- User answer: “能；能。”
- Correct reasoning: B 能继续，因为数据库等待发生在工作线程，事件循环线程已经返回并可运行其他 handler。A 不能继续，因为系统只有这一条事件循环执行流；它的调用栈停在同步数据库调用上，在返回前没有其他线程替它执行其余 handler。
- Error type: missing prerequisite
- Fix strategy: 暂停多路复用推进，先固定前置机制：`一个线程 -> 一条当前执行流/调用栈`；`同步阻塞 -> 该线程停在调用点`；`无 worker -> 无人执行其余 handler`。只问“谁在执行”，不再让可选优化方案干扰当前事实。
- Repair result: Correct on 2026-07-19: stated that no thread was executing the remaining handlers while the only event-loop thread was synchronously blocked.
- Transfer result: Correct on 2026-07-19: generalized the same single-execution-flow boundary to a five-second CPU-bound handler and explained why the other ready events still wait.

## 2026-07-19

- Topic: OS - select/poll 扫描次数分配
- Question: select 关注 `N=1000` 个 fd、只有 `k=3` 个就绪；一轮等待有几次集合复制，内核和用户各扫描几次，成本围绕 `N` 还是 `k`？
- User answer: “两次；内核和用户各扫描两次；主要重复成本围绕 N。”
- Correct reasoning: 两次集合复制和成本围绕 `N` 都正确。扫描总共只有两次：内核在收到集合后线性扫描一次并标记就绪项；结果集合回到用户态后，用户再线性扫描一次找到就绪 fd。因此是 `内核 1 + 用户 1 = 总共 2`，不是双方各两次。
- Error type: procedure confusion
- Fix strategy: 保留复制次数和 `N` 成本轴，只用三空固定 `内核 1、用户 1、总共 2`。
- Repair result: Correct on 2026-07-19: answered `kernel 1 + user 1 = total 2` without changing the already-correct copy count or `N` cost axis.
- Transfer result: Correct on 2026-07-19: explained that poll changes only the fixed bitmap representation to a dynamic linear array while retaining full-set copying and `O(N)` scanning.

## 2026-07-19

- Topic: OS - ET 读取循环的非阻塞停止条件
- Question: LT/ET 面对剩余未读数据怎样通知，ET handler 应读到什么结果停止，为什么必须使用非阻塞 fd？
- User answer: “会；ET 下不能依赖同一次边沿再次通知，等下一次；ET handler 应读到当前已经读空才停止；为什么必须使用非阻塞 fd？这个我也没理解。”
- Correct reasoning: LT 与 ET 的通知判断基本正确，但 ET 不能在仍有未读数据时等待下一次边沿。应用必须循环读取，并通过数据读空后的额外一次 `read` 探测停止点；阻塞 fd 会在这次探测上等待未来数据并占住事件循环，非阻塞 fd 会立即返回 `EAGAIN/EWOULDBLOCK`，使 handler 安全退出。
- Error type: missing prerequisite
- Fix strategy: 不重讲 LT/ET 定义，只运行 `8KB / 每次 4KB` 的三次读取时间线，对比第三次阻塞等待与第三次立即返回 `EAGAIN`。
- Repair result: Correct on 2026-07-19: stated that the empty third `read` blocks and holds the event-loop thread on a blocking fd, but immediately returns `EAGAIN/EWOULDBLOCK` on a nonblocking fd so the handler can exit.

## 2026-07-19

- Topic: OS - 一致性哈希新节点的局部迁移区间
- Question: 原环为 `A@20 -> B@50 -> C@80`，在 A 和 B 之间加入 `D@40`；哪个坐标区间迁移，从谁迁到谁？
- User answer: “`(A@20, B@50)`；数据从 `B@50` 到 `D@40`。”
- Correct reasoning: 迁移方向 `B -> D` 正确。D 插入后，只对 `(20,40]` 中的 key 成为新的顺时针第一节点，因此只有该区间从 B 迁到 D；`(40,50]` 中的 key 顺时针仍先遇到 B，不迁移。
- Error type: boundary confusion
- Fix strategy: 保留已正确的 `B -> D` 方向，只用 `(20, ___]` 一个空修复迁移区间的右端点。
- Repair result: Correct on 2026-07-19: filled the right endpoint with `40`, producing `(20,40]` and preserving the already-correct `B -> D` migration direction.

## 2026-07-19

- Topic: OS - 虚拟节点数量与硬件权重
- Question: A、B 权重为 1，C 权重为 2；每个权重单位配置 100 个虚拟节点时，三者各应配置多少？
- User answer: “25，25，50；因为 key 的哈希位置具有随机性，而且不同 key 的访问热度可能完全不同。”
- Correct reasoning: 比例 `1:1:2` 和实时请求不保证精确比例的解释都正确。题设给的是“每个权重单位 100 个”，因此应逐节点计算 `weight × 100`，得到 `A=100, B=100, C=200`，而不是把三者总和归一化为 100。
- Error type: procedure confusion
- Fix strategy: 保留已正确的 `1:1:2` 比例和热 key 边界，只填写三个 `weight × 100` 的绝对数量。
- Repair result: Correct on 2026-07-19: answered `100,100,200`, applying the absolute baseline while preserving the correct ratio and hot-key boundary.
