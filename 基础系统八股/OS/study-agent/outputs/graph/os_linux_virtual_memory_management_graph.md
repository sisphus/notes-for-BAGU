# Linux Virtual Memory Management Graph

```mermaid
graph TD
    Task[task_struct] -- "is used in" --> MM[mm_struct]
    Thread[共享地址空间的线程] -- "is used in" --> MM
    MM -- "is used in" --> VMA[VMA]
    Maple[Maple Tree] -- "is used in" --> VMA
    VMA -- "is part of" --> UserVA[用户虚拟地址空间]
    ELF[ELF Section/Segment] -- "transfers to" --> VMA
    Flags[VMA 权限与后备] -- "is part of" --> VMA
    VMA -- "depends on" --> PT[页表映射]
    Fault[缺页处理] -- "is used in" --> PT
    PT -- "transfers to" --> PA[物理地址]
    KernelVA[共享内核虚拟空间] -- "depends on" --> PT
    PA -- "is used in" --> MC[内存控制器]
    MC -- "transfers to" --> DRAM[DRAM 数据]
```

## Edge semantics

- `task_struct / 线程 -> mm_struct`: is used in
- `mm_struct / Maple Tree -> VMA`: is used in
- `VMA -> 用户虚拟地址空间`: is part of
- `ELF -> VMA`: transfers to
- `权限与后备 -> VMA`: is part of
- `VMA / 内核虚拟空间 -> 页表`: depends on
- `缺页处理 -> 页表`: is used in
- `页表 -> 物理地址 -> 内存控制器 -> DRAM`: transfers to / is used in
