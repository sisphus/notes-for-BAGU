# Review Schedule

Use this file for short, targeted spaced-review prompts.

## Template

```markdown
## YYYY-MM-DD

- Topic: <topic>
- Review timing: <same day / next day / 3 days / 1 week>
- Prompt: <one short recall, boundary, transfer, or diagnosis question>
- Target: <schema or weak boundary being tested>
- Result: Correct after repair on 2026-07-19: 用“从一组被关注的 fd 中返回已就绪事件”给出了适用于 select、poll、epoll 的接口责任，并保持 Reactor dispatch、Handler 主动 `read` 的分层。
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - Reactor 三角色事件路由
- Review timing: same day
- Prompt: 同一轮等待返回监听 fd 可读和已连接 fd 可读时，Reactor 分别 dispatch 给谁？两条路径下一步各调用什么系统调用？
- Target: 用“监听 fd -> Acceptor、连接 fd -> Handler”路由 Reactor 事件
- Result: Correct after repair on 2026-07-19: 已将监听 fd 映射到 `Acceptor -> accept`，并把已连接 fd 的可读事件精确映射到 `Handler -> read`，不再混入后续 `send`。
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - 单 Reactor 单执行实体
- Review timing: same day
- Prompt: A、B 同时可读，唯一事件循环先执行 A 的 200ms 业务。为什么 B 即使已被 epoll 报告就绪，也不能立刻被 dispatch？
- Target: 用“一个事件循环的串行临界路径”判断单 Reactor 单执行实体
- Result: Correct on 2026-07-19: 明确指出唯一线程仍在 Handler A 中，Reactor 无法继续 dispatch，B 即使已就绪也只能等待。
- Next review: 2026-07-26

## 2026-07-19

- Topic: OS - 单 Reactor 多线程
- Review timing: same day
- Prompt: A 的业务计算需 200ms。说明 Handler、Processor 和主 Reactor 在这段路径中的分工，以及计算期间能否继续 dispatch B。
- Target: 用“网络 I/O 留主线程、业务计算进资源池”运行单 Reactor 多线程
- Result: Correct on 2026-07-19: 完整给出 `Handler read -> Processor 业务 -> 结果回 Handler -> send`，并正确判断 200ms 业务期间主 Reactor 可继续 dispatch B。
- Next review: 2026-07-26

## 2026-07-19

- Topic: OS - 多 Reactor 多执行实体
- Review timing: same day
- Prompt: 从新连接到后续可读事件，分别说明 MainReactor、Acceptor、SubReactor、Handler 的动作；响应是否需要回到 MainReactor？
- Target: 用“主 Reactor 接连接、子 Reactor 管连接”运行多 Reactor
- Result: Correct on 2026-07-19: 完整运行 MainReactor 监听、Acceptor `accept`、SubReactor 注册、Handler 在子执行实体内 `read -> 业务 -> send`，并指出响应无需回到 MainReactor。
- Next review: 2026-07-26

## 2026-07-19

- Topic: OS - Reactor 与 Proactor
- Review timing: same day
- Prompt: 一个回调只通知 Socket 可读、另一个回调发生时用户缓冲区已经填好；分别属于哪种模式，实际 I/O 由谁完成？
- Target: 用“就绪事件 vs 完成事件”区分 Reactor 与 Proactor
- Result: Correct on 2026-07-19: 将“可读后应用调用 `read`”分类为 Reactor，将“缓冲区已填好后回调”分类为 Proactor，并正确区分 Handler 主动发起读取与 OS 完成异步 I/O。
- Next review: 2026-07-26

## 2026-07-19

- Topic: OS - Reactor 与 Proactor 整章综合迁移
- Review timing: same day
- Prompt: 为 8 核、50,000 长连接、每活跃请求 30ms CPU 业务的服务器选择 Reactor 变体，运行新连接和可读路径，再说明真正异步 Socket I/O 下怎样改成 Proactor。
- Target: 综合选择 Reactor 变体并迁移到 Proactor
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS × Redis - 线程模型迁移
- Review timing: same day
- Prompt: Redis 开启 I/O threads 后遇到 100ms 的大集合命令。分别说明网络 I/O、协议解析、命令执行由哪些线程完成，以及其他命令是否能并行执行。
- Target: 用“命令执行路径 ≠ 整个进程”分析 Redis 线程模型
- Result: Pending
- Next review: TBD
```

## Default Intervals

- New weak concept: same day.
- Missed again: next day.
- Correct after repair: 3 days.
- Stable: 1 week.

## 2026-07-19

- Topic: OS - Reactor 与 Proactor
- Review timing: same day
- Prompt: `epoll_wait` 返回某已连接 fd 可读后，I/O 多路复用、Reactor 和 Handler 各自负责哪一步？数据是否已经自动进入用户缓冲区？
- Target: 用“就绪发现机制 ≠ 事件协调模式”区分多路复用与 Reactor
- Result: Pending
- Next review: TBD

## 2026-07-12

- Topic: OS - malloc 动态内存分配
- Review timing: same day
- Prompt: `malloc(64)` 成功时，为什么不能说每次都直接执行了一次系统调用？请区分 `malloc`、内存池和 `brk/mmap`。
- Target: 用“库函数 -> 系统调用 -> 地址区域”定位 malloc
- Result: Partially correct on 2026-07-13: allocator-pool reuse versus kernel expansion was correct; immediate physical-page allocation was not addressed.
- Next review: 2026-07-13

## 2026-07-13

- Topic: OS - malloc 动态内存分配
- Review timing: same day repair
- Prompt: `malloc(4096)` 成功但从未访问该区域，能否断言 4096B 已经驻留在物理内存？说明首次访问的作用。
- Target: 区分“获得虚拟空间”与“获得物理页”
- Result: Correct on 2026-07-13: distinguished returned virtual address from resident physical pages and explained the first-touch fault recovery chain.
- Next review: 2026-07-16

## 2026-07-16

- Topic: OS - malloc 动态内存分配
- Review timing: 3 days after repair
- Prompt: `malloc(4096)` 成功但尚未访问时，虚拟地址和物理页分别处于什么状态？首次写入可能发生什么？
- Target: 区分“获得虚拟空间”与“获得物理页”
- Result: Correct after repair on 2026-07-13: named repeated syscalls and renewed first-touch faults for mmap, plus heap fragmentation/high-water retention and return-to-OS behavior for brk.
- Next review: 2026-07-16

## 2026-07-16

- Topic: OS - malloc 动态内存分配
- Review timing: 3 days after repair
- Prompt: 为什么频繁小块不适合每次独立 `mmap/unmap`，而低频大块又不适合长期留在 `brk` heap？分别说明 CPU 和空间成本。
- Target: 用“调用/缺页成本 vs 碎片/驻留成本”理解混合策略
- Result: Pending
- Next review: TBD

## 2026-07-20

- Topic: OS - malloc 内存分配器内部实现
- Review timing: 1 week
- Prompt: 对 `[16/1][32/0][0/1]`，说明如何从第一块走到第二块、如何判断第二块空闲，以及在哪里停止遍历。
- Target: 用块内 header 跟踪状态并隐式遍历 heap
- Result: Correct on 2026-07-13: used current header plus block size, decoded the free bit, and recognized the heap-end sentinel.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - malloc 内存分配器内部实现
- Review timing: 1 week
- Prompt: 空闲块为 `[8B, 32B, 12B, 24B]`，请求 `10B`，Next Fit 从 `24B` 开始。First、Next、Best Fit 各选哪块，哪个必须全扫描？
- Target: 用“搜索起点 + 停止条件”比较 First/Next/Best Fit
- Result: Correct on 2026-07-13: selected 32B, 24B, and 12B respectively and identified Best Fit's full scan.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - malloc 内存分配器内部实现
- Review timing: 1 week
- Prompt: `32B` 空闲块、`4B` header、`12B` payload 请求：不切分会产生多少块内未用空间？切分后两块总大小各是多少？
- Target: 用“payload + header + 余量”决定是否切分大块
- Result: Correct on 2026-07-13: calculated 16B internal waste and a 16B allocated plus 16B free split.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - malloc 内存分配器内部实现
- Review timing: 1 week
- Prompt: 两个相邻 `16B` 块释放后马上有 `20B` 请求，与反复 `free(12)->malloc(12)` 这两种负载，分别更适合立即还是延迟合并？为什么？
- Target: 用“连续大块需求 vs 重复合并开销”选择合并时机
- Result: Correct on 2026-07-13: chose immediate coalescing for the large contiguous request and deferred coalescing for same-size reuse.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - malloc 内存分配器内部实现
- Review timing: 1 week
- Prompt: 当前 header 地址为 `C`，`C-4` 处 footer 记录前块大小 `32B`。前块 header 在哪里？为什么仅当前 header 不足以反向定位？
- Target: 用 footer 边界标记从当前块反向定位前一块
- Result: Correct on 2026-07-13: computed `C-32`, allowed coalescing, and identified the missing previous-size information in the current header.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - malloc 内存分配器内部实现
- Review timing: 1 week
- Prompt: 设计一个基本动态内存分配器时，除了“找到空闲块”，还必须依次回答哪四类问题？
- Target: 用“表示 -> 选择 -> 切分 -> 合并”拆解分配器
- Result: Correct on 2026-07-13: recalled all four decisions in source order.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - malloc 动态内存分配
- Review timing: 1 week
- Prompt: `free(p)` 没有长度参数时如何找到块大小？为什么向 `p` 前方越界写可能直到后续 `free` 才爆发？
- Target: 用“用户指针前的块头”解释 free 如何知道大小
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 两个进程都访问虚拟地址 `0x1000`。为什么它们通常不会访问同一物理位置？请分别说明操作系统维护的映射与 MMU 的职责。
- Target: 用“进程页表 + MMU 映射”解释地址隔离
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 某段基址为 `4000`、段界限为 `600`。偏移 `700` 能否转换成物理地址 `4700`？说明检查顺序。
- Target: 用“段号查表 + 偏移验界”完成内存分段转换
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 总空闲内存为 `300MB`，但最大的连续空洞只有 `120MB`。一个需要连续 `200MB` 的段能否装入？这是什么碎片？
- Target: 用“总空闲量 vs 最大连续空洞”识别外部碎片
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 为什么把一个 `600MB` 段整体换出通常比分页方式只换出几个冷页更慢？请同时说明存储设备速度和搬运粒度。
- Target: 用交换粒度解释分段换出为何容易卡顿
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 页大小为 `4KB`，程序需要 `13KB`。需要分配几个页、共多少 KB、浪费多少 KB？属于什么碎片？
- Target: 用固定页框消除外部碎片并识别内部碎片
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 页大小为 `4KB`，虚拟页 `9`、偏移 `200B` 映射到物理页框 `4`。写出转换后的两个字段和物理字节地址。
- Target: 用“替换页号、保留偏移”完成分页地址转换
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 一个合法虚拟页当前不在物理内存时，为什么缺页异常不等于段错误？按顺序写出内核的恢复动作。
- Target: 用“访问是否合法 + 页面是否在场”判断缺页结果
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 32 位地址、4KB 页、每个 PTE 4B 时，一个单级页表多大？50 个进程的页表约占多少内存？
- Target: 用“地址空间 ÷ 页大小 × PTE 大小”估算单级页表
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 一级表占 4KB，若只创建完整 4MB 二级表空间的 10%，总占用约多少？节省来自什么条件？
- Target: 用“顶级全覆盖、下级按需创建”理解多级页表
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: TLB 未命中，但 page walk 找到在场且权限合法的 PTE。是否发生缺页异常？接下来发生什么？
- Target: 用“TLB 命中/未命中”分流地址转换
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 不考虑 TLB，段页式地址包含哪三个字段？为什么一次实际数据读取被源文计为三次内存访问？
- Target: 用“段号 → 页表 → 页框”追踪段页式地址
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 写出 x86 上从逻辑地址到物理地址的两级转换链，并说明 Linux 把段基址设为 0 的目的。
- Target: 用“逻辑地址 → 线性地址 → 物理地址”解释 Linux x86
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 为什么不同进程的相同用户虚拟地址可映射到不同物理页，而它们的内核虚拟地址通常映射公共内核物理内存？用户态权限边界是什么？
- Target: 区分每进程私有用户映射与共享内核映射
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 虚拟内存
- Review timing: 1 week
- Prompt: 按低地址到高地址写出 text、data、bss、heap、file mapping、stack，并说明 malloc/mmap 的典型区域与堆栈增长方向。
- Target: 用“静态区域 → 动态区域 → 栈”定位 Linux 用户空间
- Result: Pending
- Next review: TBD

## 2026-07-12

- Topic: OS - Linux 软中断
- Review timing: same day
- Prompt: 硬中断打断一个正在运行的进程时，为什么应说它“被中断/暂停执行”，而不能直接说它进入“阻塞态”？
- Target: 区分硬中断造成的执行暂停与进程等待事件造成的阻塞
- Result: Correct after repair on 2026-07-12
- Next review: 2026-07-15

## 2026-07-15

- Topic: OS - Linux 软中断
- Review timing: 3 days
- Prompt: 一个运行中的用户进程被硬中断打断时，它是否必然进入阻塞态？请区分“执行暂停”和“等待事件”。
- Target: 区分硬中断造成的执行暂停与进程等待事件造成的阻塞
- Result: Pending
- Next review: TBD

## 2026-07-12

- Topic: OS - Linux 软中断
- Review timing: same day
- Prompt: `/proc/softirqs` 两次采样间隔 4 秒，某计数从 20,000 增至 28,000。累计值、区间增量和每秒速率分别是多少？
- Target: 区分 softirq 累计计数、采样增量与当前变化速率
- Result: Correct after repair on 2026-07-12
- Next review: 2026-07-15

## 2026-07-15

- Topic: OS - Linux 软中断
- Review timing: 3 days
- Prompt: `/proc/softirqs` 两次采样间隔 5 秒，NET_RX 从 300,000 增至 350,000。写出区间增量和每秒速率。
- Target: 区分 softirq 累计计数、采样增量与当前变化速率
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - Linux 软中断
- Review timing: 1 week
- Prompt: 某台机器只有 CPU0 的 NET_RX 速率显著偏高。为什么不能仅凭这一点断定故障？说出一种合理倾斜原因和一项后续证据。
- Target: 把 softirq 的 CPU 分布不均当成诊断线索而非故障结论
- Result: Pending
- Next review: TBD

## 2026-07-12

- Topic: OS - 存储器层次结构
- Review timing: same day
- Prompt: 哪一级 Cache 在本文中明确分成指令缓存和数据缓存？L2 与它的常见组织差异是什么？
- Target: 区分 L1 的指令/数据分离与 L2 的每核私有
- Result: Correct after repair on 2026-07-12
- Next review: 2026-07-15

## 2026-07-15

- Topic: OS - 存储器层次结构
- Review timing: 3 days
- Prompt: L1、L2、L3 中，哪一级明确分成指令/数据缓存，哪一级通常多核共享？若在 L3 命中，是否还访问内存？
- Target: 区分 L1/L2/L3 的常见组织与命中停止规则
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 存储器层次结构
- Review timing: 1 week
- Prompt: 从寄存器一路到硬盘时，访问速度、容量、单位容量成本分别如何变化？
- Target: 用速度、容量、成本三项取舍定位存储层级
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 存储器层次结构
- Review timing: 1 week
- Prompt: SRAM 的“静态”和 DRAM 的“动态”分别描述什么？两者断电后是否保留数据？
- Target: 用刷新需求区分 SRAM 与 DRAM
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 存储器层次结构
- Review timing: 1 week
- Prompt: 一个数据只在 SSD 中，第一次供 CPU 运算时依次经过哪些层？为什么不能从 SSD 直接进入 L1？
- Target: 用相邻层与缓存命中解释完整数据流
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 存储器层次结构
- Review timing: 1 week
- Prompt: L1 为 `2 ns`、某 SSD 为 `200 μs` 时，SSD 延迟约为 L1 的多少倍？写出换算过程。
- Target: 统一时间单位再比较存储延迟
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - CPU 是如何执行程序的
- Review timing: 3 days
- Prompt: 在 `1 + 2` 中，请分别说出 `+`、控制单元、运算单元的职责。
- Target: 区分运算符指令、指令识别者和指令执行者
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - CPU 是如何执行程序的
- Review timing: 1 week
- Prompt: 某智能音箱接收语音、保存数据、执行识别并播放结果，请将这些动作映射到冯诺依曼模型的部件。
- Target: 用冯诺依曼模型定位每个部件的责任
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - CPU 是如何执行程序的
- Review timing: 3 days
- Prompt: CPU 要把数字 `3` 写入地址 `0x208` 时，地址总线、控制总线和数据总线分别承载什么？
- Target: 用三类总线解释一次内存读写
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - CPU 是如何执行程序的
- Review timing: 1 week
- Prompt: 如果地址总线是 20 位，按字节寻址时理论寻址空间是多少？
- Target: 用地址总线位宽推出可表示的地址数
- Result: Pending
- Next review: TBD

## 2026-07-11

- Topic: OS - CPU 是如何执行程序的
- Review timing: same day
- Prompt: 16 位地址总线按字节寻址时，理论寻址空间是多少 KB？
- Target: 区分“位数本身占多少字节”与“这些位能编码多少个地址”
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - CPU 是如何执行程序的
- Review timing: 3 days
- Prompt: 64 位 CPU 配 40 位地址总线，按字节寻址时，一次适合处理的数据宽度和理论寻址空间分别由什么决定？
- Target: 区分 CPU 位宽和地址总线位宽
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - CPU 是如何执行程序的
- Review timing: 3 days
- Prompt: 16 位 CPU 对两个 32 位整数做加法时，如何拆分运算，低位进位如何处理？
- Target: 用 CPU 位宽判断一次能否完成整数运算
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - CPU 是如何执行程序的
- Review timing: 1 week
- Prompt: 一个程序只处理 24 位整数，从 32 位 CPU 换成 64 位 CPU 时，能否仅根据位宽断言性能会明显提升？为什么？
- Target: 根据数据宽度判断 64 位 CPU 是否有直接优势
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - CPU 是如何执行程序的
- Review timing: 3 days
- Prompt: PC 当前是 `0x200`，下一条指令长 8 字节。取指后 PC 是多少，当前指令放在哪个寄存器？
- Target: 用程序计数器和指令寄存器解释指令周期
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - CPU 是如何执行程序的
- Review timing: 1 week
- Prompt: 请把 `b = 4 + 5` 拆成两次 `load`、一次 `add` 和一次 `store`，并标出源/目标寄存器与最终数据地址。
- Target: 把高级语言加法拆成 `load -> load -> add -> store`
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - CPU 是如何执行程序的
- Review timing: 1 week
- Prompt: 将汇编指令转换为机器码，以及 CPU 从机器码恢复操作含义，分别是什么过程？
- Target: 区分指令的编码和解码方向
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - CPU 是如何执行程序的
- Review timing: 1 week
- Prompt: 在 MIPS R 型 `sub R3, R4 -> R5` 中，`rs/rt/rd` 各表示什么，哪类字段用来区分 `sub` 与 `add`？
- Target: 用 MIPS R 型字段区分寄存器角色和具体操作
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - CPU 是如何执行程序的
- Review timing: 1 week
- Prompt: 将“指令从内存进入 IR”、“解析 opcode”、“ALU 相加”、“结果写回”映射到 Fetch/Decode/Execute/Store 和负责部件。
- Target: 把程序执行看成取指、译码、执行、写回的循环
- Result: Pending
- Next review: TBD

## 2026-07-11

- Topic: OS - CPU 是如何执行程序的
- Review timing: same day
- Prompt: 函数调用指令需跳到新地址时，目标地址应更新到 PC 还是 IR？另一个寄存器此时保存什么？
- Target: 区分跳转时 PC 和 IR 的职责
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - CPU 是如何执行程序的
- Review timing: 3 days
- Prompt: 请分类 `load`、`add`、函数调用跳转、`trap`、`nop`，并说明每类指令主要改变什么。
- Target: 通过改变什么识别指令类型
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU 是如何执行程序的
- Review timing: 1 week
- Prompt: 某优化使指令数减少 20%，CPI 和周期时间不变；另一优化使 CPI 减少 20%，其他不变。两者分别如何影响 CPU 时间？
- Target: 用 `指令数 x CPI x 时钟周期时间` 定位 CPU 性能优化入口
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU 是如何执行程序的
- Review timing: 1 week
- Prompt: 说明 64 位系统运行 32 位程序需要哪些条件，以及 32 位 CPU 为什么不能原生运行 64 位程序。
- Target: 用 ISA 执行模式和 ABI 理解 32/64 位软件兼容性
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 1 week
- Prompt: 若一个元素为 `8 B`、Cache Line 为 `64 B`，一条线能容纳几个元素？为什么连续遍历通常比跨大步长遍历命中率高？
- Target: 用 Cache Line 容量、连续布局和访问步长预测数据缓存命中率
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 1 week
- Prompt: 块 7 和块 23 都映射到同一 Cache Line；该行 `Valid = 1` 且 Tag 属于块 7。访问块 23 是否命中？按判断顺序说明原因。
- Target: 用 Index、Valid、Tag 与 Offset 判断直接映射 Cache 命中
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 1 week
- Prompt: 两段程序顺序访问相同数组，但一段条件结果随机、另一段条件结果呈长串规律。性能差异主要来自哪种机制？预测错误的主要代价是什么？
- Target: 区分数据缓存局部性与分支预测
- Result: Pending
- Next review: TBD

