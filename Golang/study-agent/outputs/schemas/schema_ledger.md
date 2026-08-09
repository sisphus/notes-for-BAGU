# Schema Ledger

## Go 后端能力分层 schema

- Trigger situation: 不知道 Go 后端面试该先学什么，或答题卡住时需要定位薄弱层。
- Compressed concepts: 语法层、Idiomatic Go 层、并发层、后端工程层、系统设计层、项目表达层。
- What the learner can do after acquiring it: 能把一个 Go 后端问题归类到对应能力层，并决定下一步该补语法、风格、并发、工程、设计还是表达。
- Common failure signal: 把复习路线当成普通目录；跳过基础直接背 GMP、GC、Redis、系统设计等结论。
- Status: Forming. First check correct: learner identified interface/code-style uncertainty as Idiomatic Go layer.

## Go 程序组织 schema

- Trigger situation: 看到一个 Go 项目入口、包结构、import、init 或 import cycle 问题时。
- Compressed concepts: package、导出标识符、import、init、main、internal、初始化顺序。
- What the learner can do after acquiring it: 能判断一个 Go 文件属于哪个包、哪些名字能被外部访问、程序初始化大致按什么顺序发生。
- Common failure signal: 把 package 当成文件夹名字死记；不理解大写导出规则；误用 init 做复杂业务逻辑；不理解为什么禁止循环导入。
- Status: Forming.
- Evidence: Learner explained export rules, ordered `config -> db -> main`, identified `service -> repo -> service`, and chose `model/domain` for shared pure types after repair.
- Review need: One later transfer check on breaking an import cycle.

## 识别并使用 Go 零值

- Trigger situation: 声明变量但没有显式初始化，或判断某个类型的默认可用状态时。
- Compressed concepts: `int` 的 `0`、`bool` 的 `false`、`string` 的 `""`、引用式类型的 `nil`，以及“有零值”不等于“所有零值都可执行任意操作”。
- Usable ability: 能预测未初始化变量的值，并在后续代码中判断该零值是否可直接使用。
- Common failure signal: 把 nil slice 和 nil map 的可用边界混为一谈，或认为声明变量后包含随机值。
- Status: Forming.
- Evidence: Learner correctly recalled the zero values of `string`, `bool`, and `[]string`.

## 选择声明方式并识别 shadowing

- Trigger situation: 声明局部或包级变量，尤其是在嵌套代码块中看到 `:=` 时。
- Compressed concepts: `var` 的作用域、`:=` 的函数内限制、至少一个新变量、内层同名变量、`=` 修改已有变量。
- Usable ability: 能选择合法的声明方式，并预测 `:=` 是否创建了遮蔽外层变量的新变量。
- Common failure signal: 以为内层 `:=` 修改了外层变量，导致错误或结果没有按预期传播。
- Status: Forming.
- Evidence: Learner predicted simple count shadowing, initially confused the returned `err`, then correctly repaired the code by reusing the outer `err` with `=`.
- Review need: Delayed transfer check on 2026-08-11.

## 让未类型常量适配目标类型

- Trigger situation: 将数字常量赋给 `int64`、`float64` 等不同目标类型，或判断常量赋值是否需要显式转换时。
- Compressed concepts: untyped constant、typed constant、目标类型上下文、值可表示范围。
- Usable ability: 能判断未显式指定类型的常量何时可适配目标类型，并区分它与已指定类型常量的赋值规则。
- Common failure signal: 认为 `const x = 10` 已经固定为 `int`，或认为 typed `int` 常量可无条件赋给 `int64`。
- Status: Forming.
- Evidence: Learner initially applied the untyped rule to a typed constant, then correctly distinguished direct assignment for `const a = 10` from explicit conversion for `const b int = 10`.
- Review need: Delayed comparison on 2026-08-11.

## 用 `iota` 构造可预测的枚举值

