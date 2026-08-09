# MySQL Knowledge Map

```mermaid
graph TD
    A[客户端提交 SQL] --> B[Server 层处理]
    B --> C[解析器]
    B --> D[预处理器]
    B --> E[优化器]
    E --> F[选择执行计划]
    F --> G[执行器]
    G --> H[存储引擎层]
    H --> I[InnoDB 页式存储]
    I --> DA[Buffer Pool]
    DA --> DB[缓存页]
    DB --> DC[控制块]
    DC --> DD[Free List]
    DC --> DE[LRU List]
    DC --> DF[Flush List]
    DB --> DK[脏页]
    DK --> DF
    DE --> DG[young/old 改良 LRU]
    DG --> DH[预读失效]
    DG --> DI[Buffer Pool 污染]
    DF --> DJ[脏页刷盘]
    DK --> CE[WAL crash-safe]
    H --> J[索引]
    J --> K[B+Tree 有序定位]
    K --> L[聚簇索引]
    K --> M[二级索引]
    M --> N[回表]
    M --> O[覆盖索引]
    K --> P[联合索引]
    P --> Q[最左匹配]
    J --> R[索引失效]
    R --> S[原始有序键定位]
    S --> T[前缀 LIKE]
    S --> U[函数和表达式]
    S --> V[隐式类型转换]
    S --> W[OR 与 index merge]
    B --> X[COUNT 聚合]
    X --> Y[表达式非 NULL 计数]
    X --> Z[Server 层计数变量]
    Z --> AA[InnoDB 扫描可见记录]
    E --> AB[选择较小扫描对象]
    M --> AB
    AA --> AC[MVCC 可见性]
    AA --> AD[大表计数优化]
    H --> AE[事务]
    AE --> AF[ACID]
    AE --> AG[隔离级别]
    AG --> AH[读提交]
    AG --> AI[可重复读]
    AH --> AJ[Read View]
    AI --> AJ
    AJ --> AK[MVCC]
    AK --> AL[trx_id]
    AK --> AM[roll_pointer]
    AM --> AN[undo 版本链]
    AI --> AO[快照读]
    AI --> AP[当前读 next-key lock]
    H --> BA[MySQL 锁]
    BA --> BB[全局锁]
    BA --> BC[表级锁]
    BA --> BD[行级锁]
    BB --> BE[一致性备份]
    BC --> BF[MDL]
    BC --> BG[意向锁]
    BC --> BH[AUTO-INC]
    BD --> BI[Record Lock]
    BD --> BJ[Gap Lock]
    BD --> BK[Next-Key Lock]
    BJ --> BL[插入意向锁]
    BK --> AP
    BK --> BM[死锁等待图]
    BL --> BM
    BM --> BN[循环等待]
    BM --> BO[死锁检测]
    BJ --> BP[未命中主键 update]
    BP --> BQ[插入意向锁等待]
    BQ --> BM
    H --> CA[日志体系]
    CA --> CB[undo log]
    CB --> CC[回滚]
    CB --> AN
    CA --> CD[redo log]
    CD --> CE[WAL crash-safe]
    CD --> CF[redo log buffer]
    CD --> CG[checkpoint]
    CA --> CH[binlog]
    CH --> CI[备份恢复]
    CH --> CJ[主从复制]
    CD --> CK[两阶段提交]
    CH --> CK
    CK --> CL[XID 崩溃判断]
    CK --> CM[组提交]
```


<!-- study-agent-visual-contract:v1 -->
## Visual Contract

- This file is the global cross-topic and cross-chapter knowledge map.
- Use Mermaid for this global map.
- Keep chapter maps and concept-local diagrams canonical inside their notes; do not duplicate them here.
<!-- /study-agent-visual-contract:v1 -->