## 2026-07-12

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: same day
- Prompt: 为什么线程已有热 L1/L2 也不能推出“一律绑核”？请用一个收益与一个代价给出条件式结论。
- Target: 区分 CPU 亲和性的缓存复用收益与负载均衡代价
- Result: Correct after repair on 2026-07-12
- Next review: 2026-07-15

## 2026-07-15

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 3 days
- Prompt: 一个计算密集线程的热点数据在核心 0 的 L1/L2 中，但核心 0 已过载、核心 1 空闲。请用条件式而非绝对结论判断是否绑核。
- Target: 比较 CPU 亲和性的缓存复用收益与调度、负载均衡代价
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 1 week
- Prompt: 写回 + Write Allocate 下，目标块写缺失且目标行保存脏块。请说明为何不能直接覆盖，并写出写回旧块到标记新块 Dirty 的顺序。
- Target: 区分写直达、写回、Dirty 与脏行替换流程
- Result: Pending
- Next review: TBD

## 2026-07-12

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: same day
- Prompt: 写入串行化要求某个预先指定的顺序，还是只要求所有核心观察到同一个顺序？请用 `100/200` 两次写解释。
- Target: 区分共同认可的写入顺序与预先指定的固定顺序
- Result: Correct after repair on 2026-07-12
- Next review: 2026-07-15

## 2026-07-15

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 3 days
- Prompt: A、B 对同一变量各写一次。什么叫写传播，什么叫写入串行化？若所有核心都看到 `B -> A`，是否满足串行化？
- Target: 区分写传播、共同写入顺序与预先指定顺序
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 1 week
- Prompt: 总线嗅探如何处理无关与相关 Cache Line？为什么广播监听之外还需要仲裁以及一致性协议状态？
- Target: 区分总线嗅探的通信作用与写入排序、唯一所有权规则
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 1 week
- Prompt: 给出四条 Cache Line：唯一且脏、唯一且干净、可能共享且干净、不可使用。分别判断 M/E/S/I，并说明哪些状态本地写前无需使其他副本失效。
- Target: 用有效性、唯一性和 Dirty 属性定位 MESI 四态
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 1 week
- Prompt: A/B 从 I/I 开始，依次执行 A 读、B 读、A 写、A 再写。写出每一步状态，并说明哪一步需要失效广播、哪一步不需要。
- Target: 用本地读写与远端请求驱动 MESI 状态转换
- Result: Pending
- Next review: TBD

## 2026-07-12

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: same day
- Prompt: “同一地址永久读旧副本”“普通 `i++` 丢失更新”“不同地址可见顺序异常”分别属于 coherence、atomicity 还是 memory ordering？
- Target: 区分缓存一致性、复合操作原子性与跨地址内存顺序
- Result: Correct after repair on 2026-07-12
- Next review: 2026-07-15

## 2026-07-15

- Topic: OS - CPU Cache 性能优化与 MESI 缓存一致性
- Review timing: 3 days
- Prompt: 请分类并解释：同一地址旧副本、普通 `i++` 丢失更新、不同地址观察顺序异常；分别需要哪类机制处理？
- Target: 区分 coherence、atomicity 与 memory ordering
- Result: Pending
- Next review: TBD

## 2026-07-15

- Topic: OS - CPU 是如何执行任务的
- Review timing: 3 days
- Prompt: 核心 0 写 A、核心 1 写 B，A/B 位于同一 Cache Line。说明另一核副本状态和写所有权如何变化，并给出一种布局修复。
- Target: 用 Cache Line 粒度诊断伪共享并隔离热点写
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU 是如何执行任务的
- Review timing: 1 week
- Prompt: 一个进程有主线程和两个工作线程。调度器看到几个任务？为什么它们能独立调度却仍属于同一进程？
- Target: 用 task_struct 统一理解进程和线程的调度对象
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU 是如何执行任务的
- Review timing: 1 week
- Prompt: 先说明 Deadline、Realtime、Fair 的类间顺序，再比较相同实时优先级下 SCHED_FIFO 与 SCHED_RR 的区别。
- Target: 区分类间优先级与类内调度策略
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU 是如何执行任务的
- Review timing: 1 week
- Prompt: 两个普通任务实际运行时间相同但 weight 分别为 1024 和 2048。谁的 vruntime 增长更慢，经典 CFS 为什么会让它获得更多 CPU 时间？
- Target: 用 vruntime 和 weight 理解经典 CFS 的加权公平
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU 是如何执行任务的
- Review timing: 1 week
- Prompt: CPU 0 的 dl_rq/rt_rq 为空，经典 cfs_rq 中三个任务 vruntime 为 9、4、13。下一候选是谁？该选择属于哪个 CPU 的 rq？
- Target: 用每 CPU 运行队列和类队列定位下一任务
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - CPU 是如何执行任务的
- Review timing: 1 week
- Prompt: 一个任务只想增加普通 CPU 份额，另一个任务需要 SCHED_FIFO。分别选择 nice/renice 还是 chrt，并说明 nice=-20 为什么仍不是实时任务。
- Target: 区分 nice/renice 的普通任务调权与 chrt 的实时策略切换
- Result: Pending
- Next review: TBD

## 2026-07-20

- Topic: OS - malloc 动态内存分配
- Review timing: 1 week
- Prompt: 文中 `malloc(1)` 的实验观察到 `132KB` heap 范围。请分别说明用户请求量、分配器批量扩容和物理驻留量为什么不能画等号。
- Target: 区分“用户请求大小”与“分配器/操作系统实际批量”
- Result: Pending
- Next review: TBD

## 2026-07-20

- Topic: OS - malloc 动态内存分配
- Review timing: 1 week
- Prompt: A 来自 `brk` heap 内存池，B 来自独立匿名 `mmap`。`free(A/B)` 后用户权限相同吗？它们的底层空间通常分别如何处理？
- Target: 先判断来源路径，再判断 free 是否归还 OS
- Result: Pending
- Next review: TBD

## 2026-07-13

- Topic: OS - malloc 动态内存分配
- Review timing: same day repair
- Prompt: 全部使用 `mmap` 会反复付出哪两类 CPU 成本？全部使用 `brk` 又容易造成哪两类空间/归还问题？
- Target: 用“调用/缺页成本 vs 碎片/驻留成本”理解混合策略
- Result: Pending
- Next review: TBD

## 2026-07-13

- Topic: OS - Linux 内存回收
- Review timing: same day
- Prompt: `malloc` 成功后的首次写入遇到无空闲物理页时，请按顺序写出缺页处理、后台回收、直接回收和 OOM，并指出哪一步阻塞申请进程。
- Target: 用“首次触页 → 回收 → OOM”追踪物理页申请
- Result: Correct after repair on 2026-07-13: identified that direct reclaim runs synchronously in the applicant's allocation path and therefore blocks it.
- Next review: 2026-07-16

## 2026-07-16

- Topic: OS - Linux 内存回收
- Review timing: 3 days after repair
- Prompt: 从首次触页开始，写出 `kswapd → direct reclaim → OOM` 的升级条件，并指出哪一步阻塞申请进程。
- Target: 用“首次触页 → 回收 → OOM”追踪物理页申请
- Result: Pending
- Next review: TBD

## 2026-07-13

- Topic: OS - Linux 内存回收
- Review timing: same day
- Prompt: 干净文件页、脏文件页和开启 Swap 时的匿名页，分别通过什么动作释放物理页？
- Target: 先按后备载体区分文件页与匿名页的回收动作
- Result: Correct on 2026-07-13: directly dropped the clean file page, wrote back the dirty file page before release, and swapped out the anonymous page with later swap-in.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - Linux 内存回收
- Review timing: 1 week
- Prompt: 为什么干净文件页可以直接丢弃，而脏文件页和匿名页不能？分别写出恢复来源和回收动作。
- Target: 先按后备载体区分文件页与匿名页的回收动作
- Result: Pending
- Next review: TBD

## 2026-07-13

- Topic: OS - Linux 内存回收
- Review timing: same day
- Prompt: `inactive_list` 尾部、`inactive_list` 头部和 `active_list` 中的页，谁应优先成为回收候选？`inactive` 是否等于已经释放？
- Target: 用“页类型 × 活跃度”筛选 LRU 回收候选
- Result: Correct on 2026-07-13: selected the `inactive_list` tail as the coldest candidate and kept candidate selection separate from the page-type-specific reclaim action.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - Linux 内存回收
- Review timing: 1 week
- Prompt: 一个页刚进入 `inactive_list`，另一个页位于该链表尾部。谁更应优先成为候选？选中后还必须判断哪些页面属性？
- Target: 用“页类型 × 活跃度”筛选 LRU 回收候选
- Result: Pending
- Next review: TBD

## 2026-07-13

- Topic: OS - Linux 内存回收
- Review timing: same day
- Prompt: 抖动期间 `pgscand/s` 显著高于 `pgscank/s` 时，优先怀疑哪种回收路径？还需要哪些相关证据才能形成根因结论？
- Target: 用 `pgscank/pgscand/pgsteal` 定位回收型抖动
- Result: Correct on 2026-07-13: prioritized direct reclaim from high `pgscand/s` and required time-correlated latency, I/O, Swap, reclaim-effectiveness, and alternative-cause evidence.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - Linux 内存回收
- Review timing: 1 week
- Prompt: 抖动时 `pgscand/s` 很高、`pgscank/s` 很低。分别解释两个指标，并说出形成根因结论还需要的两项证据。
- Target: 用 `pgscank/pgscand/pgsteal` 定位回收型抖动
- Result: Pending
- Next review: TBD

## 2026-07-13

- Topic: OS - Linux 内存回收
- Review timing: same day
- Prompt: 减少匿名页 Swap 倾向与更早启动 `kswapd` 分别应调整哪个参数？说明方向和一个代价。
- Target: 区分“回收对象倾向”与“后台回收触发时机”
- Result: Correct on 2026-07-13: lowered `swappiness`, raised `min_free_kbytes`, and identified the reduced application-usable memory and excessive-reservation OOM risk.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - Linux 内存回收
- Review timing: 1 week
- Prompt: 一个系统匿名页换出过多，另一个系统因回收启动过晚而频繁 direct reclaim。分别选择参数、调整方向，并说明 `min_free_kbytes` 调大后的代价。
- Target: 区分“回收对象倾向”与“后台回收触发时机”
- Result: Pending
- Next review: TBD

## 2026-07-13

- Topic: OS - Linux 内存回收
- Review timing: same day
- Prompt: NUMA 机器全局有空闲页但本地 Node 紧张时，为什么文章建议一般令 `zone_reclaim_mode=0`，先使用其他 Node 空闲页而不是坚持本地回收？
- Target: 在 NUMA 上比较“远端内存”与“本地回收”成本
- Result: Correct on 2026-07-13: chose remote free memory with mode 0 and compared its stable latency penalty against local reclaim scanning, I/O, blocking, and tail latency.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - Linux 内存回收
- Review timing: 1 week
- Prompt: 全机空闲内存充足但单个 NUMA Node 紧张时，为什么只看全局 free 不够？比较远端分配与本地 direct reclaim 的成本，并给出文章建议的模式值。
- Target: 在 NUMA 上比较“远端内存”与“本地回收”成本
- Result: Pending
- Next review: TBD

## 2026-07-13

- Topic: OS - Linux 内存回收
- Review timing: same day
- Prompt: 使用文章简化公式计算两个进程的 OOM 分数，并说明 `oom_score_adj` 调低改变了什么、没有改变什么。
- Target: 用“物理页占用 + `oom_score_adj`”理解 OOM 牺牲者
- Result: Correct on 2026-07-14: calculated A as 300,000 and B as 600,000 and selected B as the higher-scoring OOM victim.
- Next review: 2026-07-21

## 2026-07-21

- Topic: OS - Linux 内存回收
- Review timing: 1 week
- Prompt: 两个进程的物理页占用与 `oom_score_adj` 不同。用文章公式比较分数，并解释为什么给泄漏业务设 `-1000` 可能伤害其他进程。
- Target: 用“物理页占用 + `oom_score_adj`”理解 OOM 牺牲者
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - Linux 内存回收
- Review timing: same day
- Prompt: 从首次触页开始，串联 watermark、两类回收、页面动作、`sar -B`、NUMA 和 OOM 评分，解释一次完整内存紧张事件。
- Target: 从首次触页串联到回收诊断、调参与 OOM
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: same day
- Prompt: 一个容量为 5 的 LRU，当前从头到尾是 `[1, 2, 3, 4, 5]`。先访问已命中的 `3`，再访问未命中的 `8`。最终链表从头到尾是什么？第二步淘汰了哪一页？
- Target: 用“命中搬头、未命中入头淘尾”运行传统 LRU
- Result: Correct on 2026-07-14: produced `[8, 3, 1, 2, 4]` and identified page `5` as the eviction victim.
- Next review: 2026-07-21

## 2026-07-21

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: 1 week
- Prompt: 容量为 4 的传统 LRU 从头到尾为 `[A, B, C, D]`。依次访问 `C`、`E`，写出每一步结果并指出淘汰页。
- Target: 用“命中搬头、未命中入头淘尾”运行传统 LRU
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: same day
- Prompt: 应用只请求 `4KB`，内核却一次顺序读取 `16KB` 到 Page Cache。若后续四个页都被访问，预读带来什么收益？若只有第一个页被访问，另外三个页属于什么现象，并会怎样伤害传统 LRU？
- Target: 用“预读收益 vs 未被使用”诊断预读失效
- Result: Incomplete on 2026-07-14: correctly identified the cache-hit/I/O benefit and read-ahead failure, but omitted the traditional-LRU hot-page eviction chain.
- Next review: same-day repair

## 2026-07-14

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: same-day repair
- Prompt: 三个未被访问的预读页若按传统 LRU 放到链表头部，缓存满时会把链表哪一端的什么页淘汰？这会让缓存命中率怎样变化？
- Target: 把未使用预读页连接到热点页淘汰与命中率下降
- Result: Correct after repair on 2026-07-14: identified the LRU tail's real hot page as the eviction victim and connected it to a lower cache hit rate.
- Next review: 2026-07-17

## 2026-07-17

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: 3 days after repair
- Prompt: 应用请求一个页，系统额外预读三个页，但后三页从未被访问。请解释传统 LRU 为什么可能因此淘汰热点，并给出最终性能后果。
- Target: 用“预读收益 vs 未被使用”诊断预读失效
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: same day
- Prompt: 页 `20` 刚被预读、尚未被应用真正访问。按文章模型，Linux 与 InnoDB 分别把它放到哪里？为什么这样不会直接挤占热区？
- Target: 用“冷区准入”隔离尚未验证的预读页
- Result: Correct on 2026-07-14: mapped the page to Linux `inactive list` head and InnoDB `old` head, and explained that neither directly occupies `active/young`.
- Next review: 2026-07-21

## 2026-07-21

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: 1 week
- Prompt: 一个尚未被应用访问的预读页，在 Linux 与 InnoDB 中分别进入哪里？若一直未被访问，它为什么会比真正热点更早淘汰？
- Target: 用“冷区准入”隔离尚未验证的预读页
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: same day
- Prompt: 某条 SQL 最终只返回 `3` 行，却因全表扫描访问了 `100 万` 个数据页。这属于预读失效还是缓存污染？为什么结果集很小仍然可能把原热点页挤出 `young` 区？
- Target: 用“确实访问一次，但未必再用”诊断缓存污染
- Result: Correct on 2026-07-14: diagnosed cache pollution, used scanned-page volume rather than result-set size, and connected repeated `active/young` insertion to original-hot-page eviction.
- Next review: 2026-07-21

## 2026-07-21

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: 1 week
- Prompt: 场景 A 的相邻预读页从未被访问；场景 B 的全表扫描页都被访问一次但以后不再使用。分别命名问题，并说明为什么 B 即使只返回几行也可能淘汰热点。
- Target: 用“确实访问一次，但未必再用”诊断缓存污染
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: same day
- Prompt: 按文章模型，Linux 中的页在第几次访问时从 `inactive` 晋升 `active`？InnoDB 的页第一次访问后，分别在 `200ms` 和 `2s` 后再次访问，哪一种会从 `old` 晋升 `young`？
- Target: 用“访问次数 + 时间间隔”提高热区晋升门槛
- Result: Correct on 2026-07-14: identified Linux second-access promotion and selected the InnoDB `2s` second access, not `200ms`, under the article's default one-second threshold.
- Next review: 2026-07-21

## 2026-07-21

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: 1 week
- Prompt: 一个 InnoDB old 页第一次访问后分别可能在 `300ms` 或 `3s` 后再次访问。判断晋升结果，并对比 Linux 的晋升门槛。
- Target: 用“访问次数 + 时间间隔”提高热区晋升门槛
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - 改进 LRU：预读失效与缓存污染
- Review timing: same day integrated transfer
- Prompt: 系统先预读 `3` 个相邻页，其中 `2` 个从未被访问；随后执行一次全表扫描，`100 万` 个页各访问一次。请用 5–7 句话说明：两种现象分别是什么；Linux 与 InnoDB 的新页先进入哪里；各自怎样判断是否晋升热区；以及为什么这种设计既保留预读收益又保护热点。
- Target: 用“冷区准入 + 晋升门槛”完整解释改进 LRU
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: Linux 虚拟内存管理：从进程地址空间到物理内存访问
- Review timing: same day
- Prompt: 一个线程要访问虚拟地址 `0x7f...`，请补全 `task_struct -> ____ -> ____`，并说明后两个对象分别管理整张地址空间还是单个连续区域。
- Target: 用 `task_struct -> mm_struct -> VMA` 定位内核管理层级
- Result: Pending
- Next review: TBD

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 同一个播放器可执行文件被启动两次；单核 CPU 在两个运行实例间快速交替。磁盘上有几份程序文件，运行中有几个进程，这属于并发还是并行？
- Target: 用“静态配方 -> 运行实例 -> 执行流”区分程序、进程和线程
- Result: Correct on 2026-07-14: identified one static program file, two running process instances, and concurrency rather than parallelism on a single core.
- Next review: 2026-07-21

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 进程 A 的时间片刚用完；进程 B 正在等待磁盘数据。分别判断 A、B 是就绪态还是阻塞态，并指出若立即获得 CPU，谁能继续执行。
- Target: 用“能不能立刻运行 + 是否在内存”判断进程状态
- Result: Correct on 2026-07-14: classified A as ready after its time slice and B as blocked while waiting for disk data; correctly identified only A as immediately runnable if given CPU.
- Next review: 2026-07-21

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 一个新进程创建并初始化完成后等待 CPU；首次被调度后时间片耗尽；之后再次被调度并正常结束。写出从创建态开始的完整六状态序列。
- Target: 用“能不能立刻运行 + 是否在内存”判断进程状态
- Result: Correct on 2026-07-14: produced `创建 -> 就绪 -> 运行 -> 就绪 -> 运行 -> 结束`, preserving both scheduler-mediated transitions after creation and time-slice expiry.
- Next review: 2026-07-21

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 进程 P 正在等待磁盘 I/O，同时其物理内存已被换出到硬盘。分别判断其运行条件状态、驻留状态，以及两个维度组合后的状态名称。
- Target: 用“能不能立刻运行 + 是否在内存”判断进程状态
- Result: Correct on 2026-07-14: classified P as blocked on the execution-condition dimension, suspended on the residency dimension, and therefore blocked-suspended overall.
- Next review: 2026-07-21

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 进程 P 时间片耗尽后，寄存器和程序计数器保存在哪里，状态改成什么，它的 PCB 应进入哪个队列？
- Target: 用“PCB + 状态队列”把进程状态变成可调度结构
- Result: Correct on 2026-07-14: saved registers and PC in the PCB, changed P to ready, and placed its PCB in the ready queue after time-slice expiry.
- Next review: 2026-07-21

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 将 PID、当前状态、打开文件列表、寄存器和程序计数器，分别匹配到 PCB 的描述信息、控制管理信息、资源清单和 CPU 相关信息。
- Target: 用“PCB + 状态队列”把进程状态变成可调度结构
- Result: Correct on 2026-07-14: matched PID to description, current state to control/management, open files to the resource list, and registers/PC to CPU-related context.
- Next review: 2026-07-21

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 磁盘 I/O 完成后，写出唤醒阻塞进程的队列移出、状态修改和队列插入三步，并判断是否能直接变为运行态。
- Target: 用“改状态 + 搬队列 + 管资源”拆解进程控制
- Result: Incomplete on 2026-07-14: correctly removed P from the blocking queue, changed it to ready, and inserted it into the ready queue; omitted whether it can skip ready and run immediately.
- Next review: same-day repair

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: I/O 完成后 P 已进入就绪队列，但 CPU 正在运行 Q。P 能否跳过就绪态直接运行？用一句话说明原因。
- Target: 固定“事件完成只恢复可运行条件，运行仍需调度器选中”的边界
- Result: Correct after repair on 2026-07-14: stated that P cannot skip ready because runnable eligibility is different from occupying the CPU, which still depends on scheduler policy.
- Next review: 2026-07-17

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 父进程 P 创建子进程 C；C 初始化完成后进入哪里？若 P 终止而 C 仍存活，C 由谁接管，P 的资源和 PCB 分别如何处理？
- Target: 用“改状态 + 搬队列 + 管资源”拆解进程控制
- Result: Incomplete on 2026-07-14: correctly placed C in the ready queue, rejected direct running, and assigned surviving C to process 1; described both P's resources and PCB only as “cleaned” without distinguishing resource return from PCB deletion.
- Next review: same-day repair

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: P 终止时，它占有的资源要怎样处理？它的 PCB 从所在队列移除后要怎样处理？
- Target: 固定“资源归还操作系统 vs PCB 删除”的终止边界
- Result: Correct after repair on 2026-07-14: stated that P's resources are released and returned to the operating system, while its PCB is deleted after removal from its queue.
- Next review: 2026-07-17

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: A 时间片耗尽、B 被选中后，A 的上下文保存在哪里，从哪里加载 B 的上下文，CPU 最后依据 B 的什么位置继续？
- Target: 用“保存旧现场 -> 加载新现场”运行 CPU 上下文切换
- Result: Correct on 2026-07-14: saved A's registers/PC in A's PCB, loaded B's context from B's PCB, and resumed from B's last saved position; normalized the final term to B's PC.
- Next review: 2026-07-21

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 将寄存器/PC、虚拟地址空间/页表上下文、内核栈分到最小 CPU 上下文或更宽的进程上下文，并判断 A 切到 B 时后两者能否继续沿用 A 的内容。
- Target: 区分最小 CPU 上下文与更宽的进程上下文
- Result: Correct on 2026-07-14: classified registers/PC as minimal CPU context, address-space/page-table context and kernel stack as wider process context, and rejected reusing A's context for B.
- Next review: 2026-07-21

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: A 运行时发生硬件中断。最初是哪类上下文切换？中断结束后是否必然切到 B，还是 A 可能继续？
- Target: 区分中断上下文进入与进程上下文切换
- Result: Correct on 2026-07-14: identified initial interrupt-context entry, rejected a guaranteed switch to B, and stated that A can resume if the scheduler chooses no other process.
- Next review: 2026-07-21

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 同进程 T1/T2 的代码、数据、打开文件、虚拟地址空间、寄存器和栈中，哪些共享、哪些私有？切换时是否更换整套页表？
- Target: 用“资源平台 vs 执行流”区分进程和线程
- Result: Incomplete on 2026-07-14: correctly classified code, data/globals, open files, and virtual address space as shared, and registers/stack as thread-private; omitted whether the page table/address space must switch.
- Next review: same-day repair

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: T1、T2 属于同一进程。切换时是否需要更换整套页表/虚拟地址空间？依据是哪项共享关系？
- Target: 固定“同进程线程共享地址空间，因此切换私有执行现场而不换整套页表”的边界
- Result: Correct after repair on 2026-07-14: stated that no full page-table/address-space switch is needed because T1 and T2 share the same virtual address space and mappings.
- Next review: 2026-07-17