- Trigger situation: 定义一组连续或按行计算的整型常量，尤其是状态枚举和位标志时。
- Compressed concepts: 每个 `const` 块从 0 开始、每条 ConstSpec 递增、省略表达式复用、`_` 仍占一行、零值状态设计。
- Usable ability: 能预测 `iota` 常量值，并避免因插行或把业务状态从 0 开始而引入兼容性问题。
- Common failure signal: 认为 `_` 不计数、认为 `iota` 跨 `const` 块连续、或忽略零值对应的状态语义。
- Status: Forming.
- Evidence: Learner initially used a one-based model, then correctly predicted `A=1, B=2` when `_ = iota` explicitly consumed zero.
- Review need: Delayed sequence prediction on 2026-08-11.

## 保持已持久化枚举编号稳定

- Trigger situation: `iota` 枚举值已经写入数据库、消息、日志或公开 API，之后需要增加或调整状态时。
- Compressed concepts: 行顺序决定数值、持久化数据、协议兼容性、显式编号、只在末尾追加。
- Usable ability: 能识别插入枚举项导致的旧数据误读，并选择显式编号或兼容追加策略。
- Common failure signal: 在枚举中间插入新行，却认为只会影响源码排列而不会改变数据库中的含义。
- Status: Forming.
- Evidence: Learner first followed Paid's new number, then correctly traced stored integer `2` to `Reviewing` under the new mapping.
- Review need: Delayed persisted-value mapping on 2026-08-11.

## 区分 UTF-8 字节长度与 rune 数量

- Trigger situation: 对 Go string 使用 `len`、索引或 `range`，尤其是字符串包含中文或其他非 ASCII 字符时。
- Compressed concepts: string 是只读字节序列、UTF-8、`byte` 是 `uint8`、`rune` 是 `int32`、`len` 数字节、`range` 解码 rune。
- Usable ability: 能预测字符串的字节长度和 rune 迭代次数，并避免把字节索引当成字符索引。
- Common failure signal: 认为 `len("你好") == 2`，或认为 `s[0]` 一定得到第一个完整字符。
- Status: Forming.
- Evidence: Learner correctly predicted `len("A好") == 4`, two `range` iterations, and that `s[0]` for `"好"` is the first UTF-8 byte.
- Review need: Delayed mixed-string check on 2026-08-11.

## 在 `string` 与 `[]byte` 之间选择并预测转换语义

- Trigger situation: 需要修改文本、处理二进制数据、调用接收 `[]byte` 的 API，或在性能敏感循环中转换字符串时。
- Compressed concepts: string 不可变、`[]byte` 可变、转换通常复制、分配与 GC 成本、只读文本与可变缓冲区。
- Usable ability: 能预测转换后修改是否影响原值，并根据只读或可变需求选择表示形式。
- Common failure signal: 认为 `[]byte(s)` 与 `s` 共享可变存储，或在循环中反复转换而忽略分配成本。
- Status: Forming.
- Evidence: Learner correctly predicted that mutating `b := []byte(s)` leaves the original string unchanged.

## 在 `strings.Builder` 与 `bytes.Buffer` 之间选择

- Trigger situation: 需要高效累积文本，或需要一个可读写的字节缓冲区参与 I/O。
- Compressed concepts: 最终产物是 string 还是 bytes、只写文本、`io.Reader/io.Writer`、零值可用、预分配。
- Usable ability: 能根据最终表示和所需接口选择 Builder 或 Buffer，避免循环字符串拼接和不必要功能。
- Common failure signal: 最终只要 string 却默认使用 Buffer，或需要字节读写接口却选择 Builder。
- Status: Forming.
- Evidence: Learner correctly selected `strings.Builder` for write-only SQL construction with a final string result.

## 按值复制 array 并把长度视为类型

- Trigger situation: 赋值、传参或比较 Go array，或判断 `[3]int` 与 `[4]int` 是否兼容时。
- Compressed concepts: array 是值类型、赋值复制全部元素、函数参数按值、长度属于类型。
- Usable ability: 能预测 array 复制后的修改隔离，并识别不同长度数组是不同类型。
- Common failure signal: 认为 array 赋值后共享元素，或把不同长度的数组当成同一类型。
- Status: Forming.
- Evidence: Learner correctly predicted that modifying copied array `b` leaves `a[0] == 1`.

## 用 `ptr + len + cap` 预测 slice 共享

