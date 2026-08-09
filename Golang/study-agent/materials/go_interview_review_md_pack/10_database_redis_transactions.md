# 10 — Database、Redis、Transaction、Idempotency

## database/sql

- sql.DB 是连接池
- Open/PingContext
- QueryContext/QueryRowContext/ExecContext
- rows.Close/rows.Err
- Scan
- prepared statement
- 连接池参数
- context timeout

## Repository

- 封装 SQL
- 参数化防注入
- Null 类型
- 分页
- 排序白名单
- migration
- index
- query plan 意识

## Transaction

- BeginTx/Commit/Rollback
- defer rollback
- 事务边界在 service
- 不要事务内慢 IO
- 隔离级别
- lost update
- row lock
- optimistic lock
- outbox

## Isolation/Concurrency

- dirty/non-repeatable/phantom read
- read committed/repeatable read/serializable
- MVCC
- select for update
- version 乐观锁
- unique constraint
- transaction retry

## Idempotency

- idempotency key
- unique constraint
- request log table
- payment callback
- order creation
- processing/succeeded/failed
- 幂等不等于锁

## Redis Basics

- string/list/hash/set/zset
- TTL
- cache aside
- penetration/breakdown/avalanche
- distributed lock 基础
- rate limit
- GEO
- pipeline
- Lua

## Cache Consistency

- 读 cache miss 读 DB 写 cache
- 更新先 DB 后删 cache
- TTL 兜底
- eventual consistency
- singleflight
- 热点 key

## Order State Machine

- 合法状态转移
- 非法转移拒绝
- 事务更新
- 幂等状态更新
- 并发控制
- 状态日志
- 超时关闭
- 补偿