## 2026-07-14

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: T1 通过共享缓冲区向 T2 传数据为什么更直接？若 T1 的未处理致命内存错误导致整个进程终止，为什么 T2 也会消失？
- Target: 用“资源平台 vs 执行流”区分进程和线程
- Result: Correct on 2026-07-15: explained that the shared address space permits both threads to access the same buffer directly, and that termination of the shared process removes T2's resource platform as well.
- Next review: 2026-07-22

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 100 个用户线程通过 N:1 只映射到 1 个内核线程时，能否在 8 核上真正并行？一个用户线程执行阻塞系统调用会影响谁？
- Target: 用“内核看见几个可调度实体”比较线程映射模型
- Result: Correct on 2026-07-15: stated that true 8-core parallelism is impossible and that blocking affects the user threads sharing the sole kernel-thread mapping; clarified that this means all mapped user threads, so the process blocks in the article's N:1 model.
- Next review: 2026-07-18

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 在 1:1 模型中，T1、T2 分别对应不同内核线程。T1 因系统调用阻塞时，T2 能否继续？二者能否在两个 CPU 核上同时执行？
- Target: 用“内核看见几个可调度实体”比较线程映射模型
- Result: Partially correct on 2026-07-15: correctly said T2 can continue when T1 blocks, and recognized that 1:1 supports multicore parallelism in general; missed that while T1 remains blocked, T1 cannot occupy a core and therefore cannot execute simultaneously with T2 at that moment.
- Next review: same-day repair

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: 在 1:1 模型中，若 T1 此刻仍阻塞、T2 已就绪，此刻二者能否分别占用两个 CPU 核同时执行？
- Target: 区分“映射模型支持并行”与“当前线程状态允许实际执行”
- Result: Correct after repair on 2026-07-15: stated that simultaneous execution is impossible at that moment because T1 is still blocked.
- Next review: 2026-07-18

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 一个服务器必须利用 8 核，且不能因一个请求线程阻塞而让其他请求全部停下；在 N:1 与 1:1 中选哪个，并承担什么主要成本？
- Target: 用“内核看见几个可调度实体”比较线程映射模型
- Result: Incorrect on 2026-07-15: selected N:1, but correctly named the higher kernel TCB/context cost that belongs to 1:1. The model label contradicted both the multicore/blocking requirements and the stated cost.
- Next review: same-day repair

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: 必须利用多个 CPU 核，所以内核必须看见多个可独立调度线程；N:1 与 1:1 中哪个满足？
- Target: 固定“需求 -> 内核可见实体数量 -> 映射模型”的选型顺序
- Result: Correct after repair on 2026-07-15: selected 1:1, matching the requirement for multiple kernel-visible independently schedulable entities.
- Next review: 2026-07-18

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 100 个用户线程映射到 4 个 LWP，LWP 再各自映射一个内核线程；8 核机器上最多多少用户线程能真正同时执行？一个 LWP 阻塞是否必然让整个进程停止？
- Target: 用 LWP/内核线程数量推导 M:N 的并行上限与阻塞范围
- Result: Correct on 2026-07-15: answered 4 simultaneous user threads and correctly stated that one blocked LWP does not necessarily stop the whole process because other LWPs remain schedulable.
- Next review: 2026-07-22

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: A 不阻塞也不退出、B 已就绪；普通时钟中断到来时，非抢占式能否仅凭中断强制换下 A？抢占式系统在 A 时间片耗尽时呢？
- Target: 用“当前进程是否可被强制收回 CPU”区分抢占式与非抢占式调度
- Result: Correct on 2026-07-15: answered that non-preemptive scheduling cannot force A off the CPU merely because of the clock interrupt, while preemptive scheduling can do so when A's time slice expires.
- Next review: 2026-07-22

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: X 在 1 分钟内 CPU 忙碌 100% 且完成 10 个作业；Y 忙碌 90% 且完成 30 个作业。谁的 CPU 利用率更高，谁的吞吐量更高？
- Target: 用“忙了多久 vs 完成多少”区分 CPU 利用率与吞吐量
- Result: Correct on 2026-07-15: identified X as having higher CPU utilization and Y as having higher throughput.
- Next review: 2026-07-22

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: P 在 t=0 到达、t=2 首次响应、累计就绪等待 5 秒、I/O 阻塞 3 秒、CPU 运行 4 秒、t=12 完成；分别计算周转、等待和响应时间。
- Target: 用“总历时、就绪排队、首次反馈”区分周转时间、等待时间与响应时间
- Result: Correct on 2026-07-15: calculated turnaround 12 seconds, ready-queue waiting 5 seconds, and response 2 seconds.
- Next review: 2026-07-22

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 非抢占式 FCFS 中，A(t=0, 8s)、B(t=1, 2s)、C(t=2, 1s) 的执行顺序及 B、C 的就绪等待时间分别是什么？
- Target: 用“到达顺序 + 运行到阻塞/结束”执行 FCFS 并识别队首长作业效应
- Result: Incomplete on 2026-07-15: correctly gave execution order A-B-C, but reported B/C start timestamps 8 and 10 as their waiting durations. Correct waiting uses start minus arrival.
- Next review: same-day repair

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: B 在 t=1 到达、t=8 开始；C 在 t=2 到达、t=10 开始。按开始时刻减到达时刻，二者各等待多久？
- Target: 区分开始运行时刻与就绪队列等待时长
- Result: Correct after repair on 2026-07-15: calculated B's wait as 7 seconds and C's wait as 8 seconds.
- Next review: 2026-07-18

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 非抢占式 SJF 中，A/B/C 同时就绪、服务时间为 8/2/1 秒；执行顺序是什么？短作业持续到达时哪类作业可能饥饿？
- Target: 用“预计运行时间最短”执行 SJF 并识别长作业饥饿
- Result: Correct on 2026-07-15: ordered C-B-A and identified long-job starvation when short jobs continue to arrive.
- Next review: 2026-07-22

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: HRRN 中 P 等待 8 秒/服务 4 秒，Q 等待 1 秒/服务 1 秒；分别计算响应比并选择先运行者。
- Target: 用“等待时间抬升响应比”执行 HRRN 并兼顾长短作业
- Result: Correct on 2026-07-15: calculated P=3 and Q=2, then selected P.
- Next review: 2026-07-22

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: RR 中 A/B 同时就绪、所需时间 5/3 秒、时间片 2 秒、初始队列 A→B；写出直到完成的 CPU 进程序列。
- Target: 用“时间片用完回队尾”执行 RR 并权衡响应与切换开销
- Result: Incorrect on 2026-07-15: answered B-A, reversing the initial queue head and omitting repeated turns for unfinished processes.
- Next review: same-day repair

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: 初始队列 A→B；A 用完 2 秒时间片后还剩 3 秒并回队尾。此时队列顺序是什么，下一位是谁？
- Target: 固定 RR 的“取队首 -> 运行时间片 -> 未完成回队尾”单步动作
- Result: Correct after repair on 2026-07-15: gave queue B-A and selected B next.
- Next review: 2026-07-18

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day continuation
- Prompt: 当前 B→A，B 剩 3 秒；B 运行 2 秒后剩 1 秒并回队尾。队列变成什么，下一位是谁？
- Target: 连续执行 RR 的第二个“取队首 -> 扣时间片 -> 回队尾”动作
- Result: Correct on 2026-07-15: gave queue A-B and selected A next.
- Next review: 2026-07-18

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day continuation
- Prompt: 已执行 A→B；当前 A→B、剩余时间 3/1 秒、时间片 2 秒。写出直到完成的剩余 CPU 进程序列。
- Target: 在 RR 中处理未完成回队尾与提前完成后移出队列
- Result: Incorrect on 2026-07-15: answered B-A, omitting the current queue-head A slice; correctly noticed that both would then have 1 second remaining, but treated their later completions as if simultaneous.
- Next review: 2026-07-16

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: 当前 A→B、剩余 3/1 秒、时间片 2 秒；补全“___运行2秒回队尾；___运行1秒完成；___运行1秒完成”。
- Target: 修复 RR 中重复遗漏当前队首，并按顺序移除已完成进程
- Result: Correct after repair on 2026-07-15: filled A-B-A and correctly distinguished requeue from completion/removal.
- Next review: 2026-07-16

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: RR 的 X 时间片 1ms/切换 0.2ms，Y 时间片 100ms/大量交互任务；谁更受切换开销影响，谁更易响应变慢并趋近 FCFS？
- Target: 用“时间片用完回队尾”执行 RR 并权衡响应与切换开销
- Result: Correct on 2026-07-15: selected X for excessive context-switch overhead and Y for slower interactive response/FCFS-like behavior.
- Next review: 2026-07-22; RR queue sequence remains separately scheduled for 2026-07-16

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: 低优先级 L 正运行，高优先级 H 到达；抢占式 HPF 此刻谁运行？高优先级任务持续到达时 L 有什么风险？
- Target: 用“最高优先级 + 是否抢占”执行 HPF 并识别低优先级饥饿
- Result: Correct on 2026-07-15: selected H under preemptive HPF and identified starvation risk for L.
- Next review: 2026-07-22

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same day
- Prompt: MLFQ 中 P 在 Q1 用满 2ms 未完成，应移到哪里？P 正在 Q2 时新进程 N 进入 Q1，此刻运行谁？
- Target: 用“高层短时间片、用满则降级、高层到达则抢占”运行 MLFQ
- Result: Incomplete on 2026-07-15: correctly moved P to Q2 and identified Q1 as the queue that should run next, but answered with a queue name rather than the concrete process N and omitted P's pause/requeue action.
- Next review: same-day repair

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: P 正在 Q2，N 进入 Q1；具体哪个进程获得 CPU，P 如何处理？
- Target: 从最高优先级非空队列继续选择具体进程，并处理被抢占的低层进程
- Result: Incomplete on 2026-07-15: correctly selected N and said P is suspended, but omitted that P returns to the tail of its original Q2 queue.
- Next review: same-day repair

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: same-day repair
- Prompt: N 从 Q1 抢占后，被暂停的 P 应插回哪个队列的什么位置？
- Target: 固定 MLFQ 跨队列抢占后的“回原队列末尾”动作
- Result: Correct after repair on 2026-07-15: placed P at the tail of its original Q2 queue.
- Next review: 2026-07-18

## 2026-07-15

- Topic: OS - 进程、线程与调度基础
- Review timing: final transfer
- Prompt: 混合短交互任务与长 CPU 任务、服务时间不可预知、要求短任务快响应且长任务逐步获更长时间片，应选哪个算法？给出两条机制理由。
- Target: 用“已知信息 + 优化目标 + 可接受代价”选择调度算法
- Result: Correct on 2026-07-15: selected MLFQ and justified it with no exact service-time requirement plus behavior-based dynamic tiers that balance short-task response and long-task progress.
- Next review: 2026-07-22

## 2026-07-16

- Topic: OS - 进程、线程与调度基础
- Review timing: next day
- Prompt: RR 中 A/B/C 同时就绪、所需 CPU 时间 4/3/1 秒、时间片 2 秒、初始队列 A→B→C；写出直到全部完成的 CPU 进程序列。
- Target: 用四列时间线独立执行 RR：当前队列、取出队首、运行后剩余、回队尾或完成移出
- Result: Pending
- Next review: TBD

## 2026-07-15

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: A、B 两个进程里都出现数值为 `0x1000` 的指针。为什么 A 不能只把这个指针数值告诉 B，就让 B 直接读到 A 的对象？请指出缺少了哪一层关系。
- Target: 用“隔离边界 -> 受控通道”解释为什么需要 IPC
- Result: Partially correct on 2026-07-15: correctly identified independent user virtual address spaces and page tables, so equal virtual addresses do not imply the same physical page; did not yet name the missing shared mapping relation.
- Next review: same-day repair

## 2026-07-15

- Topic: OS - 进程间通信
- Review timing: same-day repair
- Prompt: 若要让 A、B 真正看到同一个对象，操作系统必须额外建立什么“虚拟地址到物理页”的关系？
- Target: 补全“双方虚拟地址映射到同一物理页”的共享内存边界
- Result: Correct on 2026-07-15: named the missing shared mapping; together with the prior explanation of independent page tables and physical pages, this completes the boundary.
- Next review: 2026-07-18

## 2026-07-15

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: `pipe(fd)` 后，进程把 `hello` 写入 `fd[1]`。另一个进程应从哪个描述符读取？数据在读出前暂存在哪里？
- Target: 用“内核字节流 + 两个端点”运行匿名管道模型
- Result: Correct on 2026-07-15: identified `fd[0]` as the read end and the kernel pipe buffer as the temporary data location.
- Next review: 2026-07-18