- Trigger situation: 切片、赋值或修改 slice，或判断两个 slice 是否会互相影响时。
- Compressed concepts: slice descriptor、起始指针、长度、容量、共享底层数组、窗口视图。
- Usable ability: 能从切片范围预测可访问元素、容量窗口和通过共享底层数组传播的修改。
- Common failure signal: 把 slice 当成独立数组，认为 `b := a[1:3]` 会复制元素。
- Status: Forming.
- Evidence: Learner correctly predicted `a == []int{1,99,3,4}` after modifying `b[0]` through a shared backing array.

## 先比较 `len/cap` 再预测 append 是否换数组

- Trigger situation: 对 slice 执行 append，尤其是多个 slice 共享同一底层数组时。
- Compressed concepts: 当前长度、容量窗口、复用写入、新数组分配、元素复制、append 返回新 descriptor。
- Usable ability: 能通过 append 后所需长度与 cap 的比较，预测修改是否会反映到其他共享 slice。
- Common failure signal: 认为 append 永远修改原数组，或认为 append 永远产生新数组；忽略接住 append 返回值。
- Status: Forming.
- Evidence: Learner first miscomputed len/cap, then correctly predicted both capacity reuse and capacity-exhausted allocation, including mutation isolation after reallocation.
- Review need: Delayed two-branch append check on 2026-08-11.

## 区分 nil slice 与 empty slice 的行为和身份

- Trigger situation: 初始化空集合、判断 `nil`、序列化 JSON，或编写返回 slice 的 API 时。
- Compressed concepts: 长度都为 0、append/range 都安全、nil 身份不同、JSON `null` 与 `[]`。
- Usable ability: 能区分算法行为上的“都为空”和接口表示上的 nil/non-nil 差异。
- Common failure signal: 认为 empty slice 等于 nil，或忽略 JSON 输出的 `null` 与 `[]` 差异。
- Status: Forming.
- Evidence: Learner correctly identified both lengths as 0, nil identity differences, and JSON outputs `null` versus `[]`.
- Review need: Delayed API representation check on 2026-08-11.

## 用 `make + copy` 切断 slice 顶层共享

- Trigger situation: 需要修改一个 slice 副本而不影响源 slice，或保存不受后续顶层元素修改影响的快照时。
- Compressed concepts: descriptor 赋值仍共享、目标分配、`copy` 数量、独立底层数组、浅拷贝边界。
- Usable ability: 能创建独立的 slice 元素存储，并判断元素内部引用是否仍然共享。
- Common failure signal: 认为 `dst := src` 会复制元素，或把 `copy` 误认为递归深拷贝。
- Status: Forming.
- Evidence: Learner first propagated the destination mutation back to the source, then correctly repaired the top-level prediction and independently predicted that a copied `[][]int` still shares its inner arrays: `dst[0][0] = 9` makes `src == [][]int{{9,2},{3,4}}`.
- Review need: Run a delayed direct-assignment, top-level copy, and nested shallow-copy comparison on 2026-08-11.

## 用三索引切片限制 append 的复用范围

- Trigger situation: 从已有 slice 截取子切片并交给其他函数，同时不希望它的 `append` 覆盖原 slice 的后续元素时。
- Compressed concepts: `s[low:high:max]`、新长度 `high-low`、新容量 `max-low`、append 容量判断、强制分配新数组。
- Usable ability: 能计算三索引切片的 `len/cap`，并预测 append 是否复用原底层数组。
- Common failure signal: 把第三个索引当作长度，或认为三索引切片能阻止已有索引上的直接修改。
- Status: Forming.
- Evidence: Learner correctly computed the effective capacity of `a[1:3:3]` and predicted that append allocates, leaving `a == []int{1,2,3,4}` while `b == []int{2,3,9}`.
- Review need: Delayed comparison with two-index slicing on 2026-08-11.

## 用原地搬移理解 slice 删除

