# `what_happen_url.md` Note Rewrite Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Subagent execution is disabled for this workspace task.

**Goal:** 将 `outputs/notes/base/what_happen_url.md` 重写为来源可靠、结构统一、示例一致且符合 `AGENTS.md` 的中文 schema-led 学习笔记。

**Architecture:** 以原文执行顺序作为教学主线，以九个可运行 schema 组织 Core Concepts；跨 schema 的 IP/MAC、交换机/路由器和各类表的对比集中放入 Deep Understanding。全文使用同一套客户端、R1、R2、服务器拓扑，并仅保留四张局部图和一张章节图。

**Tech Stack:** Markdown、Mermaid、`rg`、`awk`、`mmdc`

## Global Constraints

- 事实来源只能是 `materials/network/1_base/what_happen_url.md` 和必要的明确边界说明。
- 笔记必须包含七个契约章节：主题概览、核心概念、深入理解、最小工作示例、章节知识地图、自测题、薄弱点检测。
- Core Concepts 必须包含九个 schema，每个都有 Definition、Intuition、Example、Common mistakes。
- 全文统一使用 `192.168.1.10 → R1 → R2 → 192.168.3.50` 拓扑。
- 只保留四张概念局部 Mermaid 和一张章节 Mermaid。
- 不把未回答的“交换机用源 MAC 学习端口”标记为 Stable。
- 当前 worktree 含大量无关改动；只修改计划列出的学习文件，不清理、不暂存、不提交其他文件。

---

### Task 1: 整篇重写学习笔记

**Files:**
- Modify: `outputs/notes/base/what_happen_url.md`

**Interfaces:**
- Consumes: `materials/network/1_base/what_happen_url.md` 的 URL、DNS、协议栈、TCP、IP、MAC/ARP、网卡、交换机、路由器、服务器/客户端主线。
- Produces: 符合七段式契约的完整学习笔记，供 current session、schema ledger 和 review schedule 引用。

- [ ] **Step 1: 重建七段式骨架**

将正文一级教学结构固定为：

```markdown
### 1. 主题概览
### 2. 核心概念
### 3. 深入理解
### 4. 最小工作示例
### 5. 章节知识地图
### 6. 自测题
### 7. 薄弱点检测
```

- [ ] **Step 2: 写入九个 schema-led Core Concepts**

按下列顺序写入，每节均使用 Definition、Intuition、Example、Common mistakes：

```text
2.1 URL：协议、目标、资源
2.2 DNS：缓存链与分层委派
2.3 协议栈：委托、封装、解封装
2.4 TCP：连接状态与可靠字节流
2.5 IP：目标网段、路由表、出接口、下一跳
2.6 ARP/Ethernet/NIC：下一跳 IP 到 MAC，再到链路信号
2.7 Switch：源 MAC 学习、目标 MAC 转发、未知目标泛洪
2.8 Router：拆旧帧、查目标 IP、造新帧、逐跳重复
2.9 Destination：MAC、IP、协议号、端口逐层交付
```

- [ ] **Step 3: 写入四张局部 Mermaid**

局部图只回答以下问题：

```text
DNS：缓存何时短路，未命中时问谁
封装：HTTP 消息如何变成 TCP 段、IP 包、以太网帧
交换机：怎样学习源 MAC、查询目标 MAC、处理未知目标
两跳路径：每一跳的目标 IP 与目标 MAC 分别是谁
```

每张图后写 How to read、Source anchor、Boundary。

- [ ] **Step 4: 重写 Deep Understanding**

只保留四组跨 schema 关系：

```text
最终目标 IP vs 当前下一跳 MAC
路由表为何通常可信但可能过期
DNS/route/ARP/MAC 四张表的键值与范围
交换机原样二层转发 vs 路由器重建二层帧
```

随后列出 HTTPS、NAT、连接复用、非对称路由和浏览器渲染边界。

- [ ] **Step 5: 写入统一的 Minimal Working Example**

使用下列固定拓扑，并列出三跳帧：

```text
客户端 192.168.1.10
R1 左 192.168.1.1 / 右 192.168.2.1
R2 左 192.168.2.2 / 右 192.168.3.1
服务器 192.168.3.50

Hop 1: IP 192.168.1.10 -> 192.168.3.50; MAC Client -> R1-left
Hop 2: IP 192.168.1.10 -> 192.168.3.50; MAC R1-right -> R2-left
Hop 3: IP 192.168.1.10 -> 192.168.3.50; MAC R2-right -> Server
```

- [ ] **Step 6: 写入章节地图、自测和薄弱点检测**

章节地图限定 10–15 个连接节点；自测限定 3 道 recall、2 道 transfer、1 道 explain-like-I-am-5；薄弱点表必须覆盖下一跳、IP/MAC、ARP/MAC 表和交换机/路由器边界。

### Task 2: 对齐学习状态

