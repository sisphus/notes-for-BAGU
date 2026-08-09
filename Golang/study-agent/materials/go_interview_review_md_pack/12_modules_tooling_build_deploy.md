# 12 — Modules、Tooling、Build、Deploy

## Go Modules

- module path
- go.mod
- go.sum
- direct/indirect
- minimal version selection
- go mod tidy
- go get
- replace/exclude
- private modules
- GOPATH vs modules

## Workspace

- go.work
- 多 module 联调
- workspace vs replace
- mono-repo 场景
- 是否提交 go.work

## Static Analysis

- gofmt
- goimports
- go vet
- staticcheck
- golangci-lint
- errcheck
- ineffassign
- CI gate

## Build

- go build/install/run
- build tags
- ldflags 注入 version
- CGO_ENABLED
- cross compilation
- race build
- trimpath
- binary size

## Configuration

- env
- config file
- flags
- 优先级
- secret 不进 git
- dev/staging/prod
- validation
- 脱敏打印

## Docker

- multi-stage
- builder/runtime image
- distroless/alpine/scratch
- CGO/libc
- healthcheck
- non-root user
- docker compose

## CI/CD

- test/lint/build
- cache dependencies
- docker build/push
- migration
- rollback
- secret management
- artifact

## Observability Setup

- healthz/readyz
- logs
- metrics
- tracing
- pprof
- request ID
- latency p95/p99
- DB pool metrics
- goroutine count
