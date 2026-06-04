# 01 — Go 语言核心模型

## Package / Import / Init

- package main 与普通 package
- 导出标识符规则
- import alias / blank import / dot import
- init 执行顺序
- 多文件初始化顺序
- import cycle 为什么禁止
- internal package 规则

## 变量 / 常量 / 零值

- var 与 :=
- 作用域与 shadowing
- zero value 工程意义
- untyped constant
- iota 枚举
- 多返回值与短变量声明陷阱

## 基础类型

- int/uint/float/bool/string
- byte 是 uint8 alias
- rune 是 int32 alias
- string 是只读 byte sequence
- UTF-8 与 len/range
- string/[]byte 转换成本
- strings.Builder 与 bytes.Buffer

## Array / Slice

- array 是值类型且长度属于类型
- slice descriptor: ptr/len/cap
- 底层数组共享
- append 扩容
- nil slice vs empty slice
- copy
- 三索引切片
- 删除/插入/过滤
- 小 slice 引用大数组导致内存问题
- range 取地址坑

## Map

- nil map 读写行为
- key 必须 comparable
- 遍历顺序不稳定
- comma ok
- delete 语义
- map 不是并发安全
- map as set/counter/index
- map value 是 struct 的修改坑

## Struct / Tag / Embedding

- struct 值语义
- 字段可见性
- json tag
- omitempty
- 匿名字段
- embedding 不是继承
- struct 比较规则
- 内存对齐基础

## Function / Closure

- 函数一等公民
- 多返回值
- 命名返回值
- variadic
- closure 捕获变量
- defer 与返回值交互

## Method / Receiver

- method vs function
- value receiver
- pointer receiver
- method set
- nil receiver
- receiver 选择原则
- 包含 mutex 的 struct 不可复制

## Interface

- 隐式实现
- interface 动态类型 + 动态值
- nil interface vs typed nil
- small interface
- any/interface{}
- type assertion
- type switch
- consumer-side interface

## Generics / Type Design

- type parameter
- constraint
- comparable
- type set
- 泛型函数/类型
- 何时不用泛型
- type alias vs defined type
- OrderID/UserID/OrderStatus 等领域类型
