# OS malloc Graph

```mermaid
graph TD
    Request[User malloc request] -- "is used in" --> Allocator[malloc allocator]
    Metadata[Block metadata] -- "is part of" --> Allocator
    Pool[Reusable memory pool] -- "is used in" --> Allocator
    Allocator -- "is used in" --> Brk[brk heap growth]
    Allocator -- "is used in" --> Mmap[private anonymous mmap]
    Brk -- "is part of" --> Heap[Heap region]
    Mmap -- "is part of" --> MapArea[File-mapping region]
    Heap -- "is part of" --> VA[Process virtual address space]
    MapArea -- "is part of" --> VA
    VA -- "transfers to" --> PageFault[First-touch page fault]
    PageFault -- "transfers to" --> Physical[Resident physical page]
    Free[free] -- "is used in" --> Pool
    Free -- "is used in" --> Unmap[Unmap independent mapping]
    Fragmentation[Heap fragmentation] -- "is used in" --> Hybrid[brk/mmap hybrid policy]
    SyscallCost[System-call and fault cost] -- "is used in" --> Hybrid
    Hybrid -- "is used in" --> Allocator
```

## Edge semantics

- `malloc request / memory pool / brk / mmap / hybrid policy -> allocator`: is used in
- `block metadata -> allocator`, `brk -> heap`, `mmap -> file-mapping region`, `heap / mapping region -> virtual address space`: is part of
- `virtual address space -> first-touch page fault -> resident physical page`: transfers to
- `free -> pool / unmap`: is used in
- `fragmentation / system-call and fault cost -> hybrid policy`: is used in
