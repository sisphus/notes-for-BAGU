# 09 — Go 标准库与后端开发

## net/http Server

- http.Handler
- HandlerFunc
- ServeMux
- request/response
- middleware
- server timeouts
- body limit
- method routing
- panic recovery
- graceful shutdown

## net/http Client

- 复用 http.Client
- 设置 timeout
- request context
- connection pooling
- response body close
- custom transport
- retry 谨慎

## encoding/json

- Marshal/Unmarshal
- struct tag
- omitempty
- Decoder
- unknown fields
- number 精度
- 自定义 MarshalJSON
- time 格式
- DTO 分离

## io

- Reader
- Writer
- Copy
- ReadAll
- LimitReader
- streaming
- 大文件不要一次性读入内存
- 小接口组合

## os/filepath/embed

- 文件读写
- 权限
- 临时文件
- 路径拼接
- 跨平台路径
- env
- signal
- embed static/migration

## time

- Time
- Duration
- timezone
- parse/format
- ticker/timer
- timer stop
- time.After 循环风险
- fake clock

## log/slog

- structured logging
- level
- request ID
- error logging
- PII 脱敏
- JSON logger
- 日志边界

## url/regexp/strconv

- URL parsing
- query
- escaping
- regexp compile/reuse
- Atoi/ParseInt
- 输入校验

## crypto

- crypto/rand vs math/rand
- HMAC
- SHA256
- constant time compare
- token
- 不要自创加密
- password hashing 用专门库
