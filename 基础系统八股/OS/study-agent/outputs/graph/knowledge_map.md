# Knowledge Map

Use Mermaid graphs only. Keep the global map compact and connected.

```mermaid
graph TD
    Learning[Learning] --> Schema[Reusable schemas]
    Schema --> MentalModel[Mental models]
    MentalModel --> Transfer[Transfer tasks]
    Transfer --> Review[Review prompts]
    Schema --> StorageHierarchy[OS 存储器层次结构]
    StorageHierarchy --> StorageTradeoff[速度-容量-成本取舍]
    StorageTradeoff --> CacheSystem[逐层缓存体系]
    CacheLine[Cache Line 与局部性] -- "is part of" --> CacheSystem
    CacheLine -- "is used in" --> CachePerformance[缓存命中率与程序性能]
    CacheLine -- "is used in" --> CacheCoherence[多核缓存一致性]
    MESI[MESI 状态机] -- "is used in" --> CacheCoherence
    FalseSharing[伪共享诊断与规避] -- "builds on" --> CacheLine
    LinuxScheduling[Linux 任务调度] -- "is a type of" --> Schema
    Vruntime[vruntime 与 nice-weight] -- "is used in" --> LinuxScheduling
    InterruptSplit[中断上下半部拆分] -- "is a type of" --> Schema
    SoftIRQDiag[软中断观测与诊断] -- "builds on" --> InterruptSplit
    NumberRep[计算机数值表示] -- "is a type of" --> Schema
    TwosComplement[补码统一加减] -- "is part of" --> NumberRep
    IEEE754[IEEE 754 浮点编码] -- "is part of" --> NumberRep
    FloatRounding[二进制有限精度舍入] -- "builds on" --> IEEE754
    FloatRounding -- "transfers to" --> FloatCompare[浮点结果比较与误差判断]
    VirtualMemory[虚拟内存地址转换] -- "is a type of" --> Schema
    VirtualMemory -- "builds on" --> StorageHierarchy
    Paging[分页与多级页表] -- "is part of" --> VirtualMemory
    TLB[TLB 翻译缓存] -- "is used in" --> VirtualMemory
    Locality[局部性] -- "is used in" --> TLB
    MallocAllocator[malloc allocator] -- "builds on" --> VirtualMemory
    BrkMmap[brk and anonymous mmap] -- "is used in" --> MallocAllocator
    MemoryPool[Reusable memory pool] -- "is part of" --> MallocAllocator
    AllocatorInternals[Allocator block management] -- "builds on" --> MallocAllocator
    BoundaryTags[Header and footer boundary tags] -- "is part of" --> AllocatorInternals
    PlacementSplitMerge[Placement split and coalescing] -- "is part of" --> AllocatorInternals
    ExcessMemory[超额内存申请四门判断] -- "builds on" --> VirtualMemory
    Overcommit[内存承诺策略] -- "is part of" --> ExcessMemory
    SwapOOM[Swap 与 OOM] -- "is part of" --> ExcessMemory
    CacheReplacement[缓存淘汰与准入] -- "is part of" --> CacheSystem
    TraditionalLRU[传统 LRU] -- "is part of" --> CacheReplacement
    ColdHotAdmission[冷热分区与晋升门槛] -- "builds on" --> TraditionalLRU
    ReadAheadPollution[预读失效与缓存污染] -- "builds on" --> TraditionalLRU
    ColdHotAdmission -- "is used in" --> CachePerformance
    ProcessThread[进程与线程运行模型] -- "is a type of" --> Schema
    ProcessLifecycle[进程状态 PCB 与上下文切换] -- "is part of" --> ProcessThread
    ThreadMapping[用户线程与内核调度实体映射] -- "is part of" --> ProcessThread
    ThreadCapacity[线程数量的多预算上限] -- "builds on" --> ProcessThread
    ThreadCapacity -- "builds on" --> VirtualMemory
    ThreadStackMap[每线程栈虚拟映射] -- "is part of" --> ThreadCapacity
    ThreadSystemLimits[系统线程、任务 ID 与 VMA 限制] -- "is part of" --> ThreadCapacity
    ThreadFailure[线程故障的影响范围] -- "builds on" --> ProcessThread
    NativeFault[同步非法访存与 SIGSEGV] -- "is part of" --> ThreadFailure
    NativeFault -- "builds on" --> VirtualMemory
    SignalDisposition[默认处置与自定义信号处理] -- "is used in" --> ThreadFailure
    JVMFaultTranslation[HotSpot 可识别故障翻译] -- "builds on" --> NativeFault
    JVMFaultTranslation -- "is part of" --> ThreadFailure
    ThreadSynchronization[多线程互斥与同步] -- "builds on" --> ProcessThread
    RaceCondition[共享状态与竞争条件] -- "is part of" --> ThreadSynchronization
    SemaphoreSync -- "is used in" --> ThreadSynchronization
    BoundedBufferSync[生产者消费者有界缓冲] -- "builds on" --> SemaphoreSync
    DiningPhilosophers[哲学家多资源协调] -- "is part of" --> ThreadSynchronization
    ReadersWriters[读者写者公平访问] -- "is part of" --> ThreadSynchronization
    Deadlock[死锁条件与等待闭环] -- "is part of" --> ThreadSynchronization
    WaitForGraph[线程-锁-owner 等待图] -- "is used in" --> Deadlock
    LockOrdering[资源有序分配] -- "is used in" --> Deadlock
    LockSelection[锁策略选择] -- "is part of" --> ThreadSynchronization
    BlockingSpinning[阻塞等待与自旋等待] -- "is part of" --> LockSelection
    ReaderWriterPolicy[读写锁与公平策略] -- "is part of" --> LockSelection
    ConflictTiming[悲观与乐观冲突处理] -- "is part of" --> LockSelection
    CASPrimitive[CAS 原子条件更新] -- "is used in" --> BlockingSpinning
    CASPrimitive -- "is used in" --> ConflictTiming
    LinuxScheduling -- "builds on" --> ProcessThread
    IPC[进程间通信] -- "builds on" --> ProcessThread
    KernelMediation[内核中介与 IPC 对象] -- "is part of" --> IPC
    SharedMemoryIPC[共享内存 IPC] -- "is part of" --> IPC
    SharedMemoryIPC -- "builds on" --> VirtualMemory
    SemaphoreSync[信号量同步] -- "is used in" --> SharedMemoryIPC
    SchedulingChoice[三类资源调度选择] -- "is a type of" --> Schema
    ClassicCPU[经典 CPU 调度算法] -- "is part of" --> SchedulingChoice
    ClassicCPU -- "builds on" --> ProcessThread
    PageReplacement[页面置换算法] -- "is part of" --> SchedulingChoice
    PageReplacement -- "builds on" --> VirtualMemory
    DiskScheduling[机械磁盘调度算法] -- "is part of" --> SchedulingChoice
    DiskScheduling -- "builds on" --> StorageHierarchy
    FileSystem[文件系统命名、身份与存储] -- "is a type of" --> Schema
    FileSystem -- "builds on" --> StorageHierarchy
    FileIdentity[目录项、dentry 与 inode] -- "is part of" --> FileSystem
    BlockAllocation[文件块分配与空闲空间] -- "is part of" --> FileSystem
    VFS[VFS 统一接口] -- "is part of" --> FileSystem
    FileIO[文件描述符与 I/O 模型] -- "is part of" --> FileSystem
    DeviceManagement[设备控制、驱动与 I/O] -- "is a type of" --> Schema
    DeviceManagement -- "builds on" --> StorageHierarchy
    DeviceController[设备控制器寄存器协议] -- "is part of" --> DeviceManagement
    DeviceDriver[设备驱动程序] -- "is used in" --> DeviceManagement
    IOControl[轮询、中断与 DMA] -- "is part of" --> DeviceManagement
    GenericBlock[通用块层] -- "is part of" --> DeviceManagement
    GenericBlock -- "is used in" --> FileIO
    KeyboardIO[扫描码到显示字符] -- "builds on" --> DeviceManagement
    PageCache[内核页缓存] -- "is used in" --> FileIO
    PageCache -- "builds on" --> VirtualMemory
    DirtyPage[脏页与后台写回] -- "is part of" --> PageCache
    FileDurability[文件持久化边界] -- "builds on" --> DirtyPage
    ExplicitSync[fsync 与 fdatasync] -- "is used in" --> FileDurability
    ReadAhead[Page Cache 预读] -- "is used in" --> PageCache
    DirectIO[Direct I/O] -- "is a type of" --> FileIO
    ZeroCopy[零拷贝文件传输] -- "is a type of" --> FileIO
    TransferAxes[态切换与数据拷贝双轴] -- "is part of" --> ZeroCopy
    ZeroCopy -- "builds on" --> IOControl
    ZeroCopy -- "builds on" --> PageCache
    SendfileSG[sendfile 与 SG-DMA] -- "is used in" --> ZeroCopy
    LargeFileTransfer[大文件异步 Direct I/O] -- "is a type of" --> FileIO
    DirectIO -- "is used in" --> LargeFileTransfer
    AsyncIO[异步 I/O] -- "is used in" --> LargeFileTransfer
    FileDurability -- "is part of" --> FileIO
    NetworkIO[Socket 与高并发网络 I/O] -- "is a type of" --> Schema
    NetworkIO -- "builds on" --> FileIO
    SocketLifecycle[监听与已连接 Socket] -- "is part of" --> NetworkIO
    IOMultiplexing[I/O 多路复用] -- "is part of" --> NetworkIO
    IOMultiplexing -- "builds on" --> ProcessThread
    IOMultiplexing -- "is used in" --> ReactorPattern[Reactor 就绪事件分发]
    ReactorPattern -- "is part of" --> NetworkIO
    ReactorPattern -- "is used in" --> RedisThreadModel[Redis 命令事件循环]
    ProcessThread -- "builds on" --> RedisThreadModel
    SelectPoll[select 与 poll 全量扫描] -- "is a type of" --> IOMultiplexing
    EpollReady[epoll 关注集与就绪集] -- "is a type of" --> IOMultiplexing
    LevelEdge[LT 与 ET] -- "is part of" --> EpollReady
    NonblockingIO[非阻塞 Socket I/O] -- "is used in" --> IOMultiplexing
    AsyncIO -- "is used in" --> ProactorPattern[Proactor 完成事件分发]
    ProactorPattern -- "is part of" --> NetworkIO
    KeyRouting[分布式 key 路由] -- "is a type of" --> Schema
    ModuloHash[取模哈希全局重映射] -- "is part of" --> KeyRouting
    ConsistentHash[一致性哈希局部迁移] -- "is part of" --> KeyRouting
    VirtualNode[虚拟节点均衡与分担] -- "is used in" --> ConsistentHash
    MySQL[MySQL 用户态数据库系统] -- "builds on" --> ProcessThread
    MySQL -- "builds on" --> VirtualMemory
    DatabaseFiles[数据文件与日志文件] -- "is part of" --> MySQL
    DatabaseFiles -- "builds on" --> FileSystem
    InnoDBBufferPool[InnoDB Buffer Pool] -- "is part of" --> MySQL
    InnoDBBufferPool -- "builds on" --> VirtualMemory
    InnoDBWAL[redo log 与 WAL] -- "is part of" --> MySQL
    InnoDBWAL -- "builds on" --> FileDurability
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
