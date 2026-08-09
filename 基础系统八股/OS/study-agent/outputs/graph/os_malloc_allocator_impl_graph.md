# OS malloc Allocator Implementation Graph

```mermaid
graph TD
    Heap[Heap as block sequence] -- "is used in" --> Allocator[Simple allocator]
    Four[Four design questions] -- "is part of" --> Allocator
    Header[Header size and state] -- "is part of" --> Block[Memory block]
    Payload[User payload] -- "is part of" --> Block
    Block -- "is part of" --> Heap
    Header -- "is used in" --> Traverse[Implicit traversal]
    Traverse -- "is used in" --> Placement[Placement policy]
    First[First Fit] -- "is a type of" --> Placement
    Next[Next Fit] -- "is a type of" --> Placement
    Best[Best Fit] -- "is a type of" --> Placement
    Placement -- "is used in" --> Split[Split oversized block]
    Split -- "transfers to" --> FreeBlock[Reusable free block]
    FreeBlock -- "is used in" --> Coalesce[Adjacent coalescing]
    Footer[Footer boundary tag] -- "is used in" --> Coalesce
    Coalesce -- "is used in" --> Allocator
```

## Edge semantics

- `heap / four design questions / coalescing -> allocator`: is used in / is part of
- `header / payload -> block -> heap`: is part of
- `header -> implicit traversal -> placement -> split`: is used in
- `First Fit / Next Fit / Best Fit -> placement`: is a type of
- `split -> reusable free block`: transfers to
- `free block / footer -> coalescing`: is used in
