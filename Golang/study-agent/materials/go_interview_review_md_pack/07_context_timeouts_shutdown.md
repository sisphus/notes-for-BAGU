# 07 — Context、Timeout、Cancellation、Graceful Shutdown

## Context Model

- 树状取消传播
- WithCancel/Timeout/Deadline
- 第一个参数
- 不存 struct
- 不传 nil
- Value 只放 request-scoped metadata
- 必须 cancel

## HTTP Request Context

- r.Context()
- 客户端断开取消
- 传给 service/DB/Redis
- middleware 注入 request ID
- 后台任务不要盲目用 request ctx

## Timeout Design

- client/server/DB/Redis/external API timeout
- deadline propagation
- ReadHeaderTimeout/ReadTimeout/WriteTimeout/IdleTimeout
- timeout 不是越长越好

## Cancellation Propagation

- goroutine select ctx.Done
- pipeline 取消
- worker pool 取消
- DB/Redis context
- 取消后清理资源

## Graceful Shutdown

- SIGINT/SIGTERM
- 停止接收新请求
- 等待 in-flight
- 最大等待时间
- http.Server.Shutdown
- 关闭 DB/Redis/worker
- flush logs

## Background Jobs

- app lifecycle ctx
- job-level timeout
- retry
- idempotency
- panic recovery
- shutdown 停止消费
- ticker stop

## Anti-patterns

- context 存 struct
- 忘记 cancel
- Value 放业务参数
- 用 Background 忽略上游取消
- 循环创建 timeout 不 cancel
- goroutine 不监听 Done