## 2026-07-15

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: 父进程要向子进程单向发送数据。`fork` 后，父进程应关闭并保留哪个 fd？子进程应关闭并保留哪个 fd？
- Target: 用“先 pipe 后 fork + 关闭无用端”建立单向父子管道
- Result: Correct on 2026-07-16: father closes `fd[0]` and keeps `fd[1]`; child closes `fd[1]` and keeps `fd[0]`.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day boundary check
- Prompt: `fork` 后父子进程各自都有 `fd[0]` 和 `fd[1]`。它们底层引用的是一个管道对象还是两个独立管道？为什么？
- Target: 区分复制文件描述符表项与复制内核管道对象
- Result: Correct on 2026-07-16: identified one shared kernel pipe object, because `fork` copies descriptor table entries that continue to reference the original object; connected this directly to cross-process data flow.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: 两个没有亲缘关系、也没有预先继承同一管道 fd 的本机进程，需要用管道通信。应选匿名管道还是命名管道 FIFO？它们靠什么找到同一个通道？
- Target: 用“描述符继承 vs 路径命名”区分匿名管道与 FIFO
- Result: Correct on 2026-07-16: selected named pipe FIFO and explained that unrelated processes open the same filesystem path to find the same FIFO.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: A 要发送“订单 1”和“订单 2”两个独立单元，并要求 B 能直接分辨边界。管道与消息队列中应选哪个？B 成功读取“订单 1”后，内核中的这条消息通常怎样变化？
- Target: 用“有边界消息体”区分消息队列与管道字节流
- Result: Correct on 2026-07-16: selected the message queue and correctly stated that the consumed message is normally removed from the kernel queue.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: A 要通过消息队列频繁发送 100MB 数据块给 B。请写出数据的两段复制路径，并给出消息队列不适合该场景的两个原因。
- Target: 用“用户态 -> 内核队列 -> 用户态”评估消息队列的大数据成本
- Result: Correct on 2026-07-16: traced A user space to the kernel queue to B user space, and identified copy cost plus capacity limits.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: A、B 使用 System V 消息队列通信，随后都退出，但没有显式删除队列。该队列是否必然立即消失？它通常要到什么时候才消失？
- Target: 用“是否随描述符关闭消失”区分消息队列与匿名管道生命周期
- Result: Correct on 2026-07-16: the queue does not necessarily disappear when A and B exit; it remains until explicit removal or system restart.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: 共享映射已经建立后，A 在共享区写入 100MB，B 再读取。是否还要走“A 用户空间 -> 内核消息缓冲 -> B 用户空间”的两次复制？实际数据路径是什么？
- Target: 用“映射建立一次，稳态直接访问共享页”解释共享内存低复制
- Result: Correct on 2026-07-16: no message-queue-style double copy is needed; A writes the shared physical pages and B reads the same pages through its own virtual mapping.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: 互斥信号量初值为 1。A 执行 P 后进入共享区；A 尚未执行 V 时，B 又执行 P。B 应继续还是阻塞？真正的业务数据存放在信号量还是共享内存中？
- Target: 用“共享内存传数据，信号量管秩序”区分载荷与协调
- Result: Correct on 2026-07-16: B blocks while A holds the resource, and the actual business data remains in shared memory rather than the semaphore.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: A 是生产者，B 必须等 A 生产完成后才能读取。同步信号量初值应设为多少？若 B 先执行 P 会怎样，A 生产后应执行什么操作？
- Target: 用“初值 1 管互斥，初值 0 管先后”区分两类信号量
- Result: Correct on 2026-07-16: initialized the synchronization semaphore to 0, blocked B when it performed P first, and used A's V after production to release B.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: 场景一要保证两个进程不能同时修改共享缓冲区；场景二要在用户按 Ctrl+C 时通知前台进程。两个场景分别应使用信号量还是信号？
- Target: 用“资源计数协调 vs 异步事件通知”区分信号量与信号
- Result: Correct on 2026-07-16: selected a semaphore for shared-buffer mutual exclusion and a signal for Ctrl+C asynchronous notification.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: 普通信号到达后，进程通常有哪三种处置方式？哪两个信号不能被捕捉或忽略？
- Target: 用“默认、捕捉、忽略 + 两个例外”判断信号处置
- Result: Partially correct on 2026-07-16: correctly named default, catch, ignore and the SIGKILL/SIGSTOP exceptions, but grouped blocking with ignoring even though blocking delays delivery rather than defining a disposition.
- Next review: same-day repair

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day repair
- Prompt: 同一个 `SIGTERM` 到达：进程 A 将它设为忽略，进程 B 只是暂时阻塞它。之后 B 解除阻塞时，哪一个信号仍可能被递送？为什么？
- Target: 区分忽略信号与阻塞信号
- Result: Correct on 2026-07-16: identified B's blocked SIGTERM as still deliverable after unblocking because blocking delays delivery rather than discarding the signal.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: 分别给出两种 Socket 组合：一是跨主机 IPv4 的可靠字节流；二是同一主机内的本地数据报。各自应选择什么 `domain + type`？
- Target: 用“通信域 domain + 数据语义 type”选择 Socket
- Result: Correct on 2026-07-16: selected `AF_INET + SOCK_STREAM` for cross-host IPv4 reliable byte stream and `AF_UNIX + SOCK_DGRAM` for a local datagram.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: TCP 服务端对监听 Socket 调用 `accept` 成功后，返回的是什么 Socket？后续与该客户端 `read/write` 应使用监听 Socket 还是返回的新 Socket？原监听 Socket 继续负责什么？
- Target: 用“监听 Socket + 每连接一个已连接 Socket”拆分 TCP 服务端职责
- Result: Correct on 2026-07-16: `accept` returns a new connected Socket used for that client's read/write, while the original listening Socket continues accepting new connections.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: 基本 UDP 服务端流程不需要 TCP 中的哪两个接入调用？使用 `sendto/recvfrom` 时，每个数据报需要或返回什么地址信息？
- Target: 用“连接状态 vs 每报文地址”区分 TCP 与 UDP 调用链
- Result: Correct on 2026-07-16: omitted `listen/accept`, used `recvfrom` to obtain the sender address, and `sendto` to target the response address.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day formation check
- Prompt: 同一主机上的两个进程需要本地字节流通信。应选择什么 `domain + type`？服务端通常绑定 IP/端口，还是本地路径或命名空间？
- Target: 用“本地命名替代 IP 端口”定位 Unix domain Socket
- Result: Correct on 2026-07-16: selected `AF_UNIX + SOCK_STREAM` and a local filesystem path for service discovery.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: integrated transfer
- Prompt: 同一主机上，采集进程每秒产生多个 200MB 图像，分析进程必须读取完整图像，不能读到写了一半的数据。应选择什么机制承载图像，什么机制协调读写？为什么不优先使用消息队列？
- Target: 用“范围 + 数据规模/语义 + 同步要求”组合 IPC 方案
- Result: Incomplete on 2026-07-16: correctly selected shared mapping plus synchronization and recognized message queues as suitable for small bounded control messages, but did not name the semaphore or the double-copy and capacity costs.
- Next review: same-day repair

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: same-day repair
- Prompt: 请把方案补具体：图像放在哪里，本文用什么机制保证“写完后再读”？消息队列在该场景中有哪两个具体成本？
- Target: 补全共享内存、信号量和消息队列大数据代价
- Result: Correct on 2026-07-16: selected shared memory for image data, a semaphore for synchronization, and identified message-queue double-copy plus capacity limits.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 进程间通信
- Review timing: final cross-host transfer
- Prompt: 采集进程与分析进程位于不同主机，要求通过 IPv4 可靠、有序地连续传输图像。应选择哪类 IPC 及什么 `domain + type`？为什么共享内存不适用？该字节流是否自动保留每幅图像的边界？
- Target: 用“范围 + 数据规模/语义 + 同步要求”组合 IPC 方案
- Result: Pending
- Next review: TBD

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: 初始 `i=50`，T1 读出 50 并算出 51 后被切换；T2 完整执行“读 50、加一、写回 51”；随后 T1 再写回自己的 51。最终 `i` 是多少？丢失的是哪一次更新，根因是什么？
- Target: 用“共享状态 + 非原子复合操作 + 不可控交错”识别竞争条件
- Result: Correct on 2026-07-16: gave the final value 51 and correctly identified that both threads read the same old value before their writes overwrote one another.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: 场景 A：T1、T2 都要修改同一个账户余额，不能同时进入更新代码；场景 B：T2 必须等 T1 把文件读入内存后才能处理。A、B 分别主要是互斥还是同步？各自限制的是“同时性”还是“先后性”？
- Target: 用“不能同时 vs 必须先后”区分互斥与同步
- Result: Correct on 2026-07-16: classified A as mutual exclusion limiting simultaneous entry and B as synchronization enforcing execution order.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: 初始 `flag=0`。T1 先执行 `TestAndSet(&flag, 1)`，随后 T2 也执行同一操作。T1、T2 各自得到的返回旧值是什么？两次操作后 `flag` 是多少？谁能进入临界区，谁会继续自旋？
- Target: 用“原子抢占资格”理解 Test-and-Set 锁
- Result: Correct on 2026-07-16: tracked return values 0 and 1, final `flag=1`, T1 entering the critical section, and T2 continuing to spin.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: 场景 A：多核系统中，持锁线程正在另一核运行，预计几微秒后释放；场景 B：持锁线程因 I/O 可能 200ms 后才释放。A、B 分别更适合自旋等待还是阻塞等待？各自避免了什么主要成本？
- Target: 用“等待时长 + CPU 是否能让持锁者推进”选择自旋或阻塞
- Result: Correct on 2026-07-16: chose spinning for the short cross-core wait to avoid sleep, wakeup, and context-switch overhead, and blocking for the long I/O wait to avoid wasting CPU.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: 信号量初值 `sem=2`。T1、T2、T3 依次执行 P；随后 T1 执行一次 V。每一步后的 `sem` 分别是多少？谁会阻塞？V 后谁被唤醒，唤醒后是直接运行还是先进入就绪态？
- Target: 用“资源计数 + 等待队列”运行 P/V
- Result: Correct on 2026-07-16: traced P results as 1, 0, and -1, blocked T3, then used V to return to 0 and wake T3 into the ready state rather than immediate execution.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: 容量 `N=2` 的缓冲区已经装满。错误的生产者先执行 `P(mutex)` 成功，再执行 `P(empty)` 并阻塞；消费者执行 `P(full)` 成功后，又尝试 `P(mutex)`。为什么此时双方都无法推进？正确顺序应怎样调整？
- Target: 用“资源许可在外、互斥保护在内”解有界生产者—消费者
- Result: Correct on 2026-07-16: explained that a thread must confirm empty/full availability before acquiring mutex, because blocking while holding mutex prevents the other side from entering and creating the needed resource; also reproduced both correct P/V sequences.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day boundary check
- Prompt: 容量 `N=5`，某个稳定时刻 `empty=2, full=3`。生产者执行 `P(empty)` 后暂停在 `P(mutex)` 之前：此时 `empty`、`full` 各是多少？为什么二者之和为 4 不表示容量丢失？生产者完成放入并执行 `V(full)` 后，两者又是多少？
- Target: 用“稳定计数 + 在途许可”检查有界缓冲区容量
- Result: Correct on 2026-07-16: computed `empty=1, full=3`, identified one producer-held in-flight permit, and restored the stable counts to `empty=1, full=4` after `V(full)`.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: 五位哲学家都先成功拿到左叉，再等待右叉。此时有没有任何人能继续吃饭？请按“哲学家 0 等谁持有的哪把叉子 → … → 回到哲学家 0”写出等待环，并解释为什么每把叉子的互斥都没失效，系统仍然死锁。
- Target: 用“局部互斥正确 ≠ 全局可推进”识别哲学家死锁环
- Result: Partially correct on 2026-07-16: correctly concluded that nobody can eat and expressed the general relation `i` holds `fork[i]` while waiting for `fork[(i+1)%5]`, but did not expand the five concrete edges or explicitly separate per-fork safety from system-wide progress.
- Next review: same-day repair

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day repair
- Prompt: 请只补全具体等待环：哲学家 0 等待哪把叉子、它被谁持有，然后依次展开到哲学家 4，并回到哲学家 0；最后用一句话区分“每把叉子的互斥仍正确”和“系统整体为什么不能推进”。
- Target: 展开五段循环等待，并区分局部 safety 与全局 progress
- Result: Skipped as already mastered on 2026-07-16: learner requested the next schema after correctly providing the general hold/wait relation and circular-wait conclusion; no further low-level expansion required.
- Next review: 2026-07-23

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: 方案 A 用一个全局 mutex 包住“拿两把叉子直到放回”；方案 B 让偶数哲学家先左后右、奇数先右后左。若要求既无上述死锁，又允许两个不相邻哲学家同时进餐，应选哪一个？另一个方案牺牲了什么？所选方案破坏了等待环的哪个结构？
- Target: 用“破坏循环等待 vs 全局串行”比较哲学家方案
- Result: Correct on 2026-07-16: selected asymmetric odd/even acquisition, identified the global mutex's over-serialization and efficiency loss, and named circular waiting as the broken structure.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: `state[2]=HUNGRY`，其左邻哲学家 1 为 `EATING`，右邻哲学家 3 为 `THINKING`。第一次 `test(2)` 后哲学家 2 是进入 `EATING` 还是继续等待？随后哲学家 1 放叉变为 `THINKING`，并再次触发 `test(2)`，此时状态怎样变化、哪个信号量执行 V、哲学家 2 被唤醒后先处于什么调度状态？
- Target: 用“状态表 + 邻居判定 + 个人信号量”准入哲学家
- Result: Correct on 2026-07-16: kept philosopher 2 waiting while neighbor 1 was EATING, then changed 2 to EATING, executed `V(s[2])`, and placed the awakened philosopher in the ready state after neighbor 1 released.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: 初始 `rCount=0`。读者 R1 进入后，读者 R2 再进入，随后写者 W 到达；之后 R1 先退出、R2 再退出。谁负责第一次 `P(wDataMutex)`？R2 进入时为何不再 P 它？R1 退出后是否释放？谁最终执行 `V(wDataMutex)`，写者何时才可能继续？
- Target: 用“第一个读者加锁、最后一个读者解锁”把读者组视为整体
- Result: Correct on 2026-07-16: identified R1 as the first reader acquiring the data lock, preserved reader concurrency by not reacquiring it for R2, left it held after R1 exited, and had final reader R2 release it before the writer could proceed.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day formation check
- Prompt: R1 正在读，写者 W 已到达并等待；此后 R2、R3 不断到达。读者优先方案中，R2/R3 能否继续加入，W 面临什么风险？若公平方案让 W 先取得入口 `flag`，后来的 R2/R3 会阻塞在哪里，已有读者退出后谁先获得数据锁？
- Target: 用“入口闸门能否被插队”比较读者优先、写者优先与公平策略
- Result: Partially correct on 2026-07-16: correctly allowed later readers and identified writer starvation under reader preference, and correctly blocked R2/R3 at `flag` under the fair scheme; incorrectly let R2/R3 obtain `wDataMutex` after old readers drained, even though W already holds `flag` and waits on the data lock.
- Next review: same-day repair

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: same-day repair
- Prompt: 公平方案中，W 已持有 `flag` 并阻塞在 `wDataMutex`，R2/R3 阻塞在 `flag`。最后一个旧读者执行 `V(wDataMutex)` 后，此刻谁有资格竞争或取得 `wDataMutex`？为什么 R2/R3 还不能参与竞争？
- Target: 区分已通过入口的写者与仍阻塞在入口的后来读者
- Result: Correct on 2026-07-16: selected W because it has already crossed and holds `flag`, while R2/R3 remain blocked at the entry gate and cannot yet compete for `wDataMutex`.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 多线程互斥与同步
- Review timing: integrated transfer
- Prompt: 某任务系统有三个问题：①缓冲区满时，生产者先 `P(mutex)` 再 `P(empty)`；②锁持有者可能做 200ms I/O，等待者却一直自旋；③统计模块采用读者优先且读者持续到达。请分别指出这三处属于死锁、效率浪费还是饥饿，并各给出一个最小修正。
- Target: 用“安全、顺序、进展、效率”综合诊断同步方案
- Result: Correct on 2026-07-16: identified deadlock and reordered `P(empty)` before `P(mutex)`; identified long-spin efficiency waste and switched to blocking wakeup; identified writer starvation and selected a fair `flag` entry gate.
- Next review: 2026-07-23

## 2026-07-23

- Topic: OS - 多线程互斥与同步
- Review timing: 1 week
- Prompt: 某系统同时出现“持锁等待资源”“长临界区外持续自旋”“后来者持续插队”三种现象。分别判断死锁、效率浪费或饥饿，并各给一个最小修正；最后说明为什么“没有数据竞争”仍不足以证明并发方案正确。
- Target: 用 safety / ordering / progress / efficiency 综合诊断同步方案
- Result: Pending
- Next review: TBD

## 2026-07-16

- Topic: OS - 死锁条件、等待闭环与资源有序分配
- Review timing: same-day formation check
- Prompt: 线程 A 持有 `mutex_A` 等 `mutex_B`，线程 B 持有 `mutex_B` 等 `mutex_A`。逐项说明互斥、持有并等待、不可剥夺、环路等待在这个现场怎样成立；最后指出为什么“两个线程都卡在 mutex_lock”本身仍不是完整证明。
- Target: 用“四个必要条件 + 等待闭环”判断死锁
- Result: Partially correct on 2026-07-16: mutual exclusion, no preemption, and circular wait were mapped correctly; hold-and-wait repeated the circular-wait relation instead of stating that each thread retains its first mutex while requesting the second.
- Next review: same-day repair

## 2026-07-16

- Topic: OS - 死锁四条件边界修复
- Review timing: same-day repair
- Prompt: 线程 A 等待 `mutex_B` 时仍持有什么？线程 B 等待 `mutex_A` 时仍持有什么？为什么这叫“持有并等待”，而不是“环路等待”？
- Target: 区分单个线程的 hold-and-wait 与多个线程的 circular wait
- Result: Correct on 2026-07-16: A retains `mutex_A` while waiting for `mutex_B`, and B retains `mutex_B` while waiting for `mutex_A`; hold-and-wait is now separated from the cross-thread cycle.
- Next review: 2026-07-19

## 2026-07-19

- Topic: OS - 死锁四个必要条件
- Review timing: 3 days after repair
- Prompt: 给出一个两线程两锁现场，分别指出单个线程的“持有并等待”和多个线程的“环路等待”，并说明二者为何不能互换。
- Target: 区分 hold-and-wait 与 circular wait
- Result: Pending
- Next review: TBD

## 2026-07-16

- Topic: OS - 从相反加锁顺序构造等待闭环
- Review timing: same-day formation check
- Prompt: A 持 A、B 持 B；A 先请求 B 并阻塞，随后 B 再请求 A 并阻塞。指出在哪一步从单向等待变成闭环，以及闭环形成后为什么双方都无法到达 unlock。
- Target: 把相反加锁顺序运行成等待图
- Result: Correct on 2026-07-16: step 3 has only one waiting direction, step 4 closes the cycle, and removing `sleep(1)` does not guarantee safety because sleep only enlarges the scheduling window in which both threads acquire their first locks.
- Next review: 2026-07-19

## 2026-07-19

- Topic: OS - 两线程两锁死锁复现
- Review timing: 3 days
- Prompt: 按时间运行 A 先 A 后 B、B 先 B 后 A 的加锁过程，指出闭环形成时刻；再说明删除 `sleep(1)` 为什么不等于消除死锁可能性。
- Target: 把相反加锁顺序运行成等待图
- Result: Pending
- Next review: TBD

## 2026-07-16

- Topic: OS - pstack 死锁嫌疑筛选
- Review timing: same-day formation check
- Prompt: 多次 `pstack` 都显示两个线程停在不变的 `pthread_mutex_lock` 栈帧。能确认什么、不能确认什么？下一步需要补充哪类证据？
- Target: 用多次稳定栈采样筛选死锁嫌疑
- Result: Partially correct on 2026-07-16: correctly treated stable lock-wait stacks as insufficient for confirmation and identified the observed stagnation, but described the missing evidence only as “more details” rather than naming the waited-on mutex object and its owner.
- Next review: same-day repair

## 2026-07-16

- Topic: OS - pstack 证据边界修复
- Review timing: same-day repair
- Prompt: 要把“线程卡在 `pthread_mutex_lock`”升级为等待闭环证据，必须为每个线程再查清哪两个字段或关系？
- Target: 补全 waiting mutex object 与 owner 两类证据
- Result: Correct on 2026-07-16: named both the specific lock object each blocked thread is waiting for and the thread currently owning that object.
- Next review: 2026-07-19

## 2026-07-19

- Topic: OS - pstack 证据边界
- Review timing: 3 days after repair
- Prompt: 多次稳定 `pstack` 都显示线程卡在 `pthread_mutex_lock`。区分已经获得的线索与仍需用调试器补充的两类闭环证据。
- Target: 用多次稳定栈采样筛选死锁嫌疑
- Result: Pending
- Next review: TBD

## 2026-07-16

- Topic: OS - gdb 重建死锁闭环
- Review timing: same-day formation check
- Prompt: 根据两个线程各自等待的 mutex 及两个 mutex 的 owner，写出两条 `thread -> waited mutex -> owner` 路径并判断是否闭环。
- Target: 用 `gdb` 拼接等待锁与 owner 闭环
- Result: Correct on 2026-07-16: reconstructed `B -> mutex_A -> A` and `A -> mutex_B -> B`, then used the closed A/B dependency to confirm deadlock.
- Next review: 2026-07-19

## 2026-07-19

- Topic: OS - gdb 死锁闭环重建
- Review timing: 3 days
- Prompt: 给出两个线程的阻塞栈帧、两个 mutex 的 owner 和 LWP 映射，重建 `thread -> waited mutex -> owner` 路径并判断是否闭环。
- Target: 用 `gdb` 拼接等待锁与 owner 闭环
- Result: Pending
- Next review: TBD

## 2026-07-16

- Topic: OS - 资源有序分配预防死锁
- Review timing: same-day formation check
- Prompt: 规定 `mutex_A < mutex_B` 后，怎样修改 B→A 的逆序路径？统一顺序破坏哪个必要条件，为什么仍可能等待但不会形成该等待环？
- Target: 用全局资源顺序破坏环路等待
- Result: Correct on 2026-07-16: changed B to acquire A before B, identified circular wait as the broken condition, and explained that resource ranks can only increase along waits and therefore cannot return to a lower-ranked start.
- Next review: 2026-07-23

## 2026-07-23

- Topic: OS - 资源有序分配预防死锁
- Review timing: 1 week
- Prompt: 为三把锁 `L1 < L2 < L3` 检查若干代码路径，找出逆序获取并修正；说明统一顺序消除的是哪种等待结构，而不是哪种普通竞争。
- Target: 用全局资源顺序破坏环路等待
- Result: Pending
- Next review: TBD

## 2026-07-16

- Topic: OS - deadlock 全章综合迁移
- Review timing: integrated transfer
- Prompt: 从多次稳定 `pstack`、两个 mutex owner 和两条相反加锁路径出发，区分线索与证明、重建等待闭环，并用共享资源顺序给出最小修正和无环理由。
- Target: 从停滞栈到有序加锁串联死锁全链路
- Result: Pending
- Next review: after learner answer

## 2026-07-16

- Topic: OS - CAS、自旋锁与乐观锁的协议边界
- Review timing: same-day formation check
- Prompt: `CAS(lock, 0, tid)` 被放在 `while` 中反复抢锁；线程只有抢到锁后才能修改共享数据。它为什么仍属于悲观锁，而不能因为用了 CAS 就叫乐观锁？请按“什么时候处理冲突”回答。
- Target: 用“修改前先排他 vs 提交时再校验”给高层协议分类
- Result: Correct on 2026-07-16: identified that pessimistic locking prevents conflicts and acquires exclusive qualification before modifying shared data; did not classify the protocol merely from its use of CAS.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 乐观锁版本号校验
- Review timing: same-day formation check
- Prompt: A、B 都读取文档版本 v10；B 先提交成功并把服务端升级为 v11；A 再携带基础版本 v10 提交。判断接受或拒绝，并补全“基础版本 v10 __ 当前版本 v11”。
- Target: 用基础版本与当前版本是否相等判断乐观提交冲突
- Result: Correct on 2026-07-16: rejected A's stale submission because base version v10 differs from current version v11, preventing it from directly overwriting B's accepted change.
- Next review: 2026-07-19

## 2026-07-16

- Topic: OS - 乐观与悲观策略的成本选型
- Review timing: same-day formation check
- Prompt: 场景 A 冲突率约 0.1%，失败只重做 5ms；场景 B 冲突率约 30%，每次失败作废 2 秒计算。二者加锁成本都较高，哪个更适合乐观锁？
- Target: 用冲突概率与单次失败重试成本共同判断乐观锁收益
- Result: Correct on 2026-07-17: selected scenario A and explicitly justified it by the smaller product of conflict probability and retry cost.
- Next review: 2026-07-20

## 2026-07-17

- Topic: OS - 悲观锁、乐观锁与等待策略全章综合迁移
- Review timing: integrated transfer
- Prompt: 为 200ns 多核内存临界区、读多但写者不可饥饿的配置中心、低冲突且低合并成本的版本化在线文档，分别选择等待方式、读写入口策略和冲突策略，并各给一个主导理由。
- Target: 用等待时长、访问兼容性、冲突处理时机与公平性综合选锁
- Result: Pending
- Next review: after learner answer

## 2026-07-17

- Topic: OS - 单进程线程上限的地址预算
- Review timing: same-day formation check
- Prompt: 在原文 32 位 Linux 模型里，用户空间约 `3G`，每线程预留约 `10M`。只从地址空间看约能创建多少线程？为什么它不是 Linux 的固定上限？
- Target: 用“环境 + 地址预算”估算线程上限
- Result: Partially correct on 2026-07-17: correctly stated that this specific model and stack configuration allow about 300 threads from the address-space perspective, but did not explain which changed inputs or competing budgets make the result non-universal.
- Next review: same-day boundary repair

