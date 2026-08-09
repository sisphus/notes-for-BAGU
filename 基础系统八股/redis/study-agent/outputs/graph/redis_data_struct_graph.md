# Redis Data Structures Knowledge Graph

```mermaid
graph TD
    Dict[dict 双哈希表] -->|is part of| DB[redisDb]
    Entry[dictEntry key/value] -->|is part of| Dict
    Obj[redisObject type/encoding/ptr] -->|is used in| Entry
    SDS[SDS] -->|is used in| Obj
    Intset[intset] -->|is used in| Obj
    Hash[哈希表] -->|is used in| Obj
    ZSet[ZSet 双索引] -->|is used in| Obj
    Hash -->|is part of| ZSet
    Skiplist[跳表] -->|is part of| ZSet
    Quicklist[quicklist 分段] -->|builds on| Linked[双向链表]
    Quicklist -->|builds on| Ziplist[ziplist]
    Ziplist -->|transfers to| Cascade[prevlen 连锁更新]
    Listpack[listpack] -->|builds on| Ziplist
    Listpack -->|transfers to| NoCascade[去除前项长度依赖]
```

## Edge Semantics

- `is part of`: source is a component of the target.
- `is used in`: source representation is used by the target.
- `builds on`: source combines or improves the target structure.
- `transfers to`: source mechanism produces the target consequence.
