# 14 — Go 后端系统设计

## Answer Framework

- clarify requirements
- define API
- data model
- high-level architecture
- core flow
- consistency
- cache/performance
- failure handling
- observability
- scaling trade-offs

## Order Service

- 下单
- 支付回调
- 状态机
- 幂等
- 事务
- 缓存
- 超时取消
- 状态日志
- 消息队列
- 补偿

## Nearby Staff Matching

- Redis GEO
- PostGIS
- 实时位置
- 在线状态 TTL
- 候选排序
- 并发抢单
- 超时释放
- 隐私

## WebSocket Notification

- connection manager
- user->conn map
- heartbeat
- read/write pump
- backpressure
- offline message
- Redis pubsub
- 多实例路由

## File Upload

- multipart/chunk/resumable
- hash dedup
- object storage
- metadata DB
- signed URL
- streaming
- permission
- orphan cleanup

## URL Shortener

- base62
- collision
- unique constraint
- hot redirect cache
- analytics async
- expiration
- custom alias
- abuse prevention

## Rate Limiter

- fixed/sliding window
- token/leaky bucket
- local vs distributed
- Redis Lua
- per-user/IP/route
- fail open/closed

## Audit Log

- append-only
- actor/action/resource
- before/after diff
- immutability
- search
- retention
- permission
- tamper evidence

## Go-specific Choices

- goroutine 控制并发 IO
- context 超时取消
- channel 用于任务/事件
- mutex 保护共享状态
- pprof/race/benchmark 诊断
- 单 binary 易部署