## 2026-07-17

- Topic: OS - 线程上限的环境边界修复
- Review timing: same-day repair
- Prompt: 哪两类条件一改变，“约 300 个”就可能改变？分别从计算输入和其他系统瓶颈各说一类。
- Target: 区分地址预算估算与 Linux 固定上限
- Result: Correct on 2026-07-17: identified OS/bitness as inputs that change the address-space estimate and separately recognized that a smaller non-address system bottleneck can stop thread creation first.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - 线程上限的地址预算边界
- Review timing: 3 days after repair
- Prompt: 为什么 `3G / 10M ≈ 300` 只能是特定环境下的地址预算估算，而不是 Linux 固定线程上限？分别说一个会改变分子/分母的条件和一个地址外限制。
- Target: 区分地址预算估算与 Linux 固定上限
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - `threads-max` 全系统余量
- Review timing: same-day formation check
- Prompt: `threads-max=14553`，测试前已有 180 个其他线程；忽略其他限制，单个测试进程约还能新增多少线程？为什么不是 14553？
- Target: 用系统总预算减去已占用线程得到剩余余量
- Result: Correct on 2026-07-17: computed `14553 - 180 = 14373` and explicitly treated `threads-max` as a system-wide budget whose existing usage must be subtracted.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - `threads-max` 全系统余量
- Review timing: 1 week
- Prompt: 某系统 `threads-max=20000`，创建测试前已有 650 个线程；只从该预算估算测试进程还能新增多少线程，并说明为什么参数原值不是单进程额度。
- Target: 用系统总预算减去已占用线程得到剩余余量
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - `pid_max` 与 `max_map_count` 边界
- Review timing: same-day formation check
- Prompt: 已调大 `threads-max` 和 `pid_max`，线程数仍不增长。下一步检查哪个参数？它限制任务 ID 还是单进程 VMA 数量？
- Target: 区分任务 ID 预算与进程虚拟内存区域预算
- Result: Correct on 2026-07-17: selected `vm.max_map_count` and correctly identified it as the per-process VMA-count limit rather than the system task-ID limit.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - `pid_max` 与 `max_map_count` 边界
- Review timing: 1 week
- Prompt: 大量线程创建失败时，分别说明 `pid_max` 和 `vm.max_map_count` 限制什么；若前者已调大但线程数不变，为什么还要检查后者？
- Target: 区分任务 ID 预算与进程虚拟内存区域预算
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - 线程栈的 VSZ 与 RES
- Review timing: near-transfer formation check
- Prompt: `2G` 物理内存的机器上，进程为何可能显示 `VSZ≈25T`、`RES≈400M`？按栈范围预留、页面访问和物理驻留解释。
- Target: 把虚拟空间与物理页边界迁移到线程栈
- Result: Correct on 2026-07-17: explained that most reserved stack pages remain untouched and therefore do not enter RES, and also preserved the real costs of kernel thread objects, page tables, touched stack pages, and scheduling.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - 线程栈的 VSZ 与 RES
- Review timing: 1 week
- Prompt: 一个进程为大量线程预留了数 TB 栈地址范围，但 RSS 远小于该值。解释未访问页、首次访问和物理驻留，并说出两项虚拟预留之外的真实成本。
- Target: 把虚拟空间与物理页边界迁移到线程栈
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - 单进程线程上限全章综合迁移
- Review timing: integrated transfer
- Prompt: 地址预算支持 100 万、`threads-max` 余量 6 万、`pid_max` 余量 5 万、VMA 余量支持 3 万且运行资源足够时，哪个预算先限制线程数？只减小线程栈是否会提高最终上限？
- Target: 用多个预算的最小余量诊断线程创建瓶颈
- Result: Partially correct on 2026-07-17: correctly predicted failure at about 30,000 threads with VMA headroom as the minimum budget, but incorrectly claimed that reducing stack size would increase the VMA-count allowance.
- Next review: same-day size-vs-count repair

## 2026-07-17

- Topic: OS - 线程栈大小与 VMA 数量边界修复
- Review timing: same-day repair
- Prompt: 把栈从 `8M` 降到 `1M` 改变的是每线程地址占用还是 `vm.max_map_count` 的数量上限？若 VMA 仍只支持约 3 万线程，最终上限会提高吗？
- Target: 区分地址大小预算与映射数量预算
- Result: Correct on 2026-07-17 after repair: distinguished the per-thread stack setting from the per-process VMA-count limit and correctly concluded that the final ceiling remains about 30,000 while VMA headroom is still the minimum budget. Preferred wording: stack mapping size changes; VMA-count limit does not.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - 单进程线程上限全章综合复习
- Review timing: 1 week
- Prompt: 给定地址空间/栈、`threads-max` 剩余量、`pid_max` 剩余量、VMA 余量和 CPU/内存状态，先选最小预算；再判断减小栈或调大某个非瓶颈参数是否会改变最终线程上限，并解释 `VSZ` 与 `RES` 的差异。
- Target: 用“环境 + 多预算取最小值”估算和诊断线程上限
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - 线程故障类型与影响范围
- Review timing: same-day formation check
- Prompt: 场景 A 是 Java 工作线程抛出未捕获 `NullPointerException`；场景 B 是 C 工作线程执行 `*NULL = 1`，Linux 递送 `SIGSEGV`，且进程没有自定义处理器。常见默认情况下，A 与 B 分别通常终止“当前线程”还是“整个进程”？各写出负责处置的那一层。
- Target: 用“故障类型 -> 处置层 -> 影响范围”判断线程故障结局
- Result: Partially correct on 2026-07-17: correctly judged that A usually terminates only the current Java thread and B terminates the entire process, and correctly ran B through CPU synchronous fault to Linux `SIGSEGV` default termination; called A a normal thread exit instead of abnormal termination through the JVM/Java exception mechanism.
- Next review: same-day normal-return-vs-abnormal-termination repair

## 2026-07-17

- Topic: OS - Java 线程正常返回与异常终止边界
- Review timing: same-day repair
- Prompt: 未捕获 `NullPointerException` 由哪一层的什么机制处理？当前 Java 线程属于“正常返回”还是“异常终止”？
- Target: 区分线程函数正常 return 与未捕获语言级异常导致的线程终止
- Result: Correct on 2026-07-17: identified the JVM layer and correctly classified the current Java thread as abnormally terminated; completed wording is the JVM-supported Java exception mechanism rather than a normal function return.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - 线程故障类型与影响范围
- Review timing: 3 days after repair
- Prompt: 比较线程函数正常 `return`、未捕获 Java 异常和 C 线程非法访存：分别指出处置层与常见默认影响范围。
- Target: 用“故障类型 -> 处置层 -> 影响范围”判断线程故障结局
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - 原生非法访存到进程终止
- Review timing: same-day formation check
- Prompt: 一个线程向只读页执行写入：①谁首先发现权限不允许？②Linux 通常把它转换为什么信号，并递送给谁？③进程没有自定义处理器时，默认终止当前线程还是整个进程？
- Target: 用“地址检查 -> 同步异常 -> SIGSEGV -> 信号处置”运行原生故障链
- Result: Correct on 2026-07-17: identified CPU/MMU as the first detector, `SIGSEGV` as the Linux signal delivered to the faulting thread, and whole-process termination as the default action without a custom handler.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - 原生非法访存到进程终止
- Review timing: 1 week
- Prompt: 对一次只读页写入，按顺序说明硬件检测、内核转换、信号递送对象与默认终止范围，并解释为什么“递送给线程”不等于“只终止线程”。
- Target: 用“地址检查 -> 同步异常 -> SIGSEGV -> 信号处置”运行原生故障链
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - 捕捉信号与修复故障的边界
- Review timing: same-day formation check
- Prompt: 原文程序忽略 `raise(SIGSEGV)` 后仍能打印，能否据此推出真实空指针解引用也能被安全忽略？回答能/不能，并说明若处理器既不修复地址也不改变 PC，返回后通常会发生什么。
- Target: 用“信号已处理 ≠ 故障已修复”判断能否恢复
- Result: Partially correct on 2026-07-17: correctly rejected the inference that a real null dereference can be safely ignored, but said execution would continue at the next statement; that continuation applies to software-generated `raise(SIGSEGV)`, not to a synchronous fault whose address and PC remain unchanged.
- Next review: same-day fault-PC repair

## 2026-07-17

- Topic: OS - 同步故障处理器返回位置
- Review timing: same-day repair
- Prompt: 真实空指针解引用触发处理器后，若地址和 PC 都没改变，处理器返回时 CPU 下一步会重试原故障指令还是执行后面的正常语句？随后通常会发生什么？
- Target: 区分软件主动信号后的顺序执行与同步故障的原指令重试
- Result: Correct on 2026-07-17: stated that the CPU retries the original faulting instruction and normally triggers `SIGSEGV` again when neither the address nor PC has changed.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - 同步故障处理器返回位置
- Review timing: 3 days after repair
- Prompt: 对比被忽略的 `raise(SIGSEGV)` 与真实空指针故障：处理结束后的下一条 CPU 指令分别在哪里，为什么结果不同？
- Target: 区分软件主动信号后的顺序执行与同步故障的原指令重试
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - SIGTERM、SIGKILL 与优雅停机
- Review timing: same-day formation check
- Prompt: Java 服务停机前必须执行关闭钩子和资源清理，应使用 `kill <pid>` 还是 `kill -9 <pid>`？两者默认分别发送什么信号、能否被进程捕捉？另外，除 `SIGKILL` 外还有哪个信号也不能被捕捉或忽略？
- Target: 用信号是否可捕捉决定优雅退出或强制终止
- Result: Correct on 2026-07-17: selected `kill <pid>` for graceful shutdown, mapped it to catchable `SIGTERM(15)`, mapped `kill -9` to uncatchable/unignorable/unblockable `SIGKILL(9)`, and named `SIGSTOP` as the other exception.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - 信号处置与受控恢复综合复习
- Review timing: 1 week
- Prompt: 对比 `raise(SIGSEGV)`、真实同步内存故障、`SIGTERM` 与 `SIGKILL`：分别说明能否忽略/捕捉、处理后从哪里继续，以及是否有用户态清理机会。
- Target: 用“信号已处理 ≠ 故障已修复”判断能否恢复
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - HotSpot continuation stub
- Review timing: same-day formation check
- Prompt: HotSpot 收到 `SIGSEGV` 并识别为可恢复的隐式空指针故障，得到 `stub != NULL`。处理器返回时 CPU 会重试原故障指令还是跳到 stub？随后 Java 层看到什么？
- Target: 用“识别故障 -> 选择 stub -> 改写 PC -> 抛出 Throwable”解释 HotSpot 恢复
- Result: Correct on 2026-07-17: stated that the CPU jumps to the continuation stub rather than retrying the faulting instruction, then enters the Java exception path and throws `NullPointerException` or `StackOverflowError`.
- Next review: same-day caught-vs-uncaught transfer

## 2026-07-17

- Topic: OS - JVM 已翻译异常的捕获边界
- Review timing: near-transfer formation check
- Prompt: 同一个被 HotSpot 翻译出的 `NullPointerException`：线程 T1 有匹配的 `catch`，线程 T2 没有任何匹配的 `catch`。T1、T2 各自怎样结束或继续？两种情况下 JVM 进程通常是否继续运行？
- Target: 把 HotSpot 底层故障翻译接到 Java catch 与未捕获线程终止语义
- Result: Correct on 2026-07-17: T1 unwinds to and executes the matching `catch`; T2 propagates to the thread entry and terminates abnormally; in both cases the JVM normally continues because HotSpot successfully translated the fault.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - HotSpot 已识别故障翻译
- Review timing: 1 week
- Prompt: 从一个被识别的隐式空指针 `SIGSEGV` 开始，说明 stub 选择、PC 改写、Java Throwable，以及被捕获/未捕获时线程和 JVM 的不同结局。
- Target: 用“识别故障 -> 选择 stub -> 改写 PC -> 抛出 Throwable”解释 HotSpot 恢复
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - HotSpot fatal fallback
- Review timing: same-day formation check
- Prompt: JNI 野指针触发 `SIGSEGV`，HotSpot 无法匹配已知空指针/栈溢出模式，因此 `stub == NULL`。接下来会只终止 JNI 所在线程，还是终止整个 JVM？关键会进入哪个方法，并尝试生成什么诊断文件？
- Target: 用“stub == NULL -> VMError -> report_and_die”判断 JVM 致命故障
- Result: Correct on 2026-07-17: concluded that the entire JVM terminates through `report_and_die` and that HotSpot attempts to generate `hs_err_pid...log`.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - HotSpot fatal fallback
- Review timing: 1 week
- Prompt: JNI 未知野指针故障得到 `stub == NULL` 时，从 `VMError`、`report_and_die`、诊断日志到最终影响范围完整说明路径，并解释为什么不能只丢弃 JNI 线程。
- Target: 用“stub == NULL -> VMError -> report_and_die”判断 JVM 致命故障
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - thread_crash 全章综合迁移
- Review timing: integrated transfer
- Prompt: 综合判断三个场景的“处理路径 + 最终影响范围”：A）C 线程写只读页，进程无自定义处理器；B）HotSpot 识别隐式空指针并得到 `stub != NULL`，但 Java 层无匹配 `catch`；C）JNI 野指针触发未知 `SIGSEGV`，`stub == NULL`。分别说明信号/运行时怎样处理，以及最终终止当前线程还是整个进程/JVM。
- Target: 用“故障类型 -> 处置层 -> 安全续点 -> 影响范围”综合判断线程故障
- Result: Pending
- Next review: after learner answer

## 2026-07-17

- Topic: OS - 进程调度、页面置换与磁盘调度统一框架
- Review timing: same-day formation check
- Prompt: 发生合法缺页且物理内存已满时，按“触发条件 -> 候选集合 -> 选择对象 -> 优化目标”四项说明页面置换算法正在解决什么问题。
- Target: 用“触发条件 -> 候选集合 -> 选择对象 -> 优化目标”统一三类调度
- Result: Correct on 2026-07-17: identified the exact trigger as a page fault with no free physical frame, the candidates as currently resident replaceable physical pages, the choice as a victim page, and the goal as reducing page faults, swaps and dirty-page writeback cost.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - 三类调度统一选择框架
- Review timing: 1 week
- Prompt: 分别对 CPU 调度、页面置换和机械磁盘调度填写“触发条件、候选集合、选择对象、主要目标”，并指出页面置换为什么不是选择要调入哪一页。
- Target: 用“触发条件 -> 候选集合 -> 选择对象 -> 优化目标”统一三类调度
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - CPU 调度发生点与抢占边界
- Review timing: same-day formation check
- Prompt: A 因等待磁盘从运行态进入等待态；B 因时间片耗尽从运行态回到就绪态。两种情况是否都会触发 CPU 重新选择运行者？哪一种属于抢占，为什么？
- Target: 区分“发生 CPU 调度”与“抢占当前运行者”
- Result: Partially correct on 2026-07-17: correctly identified B as preemption because the time slice expired while the process remained runnable, but answered that the two cases do not both trigger scheduling. A also requires a new CPU choice because the waiting process can no longer run and releases the CPU; this is scheduling without preemption.
- Next review: same-day repair

## 2026-07-17

- Topic: OS - 调度不等于抢占的单边修复
- Review timing: same-day repair
- Prompt: A 从运行态进入等待态后已经不能继续使用 CPU。此时操作系统是否仍要从就绪队列选择下一个运行者？这属于调度还是抢占？
- Target: 保留已掌握的 B 抢占，只修复“非抢占场景仍会发生调度”
- Result: Correct after repair on 2026-07-17: answered that the OS still selects another ready process and classified the event as scheduling, not preemption.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - 调度与抢占边界
- Review timing: 3 days after repair
- Prompt: 分别判断“运行进程因 I/O 阻塞”“运行进程时间片耗尽”“等待中的高优先级进程被唤醒”是否发生调度、是否一定发生抢占，并给出同一个判断标准。
- Target: 区分“发生 CPU 调度”与“抢占当前运行者”
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - FCFS、SJF、HRRN 决策证据比较
- Review timing: near-transfer check
- Prompt: CPU 空闲时，A 比 B 更早到达；A 已等待 6 秒、要求服务 6 秒，B 已等待 1 秒、要求服务 2 秒。FCFS、SJF、HRRN 分别选谁？HRRN 写出两个响应比。
- Target: 用“到达顺序、服务时间、等待老化”区分 FCFS、SJF、HRRN
- Result: Skipped by learner on 2026-07-17 because the CPU scheduling algorithms were previously learned; not reassessed in this chapter.
- Next review: retain existing prior review schedule

## 2026-07-17

- Topic: OS - OPT、FIFO、LRU 三条时间轴
- Review timing: same-day formation check
- Prompt: 内存中的 A 最早装入且刚被访问，未来 2 步后再访问；B 第二个装入但过去最久没被访问，未来 5 步后再访问；C 最晚装入、最近访问比 B 新，未来 20 步后再访问。发生缺页且无空闲页框时，OPT、FIFO、LRU 分别淘汰谁？
- Target: 用“未来、进入时间、最近访问”区分 OPT、FIFO、LRU
- Result: Correct on 2026-07-17: selected C for OPT from the farthest future access, A for FIFO from the earliest admission, and B for LRU from the oldest past access.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - OPT、FIFO、LRU 三条时间轴
- Review timing: 1 week
- Prompt: 构造三个驻留页，使 OPT、FIFO、LRU 分别淘汰不同页面；说明每个算法读取的是未来、装入时间还是过去访问历史。
- Target: 用“未来、进入时间、最近访问”区分 OPT、FIFO、LRU
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - Clock 第二次机会与 LFU 频率老化
- Review timing: same-day formation check
- Prompt: Clock 指针依次检查 A:1、B:1、C:0，发生缺页时会修改哪些位并淘汰谁？同一时刻 LFU 计数为 A=100（访问全在很久以前）、B=3（最近刚开始频繁访问）、C=5，它会淘汰谁，为什么这种选择可能误伤当前热点？
- Target: 用“访问位第二次机会 + 频率计数老化”运行 Clock 与 LFU
- Result: Correct on 2026-07-17: cleared A and B from 1 to 0 before evicting C at bit 0; selected B under LFU and explained that A's old count of 100 hides its current coldness while B's new-hot count is still small.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - Clock 第二次机会与 LFU 频率老化
- Review timing: 1 week
- Prompt: 给定一圈访问位，逐步写出 Clock 的清零、前移和淘汰过程；再解释一个过去高频但现在冷的页为什么会让无老化 LFU 误伤新热点，以及计数衰减如何修复。
- Target: 用“访问位第二次机会 + 频率计数老化”运行 Clock 与 LFU
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - 机械磁盘 FCFS 与 SSTF
- Review timing: same-day formation check
- Prompt: 初始磁头在 53，请求按到达顺序为 98、14、65。磁盘 FCFS 的服务顺序是什么？SSTF 每一步重新计算距离后的服务顺序是什么？哪一种存在远端请求饥饿风险？
- Target: 用“到达顺序 vs 当前磁头距离”运行磁盘 FCFS 与 SSTF
- Result: Correct on 2026-07-17: preserved arrival order 98-14-65 for FCFS, recomputed SSTF as 65-98-14 from head 53, and identified SSTF's remote-request starvation risk.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - 机械磁盘 FCFS 与 SSTF
- Review timing: 1 week
- Prompt: 给定一个初始磁头和四个请求，分别写出 FCFS 与 SSTF 路径；SSTF 每步标出当前距离，并构造附近请求持续到达导致远端请求饥饿的情形。
- Target: 用“到达顺序 vs 当前磁头距离”运行磁盘 FCFS 与 SSTF
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - SCAN 与 C-SCAN 的返程服务边界
- Review timing: same-day formation check
- Prompt: 磁道范围 0-199，磁头在 53，请求为 14、37、65、67、98，初始方向向大磁道。写出 SCAN 和 C-SCAN 在所有请求完成前的移动路径，包含到达的物理端点；C-SCAN 的 199->0 途中是否服务请求？
- Target: 用“返程是否服务请求”区分 SCAN 与 C-SCAN
- Result: Partially correct on 2026-07-17: gave the correct request-service orders, SCAN as 65-67-98-37-14 and C-SCAN as 65-67-98-14-37, and correctly said C-SCAN does not serve during reset; omitted the physical endpoints from the requested movement paths.
- Next review: same-day endpoint repair

## 2026-07-17

