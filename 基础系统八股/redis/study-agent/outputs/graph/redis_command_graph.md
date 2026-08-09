# Redis Data Types and Commands Knowledge Graph

```mermaid
graph TD
    Shape[业务数据形状] -->|transfers to| TypeChoice[Redis 类型选择]
    TypeChoice -->|is a type of| String[String 标量与原子操作]
    TypeChoice -->|is a type of| Hash[Hash 对象字段]
    TypeChoice -->|is a type of| List[List 有序序列]
    TypeChoice -->|is a type of| Set[Set 唯一集合]
    TypeChoice -->|is a type of| ZSet[ZSet 带权排序]
    TypeChoice -->|is a type of| Bitmap[BitMap 二值状态]
    TypeChoice -->|is a type of| HLL[HyperLogLog 近似基数]
    TypeChoice -->|is a type of| GEO[GEO 地理位置]
    TypeChoice -->|is a type of| Stream[Stream 消息流]
    String -->|is used in| Bitmap
    ZSet -->|is used in| GEO
    List -->|transfers to| Queue[简单消息队列]
    Stream -->|builds on| Queue
    Stream -->|depends on| Ack[Pending 与 ACK]
```

## Edge Semantics

- `depends on`: target concept is a prerequisite.
- `builds on`: source extends the target into a stronger mechanism.
- `is a type of`: source is one concrete member of the target category.
- `is used in`: source structure implements the target feature.
- `transfers to`: source schema is applied to the target task.
