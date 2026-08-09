# OS 改进 LRU 知识图

```mermaid
graph TD
    A[有限的页缓存] -->|depends on| B[缓存淘汰]
    B -->|is used in| C[传统 LRU]
    C -->|depends on| D[最近访问顺序]
    E[预读机制] -->|builds on| F[空间局部性]
    G[预读失效] -->|is a type of| H[命中率下降]
    G -->|depends on| E
    I[缓存污染] -->|is a type of| H
    I -->|depends on| J[一次性批量扫描]
    K[冷区准入] -->|builds on| C
    K -->|is used in| G
    L[提高晋升门槛] -->|builds on| K
    L -->|is used in| I
    M[Linux active/inactive] -->|is a type of| L
    N[InnoDB young/old] -->|is a type of| L
```

## Edge reading

- 有限容量要求淘汰策略；传统 LRU 用最近访问顺序决定页的头尾位置。
- 预读建立在空间局部性上，但错误预读会让未使用页伤害命中率。
- 冷区准入先隔离没有真实访问证据的预读页；提高晋升门槛再过滤只访问一次的扫描页。
- Linux 用 active/inactive 两条 LRU 表达冷热；InnoDB 用一条 LRU 的 young/old 两个区域表达冷热。
