# 04 — Error、defer、panic、recover

## Error Basics

- error 是 interface
- errors.New
- fmt.Errorf
- 不要忽略 error
- error 携带上下文
- 日志边界
- 对外错误与内部错误分离

## Sentinel Error

- ErrNotFound
- errors.Is
- 稳定业务状态
- API 契约
- 不要字符串匹配

## Typed Error

- struct 实现 Error()
- errors.As
- validation/permission/rate limit error
- typed nil 陷阱

## Wrapping

- %w vs %v
- error chain
- errors.Unwrap
- errors.Join
- 避免过度 wrap

## Backend Error Mapping

- infra/domain/validation/auth error
- handler 转 HTTP status
- service 不依赖 HTTP
- 敏感信息不外泄

## defer

- LIFO
- 参数注册时求值
- 命名返回值
- 资源释放
- 循环 defer 风险
- defer unlock/close/rollback

## panic/recover

- panic 不是业务控制流
- panic 执行 defer
- recover 只在 deferred function 生效
- 只能捕获当前 goroutine
- HTTP recovery middleware
- worker recovery

## Resource Cleanup

- file.Close
- rows.Close
- rows.Err
- resp.Body.Close
- tx.Rollback
- mutex Unlock
- temporary cleanup
