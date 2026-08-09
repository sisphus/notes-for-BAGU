# 15 — 项目面试表达与深挖

## Order Service Story

- REST API
- 状态机
- PostgreSQL schema
- JSONB 取舍
- transaction
- idempotency
- payment callback
- Redis GEO
- context timeout
- logging
- tests

## Contract Module Story

- template/sign_record 表
- versioning
- required/status/effective_date
- signed PDF path
- 权限
- 审计
- 历史记录不受模板更新影响

## Redis GEO Dispatch Story

- GEO key 设计
- staff_id member
- DB 持久化
- Redis 实时索引
- online TTL
- 距离排序
- fallback
- 并发接单

## Framework Story

- 为什么 GoFrame
- 框架解决什么
- 标准库机制是否理解
- ORM/codegen 优缺点
- 如何避免框架绑定

## Concurrency Story

- 异步任务
- worker pool
- goroutine 生命周期
- context cancellation
- retry
- panic recovery
- shutdown
- backpressure

## Bug/Performance Story

- 一个真实 bug
- 定位过程
- 日志/metrics/pprof
- 修复方案
- 验证结果
- 复盘
