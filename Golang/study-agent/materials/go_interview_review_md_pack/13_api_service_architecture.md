# 13 — Go API Service Architecture

## Layered Architecture

- handler/controller
- service/usecase
- repository/store
- domain/model
- infrastructure
- middleware
- config/logger/app wiring
- 业务不依赖框架

## DTO/Command/Domain

- request DTO
- response DTO
- domain model
- command object
- 输入校验
- 输出脱敏
- DB entity 不直接暴露

## API Design

- REST methods
- status code
- pagination
- filtering
- sorting
- error response
- idempotency key
- versioning
- backward compatibility

## Middleware

- logging
- recovery
- request ID
- auth
- timeout
- rate limit
- CORS
- body limit
- metrics
- 顺序

## Auth/Authz

- authentication vs authorization
- session/JWT/API key
- RBAC
- resource ownership
- service 层权限
- context identity
- audit log

## Transaction Boundary

- service/usecase 开事务
- repo tx-aware
- transaction manager
- commit 后事件
- outbox
- 幂等结合事务

## Observability

- structured logs
- request/trace ID
- latency
- status code
- error rate
- business metrics
- DB/Redis latency
- PII 脱敏

## Reliability Patterns

- timeout
- retry/backoff
- circuit breaker
- bulkhead
- rate limit
- idempotency
- degradation
- queue
- dead letter
- compensation

## Security Basics

- input validation
- SQL injection
- password hashing
- secret management
- TLS
- webhook signature
- least privilege
- audit log
