# OS Storage Hierarchy Graph

```mermaid
graph TD
    Hierarchy[存储器层次结构] -- "is part of" --> Computer[计算机系统]
    Register[寄存器] -- "is a type of" --> Hierarchy
    Cache[CPU Cache] -- "is a type of" --> Hierarchy
    Memory[DRAM 内存] -- "is a type of" --> Hierarchy
    Disk[SSD/HDD] -- "is a type of" --> Hierarchy
    L1[L1 Cache] -- "is part of" --> Cache
    L2[L2 Cache] -- "is part of" --> Cache
    L3[L3 Cache] -- "is part of" --> Cache
    SRAM[SRAM] -- "is used in" --> Cache
    DRAM[DRAM] -- "is used in" --> Memory
    Disk -- "transfers to" --> Memory
    Memory -- "transfers to" --> Cache
    Cache -- "transfers to" --> Register
    Tradeoff[速度-容量-成本取舍] -- "is used in" --> Hierarchy
    Lookup[逐层查找与缓存命中] -- "depends on" --> Hierarchy
```

## Edge semantics

- `寄存器 / CPU Cache / DRAM 内存 / SSD-HDD -> 存储器层次结构`: is a type of
- `L1 / L2 / L3 -> CPU Cache`: is part of
- `SRAM -> CPU Cache`, `DRAM -> 内存`: is used in
- `SSD-HDD -> 内存 -> CPU Cache -> 寄存器`: transfers to
- `逐层查找与缓存命中 -> 存储器层次结构`: depends on
