# CPU 如何执行程序 Graph

```mermaid
graph TD
    Von[冯诺依曼模型] -- "builds on" --> Turing[图灵机执行模型]
    Memory[内存存放程序和数据] -- "is part of" --> Von
    CPU[CPU: 控制器/运算器/寄存器] -- "is part of" --> Von
    Bus[总线: 地址/数据/控制] -- "is used in" --> Von
    Source[高级语言代码] -- "is used in" --> Compile[编译和汇编]
    Compile -- "is used in" --> Machine[机器码指令]
    Machine -- "is part of" --> Text[正文段]
    Source -- "is used in" --> Data[数据段]
    Text -- "is used in" --> PC[程序计数器: 下一条指令地址]
    Fetch[Fetch 取指] -- "depends on" --> PC
    IR[指令寄存器: 当前指令] -- "depends on" --> Fetch
    Decode[Decode 译码] -- "depends on" --> IR
    Execute[Execution 执行] -- "depends on" --> Decode
    Store[Store 写回] -- "depends on" --> Execute
    Addressing[寻址空间] -- "depends on" --> Width[CPU/线路位宽]
    Performance[大数计算边界] -- "depends on" --> Width
```

## Edge Meanings

- `冯诺依曼模型 -> 图灵机执行模型`: builds on
- `内存/CPU -> 冯诺依曼模型`: is part of
- `总线 -> 冯诺依曼模型`: is used in
- `高级语言代码 -> 编译和汇编 -> 机器码指令`: is used in
- `Fetch -> 程序计数器 -> 指令寄存器 -> Decode -> Execution -> Store`: depends on
- `寻址空间/大数计算边界 -> CPU/线路位宽`: depends on