- Trigger situation: 使用 `append(s[:i], s[i+1:]...)` 删除 slice 中间元素，尤其是还有其他 slice 共享底层数组时。
- Compressed concepts: 前缀窗口、后缀展开、append 复用、元素左移、返回 slice 的新长度、旧别名仍能看到尾部残值。
- Usable ability: 能预测删除后的逻辑 slice，以及共享同一底层数组的其他别名所观察到的内容。
- Common failure signal: 认为删除会创建完全独立的新数组，或忽略底层数组最后一个槽位仍保留旧值。
- Status: Forming.
- Evidence: Learner correctly predicted both views after ordered deletion: shortened `a == []int{1,3,4}` and full-length alias `b == []int{1,3,4,4}`.
- Review need: Delayed deletion-and-alias check on 2026-08-12.

## 用 `s[:0]` 复用存储进行原地过滤

- Trigger situation: 需要保留部分 slice 元素，同时希望减少新数组分配，并能接受修改原 slice 的底层数组时。
- Compressed concepts: 零长度同容量窗口、append 复用、保留元素向前覆盖、结果新长度、旧别名与尾部残值。
- Usable ability: 能预测原地过滤后的结果 slice，以及仍共享底层数组的旧别名所观察到的内容。
- Common failure signal: 认为 `out := s[:0]` 已经分配新数组，或认为过滤后原 slice 的旧长度和尾部槽位自动消失。
- Status: Forming.
- Evidence: After a prerequisite bridge, learner correctly predicted both one append to `s[:0]` and the full filtering loop: `out == []int{2,4}` while the longer alias observes `[]int{2,4,3,4}`.
- Review need: Review zero-length reslicing, tail values, and pointer cleanup on 2026-08-12.

## 用 `writeIndex <= readIndex` 判断原地过滤安全性

- Trigger situation: 质疑边遍历源 slice 边写回同一底层数组是否会覆盖尚未读取的元素时。
- Compressed concepts: 读取下标、保留元素计数、写入下标、只跳过或保留、不变量 `writeIndex <= readIndex`。
- Usable ability: 能解释标准原地过滤为什么不会破坏未来输入，并识别会向读取位置右侧写入的非标准算法需要额外分析。
- Common failure signal: 只凭经验说原地过滤安全，不能说明写入位置为何永远不超过读取位置。
- Status: Stable.
- Evidence: Learner independently stated that the number of retained elements cannot exceed the number already read, so the write position cannot move to the right of the read position.
- Review need: Brief invariant recall on 2026-08-16.

## 清理指针 slice 过滤后的尾部引用

- Trigger situation: 对 pointer、map、slice 或其他含引用元素的 slice 做原地删除或过滤，并关心被删除对象能否被 GC 时。
- Compressed concepts: 逻辑长度、容量内尾部槽位、残留引用、GC 可达性、`clear`、其他引用与回收时机。
- Usable ability: 能识别缩短 slice 后仍位于底层数组尾部的引用，并在需要时清零相应槽位。
- Common failure signal: 认为缩短 `len` 就会自动把底层数组尾部设为 nil，或认为 `clear` 会保证对象立即被 GC。
- Status: Forming.
- Evidence: Learner correctly predicted that filtering out `c` yields `out == {a,b}` while the full-length alias still observes `{a,b,c}`, demonstrating recognition of the residual tail reference.
- Review need: Delayed `clear` and GC-reachability check on 2026-08-12.

## 区分 range 变量副本与原 slice 元素地址

- Trigger situation: 在 range 循环中保存 `&v`、启动捕获 `v` 的 goroutine，或需要通过指针修改原 slice 元素时。
- Compressed concepts: range 值副本、元素地址、Go 1.22 每轮变量、`:=` 声明、`=` 重用已有变量、`&users[i]`。
- Usable ability: 能按 Go 版本和声明形式预测指针是否相同，并判断指针指向副本还是容器中的实际元素。
- Common failure signal: 无条件背诵“`&v` 都相同”，或在 Go 1.22+ 误认为 `&v` 可以修改 `users[i]`。
- Status: Forming.
- Evidence: Introduced with the official Go 1.22 version boundary after pointer-tail cleanup; first prediction pending.

## 用三条边界检查 map

