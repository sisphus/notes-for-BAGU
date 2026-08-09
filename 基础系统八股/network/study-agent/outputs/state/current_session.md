# Current Session

- Topic: HTTP/2 性能机制
- Source files: materials/network/2_http/http2.md
- Note file: outputs/notes/http/http2.md
- Current schema: 从 HTTP/1.1 外部优化上限识别 HTTP/2 协议重设计需求
- Learner level: Level 2 起点；已学过 HTTP/1.1 的“不发、少发、少传”和响应队头阻塞，可从已有优化模型迁移到 HTTP/2
- Pending question: 页面已经把 100 张小图合成一张 Sprite，但同一 HTTP/1.1 连接里一个慢响应仍挡住后续响应；继续合图能解决这个阻塞吗？为什么？
- Last answer: 尚未作答；本轮已完整读取 `http2.md` 并创建源材料笔记
- Next action: 先检查能否区分“减少请求次数”的外部优化与“改变同连接并发规则”的协议重设计；稳定后进入语义兼容与 HPACK
- Previous session: `ip_base.md` 的章末综合迁移因用户明确切换到 `http2.md` 而延期，不用旧题阻塞新章节
- Updated at: 2026-07-23

## Roadmap

1. HTTP/1.1 外部优化的上限与 HTTP/2 重设计动机 ← **当前**
2. HTTP 语义兼容 vs 二进制传输语法
3. HPACK：静态表、动态表、Huffman 编码
4. 二进制 Frame 与 9 字节 Frame Header
5. Connection、Stream、Message、Frame 层级
6. 多 Stream 交错、奇偶 ID、唯一递增与优先级
7. `PUSH_PROMISE` 与服务器主动推送
8. HTTP 层队头阻塞 vs TCP 层队头阻塞
9. 章末迁移：从页面资源加载诊断 HTTP/2 收益与边界
