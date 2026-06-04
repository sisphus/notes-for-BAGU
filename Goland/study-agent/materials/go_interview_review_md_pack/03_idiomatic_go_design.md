# 03 — Idiomatic Go 与工程设计

## Go Philosophy

- simple
- explicit
- composition over inheritance
- small interfaces
- errors are values
- standard library first
- less magic
- readability over cleverness

## Naming

- package 短名
- 避免 stutter
- Reader/Writer 风格接口
- ErrXxx
- receiver 短命名
- HTTP/URL/ID 缩写规则
- 导出标识符注释

## Package Design

- 按能力组织
- internal
- cmd
- pkg 是否必要
- 公开面最小化
- 避免循环依赖
- import graph 简洁

## Interface Design

- 由使用方定义
- 小接口
- 不要为每个 struct 建 interface
- 行为抽象
- io.Reader 典范
- mock 不等于必须 interface

## Composition

- embedding
- middleware chain
- decorator
- functional options
- 组合替代继承

## Dependency Injection

- constructor injection
- interface injection
- main wiring
- 避免全局变量
- fake repo 测试
- lifecycle 管理

## Abstraction Boundaries

- handler/service/repository/domain
- 框架不污染业务
- DTO 与 domain 分离
- SQL 不泄漏到 handler
- 避免过早抽象

## Code Review Checklist

- race
- goroutine leak
- missing context
- ignored error
- unclosed rows/body
- transaction rollback
- duplicate logs
- weak tests
- bad names
