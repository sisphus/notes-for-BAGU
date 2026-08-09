# 05 — Go 并发基础

## Goroutine

- 轻量执行单元
- 生命周期
- main 退出影响
- goroutine leak
- panic in goroutine
- G/M/P 直觉
- 不要无限创建
- 循环变量捕获

## Channel Basics

- unbuffered 同步
- buffered 阻塞条件
- direction
- nil channel
- closed channel 读取
- 向 closed channel 发送 panic
- 发送方关闭原则
- range channel

## select

- 等待多个 channel
- ready case 随机选择
- default 非阻塞
- timeout
- cancellation
- nil channel 禁用 case
- select in loop
- busy loop 风险

## WaitGroup

- Add/Done/Wait
- Add 在 go 前
- defer Done
- 不能复制
- Add/Wait 并发坑
- 只等待不传播错误

## Worker Pool

- 固定 worker
- jobs/results channel
- close jobs
- WaitGroup
- context cancellation
- backpressure
- 错误处理

## Pipeline

- stage
- generator
- transformer
- fan-out
- fan-in
- close outbound
- drain inbound
- 避免泄漏

## Semaphore/Rate Limit

- buffered channel 作为 semaphore
- 限制并发
- ticker rate limit
- token bucket 直觉
- defer 释放 token

## Channel vs Mutex

- channel 适合通信/任务/事件
- mutex 适合保护共享状态
- 不要为 channel 而 channel
- 简单状态用 mutex 更清晰