- Topic: OS - SCAN/C-SCAN 物理端点补全
- Review timing: same-day repair
- Prompt: 只补非请求位置：SCAN 在服务 98 后先到哪个物理端点再反向服务 37、14？C-SCAN 在服务 98 后依次经过哪两个物理端点，才继续服务 14、37？
- Target: 保留正确服务顺序，只补 SCAN/C-SCAN 移动路径中的 199 与 0
- Result: Correct after repair on 2026-07-17: completed SCAN as 65-67-98-199-37-14 and C-SCAN as 65-67-98-199-0-14-37.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - SCAN/C-SCAN 完整移动路径
- Review timing: 3 days after repair
- Prompt: 给定磁道范围、初始位置、方向和两侧请求，分别写出 SCAN 与 C-SCAN 的完整磁头移动路径；圈出没有请求但必须经过的物理端点，并标出 C-SCAN 的不服务复位段。
- Target: 用“返程是否服务请求”区分 SCAN 与 C-SCAN
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - LOOK 与 C-LOOK 端点优化
- Review timing: same-day formation check
- Prompt: 磁头在 53，请求为 14、37、65、67、98，初始方向向大磁道。写出 LOOK 与 C-LOOK 在所有请求完成前的路径；两条路径是否还经过 199 或 0？C-LOOK 从 98 跳到 14 时是否服务途中请求？
- Target: 用“最远请求替代物理端点”从 SCAN/C-SCAN 推导 LOOK/C-LOOK
- Result: Correct on 2026-07-17: produced LOOK as 65-67-98-37-14 and C-LOOK as 65-67-98-14-37, excluded physical endpoints 199 and 0, and correctly treated the 98->14 C-LOOK jump as unserved.
- Next review: 2026-07-24

## 2026-07-24

- Topic: OS - LOOK 与 C-LOOK 端点优化
- Review timing: 1 week
- Prompt: 从一组 SCAN/C-SCAN 路径出发，删除没有请求的物理端点得到 LOOK/C-LOOK；说明 LOOK 返程服务、C-LOOK 跳转不服务的边界。
- Target: 用“最远请求替代物理端点”从 SCAN/C-SCAN 推导 LOOK/C-LOOK
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - 三类调度算法综合选型
- Review timing: integrated transfer
- Prompt: 三个场景分别选一个本章算法并给出一句机制理由：A）CPU 同时有短交互任务和长 CPU 任务，服务时间未知；B）页面置换希望接近 LRU，但不能承担每次访问维护完整链表的成本；C）机械磁盘希望只沿一个方向服务、响应频率较均匀，并避免走到无请求的物理端点。
- Target: 用“资源对象 + 可用信息 + 主导代价”跨域选择调度算法
- Result: Partially correct on 2026-07-17: correctly selected MLFQ for the mixed unknown-length CPU workload and C-LOOK for one-direction uniform disk service without physical-endpoint travel; selected LFU for the low-cost LRU approximation, but the intended mechanism is Clock. LFU tracks frequency, whereas Clock uses a reference bit and circular scan to approximate recent use without maintaining an exact LRU list.
- Next review: same-day page-policy repair

## 2026-07-17

- Topic: OS - Clock vs LFU 选型边界
- Review timing: same-day repair
- Prompt: 页面置换的需求是“接近 LRU，但不维护每次访问都更新的完整链表”。应选 Clock 还是 LFU？一句话说明它用什么低成本信息近似 LRU；另一算法实际统计的又是什么。
- Target: 保留 MLFQ 与 C-LOOK，只修复“最近使用近似 vs 访问频率”
- Result: Correct after repair on 2026-07-17: selected Clock, explained that it avoids exact access ordering while approximating whether a page was used recently, and correctly separated LFU as cumulative-frequency tracking vulnerable to old-history pollution.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - 三类调度算法综合选型
- Review timing: 3 days after repair
- Prompt: 三个变体各选算法并给机制理由：未知服务时间的混合 CPU 负载；不能维护精确 LRU 但要近似近期使用的页面置换；只沿一个方向服务且不走无请求物理端点的机械磁盘调度。再指出为什么 LFU 不满足第二项。
- Target: 用“资源对象 + 可用信息 + 主导代价”跨域选择调度算法
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - 文件系统名字、身份与内容
- Review timing: same-day formation check
- Prompt: `/docs/a.txt` 和 `/backup/a-link` 是同一个文件的两个硬链接。它们应有几个文件名、几个 dentry、几个 inode、几份文件数据？
- Target: 用“名字 -> inode -> 数据块”定位文件对象
- Result: Partially correct on 2026-07-17: correctly identified two names, one inode and one data copy, but compressed the two name mappings into one dentry. Each hard-link name has its own directory record/dentry before both point to the shared inode.
- Next review: same-day dentry repair

## 2026-07-17

- Topic: OS - 硬链接的 dentry 基数
- Review timing: same-day repair
- Prompt: 两个硬链接名字各自需要缓存自己的“名字 -> inode”映射。它们应有一个还是两个 dentry？为什么不能共用一个？
- Target: 保留共享 inode 与数据，只修复“每个名字各有一个 dentry”
- Result: Correct after repair on 2026-07-17: drew `/docs/a.txt -> dentry A -> inode 42` and `/backup/a-link -> dentry B -> inode 42`, preserving the shared inode and data.
- Next review: 2026-07-20

## 2026-07-20

- Topic: OS - 文件系统名字、dentry 与 inode
- Review timing: 3 days after repair
- Prompt: 同一 inode 有三个硬链接名字，分别位于两个目录。画出名字、目录记录、dentry、inode 和数据块的关系，并说明删除一个名字后哪些对象仍可继续使用。
- Target: 用“名字 -> inode -> 数据块”定位文件对象，复查每个名字各有 dentry
- Result: Pending
- Next review: TBD

## 2026-07-17

- Topic: OS - VFS 统一接口与具体后端
- Review timing: same-day formation check
- Prompt: 程序读取 Ext4 普通文件和 `/proc/cpuinfo` 都能调用 `read`。是哪一层提供相同接口？两者的实际数据来源分别是什么？
- Target: 用 VFS 区分统一接口与具体文件系统后端
- Result: Correct on 2026-07-18: identified VFS as the common interface layer and distinguished Ext4 local-disk data from `/proc/cpuinfo` information generated from kernel data.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - VFS 统一接口与具体后端
- Review timing: 1 week
- Prompt: 同一个程序分别读取 Ext4 文件、`/sys` 属性和 NFS 文件。指出共同接口层、三个具体后端的数据来源，以及挂载在其中承担的作用。
- Target: 用 VFS 区分统一接口与磁盘、内存、网络文件系统后端
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - `open` 与文件描述符
- Review timing: same-day formation check
- Prompt: `fd = open("/tmp/a.txt", O_WRONLY)` 返回 `3`。后续 `write(3, ...)` 中的 `3` 表示文件名、inode 编号，还是当前进程打开文件表中的一个句柄？为什么不必再次传路径？
- Target: 用 `open` 将路径名转换为打开实例 `fd`
- Result: Correct on 2026-07-18: identified fd as a handle in the current process open-file table and explained that `read/write/close` recover the established open state instead of reparsing the path.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - `open`、路径名与文件描述符
- Review timing: 1 week
- Prompt: 两个进程都持有数值为 `3` 的 fd。为什么不能据此断言它们打开同一文件？分别说明路径名、inode 和 fd 的角色。
- Target: 用 `open` 将路径名转换为进程内打开实例 `fd`
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 用户字节请求与文件系统逻辑块
- Review timing: same-day formation check
- Prompt: 文件系统逻辑块为 4KB，进程在文件偏移 100B 处只写 1B。文件系统是只定位并操作一个独立的 1B 磁盘单元，还是先定位包含该字节的逻辑块并修改其中对应部分？为什么？
- Target: 区分用户字节请求与文件系统逻辑块操作
- Result: Correct on 2026-07-18: stated that the file system first locates the containing logical block and modifies the corresponding part because logical blocks are its basic management unit.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 用户字节请求与文件系统逻辑块
- Review timing: 1 week
- Prompt: 逻辑块为 4KB，分别读取偏移 4095B 和 4096B 处的 1B。写出各自的逻辑块号与块内偏移，并说明用户的 1B 请求为什么不等于独立的 1B 磁盘块。
- Target: 区分用户字节范围与文件系统逻辑块级定位
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 连续文件分配
- Review timing: same-day formation check
- Prompt: 磁盘有两个不相邻的空洞，分别为 4 块和 6 块；新文件需要连续 8 块。总空闲量是 10 块，连续分配能否成功？这暴露了什么问题？
- Target: 用“起始块 + 长度”理解连续文件分配的取舍
- Result: Correct on 2026-07-18: concluded allocation fails because no contiguous hole reaches 8 blocks despite 10 total free blocks, and identified external fragmentation.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 连续文件分配
- Review timing: 1 week
- Prompt: 文件起始块为 100、长度为 5；块 105 已被占用。写出文件当前范围，并解释新增 2 块为什么不能直接扩展，以及这与外部碎片有什么关系。
- Target: 用“起始块 + 长度”判断连续布局、扩展与外部碎片
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 隐式链式文件分配
- Review timing: same-day formation check
- Prompt: 文件的物理块链为 `4 -> 7 -> 2 -> 10`。要访问文件的第 3 个逻辑块，能否从文件头直接跳到物理块 2？必须经过怎样的路径，这说明随机访问性能如何？
- Target: 用块内 `next` 指针运行隐式链式分配
- Result: Correct on 2026-07-18: followed `4 -> 7 -> 2`, explained that the file header only exposes the chain head, and concluded random access is poor because pointers must be followed from the start.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 隐式链式文件分配
- Review timing: 1 week
- Prompt: 块链为 `8 -> 3 -> 20 -> 6 -> 11`。写出访问第 4 个逻辑块的路径，并说明块 3 的 next 指针损坏后哪些逻辑块仍可定位、哪些会丢失。
- Target: 用块内 `next` 指针运行隐式链并判断链断裂边界
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - FAT 显式链接
- Review timing: same-day formation check
- Prompt: 按原文估算：200GB 磁盘、1KB 数据块、每个 FAT 表项 4B。FAT 约有多少个表项、占多少内存？这说明它为什么不适合大磁盘？
- Target: 用全盘 FAT 将块链指针移到内存表
- Result: Partially correct on 2026-07-18: correctly explained one FAT entry per block and the large-disk/small-block memory trend, but converted 200GB to only 200,000KB, making both results 1000 times too small. Correct scale is 200 million entries and about 800MB.
- Next review: same-day capacity-unit repair

## 2026-07-18

- Topic: OS - FAT 容量单位链
- Review timing: same-day repair
- Prompt: 只补单位换算：按原文十进制估算，`200GB = ? KB`；除以 `1KB/block` 后有多少表项；再乘 `4B` 等于多少 MB？
- Target: 保留 FAT 机制，只修复 `GB -> KB -> entries -> MB`
- Result: Correct after repair on 2026-07-18: converted 200GB to 200,000,000KB, obtained 200 million FAT entries, and calculated about 800MB.
- Next review: 2026-07-21

## 2026-07-21

- Topic: OS - FAT 结构与容量估算
- Review timing: 3 days after repair
- Prompt: 500GB 磁盘使用 2KB 块，每个 FAT 表项 4B。按十进制量级估算表项数和 FAT 大小，并解释为什么 FAT 查链比隐式链快但仍不是索引式直接定位。
- Target: 用全盘 FAT 将块链指针移到内存表，并保持容量单位链
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 每文件索引块
- Review timing: same-day formation check
- Prompt: 某文件索引块的前四项是 `[9, 4, 12, 7]`。访问文件第 3 个逻辑块（从 1 开始）时，应直接读哪一项、得到哪个物理块？是否需要先遍历前两项？
- Target: 用每文件索引块直接映射逻辑块到物理块
- Result: Correct on 2026-07-18: read the third index entry directly, obtained physical block 12, and correctly avoided traversing the preceding entries.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 每文件索引块
- Review timing: 1 week
- Prompt: 索引块为 `[31, 8, 17, 40, 6]`。分别定位第 2 和第 5 个逻辑块，并解释为什么不需要经过前面的数据块；再指出一个只有 1 个数据块的小文件采用该方案的额外成本。
- Target: 用索引项直接映射逻辑块到物理块并识别元数据开销
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 链式索引块与多级索引块
- Review timing: same-day formation check
- Prompt: 若链式索引块 A 指向下一索引块 B 的 `next` 指针损坏，B 及其后的数据还能否从 A 找到？若希望由顶层索引按层级选择下层索引块，应采用链式索引还是多级索引？
- Target: 区分 `next` 链扩展与层级索引扩展
- Result: Correct on 2026-07-18: concluded B and later blocks become unreachable after the chained-index pointer breaks, and selected multilevel indexing for hierarchical lookup.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 链式索引块与多级索引块
- Review timing: 1 week
- Prompt: 画出三个索引块的 next 链和一个两级索引树；分别说明中间指针损坏的影响范围，以及访问深层数据块需要经过哪些元数据节点。
- Target: 区分链式索引扩展与多级索引扩展
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - Unix inode 混合索引
- Review timing: same-day formation check
- Prompt: 早期 Unix inode 有 10 个直接指针及一级、二级、三级间接指针。文件只需 8 个数据块时走哪类指针？访问文件第 11 个逻辑块时，为什么开始经过一级间接索引块？
- Target: 用 Unix inode 的直接与间接指针匹配文件大小
- Result: Correct on 2026-07-18: used direct pointers for an 8-block file and correctly traced the 11th logical block through the inode's single-indirect pointer, index block, and data block.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - Unix inode 混合索引
- Review timing: 1 week
- Prompt: 对一个经典 `10 direct + single + double + triple` inode，分别说明第 6 个、第 11 个以及超出一级间接容量后的逻辑块走什么路径，并比较查询层数。
- Target: 用直接与间接指针匹配文件大小和访问成本
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 空闲表
- Review timing: same-day formation check
- Prompt: 空闲表依次为 `(10,3)、(20,6)、(40,2)`，每项是“起始块、连续块数”。请求连续 5 块时，顺序扫描会选哪项、分配哪些块？该项剩余部分怎样更新？
- Target: 用“起始块 + 块数”运行空闲表
- Result: Correct on 2026-07-18: selected `(20,6)`, allocated blocks 20 through 24, and updated the remaining free extent to `(25,1)`.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 空闲表
- Review timing: 1 week
- Prompt: 空闲表为 `(4,2)、(10,7)、(30,5)`，请求连续 4 块。写出选择、分配范围和更新后条目；再解释大量小空洞为何让空闲表不适合大型文件系统。
- Target: 用“起始块 + 块数”运行和评价空闲表
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 空闲链表
- Review timing: same-day formation check
- Prompt: 空闲链表为 `5 -> 9 -> 2 -> 14`，创建文件需要 2 块。应从链头依次分配哪两块？分配后新的链头和剩余链是什么？
- Target: 用链头弹出与接回运行空闲链表
- Result: Correct on 2026-07-18: allocated blocks 5 and 9 from the head, then set the new head to block 2 with remaining chain `2 -> 14`.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 空闲链表
- Review timing: 1 week
- Prompt: 空闲链为 `12 -> 4 -> 19 -> 7`。先分配 3 块，再回收块 30 并接到链头；写出每一步链头和剩余链，并说明该结构的大型系统瓶颈。
- Target: 用链头弹出与接回运行空闲链表
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 空闲位图
- Review timing: same-day formation check
- Prompt: 位图为 `110010`，位下标从 0 开始，`0=空闲、1=已分配`。哪些块空闲？若分配块 3，位图更新为什么？
- Target: 用“一块一位”读写空闲位图
- Result: Correct on 2026-07-18: identified blocks 2, 3 and 5 as free and changed bit 3 from 0 to 1, producing `110110`.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 空闲位图
- Review timing: 1 week
- Prompt: 位图 `10100100` 使用 `0=空闲、1=已分配`。列出空闲块；依次分配块 1、释放块 2 后写出新位图，并说明 inode 为什么需要另一张位图。
- Target: 用“一块一位”读写数据块与 inode 空闲状态
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 位图覆盖范围与 Ext 块组
- Review timing: same-day formation check
- Prompt: 一个 4KB 位图、每 bit 管理一个 4KB 数据块。它共有多少 bit、能管理多少数据块、覆盖多少 MB 数据空间？为什么大型文件系统需要多个块组？
- Target: 用位图覆盖上限推导 Ext 块组
- Result: Correct on 2026-07-18: calculated 32K bits, 32K managed data blocks and 128MB coverage, then explained that multiple block groups extend the limited range of one bitmap.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 位图覆盖范围与 Ext 块组
- Review timing: 1 week
- Prompt: 一个 2KB 位图、每个数据块 8KB。计算可管理块数和覆盖容量；再说明为什么多个块组共同组成一个文件系统，而不是多个独立文件系统。
- Target: 用位图覆盖上限推导块组规模扩展
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - Ext 块组布局目标
- Review timing: same-day formation check
- Prompt: Ext2 为什么在多个块组保留超级块/块组描述符副本，同时又尽量让文件的 inode、位图和数据块靠近？分别对应哪两个设计目标？
- Target: 用“冗余恢复 + 数据局部性”解释块组布局
- Result: Correct on 2026-07-18: tied duplicate critical metadata to recovery reliability and local inode/bitmap/data placement to reduced seek/rotation cost and better performance.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - Ext 块组布局目标
- Review timing: 1 week
- Prompt: 设计块组时，为什么不只保留唯一超级块，也不把所有 inode 集中在磁盘一端？分别从故障恢复、空间开销和机械磁盘访问成本回答。
- Target: 用冗余恢复与数据局部性解释块组布局
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 目录文件的列表与哈希存储
- Review timing: same-day formation check
- Prompt: 目录 `/docs` 中有 `a.txt`，其 inode 为 42。目录数据块保存的是 `a.txt` 的正文，还是 `a.txt -> inode 42` 的记录？若目录有一百万个名字，列表与哈希表更适合哪个，为什么？
- Target: 把目录文件运行成“文件名 -> inode”的索引
- Result: Correct on 2026-07-18: identified the persistent directory record as `a.txt -> inode 42`, not file contents, and selected hashing for typically faster lookup in a million-name directory.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 目录文件的列表与哈希存储
- Review timing: 1 week
- Prompt: 一个目录从 20 个文件增长到一百万个文件时，列表查找与哈希查找的路径有何不同？哈希为什么通常更快，又为什么仍须处理冲突？
- Target: 把目录文件运行成“文件名 -> inode”的索引
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 软链接与硬链接的身份模型
- Review timing: same-day formation check
- Prompt: `/docs/a.txt` 的 inode 是 42；再创建硬链接 `/backup/hard` 和软链接 `/backup/soft`。这两个新名字中，谁也指向 inode 42，谁拥有独立 inode 且文件内容保存 `/docs/a.txt`？删除 `/docs/a.txt` 后，两者分别还能否访问原数据？
- Target: 用“共享 inode”与“保存路径”区分硬链接和软链接
- Result: Correct on 2026-07-18: identified the hard link as another name for inode 42, the symbolic link as an independent inode storing the target path, and correctly predicted only the hard link would retain access after deleting the target name.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 软链接与硬链接的身份模型
- Review timing: 1 week
- Prompt: 为什么硬链接不能跨文件系统而软链接可以？删除最初创建的文件名后，分别沿 inode 与路径解析预测两种链接的结果。
- Target: 用“共享 inode”与“保存路径”区分硬链接和软链接
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 标准库缓冲与内核页缓存
- Review timing: same-day formation check
- Prompt: 程序直接调用 `write(fd, buf, n)`，没有经过 `fwrite/printf`，也没有设置 `O_DIRECT`。按原文的两条分类轴，它属于缓冲还是非缓冲 I/O？属于直接还是非直接 I/O？分别依据哪一层缓存判断？
- Target: 分开标准库缓冲与内核页缓存两条分类轴
- Result: Correct on 2026-07-18: classified the call as unbuffered at the standard-library layer and indirect at the operating-system layer because it still uses the kernel page cache.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 标准库缓冲与内核页缓存
- Review timing: 1 week
- Prompt: 比较 `fwrite`、普通 `write` 和带 `O_DIRECT` 的 `write`：分别是否经过标准库缓冲和内核页缓存，并给出两条轴上的分类。
- Target: 分开标准库缓冲与内核页缓存两条分类轴
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 非阻塞与异步的两阶段边界
- Review timing: same-day formation check
- Prompt: socket 设置了 `O_NONBLOCK`：第一次 `read` 发现数据未准备好便立即返回；稍后再次 `read`，由这次调用等待内核把数据复制到用户缓冲区后才返回。它是阻塞还是非阻塞 I/O？是同步还是异步 I/O？请分别用阶段 1 和阶段 2 解释。
- Target: 用 I/O 两阶段区分非阻塞与异步
- Result: Correct on 2026-07-18: classified the model as nonblocking but synchronous, using immediate return in stage 1 and application-initiated waiting for the kernel-to-user copy in stage 2.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 非阻塞与异步的两阶段边界
- Review timing: 1 week
- Prompt: 比较非阻塞 `read`、I/O 多路复用和真正异步 I/O：阶段 1 与阶段 2 分别由谁等待或完成？为什么前两者仍属于同步 I/O？
- Target: 用 I/O 两阶段区分非阻塞与异步
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - open/read 全链路综合迁移
- Review timing: final transfer
- Prompt: 假设页缓存最初未命中，程序直接执行 `fd = open("/docs/a.txt", O_RDONLY); read(fd, buf, 1);`，未使用标准库、`O_DIRECT` 或 `O_NONBLOCK`。按顺序追踪路径、目录记录/dentry、inode、fd、偏移、逻辑块、物理块、页缓存和用户缓冲区，并在四条 I/O 分类轴上归类。
- Target: 运行从路径名到用户缓冲区的 `open/read` 全链路
- Result: Pending
- Next review: after learner answer