- Trigger situation: 创建、读取、写入或共享一个 map，尤其是 map 来自 struct 零值、函数参数或多个 goroutine 时。
- Compressed concepts: 初始化边界、comparable key、并发访问、缺失 key 的零值、comma-ok。
- Usable ability: 能按“是否初始化 -> key 是否可比较 -> 是否并发”依次检查 map，并预测 read、write、delete 的行为。
- Common failure signal: 认为 nil map 完全不可用，或因为读取安全就误判写入也安全；把缺失 key 的零值当成真实数据。
- Status: Forming.
- Evidence: Learner correctly predicted a nil-map lookup as `v=0, ok=false` and correctly predicted panic for `counts["go"]++`, but incorrectly marked `delete` unsafe and attributed the panic partly to the missing key rather than to writing through a nil map.
- Repair evidence: Learner correctly predicted that `delete` on a nil map does not panic, `make` establishes writable storage, and the following increment produces `m["x"] == 1`.
- Comparability evidence: Learner correctly rejected `map[Key]int` when `Key` contains an `IDs []int` field, explaining that a slice cannot be compared with `==` and therefore makes the whole struct non-comparable.
- Concurrency evidence: Learner correctly classified overlapping map read/write as unsafe, but explained it only as reading dirty or unexpected data; the missing boundary is the unsynchronized data race and possible runtime fatal error.
- Concurrency repair evidence: Learner correctly recognized that a writer-side lock alone is insufficient and that readers must participate in the same synchronization discipline, using `RLock` for this `RWMutex` design.
- Review need: Delayed integrated transfer on initialization, comparable keys, and concurrent access; keep Forming until independent review.

## 用“取出—修改—写回”更新 map 中的 struct value

- Trigger situation: map 的 value 是 struct，并且需要修改其中一个字段时。
- Compressed concepts: map index expression、不可寻址值、struct 副本、取出—修改—写回、pointer value 的共享边界。
- Usable ability: 能解释为什么 `m[key].Field = value` 不能编译，并能选择复制写回或 pointer value 方案。
- Common failure signal: 把 map entry 当成普通可寻址变量，或只修改取出的副本却忘记写回 map。
- Status: Forming.
- Evidence: Learner predicted that the map would contain `"new"` after modifying only the local struct copy, and incorrectly stated that the copy had been written back even though the required `users["a"] = u` assignment was explicitly absent.
- Repair evidence: Learner then independently separated the two values: local `u.Name == "new"` while `users["a"].Name == "old"` without explicit write-back.
- Review need: Delayed copy/write-back transfer on 2026-08-13; keep Forming until then.

## 为 map 遍历显式建立稳定顺序

- Trigger situation: map 内容需要用于测试断言、API 输出、签名计算、日志对比或任何要求可重复顺序的场景。
- Compressed concepts: 未指定遍历顺序、收集 key、排序、按排序后的 key 访问 value。
- Usable ability: 能识别依赖 map range 顺序的脆弱代码，并通过 `keys -> sort -> lookup` 建立确定性输出。
- Common failure signal: 把当前一次观察到的顺序当成插入顺序，或测试直接断言 range 的输出排列。
- Status: Forming.
- Evidence: Learner correctly rejected direct range output as unreliable and gave the minimal deterministic procedure: sort the collected keys, then output values in that order.
- Review need: Delayed stable-output transfer on 2026-08-13.

## 按任务选择 map 的 value 形状

- Trigger situation: 需要用 map 表达成员集合、出现次数或按 ID 快速定位对象时。
- Compressed concepts: set=`map[K]struct{}`、counter=`map[K]int`、index=`map[K]V`、comma-ok membership。
- Usable ability: 能根据“只关心存在、需要累计数量、需要保存对象”选择 value 类型，并使用匹配的读写方式。
- Common failure signal: 只需要成员关系却存放多余值，或用读取到的零值代替 comma-ok 判断成员是否存在。
- Status: Forming.
- Evidence: Learner correctly selected `map[string]struct{}` for username membership, inserted `"alice"` with `struct{}{}`, and used comma-ok to test existence.
- Review need: Delayed set/counter/index selection on 2026-08-13.

## 区分 struct 外层值复制与字段内部共享

