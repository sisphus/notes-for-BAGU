# 06 — sync、atomic、race、Memory Model

## Data Race

- 两个 goroutine 并发访问同一变量，至少一个写，且无同步
- race detector
- 本地能跑不等于安全
- 闭包捕获/map 并发写高危

## Mutex

- 保护临界区
- Lock/Unlock
- 不能复制
- 临界区大小
- 死锁
- 锁顺序
- 持锁期间避免慢 IO

## RWMutex

- 读多写少
- RLock/RUnlock
- 不能锁升级
- 不一定比 Mutex 快
- benchmark 验证

## Once

- 只执行一次
- lazy init
- 并发安全初始化
- 与 init 区别
- 初始化失败策略

## Cond

- 条件变量
- Wait 释放锁并等待
- Signal/Broadcast
- Wait 放在 for 里
- 复杂条件等待

## Atomic

- atomic.Int64
- Load/Store/Add/CAS
- 简单 counter/flag
- 不能保护复杂不变量
- 可读性取舍

## sync.Map

- 并发安全
- 读多写少/key 稳定
- 类型安全弱
- Range 非强一致快照
- 不是默认选择

## Memory Model

- happens-before
- channel send/receive 同步
- mutex unlock/lock 同步
- atomic 同步语义
- 无同步共享变量不可靠
- 重排序意识

## Deadlock/Starvation

- channel deadlock
- mutex deadlock
- livelock
- starvation
- lock ordering
- timeout
- goroutine dump
