# OS Virtual Memory Graph

```mermaid
graph TD
    VA[每进程虚拟地址空间] -- "is used in" --> Isolation[进程地址隔离]
    PageTable[页表] -- "is used in" --> Translation[虚拟地址翻译]
    MMU[MMU] -- "is used in" --> Translation
    Translation -- "transfers to" --> PA[物理内存地址]
    Segmentation[分段] -- "is a type of" --> Translation
    Paging[分页] -- "is a type of" --> Translation
    Segmentation -- "is part of" --> SegPaged[段页式管理]
    Paging -- "is part of" --> SegPaged
    MultiLevel[多级页表] -- "builds on" --> Paging
    Locality[局部性] -- "is used in" --> MultiLevel
    TLB[TLB] -- "is used in" --> MMU
    Locality -- "is used in" --> TLB
    PageFault[缺页异常] -- "is used in" --> DemandPaging[按需分页]
    DemandPaging -- "builds on" --> Paging
    LinuxVM[Linux 虚拟内存] -- "builds on" --> Paging
```

## Edge semantics

- `虚拟地址空间 -> 地址隔离`: is used in
- `页表 / MMU -> 虚拟地址翻译`: is used in
- `地址翻译 -> 物理地址`: transfers to
- `分段 / 分页 -> 地址翻译`: is a type of
- `分段 / 分页 -> 段页式`: is part of
- `多级页表 -> 分页`, `按需分页 -> 分页`, `Linux 虚拟内存 -> 分页`: builds on
- `局部性 -> 多级页表 / TLB`, `TLB -> MMU`, `缺页异常 -> 按需分页`: is used in
