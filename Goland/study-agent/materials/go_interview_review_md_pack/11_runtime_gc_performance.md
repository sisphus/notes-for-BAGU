# 11 — Runtime、GC、Escape、pprof、性能优化

## Runtime Basics

- runtime 负责调度/GC/内存/channel/map/panic
- goroutine 不是 OS thread
- G/M/P
- work stealing 直觉
- 可增长栈
- network poller
- syscall

## Memory Allocation

- stack vs heap
- escape analysis
- 指针返回可能逃逸
- interface/closure 可能逃逸
- 大对象分配
- slice 预分配
- string/[]byte 转换
- struct 对齐

## GC

- 自动回收堆
- allocation rate
- live heap
- STW 概念
- concurrent GC
- GOGC
- memory limit
- 减少短生命周期对象
- sync.Pool 边界

## pprof

- CPU profile
- heap profile
- goroutine profile
- block profile
- mutex profile
- inuse vs alloc
- top/list/flamegraph
- 线上安全
- 真实负载采集

## Benchmark

- b.N
- ReportAllocs
- ResetTimer
- 防优化
- benchstat
- microbenchmark 陷阱
- 结合 pprof

## Trace

- goroutine 调度
- 阻塞
- syscall
- GC
- scheduler latency
- 与 pprof 区别

## sync.Pool

- 复用临时对象
- 对象可被 GC 清理
- 不管理必须释放资源
- buffer 复用
- Reset
- benchmark 验证

## Optimization Method

- 先定位瓶颈
- 正确性优先
- 减少 allocation
- 减少锁竞争
- 减少 DB 查询
- 批量处理
- 缓存热点
- 合理并发
- before/after 数据
