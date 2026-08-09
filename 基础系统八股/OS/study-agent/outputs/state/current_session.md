# Current Session

- Topic: 一致性哈希：从数据定位到局部迁移
- Source type: Local Markdown chapter
- Source file: materials/os/8_network_system/hash.md
- Note file: outputs/notes/network_system/os_consistent_hashing_notes.md
- Current schema: 整章综合迁移：扩容 token、寻址、局部迁移、权重与故障分担（Pending）
- Roadmap position: 5 个局部 schema 已全部 Stable；第 5 个的绝对权重基准已修复为 `100,100,200`；章节仍保持打开，等待 final transfer
- Learner level: 已分别稳定数据布局选择、`%N` 重映射、哈希环寻址/迁移、所有权倾斜/雪崩和虚拟节点的两层映射/故障分担/权重；尚未在同一新场景中无提示串联。
- Pending question: 分片 KV 集群中，A、B 的权重各为 1，每个权重单位 100 个虚拟节点；现加入权重为 2 的真实节点 C。在 C 的 200 个 token 中，有一个代表 token `C-03@60` 插入 `B-07@45 -> A-12@80` 之间，且 `key@55`。请一次说明：C 为什么配 200 个 token；`key@55` 在插入前后分别访问哪个真实节点；哪个坐标区间从谁迁到谁；为什么其他区间不像 `%N` 那样全局重映射；如果 C 之后故障，其 200 个 token 的区间如何分担。
- Last answer: 用户将权重绝对数量修正为 `A=100, B=100, C=200`，同时保留了正确的 `1:1:2` 比例与热 key 边界。
- Next action: 评估学习者能否串联 `C=200`、`key@55: A -> C`、`(45,60]: A -> C`、固定环的局部区间更新，以及 C 的多个 token 分别转给各自顺时针后继所实现的故障分担；全部稳定后再关闭章节。
- Updated at: 2026-07-19

## Roadmap

1. 用“副本同构 vs 数据分片”选择请求分配策略。（Stable）
2. 用“取模基数是映射函数的一部分”诊断普通哈希的全局重映射。（Stable；算术表 deliberate skip）
3. 用“固定环 + 顺时针后继”运行一致性哈希寻址与局部迁移。（Stable）
4. 用“所有权区间大小”诊断节点倾斜与雪崩传导。（Stable）
5. 用“key -> 虚拟节点 -> 真实节点”运行均衡、容灾和加权。（Stable）

Final transfer: 给定环坐标、key 坐标与节点变化，完成寻址、迁移区间判定与虚拟节点选择。（Current: pending learner answer）

## Scope Boundary

本会话以 `materials/os/8_network_system/hash.md` 为唯一章节主线。它覆盖旧的 Reactor/Redis pending question，但不删除旧章节已有笔记与复习记录。笔记保留原文 `2^32` 哈希环和 Nginx 虚拟节点例子的语境；不把本文扩展成完整的分布式一致性、复制或故障检测协议课程。