## 2026-07-18

- Topic: OS - Page Cache 写后崩溃边界
- Review timing: same-day formation check
- Prompt: 同样是“崩溃”，为什么进程在普通 `write` 成功后被 `kill -9`，与机器在脏页回写前断电，数据结论不同？请只沿用户缓冲区、dirty Page Cache 和持久化设备说明。
- Target: 用“数据当前在哪一层”判断写后崩溃是否丢失
- Result: Correct on 2026-07-18: correctly kept successfully written bytes in the live kernel Page Cache after `kill -9`, and identified loss risk when whole-system power failure removes dirty pages before writeback.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - Page Cache 写后崩溃边界
- Review timing: 1 week
- Prompt: 普通 `write` 返回成功后，分别讨论进程崩溃、内核崩溃以及完成 `fsync` 后断电三个时间点；逐一说明最新数据位于哪一层、其他进程是否可见、重启后是否可靠。
- Target: 用“数据当前在哪一层”判断写后崩溃是否丢失
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 文件页与匿名页回收动作
- Review timing: same-day formation check
- Prompt: 内存紧张时，内核要回收三个页：A 是未修改的文件页，B 是已修改的文件页，C 是仍需保留的匿名页。A、B、C 分别能直接丢弃、需先写回原文件，还是需写入 Swap？
- Target: 用“可否从已有后备重建”决定页面回收动作
- Result: Correct on 2026-07-18: directly discarded the clean file-backed page, wrote the dirty file-backed page back to its original file, and sent the still-needed anonymous page to Swap.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 文件页与匿名页回收动作
- Review timing: 1 week
- Prompt: 内存回收候选包含 clean 文件页、dirty 文件页、仍需保留的匿名页和已经无引用的匿名页。分别选择直接释放、写回原文件或 swap out，并说明判断依据是“可否重建”还是“是否修改”。
- Target: 用“可否从已有后备重建”决定页面回收动作
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - Page Cache 文件页与设备块映射
- Review timing: same-day formation check
- Prompt: 一个 `4KB` 文件页对应四个 `1KB` 设备块。若该文件页已在 Page Cache 中，读取页内 1 字节时，缓存命中按“文件页”还是按“设备块”判断？文件系统底层仍需维护几个设备块的映射？
- Target: 用“文件页索引 → 块映射”分开 Page Cache 与 buffer cache
- Result: Correct on 2026-07-18: identified the Page Cache hit at file-page granularity and retained four `1KB` device-block mappings beneath one `4KB` file page.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - Page Cache 文件页与设备块映射
- Review timing: 1 week
- Prompt: 文件页大小为 `8KB`、设备块大小为 `2KB`。读取页内一个字节时，按什么对象判断 Page Cache 命中？该页在块层需要多少个设备块映射？为什么这不表示内核必然缓存两份相同数据？
- Target: 用“文件页索引 → 块映射”分开 Page Cache 与 buffer cache
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - SwapCached 的后备条件
- Review timing: same-day formation check
- Prompt: 一个匿名页曾被 swap out，随后 swap in 回内存。按原文逻辑，它进入 SwapCached 的关键条件是“曾经进过 Swap”，还是“Swap 中的旧副本仍然保留”？它因此变成普通文件内容了吗？
- Target: 用“Swap 副本仍在”解释 SwapCached
- Result: Incomplete on 2026-07-18: correctly required the Swap copy to remain, but incorrectly classified the page as content of an ordinary file. The backing object is a Swap slot, not a named file with an ordinary inode/path identity.
- Next review: same-day targeted repair

## 2026-07-18

- Topic: OS - SwapCached 与普通文件后备边界修复
- Review timing: same-day targeted repair
- Prompt: SwapCached 页的磁盘后备是什么？普通文件页的磁盘后备是什么？前者是否因此拥有普通文件的名字和 inode？
- Target: 用“Swap 副本仍在”解释 SwapCached
- Result: Correct after repair on 2026-07-18: identified the Swap slot as the SwapCached page's backing, ordinary file data as the file page's backing, and denied ordinary filename/inode identity to the anonymous page.
- Next review: 2026-07-21

## 2026-07-21

- Topic: OS - SwapCached 与普通文件后备边界
- Review timing: 3 days after repair
- Prompt: 对比一个 swap-in 且 Swap 副本仍保留的匿名页，以及一个普通文件页：分别指出磁盘后备、是否具有普通文件名/inode 身份，以及前者为什么只是在广义后备关系上类似 file-backed。
- Target: 用“Swap 副本仍在”解释 SwapCached
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - Page Cache 预读的本章迁移
- Review timing: source transfer check
- Prompt: 应用只请求 `4KB`，内核实际把相邻 `16KB` 读入 Page Cache。场景 X 后续顺序读完其余 `12KB`；场景 Y 完全不再访问。X 的收益是什么？Y 的两个代价是什么？
- Target: 用“预读收益 vs 未被使用”诊断预读失效
- Result: Correct on 2026-07-18: identified avoided future disk I/O after successful sequential prefetch, plus wasted memory and unnecessary disk I/O when the prefetched `12KB` remained unused. Unused pages may further evict hot pages.
- Next review: existing scheduled review remains active

## 2026-07-18

- Topic: OS - fsync、fdatasync 与 sync 的范围选择
- Review timing: same-day formation check
- Prompt: 三个场景分别选 `fdatasync`、`fsync` 还是 `sync`：A 只要求某文件的数据和后续访问必需的元数据落盘；B 要求某文件的数据及全部相关元数据落盘；C 要求系统范围的脏文件数据和元数据都进入同步流程。
- Target: 用“同步范围”选择 `fsync`、`fdatasync` 与 `sync`
- Result: Correct on 2026-07-18: selected `fdatasync(fd)` for file data plus necessary metadata, `fsync(fd)` for file data plus all related metadata, and `sync()` for system-wide dirty file state.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - fsync、fdatasync 与 sync 的范围选择
- Review timing: 1 week
- Prompt: 一个追加写需要保证新数据与文件大小，一个审计场景还要求修改时间等全部元数据，一个运维操作要求系统范围同步。分别选择接口并说明对象范围与元数据强度。
- Target: 用“同步范围”选择 `fsync`、`fdatasync` 与 `sync`
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 显式同步与后台 write-back 触发
- Review timing: same-day formation check
- Prompt: dirty page 有三种触发：A 应用调用 `fsync(fd)`；B 内核周期性唤醒写回；C 内存或脏页压力触发写回。A、B、C 分别由谁发起？其中哪一种让应用明确等待自己文件的同步边界？
- Target: 用“谁触发 + 应用是否等待”区分显式同步与后台 write-back
- Result: Correct on 2026-07-18: assigned A to explicit application initiation, B to kernel periodic writeback, C to kernel pressure-triggered writeback, and identified A as the application-controlled wait boundary.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 显式同步与后台 write-back 触发
- Review timing: 1 week
- Prompt: 普通 `write` 后 dirty page 尚未落盘。分别解释应用 `fsync`、内核周期任务和内存压力如何触发写回，以及哪种方式让应用在业务提交点主动等待。
- Target: 用“谁触发 + 应用是否等待”区分显式同步与后台 write-back
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - Direct I/O 的缓存路径与持久化边界
- Review timing: same-day formation check
- Prompt: 程序 A 使用普通 buffered `write`，返回后未调用 `fsync`；程序 B 使用 `O_DIRECT` 写，返回后也未使用 `O_SYNC` 或 `fsync`。谁绕过 Page Cache？能否仅凭两次 `write` 返回，就断言 A 或 B 已经获得断电持久性？
- Target: 用“缓存路径 ≠ 持久化保证”比较 buffered I/O 与 Direct I/O
- Result: Correct on 2026-07-18: identified program B as bypassing Page Cache and denied power-loss durability to both programs based only on `write` return.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - Direct I/O 的缓存路径与持久化边界
- Review timing: 1 week
- Prompt: 比较普通 buffered `write` 与 `O_DIRECT write`：分别是否经过 Page Cache、是否仍经过内核 I/O 栈、为什么 `O_DIRECT` 不能单独替代 `O_SYNC/fsync` 和恢复协议。
- Target: 用“缓存路径 ≠ 持久化保证”比较 buffered I/O 与 Direct I/O
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - Page Cache 全章综合迁移
- Review timing: final transfer
- Prompt: 服务用普通 buffered I/O 向已有日志文件末尾追加 `4KB`；`write` 返回后立即确认，未同步，随后进程被 `kill -9`。系统仍运行，另一进程读到了新记录；后台写回前机器断电。沿时间线判断数据位置、可见性、断电风险；若只要求数据和文件大小可靠，选择确认前的同步接口；再判断仅改为 `O_DIRECT` 能否替代同步。
- Target: 运行 Page Cache 写路径并选择可靠性边界
- Result: Incomplete on 2026-07-18: correctly traced data through dirty Page Cache, process death, cross-process visibility and power-loss risk. `fsync` would satisfy the requirement but `fdatasync` is the minimal match for data plus required file-size metadata. The final decision incorrectly treated `O_DIRECT` as automatic power-loss durability.
- Next review: same-day targeted repair

## 2026-07-18

- Topic: OS - Direct I/O 路径与持久化迁移修复
- Review timing: same-day targeted repair
- Prompt: `O_DIRECT` 决定是否绕过什么？`fdatasync/fsync/O_SYNC` 决定应用是否等待什么？只使用 `O_DIRECT`、不增加同步语义，能否替代同步？
- Target: 运行 Page Cache 写路径并选择可靠性边界
- Result: Correct after repair on 2026-07-18: separated the Page Cache path selected by `O_DIRECT` from the persistence wait selected by `fdatasync/fsync/O_SYNC`, and correctly denied that `O_DIRECT` alone can replace synchronization.
- Next review: 2026-07-21

## 2026-07-21

- Topic: OS - Direct I/O 与断电持久化迁移复查
- Review timing: 3 days after repair
- Prompt: 一个数据库使用 `O_DIRECT` 写数据页，`write` 返回后立刻确认事务，但没有 `O_SYNC`、`fsync/fdatasync` 或 WAL 刷盘。分别判断缓存路径和持久化边界，并说明断电时为什么仍有风险。
- Target: 运行 Page Cache 写路径并选择可靠性边界
- Result: Pending
- Next review: TBD

## 2026-07-25

- Topic: OS - Page Cache 全章一周迁移复习
- Review timing: 1 week
- Prompt: 设计一个日志追加确认流程：从 buffered `write`、dirty Page Cache、进程崩溃、后台写回和系统断电追踪数据；只要求数据和文件大小可靠时选择同步接口；再比较改用 `O_DIRECT` 后哪些路径改变、哪些持久化责任不变。
- Target: 运行 Page Cache 写路径并选择可靠性边界
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS 与 MySQL - 数据库所在层次
- Review timing: same-day cross-topic formation check
- Prompt: 当 InnoDB Buffer Pool 未命中、需要读取 `.ibd` 中一个 `16KB` 页时，假设 OS Page Cache 也未命中或使用 Direct I/O，确实需要设备 I/O：谁在用户态决定读哪个页？谁在内核态把请求变成文件/块 I/O？数据最终从哪类硬件回来？
- Target: 用“用户态数据库 → 内核服务 → 硬件资源”定位 MySQL
- Result: Correct after repair on 2026-07-18: InnoDB decides which 16KB page is needed; Buffer Pool checks whether that page is cached in user-space memory. The kernel and device layers were already correct.
- Next review: 2026-07-21

- Topic: OS 与 MySQL - Buffer Pool 与 Page Cache 的双层缓存
- Review timing: same-day formation check
- Prompt: 同一个 `.ibd` 数据页通过 buffered I/O 读取时，为什么它可能同时存在于 OS Page Cache 和 InnoDB Buffer Pool？请分别说出两层缓存的所在层次与管理者。
- Target: 用“层次 + 语义 + 粒度”区分 Buffer Pool 与 Page Cache
- Result: Correct on 2026-07-18: explained kernel Page Cache to user-space Buffer Pool copying, mapped one 16KB InnoDB page to four common 4KB OS pages, and assigned database-page semantics to Buffer Pool/InnoDB.
- Next review: 2026-07-21

- Topic: OS 与 MySQL - SELECT 未命中的跨层路径
- Review timing: same-day formation check
- Prompt: 将 Server 层解析优化、InnoDB/B+Tree 确定页、Buffer Pool miss、OS 取文件页、回填 Buffer Pool 与返回行排成因果顺序。
- Target: 运行一次 `SELECT` 的跨层读路径
- Result: Pending
- Next review: after learner answer

## 2026-07-18

- Topic: OS - 设备控制器寄存器协议
- Review timing: same-day formation check
- Prompt: 设备仍在忙时，CPU 为什么不能只写数据和命令？状态寄存器、数据寄存器、命令寄存器在一次输出中各负责什么？
- Target: 用“状态、命令、数据”运行设备控制器协议
- Result: Correct on 2026-07-18: first identified the status register as the timing gate, then transferred the protocol to a network-controller scenario by mapping payload `0x41` to the data register and “start sending” to the command register.
- Next review: 2026-07-21

## 2026-07-18

- Topic: OS - 轮询等待与中断通知
- Review timing: same-day formation check
- Prompt: 一个慢设备很久才完成一次操作。为什么中断通常比 CPU 持续轮询状态寄存器更合适？
- Target: 用“CPU 等不等、CPU 搬不搬”区分轮询、中断和 DMA
- Result: Correct on 2026-07-18: chose interrupt notification to avoid polling busy-wait, then correctly separated DMA bulk transfer from interrupt completion notification and explained why DMA completion still interrupts CPU.
- Next review: 2026-07-21

## 2026-07-18

- Topic: OS - 设备控制器与设备驱动边界
- Review timing: same-day formation check
- Prompt: 键盘扫描码由谁产生和缓冲？谁读取并翻译它？分别判断控制器与驱动的硬件/软件属性。
- Target: 用“硬件控制器 vs 软件驱动”定位设备差异的两道边界
- Result: Correct on 2026-07-18: located scan-code reading and translation in the OS driver, identified the controller as hardware, and explained that saving and restoring CPU context preserves the interrupted program's registers and continuation point.
- Next review: 2026-07-21

## 2026-07-18

- Topic: OS - 通用块层的抽象与调度
- Review timing: same-day formation check
- Prompt: 文件系统提交多个块请求时，通用块层的统一接口职责与请求组织职责分别是什么？
- Target: 用“统一接口 + 请求调度”理解通用块层
- Result: Correct on 2026-07-18: explained that the generic block layer queues, sorts, reschedules, or merges block requests before selecting requests for the device driver, while retaining the unified-interface role.
- Next review: 2026-07-21

## 2026-07-18

- Topic: OS - 存储 I/O 三层软件栈
- Review timing: same-day formation check
- Prompt: 把应用 `read` 依次映射到文件系统层、通用块层和设备层，并说明每层把什么交给下一层。
- Target: 用“三层软件栈”追踪存储 I/O
- Result: Correct after repair on 2026-07-18: restored the missing generic block layer and then reran the complete order as `C -> A -> B`, from file-system request formation through block-layer organization to device-driver execution.
- Next review: 2026-07-21

## 2026-07-18

- Topic: OS - 键盘输入到字符显示链路
- Review timing: same-day formation check
- Prompt: 从按下 `A` 开始，按顺序说明扫描码、中断、键盘驱动、ASCII、读/写缓冲区、显示驱动和显示控制器之间的流动。
- Target: 运行“按键 -> 扫描码 -> 字符编码 -> 显示”的设备链路
- Result: Both halves correct on 2026-07-18: ordered the input half as controller/interrupt -> context save/handler -> driver translation/read buffer, and the output half as display driver/write buffer -> display controller/screen -> context restore; unprompted full-chain integration remains pending.
- Next review: after full-chain answer

## 2026-07-18

- Topic: OS - 传统文件传输的两轴账本
- Review timing: first formation check
- Prompt: 传统 `read(file, tmp_buf, len) + write(socket, tmp_buf, len)` 为什么是 4 次用户态/内核态切换、4 次数据拷贝？列出四条 payload 路径，并标明 CPU 或 DMA。
- Target: 用“态切换轴 + 数据路径轴”追踪文件传输开销
- Result: Correct on 2026-07-18: independently listed all four payload edges, assigned disk/Page Cache and socket/NIC movement to DMA, assigned the two memory-buffer copies to CPU, and ran both system calls through kernel entry and user return.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - 传统文件传输的两轴账本
- Review timing: 1 week
- Prompt: 不背结论，重新画出静态文件经 `read + write` 发往网卡的控制流与 payload 数据流，并分别计算系统调用、态切换、CPU 拷贝和 DMA 拷贝。
- Target: 用“态切换轴 + 数据路径轴”追踪文件传输开销
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - `mmap + write` 的映射与复制边界
- Review timing: first formation check
- Prompt: 请完成 `mmap + write` 账本：系统调用、态切换、总数据拷贝各多少；相比 `read + write`，消失和保留的 CPU 拷贝分别是哪一条？
- Target: 先判断用户是否加工 payload，再删除用户缓冲区中转
- Result: Incomplete on 2026-07-18: counts were correct at 2 system calls, 4 state transitions and 3 data copies, and the retained `Page Cache -> socket buffer` CPU copy was correct. The removed copy was labeled with execution states rather than the concrete source and destination buffers.
- Next review: same-day targeted repair

## 2026-07-18

- Topic: OS - 数据对象与执行态的边界修复
- Review timing: same-day targeted repair
- Prompt: 只把 `mmap + write` 消失的 CPU 拷贝写成 `具体数据对象 -> 具体数据对象`，不要使用“内核态/用户态”作为端点。
- Target: 先判断用户是否加工 payload，再删除用户缓冲区中转
- Result: Correct after repair on 2026-07-18: rewrote the removed CPU copy as `Page Cache -> tmp_buf`, using concrete data objects rather than execution-state labels.
- Next review: 2026-07-21

## 2026-07-21

- Topic: OS - 数据对象与执行态的边界复查
- Review timing: 3 days after repair
- Prompt: 比较 `read + write` 与 `mmap + write`：分别用具体缓冲区名称写出被消除和仍保留的 CPU payload 拷贝，再单独给出态切换次数。
- Target: 先判断用户是否加工 payload，再删除用户缓冲区中转
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - 普通 `sendfile` 的调用与拷贝账本
- Review timing: first formation check
- Prompt: 普通 `sendfile` 有几次系统调用、几次态切换、几次总数据拷贝和几次 CPU payload 拷贝？相对 `mmap + write`，它只减少了哪一类开销？
- Target: 先判断用户是否加工 payload，再删除用户缓冲区中转
- Result: Correct on 2026-07-18: gave 1 system call, 2 state transitions, 3 total data copies and 1 CPU payload copy, and identified system-call/state-transition reduction as the only improvement over `mmap + write` in the ordinary-sendfile model.
- Next review: 2026-07-25

## 2026-07-25

- Topic: OS - `mmap + write` 与普通 `sendfile` 比较
- Review timing: 1 week
- Prompt: 不看表格，比较两条路径的系统调用、态切换、总 payload 拷贝和 CPU payload 拷贝；指出普通 `sendfile` 相对 `mmap + write` 改变和未改变的各是哪一轴。
- Target: 先判断用户是否加工 payload，再删除用户缓冲区中转
- Result: Pending
- Next review: TBD

## 2026-07-18

- Topic: OS - `sendfile + SG-DMA` 的零拷贝边界
- Review timing: first formation check
- Prompt: 写出磁盘到 Page Cache、Page Cache 到网卡的搬运者，计算总 payload 与 CPU payload 拷贝次数，并解释仍有 DMA 搬运为什么叫零拷贝。
- Target: 用“payload 描述符 vs payload 内容”理解 SG-DMA 零拷贝
- Result: Incomplete on 2026-07-19: correctly gave the two DMA payload edges and counted 2 total payload copies with 0 CPU payload copies, but did not yet explain the naming boundary of “zero-copy.”
- Next review: same-day targeted repair

## 2026-07-19

- Topic: OS - “零拷贝”的零所指边界
- Review timing: same-day targeted repair
- Prompt: 零拷贝的“零”不是零次什么，而是零次什么？两次 DMA 是否仍在搬运 payload？
- Target: 用“payload 描述符 vs payload 内容”理解 SG-DMA 零拷贝
- Result: Correct after repair on 2026-07-19: stated that zero-copy means zero CPU payload copies rather than zero physical movement, and explicitly kept both DMA edges as real payload transfers.
- Next review: 2026-07-22