- Trigger situation: 赋值或传递一个含 slice、map、pointer 等字段的 struct，并需要预测修改是否传播时。
- Compressed concepts: struct 外层按值复制、普通字段隔离、引用式字段复制 descriptor 或引用、底层数据仍可共享。
- Usable ability: 能逐字段预测 struct 复制后的修改传播，而不是把整个 struct 一概称为深拷贝或浅拷贝。
- Common failure signal: 看到 struct 是值类型就认为所有嵌套数据都完全独立，或因为一个 slice 字段共享就认为所有普通字段也共享。
- Status: Forming.
- Evidence: Learner correctly predicted that after copying `Profile`, reassigning `p2.Name` leaves `p1.Name == "A"`, while mutating the shared slice backing array makes `p1.Tags[0] == "rust"`.
- Review need: Delayed mixed-field copy prediction on 2026-08-13.

## 区分字段可见性、JSON 名称与省略规则

- Trigger situation: 使用 `encoding/json` 编解码 struct，或设计 API 中字段名称、缺失值和零值语义时。
- Compressed concepts: exported field、unexported field、json tag、`omitempty`、零值与缺失字段。
- Usable ability: 能预测哪些字段会被编码、使用什么 JSON key，以及 `omitempty` 何时省略字段。
- Common failure signal: 认为小写字段加 tag 后也会被编码，或把 `omitempty` 当成默认值、校验规则或“反序列化时必填”。
- Status: Forming.
- Evidence: Learner correctly identified that `Name` is exported and `age` is unexported, but did not give the JSON result or apply `omitempty` to the exported empty string.
- Repair evidence: Learner correctly stated that an exported empty string tagged with `omitempty` does not appear in JSON because the empty value triggers omission.
- Review need: Delayed visibility-plus-omitempty transfer on 2026-08-13.

## 区分 embedding 的方法提升与继承关系

- Trigger situation: struct 嵌入另一个类型，并通过外层值调用被嵌入类型的字段或方法，或判断赋值与接口实现时。
- Compressed concepts: composition、anonymous field、promoted selector、method set、distinct defined types、not inheritance。
- Usable ability: 能说明 `s.Log()` 为什么可调用，同时不把 `Service` 当成 `Logger` 的子类，并能分别判断接口赋值与具体类型赋值。
- Common failure signal: 因为方法可以提升调用，就认为外层 struct 可直接赋给被嵌入的具体类型，或使用“父类/子类”解释 embedding。
- Status: Forming.
- Evidence: Learner correctly accepted assigning `Service{}` to an interface requiring `Log(string)` because of the promoted method, and rejected assigning `Service{}` to concrete `Logger` because embedding does not create a subtype relationship.
- Review need: Delayed promotion-versus-type-identity comparison on 2026-08-13.

## 用 alignment 与 padding 解释 struct 大小

- Trigger situation: struct 数量巨大、参与缓存或网络批处理，或 `unsafe.Sizeof` 显示大小明显超过字段之和时。
- Compressed concepts: field alignment、padding、struct alignment、field order、measurement before optimization。
- Usable ability: 能按字段对齐要求估算常见 struct 布局，并解释为什么重新排列字段可能减少 padding。
- Common failure signal: 只把字段字节数直接相加，或为了省几个字节无条件破坏领域可读性而不测量实际收益。
- Status: Forming.
- Evidence: Learner correctly estimated `A{bool; int64; byte}` as 24 bytes and reordered `B{int64; bool; byte}` as 16 bytes, identifying 7-byte internal plus 7-byte tail padding in A and 6-byte tail padding in B.
- Review need: Delayed layout estimation on 2026-08-13.

## 用 closure 的捕获环境解释状态持久化

- Trigger situation: 函数返回函数、回调需要保存局部状态，或多个 closure 看似使用同名局部变量却表现独立时。
- Compressed concepts: function as value、closure、captured variable、lifetime extension、independent invocation environment。
- Usable ability: 能预测 closure 多次调用的状态变化，并区分同一个 closure 的共享捕获与多次工厂调用产生的独立捕获环境。
- Common failure signal: 认为 closure 创建时只复制一次数值，或认为多个 `counter()` 调用共享同一个局部 `n`。
- Status: Forming.
- Evidence: Learner correctly predicted `a(), a(), b()` as `1, 2, 1`, explaining that `a` and `b` have independent captured environments and therefore distinct `n` variables.
- Review need: Delayed closure-state trace on 2026-08-13.