**Files:**
- Modify: `outputs/state/current_session.md`
- Inspect only: `outputs/schemas/schema_ledger.md`
- Inspect only: `outputs/review/schedule.md`
- Inspect only: `outputs/weaknesses/profile.md`

**Interfaces:**
- Consumes: 重写后的笔记路径和当前真实学习进度。
- Produces: 指向正确笔记、保留真实 pending question 的 session 状态。

- [ ] **Step 1: 保持当前 schema 与 pending question**

保留：

```text
Current schema: 交换机用源 MAC 学习设备所在端口
Pending question: 源 MAC 为 MAC_A 的帧从端口 1 进入，应新增哪条 MAC 表记录？
```

- [ ] **Step 2: 更新 Next action**

将 Next action 写成：笔记重写完成后，从源 MAC 学习问题恢复微步教学，不把该 schema 提前标为 Stable。

- [ ] **Step 3: 检查其他学习状态而不批量改写**

确认 ledger 仍将“用源 MAC 学习交换机地址表”标为 Forming，review schedule 仍将对应问题标为 Pending，weakness 中的路由迁移检查没有被误删。

### Task 3: 结构和来源覆盖验证

**Files:**
- Verify: `outputs/notes/base/what_happen_url.md`
- Verify: `outputs/state/current_session.md`

**Interfaces:**
- Consumes: Task 1 与 Task 2 的产物。
- Produces: 可复现的结构、覆盖和状态验证证据。

- [ ] **Step 1: 检查七个章节**

Run:

```bash
rg -n '^### [1-7]\. ' outputs/notes/base/what_happen_url.md
```

Expected: 按 1–7 顺序恰好输出七个主章节。

- [ ] **Step 2: 检查九个 schema 及四个字段**

Run:

```bash
rg -c '^#### 2\.[1-9] Schema:' outputs/notes/base/what_happen_url.md
rg -c '^\- \*\*Definition\*\*:' outputs/notes/base/what_happen_url.md
rg -c '^\- \*\*Intuition\*\*:' outputs/notes/base/what_happen_url.md
rg -c '^\- \*\*Example\*\*:' outputs/notes/base/what_happen_url.md
rg -c '^\- \*\*Common mistakes\*\*:' outputs/notes/base/what_happen_url.md
```

Expected: 五条命令均输出 `9`。

- [ ] **Step 3: 检查原文主线覆盖**

Run:

```bash
for term in URL DNS TCP 路由表 ARP 网卡 交换机 路由器 服务器; do rg -q "$term" outputs/notes/base/what_happen_url.md || exit 1; done
```

Expected: exit code `0`。

- [ ] **Step 4: 检查修补痕迹、围栏和状态**

Run:

```bash
! rg -n '下一小步|再下一步|再下一跳' outputs/notes/base/what_happen_url.md
awk '/^```/{n++} END {exit n%2}' outputs/notes/base/what_happen_url.md
rg -F 'Note file: outputs/notes/base/what_happen_url.md' outputs/state/current_session.md
rg -F 'Current schema: 交换机用源 MAC 学习设备所在端口' outputs/state/current_session.md
```

Expected: 所有命令 exit code `0`。

### Task 4: Mermaid 渲染验证

**Files:**
- Verify: `outputs/notes/base/what_happen_url.md`
- Temporary output: `/tmp/what-happen-url-mermaid/`

**Interfaces:**
- Consumes: 笔记中的五个 Mermaid 代码块。
- Produces: 五个成功生成的 SVG，仅用于验证，不写入仓库。

- [ ] **Step 1: 检查 Mermaid 数量**

Run:

```bash
rg -c '^```mermaid$' outputs/notes/base/what_happen_url.md
```

Expected: `5`。

- [ ] **Step 2: 提取并逐一渲染**

Run:

```bash
rm -rf /tmp/what-happen-url-mermaid
mkdir -p /tmp/what-happen-url-mermaid
awk '
  /^```mermaid$/ {inside=1; n++; next}
  inside && /^```$/ {inside=0; close("/tmp/what-happen-url-mermaid/diagram-" n ".mmd"); next}
  inside {print > ("/tmp/what-happen-url-mermaid/diagram-" n ".mmd")}
' outputs/notes/base/what_happen_url.md
for f in /tmp/what-happen-url-mermaid/*.mmd; do mmdc -i "$f" -o "${f%.mmd}.svg"; done
test "$(find /tmp/what-happen-url-mermaid -name '*.svg' | wc -l | tr -d ' ')" = 5
```

Expected: 五次 `Generating single mermaid chart`，最后 exit code `0`。

- [ ] **Step 3: 最终直接回读**

Run:

```bash
sed -n '1,220p' outputs/notes/base/what_happen_url.md
sed -n '221,520p' outputs/notes/base/what_happen_url.md
sed -n '1,40p' outputs/state/current_session.md
```

Expected: 无重复修补段、无断裂标题、示例地址一致、pending question 未被提前判定。