## 2026-07-22

- Topic: OS - `sendfile + SG-DMA` 零拷贝边界复查
- Review timing: 3 days after repair
- Prompt: 不看笔记，写出 SG-DMA 文件传输的两条 payload 搬运边、搬运者和 CPU payload 拷贝次数，再解释 socket 侧传递描述符为何不等于复制整份文件内容。
- Target: 用“payload 描述符 vs payload 内容”理解 SG-DMA 零拷贝
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - Page Cache 的复用收益与大文件污染
- Review timing: source-specific transfer check
- Prompt: 反复访问的 `64KB` 配置文件与几乎只顺序读取一次的 `8GB` 视频，哪个更可能从 Page Cache 获益？对另一个文件，分别说明本次传输自身和其他热点文件受到的一个代价。
- Target: 用“复用收益 vs 缓存污染”判断 Page Cache
- Result: Correct on 2026-07-19: selected the repeatedly accessed `64KB` file for Page Cache benefit, then identified memory/cache-management cost without reuse for the `8GB` stream and eviction of other hot small-file pages.
- Next review: 2026-07-26

## 2026-07-26

- Topic: OS - Page Cache 的复用收益与大文件污染
- Review timing: 1 week
- Prompt: 比较一个反复请求的小型静态资源与一个单次顺序扫描的多 GB 文件：分别判断缓存复用、预读收益、无收益页占用和对其他热点的影响，再选择是否保留 Page Cache 路径。
- Target: 用“复用收益 vs 缓存污染”判断 Page Cache
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - Direct I/O 与异步 I/O 的两条轴
- Review timing: first formation check
- Prompt: 对 `8GB` 低复用视频，Direct I/O 与异步 I/O 分别解决什么问题？为什么只用其中一个不能同时解决缓存污染和调用者等待？
- Target: 用“缓存路径 vs 等待方式”组合 Direct I/O 与异步 I/O
- Result: Correct on 2026-07-19: mapped Direct I/O to avoiding Page Cache pollution, mapped asynchronous I/O to avoiding caller-side blocking, and explained why either mechanism alone leaves the other axis unresolved.
- Next review: 2026-07-26

## 2026-07-26

- Topic: OS - Direct I/O 与异步 I/O 的两条轴
- Review timing: 1 week
- Prompt: 为一个多 GB、低复用、高并发读取场景分别填写缓存路径、等待方式和持久化语义三轴；解释 Direct I/O、异步 I/O 与同步/持久化保证为什么不能互相替代。
- Target: 用“缓存路径 vs 等待方式”组合 Direct I/O 与异步 I/O
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 零拷贝全章综合选型
- Review timing: final transfer
- Prompt: A 反复访问的 `64KB` 静态文件、不加工且支持 SG-DMA；B 高并发 `8GB` 低复用视频、不加工；C `2MB` 文件发送前必须由应用压缩。分别选路径；A 计算调用/切换/拷贝账本，B 说明缓存与等待两轴，C 说明用户态加工边界。
- Target: 运行零拷贝全章综合选型
- Result: Pending
- Next review: after learner answer

## 2026-07-19

- Topic: OS - C10K 的理论连接数与实际服务能力
- Review timing: first formation check
- Prompt: 固定服务端 IP 和端口后，IPv4 四元组理论上仍能区分约 `2^48` 条连接。为什么这不能证明服务器真能承载这么多连接？至少说出两个实际限制，其中一个必须来自执行模型。
- Target: 用“连接身份空间 ≠ 实际服务能力”理解 C10K
- Result: Correct recall on 2026-07-19: reproduced the minimum-budget schema and named fd, memory, execution-model, and CPU/network-workload budgets. Concrete bottleneck selection was not yet demonstrated.
- Next review: same-day application check

## 2026-07-19

- Topic: OS - C10K 最小预算迁移
- Review timing: same-day application check
- Prompt: fd/内存/CPU网络/一连接一线程预算分别支持 `100000/60000/80000/20000` 条连接。实际连接上限是多少？为什么继续提高 fd 上限没有帮助？
- Target: 用“连接身份空间 ≠ 实际服务能力”理解 C10K
- Result: Correct on 2026-07-19: selected `20000` as the first exhausted execution-model budget and explained that capacity is determined by the minimum budget, so enlarging the non-bottleneck fd budget cannot raise the result.
- Next review: 2026-07-26

## 2026-07-26

- Topic: OS - C10K 最小预算复查
- Review timing: 1 week
- Prompt: 四元组空间充足，fd/内存/执行模型/CPU网络预算分别支持 `120000/50000/70000/90000` 条连接。实际容量是多少？只提高 fd 上限是否有效？
- Target: 用“连接身份空间 ≠ 实际服务能力”理解 C10K
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 连接数与执行实体数
- Review timing: first formation check
- Prompt: 服务器维持 `10000` 条连接但只有 `20` 条可读。多路复用仍需维护多少个 Socket，为什么不必维护 `10000` 个阻塞线程，本轮事件循环处理多少个就绪项？
- Target: 用“连接数 ≠ 执行实体数”理解 I/O 多路复用
- Result: Correct on 2026-07-19: kept all `10000` Socket connection objects, removed the need for `10000` blocked threads, and limited this iteration to the `20` ready Socket events. Long-handler blocking remains to be checked.
- Next review: same-day blocking-boundary check

## 2026-07-19

- Topic: OS - 多路复用的事件循环阻塞边界
- Review timing: same-day blocking-boundary check
- Prompt: `20` 个 Socket 已就绪；事件循环处理第一个时同步等待数据库 `5` 秒。这期间能否继续处理其余 `19` 个？为什么？
- Target: 用“连接数 ≠ 执行实体数”理解 I/O 多路复用
- Result: Incorrect on 2026-07-19: answered that the loop could continue because it need not block, replacing the stated synchronous execution path with a possible nonblocking design. A single event-loop thread blocked in the database call cannot process the other ready events until the call returns.
- Next review: same-day A/B repair

## 2026-07-19

- Topic: OS - 同步阻塞与任务转交的条件修复
- Review timing: same-day A/B repair
- Prompt: A 在事件循环线程里同步等待数据库 `5` 秒；B 把数据库任务交给工作线程后立即返回事件循环。A、B 各能否在这 `5` 秒内处理其余 `19` 个就绪 Socket？
- Target: 用“连接数 ≠ 执行实体数”理解 I/O 多路复用
- Result: Partially correct on 2026-07-19: answered “can” for both. B was correct because the worker thread owns the wait and the event loop has returned. A repeated the same error: the only event-loop thread is blocked in the synchronous call, so no execution flow remains to run the other handlers.
- Next review: same-day prerequisite repair

## 2026-07-19

- Topic: OS - 单线程同步阻塞前置修复
- Review timing: immediate prerequisite repair
- Prompt: 只有一个事件循环线程且没有 worker；它同步等待数据库时，哪个线程正在执行其余 `19` 个 handler？
- Target: 用“一条线程只有一条当前执行流”理解同步阻塞
- Result: Correct on 2026-07-19: answered that no thread was processing the remaining handlers while the only event-loop thread was blocked.
- Next review: 2026-07-22

## 2026-07-22

- Topic: OS - 单事件循环线程的同步阻塞复查
- Review timing: 3 days after repair
- Prompt: 只有一个事件循环线程且没有 worker。它进入同步数据库调用后，谁在执行其余已就绪 Socket 的 handler？调用返回前这些 handler 处于什么状态？
- Target: 区分“事件循环内同步等待”与“转交后的后台等待”
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - 事件循环 CPU 长任务迁移
- Review timing: near-transfer after prerequisite repair
- Prompt: 单个事件循环线程在一个 handler 内连续计算 `5` 秒，没有阻塞 I/O。这期间能否处理其他 `19` 个就绪 Socket？为什么？
- Target: 用“连接数 ≠ 执行实体数”理解 I/O 多路复用
- Result: Correct on 2026-07-19: explained that the single event-loop thread cannot process the other `19` ready Socket events while its only execution flow is occupied by five seconds of CPU computation.
- Next review: 2026-07-26

## 2026-07-26

- Topic: OS - 多路复用执行实体边界复查
- Review timing: 1 week
- Prompt: 一个事件循环管理大量连接时，分别说明同步阻塞、CPU 长任务和转交 worker 对其他就绪事件推进的影响，并指出每种路径中谁占用或释放了事件循环线程。
- Target: 用“连接数 ≠ 执行实体数”理解 I/O 多路复用
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - select/poll 全量复制与双扫描
- Review timing: first formation check
- Prompt: select 关注 `N=1000` 个 fd、只有 `k=3` 个就绪。一轮等待有几次集合复制、内核与用户各几次线性扫描，主要成本围绕 `N` 还是 `k`？
- Target: 用“全量集合复制 + 内核/用户双扫描”运行 select/poll
- Result: Incomplete on 2026-07-19: correctly gave two collection copies and identified `N` as the dominant repeated-cost axis, but said the kernel and user each scan twice. The correct distribution is one kernel scan plus one user scan, two scans total.
- Next review: same-day scan-count repair

## 2026-07-19

- Topic: OS - select/poll 扫描总数与每侧次数
- Review timing: same-day scan-count repair
- Prompt: 只填写：内核扫描几次、用户扫描几次、总共扫描几次？
- Target: 用“全量集合复制 + 内核/用户双扫描”运行 select/poll
- Result: Correct on 2026-07-19: gave one kernel scan, one user scan, and two scans total.
- Next review: 2026-07-22

## 2026-07-22

- Topic: OS - select/poll 扫描次数复查
- Review timing: 3 days after repair
- Prompt: 不看笔记，运行一次 select 等待并分别数出集合复制次数、内核扫描次数、用户扫描次数和总扫描次数。
- Target: 用“全量集合复制 + 内核/用户双扫描”运行 select/poll
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - poll 动态数组的能力边界
- Review timing: near-transfer after scan-count repair
- Prompt: poll 关注 `N=10000` 个 fd、只有 `k=4` 个就绪。它突破 select 固定容量后，一轮等待仍复制/扫描全部 `N`，还是只处理 `k`？说明改变与未改变的各一项。
- Target: 用“全量集合复制 + 内核/用户双扫描”运行 select/poll
- Result: Correct on 2026-07-19: stated that poll still copies and scans all `N`; it changes the fixed bitmap into a dynamic linear array but leaves full-set copying and linear scanning unchanged.
- Next review: 2026-07-26

## 2026-07-26

- Topic: OS - select 与 poll 完整边界复查
- Review timing: 1 week
- Prompt: 比较 select 与 poll 的集合表示、容量限制、每轮复制和扫描成本，并说明 `N` 大、`k` 小时 poll 为什么没有从根本上解决问题。
- Target: 用“全量集合复制 + 内核/用户双扫描”运行 select/poll
- Result: Pending
- Next review: TBD

## 2026-07-19

- Topic: OS - epoll 关注集合与就绪集合
- Review timing: first formation check
- Prompt: epoll 关注 `N=10000` 个 fd、只有 `k=4` 个就绪。红黑树和就绪链表分别保存哪一组？`epoll_wait` 复制整个关注集合还是本轮就绪记录？
- Target: 用“持久关注集合 + 就绪列表”运行 epoll
- Result: Correct on 2026-07-19: identified the red-black tree as the persistent `N`-fd interest set, the ready list as the current `k`-event subset, and `epoll_wait` as copying only the `k` ready event records.
- Next review: 2026-07-22

## 2026-07-22

- Topic: OS - epoll 关注集合与就绪集合
- Review timing: 3 days
- Prompt: 不使用“epoll 零拷贝”这句话，解释 `epoll_ctl`、红黑树、就绪链表和 `epoll_wait` 分别处理 `N` 还是 `k`，以及仍保留哪次复制。
- Target: 用“持久关注集合 + 就绪列表”运行 epoll
- Result: Pending
- Next review: 2026-07-26 if correct

## 2026-07-19

- Topic: OS - LT/ET 的通知条件与读取停止条件
- Review timing: first formation check
- Prompt: 缓冲区到达 `8KB`，handler 只读 `2KB`。LT 与 ET 对剩余 `6KB` 的后续通知有什么不同？ET 应读到什么结果停止，为什么要用非阻塞 fd？
- Target: 用“条件持续 vs 状态边沿”区分 LT 与 ET
- Result: Partially correct on 2026-07-19: correctly predicted repeated LT notification, rejected relying on the same ET edge, and said ET should drain the current data; did not yet understand why detecting the empty point requires a nonblocking `read`, and suggested waiting for another notification despite unread ET data.
- Next review: same-day blocking-vs-nonblocking third-read repair

## 2026-07-19

- Topic: OS - ET 读空探测与非阻塞停止条件
- Review timing: immediate prerequisite repair
- Prompt: 缓冲区有 `8KB`，每次 `read` 最多取 `4KB`，连接保持打开。前两次各返回 `4KB` 后，第三次 `read` 在阻塞 fd 与非阻塞 fd 上分别发生什么？哪一种会立即归还事件循环？
- Target: 用“多读一次探测空缓冲区”理解 ET 为什么需要非阻塞 I/O
- Result: Correct on 2026-07-19: blocking fd sleeps on the empty third read and holds the only event-loop thread; nonblocking fd immediately returns `EAGAIN/EWOULDBLOCK`, allowing the handler to exit and the loop to process other sockets.
- Next review: 2026-07-22

## 2026-07-22

- Topic: OS - LT/ET 与非阻塞读空复查
- Review timing: 3 days after repair
- Prompt: 一个 ET Socket 的接收缓冲区恰好有 `8KB`，每次最多读 `4KB`。说明第三次 `read` 的必要性，并比较阻塞 fd 与非阻塞 fd 对事件循环的影响。
- Target: 用“条件持续 vs 状态边沿”区分 LT 与 ET
- Result: Pending
- Next review: 2026-07-26 if correct

## 2026-07-19

- Topic: OS - select/poll/epoll/ET 整章综合迁移
- Review timing: final transfer
- Prompt: `N=10000`、`k=20`。运行 select/poll 一轮等待的复制与扫描，运行 epoll 的红黑树/就绪链表/返回记录，再运行一个 `8KB` ET Socket 的非阻塞读取序列，并指出阻塞第三次读取对其余 `19` 个事件的影响。
- Target: 综合运行 select/poll、epoll 与 ET 的一次服务循环
- Result: Pending
- Next review: after learner answer

## 2026-07-19

- Topic: OS - 分布式请求分配的数据布局前提
- Review timing: first formation check
- Prompt: A、B、C 三个 KV 节点存的 key 彼此不同，`user:42` 只在 B。为什么“把这次查询发给当前最空闲的节点”不是充分的路由规则？这个集群的路由至少还必须保证什么？
- Target: 用“副本同构 vs 数据分片”选择请求分配策略
- Result: Correct on 2026-07-19: explained that routing to a node without the target key cannot find the data and that the same key must map stably to its storage node.
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - 普通取模哈希的扩容重映射
- Review timing: first formation check
- Prompt: `hash(key-01)=6`、`hash(key-02)=7`、`hash(key-03)=8`。三个节点时用 `% 3`，增加到四个节点后用 `% 4`。请算出这三个 key 在扩容前后的余数，并用一句话说明：为什么 `hash(key)` 没变，映射节点却变了？
- Target: 用“取模基数是映射函数的一部分”诊断普通哈希的全局重映射
- Result: Correct core mechanism on 2026-07-19: stated that stable `hash(key)` does not imply a stable destination because changing the node count changes the mapping function. The arithmetic table was explicitly skipped by the learner and remains unchecked, not incorrect.
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - 一致性哈希环的顺时针寻址
- Review timing: first formation check
- Prompt: 在 `0..99` 的哈希环上有 `A@20`、`B@50`、`C@80`。按“从 key 位置顺时针遇到的第一个节点”规则，`key@10`、`key@35`、`key@65`、`key@90` 分别归属哪个节点？
- Target: 用“固定环 + 顺时针后继”运行一致性哈希寻址与局部迁移
- Result: Correct on 2026-07-19: answered `ABCA`, correctly assigning the four keys to their clockwise first nodes and wrapping `key@90` across the ring end to A.
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - 一致性哈希新节点的局部迁移区间
- Review timing: completion check for the forming schema
- Prompt: 原环为 `A@20 -> B@50 -> C@80 -> 回到 A`，现在在 A 和 B 之间加入 `D@40`。请指出哪个哈希坐标区间的 key 会迁移，并说明它们从哪个节点迁到哪个节点。
- Target: 用“固定环 + 顺时针后继”运行一致性哈希寻址与局部迁移
- Result: Partially correct on 2026-07-19: correctly moved data from B to D, but gave the old whole A-to-B ownership interval instead of D's new `(20,40]` interval; keys in `(40,50]` still map to B.
- Next review: immediate right-endpoint repair

## 2026-07-19

- Topic: OS - 一致性哈希迁移区间右端点修复
- Review timing: immediate boundary repair
- Prompt: 只填一个右端点：从 B 迁到 D 的哈希坐标区间是 `(20, ___]`。
- Target: 用“固定环 + 顺时针后继”运行一致性哈希寻址与局部迁移
- Result: Correct on 2026-07-19: repaired the right endpoint to `40`, yielding the exact `(20,40]` interval that migrates from B to D.
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - 一致性哈希所有权弧长与雪崩传导
- Review timing: first formation check
- Prompt: 在 `0..99` 的环上，三个真实节点挤在 `A@10`、`B@20`、`C@30`。哪个节点拥有最长的 key 区间？如果它故障，该区间会整段转给哪个顺时针后继，为什么这可能触发雪崩？
- Target: 用“所有权区间大小”诊断节点倾斜与雪崩传导
- Result: Correct on 2026-07-19: identified A as owning the longest wraparound interval, transferred A's full load to successor B after failure, and traced B overload into continued successor-to-successor collapse.
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - 虚拟节点的两层寻址
- Review timing: first formation check
- Prompt: 环上按顺时针排列 `A-01@10 -> B-01@20 -> A-02@30 -> C-01@40`，其中 A-01/A-02 都对应真实节点 A。`key@25` 首先命中哪个虚拟节点，最终访问哪个真实节点？
- Target: 用“key -> 虚拟节点 -> 真实节点”运行均衡、容灾和加权
- Result: Correct on 2026-07-19: followed `key@25 -> A-02@30 -> real node A`, preserving both the ring lookup and virtual-to-real mapping layers.
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - 多虚拟节点的故障压力分担
- Review timing: continuation of forming schema
- Prompt: 仍使用 `A-01@10 -> B-01@20 -> A-02@30 -> C-01@40 -> 回到 A-01`。若真实节点 A 故障，A-01 和 A-02 都被移除：A-01 原有区间会交给哪个真实节点，A-02 原有区间又会交给哪个真实节点？
- Target: 用“key -> 虚拟节点 -> 真实节点”运行均衡、容灾和加权
- Result: Correct on 2026-07-19: transferred A-01's interval through successor B-01 to real node B and A-02's interval through successor C-01 to real node C, demonstrating multi-successor failure spreading.
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - 虚拟节点数量与硬件权重
- Review timing: completion check for the forming schema
- Prompt: A、B 的硬件处理能力均为 1，C 的处理能力为 2。若每个权重单位配置 100 个虚拟节点，A、B、C 应各配置多少个？为什么这个比例只表达期望所有权份额，而不保证实时请求精确按 1:1:2 分布？
- Target: 用“key -> 虚拟节点 -> 真实节点”运行均衡、容灾和加权
- Result: Partially correct on 2026-07-19: preserved the correct `1:1:2` ratio and correctly explained hash randomness and hot-key skew, but answered `25,25,50` by normalizing the total to 100 instead of applying the stated baseline of 100 virtual nodes per weight unit.
- Next review: immediate absolute-count repair

## 2026-07-19

- Topic: OS - 虚拟节点权重的绝对基准单位
- Review timing: immediate boundary repair
- Prompt: 只修正绝对数量：`A = 1 × 100 = ?`，`B = 1 × 100 = ?`，`C = 2 × 100 = ?`。
- Target: 用“key -> 虚拟节点 -> 真实节点”运行均衡、容灾和加权
- Result: Correct on 2026-07-19: applied the per-weight baseline and answered `100,100,200`, preserving the already-correct `1:1:2` ratio.
- Next review: 2026-07-22

## 2026-07-19

- Topic: OS - 一致性哈希整章综合迁移
- Review timing: final transfer
- Prompt: 分片 KV 集群中，A、B 权重各为 1，每权重单位 100 个虚拟节点；加入权重 2 的真实节点 C。C 的一个代表 token `C-03@60` 插入 `B-07@45 -> A-12@80` 之间，且 `key@55`。说明 C 的 token 数、key 在插入前后的真实目标、迁移区间与方向、为什么其他区间不全局重映射，以及 C 故障时 200 个 token 的区间如何分担。
- Target: 综合运行普通哈希对比、一致性哈希寻址与迁移、虚拟节点权重和故障分担
- Result: Pending
- Next review: after learner answer
