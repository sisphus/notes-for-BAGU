# 08 — Testing、Benchmark、Fuzz、质量工程

## Testing Basics

- xxx_test.go
- TestXxx
- t.Fatal vs t.Error
- t.Helper
- t.Run
- t.TempDir
- t.Cleanup
- t.Parallel
- 测试隔离

## Table-driven Tests

- case struct
- name/input/expected
- error expectation
- 子测试
- 边界条件
- 避免测试逻辑过度复杂

## Testing Errors

- errors.Is
- errors.As
- wrapped error
- typed error
- 不要比对 error string

## HTTP Handler Tests

- httptest.NewRequest
- httptest.NewRecorder
- status/body/header
- middleware
- fake service
- JSON request/response

## Service Tests

- fake repository
- fake clock
- fake ID generator
- 状态机
- 幂等
- 并发业务测试

## Repository Integration Tests

- 真实 DB 更可信
- migration
- test schema
- transaction rollback 清理
- seed data
- context timeout

## Benchmark

- BenchmarkXxx
- b.N
- ResetTimer
- ReportAllocs
- 避免 compiler 消除
- benchstat
- microbenchmark 陷阱

## Fuzzing

- parser/validator/encoder
- seed corpus
- invariant
- crash input
- 不是替代单测

## CI Quality Gate

- go test ./...
- coverage
- race
- go vet
- staticcheck
- golangci-lint
- integration build tags
- flaky test