## 按返回槽与 defer 时序预测返回值

- Trigger situation: 函数包含 `defer`，尤其同时使用命名返回值、裸 `return` 或 defer closure 修改局部变量时。
- Compressed concepts: evaluate return expressions、assign result slots、LIFO defer execution、named result visibility、local variable versus result slot。
- Usable ability: 能按“确定返回值 -> 执行 defer -> 真正返回”预测结果，并判断 defer 修改的是命名返回槽还是普通局部变量。
- Common failure signal: 认为 `return` 立即离开函数，或认为 defer 修改任意同名局部变量都会改变已经确定的返回值。
- Status: Forming.
- Evidence: Learner correctly predicted `f() == 2` for a named result modified by defer and `g() == 1` when defer modifies only a local after its value was copied into an unnamed result slot.
- Review need: Delayed return-slot trace on 2026-08-13.

## 区分 variadic 独立实参与 slice 展开

- Trigger situation: 调用 `func f(xs ...T)`，尤其是把已有 `[]T` 以 `slice...` 形式传入并在函数内修改元素时。
- Compressed concepts: variadic parameter as slice、individual arguments、slice expansion、shared backing array、forwarding syntax。
- Usable ability: 能正确调用和转发 variadic 参数，并预测 `f(slice...)` 时函数内元素修改是否影响调用方 slice。
- Common failure signal: 忘记展开已有 slice，或认为 variadic 调用一定复制一份独立元素存储。
- Status: Forming.
- Evidence: Learner correctly predicted `s[0] == 99` after `setFirst(s...)` and identified that `nums` and `s` reach the same backing array; terminology was refined to two descriptor values sharing one array.
- Review need: Delayed variadic slice-expansion transfer on 2026-08-13.

## 用修改语义选择 value 或 pointer receiver

- Trigger situation: 为 struct 定义方法，并需要决定方法应操作副本还是原值时。
- Compressed concepts: method receiver、value receiver copy、pointer receiver mutation、addressable-call syntax、receiver consistency。
- Usable ability: 能预测方法调用是否修改原 struct，并根据“是否需要修改原值”选择最基本的 receiver 形式。
- Common failure signal: 认为方法语法天然按引用传递，或给需要持久修改状态的方法使用 value receiver。
- Status: Forming.
- Evidence: Learner correctly predicted final `c.N == 1`, identifying `IncValue` as operating on a receiver copy and `IncPointer` as operating on the original value.
- Review need: Delayed receiver-mutation prediction on 2026-08-13.

## 用 method set 判断 interface 实现

- Trigger situation: 把值或指针赋给 interface，尤其是所需方法使用 pointer receiver，而普通变量上又能直接调用该方法时。
- Compressed concepts: method set of `T`、method set of `*T`、pointer receiver、addressable-call sugar、interface assignment。
- Usable ability: 能分别判断方法调用是否可写和某个具体类型是否满足 interface，不被编译器的自动取地址调用语法误导。
- Common failure signal: 因为 `c.Reset()` 能调用，就认为值类型 `Counter` 的方法集包含 `Reset` 并实现相应 interface。
- Status: Forming.
- Evidence: Introduced after the learner demonstrated value-versus-pointer receiver mutation; first interface assignment comparison pending.

## 区分 Slice 队列的逻辑出队与内存释放

- Trigger situation: 用 slice 实现 BFS 队列或长期任务队列，并通过 `q = q[1:]` 出队时。
- Compressed concepts: slice 描述符、底层数组共享、`len/cap`、指针槽位、GC 可达性、head 下标、周期压缩、环形队列。
- Usable ability: 能在保持 FIFO 语义的同时，判断 `O(1)` 头删是否会保留大数组或已处理对象，并按队列生命周期选择清空、压缩或环形复用。
- Common failure signal: 认为 `len(q)` 变小就等于底层数组和已消费对象已经释放；或每次出队都复制剩余元素导致 `O(n²)`。
- Status: Forming.
- Evidence: Introduced from Chapter 02; learner transfer check pending.
