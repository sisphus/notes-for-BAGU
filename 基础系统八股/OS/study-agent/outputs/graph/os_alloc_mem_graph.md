# OS 超额内存申请知识图

```mermaid
graph TD
    Q[超额内存申请问题] -->|depends on| G[四道门判断]
    G -->|depends on| A[进程虚拟地址空间]
    G -->|depends on| C[内存承诺策略]
    G -->|depends on| T[是否真正触页]
    G -->|depends on| B[物理与磁盘后备]
    M[malloc 返回虚拟地址] -->|is used in| A
    O[overcommit_memory] -->|is used in| C
    T -->|transfers to| F[page fault]
    F -->|transfers to| R[物理页驻留]
    V[VSZ 与 RSS] -->|is used in| R
    R -->|depends on| P[空闲页与内存回收]
    S[Swap 换出换入] -->|is used in| P
    D[磁盘 I/O 与性能下降] -->|builds on| S
    K[OOM Killer] -->|builds on| P
```

## Edge reading

- 超额申请必须依次经过地址空间、承诺策略、是否触页和物理/磁盘后备四层判断。
- `malloc` 返回的是虚拟地址；首次触页通过 page fault 把需要使用的页面落实为物理驻留。
- VSZ 与 RSS 帮助区分地址范围和驻留页面；物理页不足时进入回收。
- Swap 为冷匿名页提供磁盘后备，但引入磁盘 I/O；回收仍失败时才进入 OOM 兜底。
