# Error Log

Record real learner mistakes so they can become review assets.

## Template

```markdown
## YYYY-MM-DD

- Topic: <topic>
- Question: <question asked>
- User answer: <learner answer>
- Correct reasoning: <short correct reasoning>
- Error type: <missing prerequisite / concept misunderstanding / procedure confusion / boundary confusion / overloaded working memory / surface-level memorization / transfer failure>
- Fix strategy: <targeted repair>
```

## 2026-07-17

- Topic: HTTP 定义三问拆解
- Question: 为什么“HTTP 是用于从互联网服务器传输超文本到本地浏览器的协议”这个说法不够准确？请用“两点之间”和“传输方向”两个关键词回答。
- User answer: 不够准确；HTTP 是一个在计算机世界里，专门在两点之间传输文字、图片、音频、视频等超文本数据的约定和规范。
- Correct reasoning: 答案正确覆盖了“两点之间”和超文本范围，但还需明确 HTTP 是双向协议：请求方可以向响应方发送请求，响应方也会向请求方返回响应；端点也不限于浏览器和服务器。
- Error type: boundary confusion
- Fix strategy: 只追问请求与响应各自的传输方向，确认学习者能明确说出“双向”。

## 2026-07-20

- Topic: 把客户端重定向改成代理内部改写
- Question: 哪种方案减少客户端到代理的请求次数，具体消失了哪一组客户端可见消息？
- User answer: 代理内部改写；具体消失了新地址，直接给了新资源。
- Correct reasoning: `url2` 没有消失，代理内部仍可能使用它访问源服务器；客户端链路上消失的是代理返回的 `302 + Location: url2`，以及客户端随后发出的第二次 `url2` 请求。
- Error type: boundary confusion
- Fix strategy: 固定观察边界为“客户端 ↔ 代理”，用两个方向填空，只修复消失的响应与请求消息。
- Repair answer: 代理到客户端的 `302 + Location: url2`；客户端到代理的再次请求 `url2`。
- Result: Correct after targeted repair

### TLS 握手消息、record 与 TCP 分段边界

- Topic: 分清 Handshake message、TLS record、TCP segment 和 flight
- Question: 同一个 TCP segment 里出现 `ServerHello`、`Certificate`、`ServerHelloDone`，是否说明三者是同一个 handshake message？请分别从 handshake message、TLS record、TCP segment 三层回答。
- User answer: 正确说明三者仍是三个 handshake messages，并把 handshake message、TLS record、TCP segment 类比为信的内容、信封和运输工具；没有明确判断 TLS record 的数量能否由 TCP segment 推出。
- Correct reasoning: 三者是三个协议语义消息；它们可以由一个或多个 TLS records 承载，而一个 TCP segment 也可以承载一个或多个 records，因此仅凭“位于同一个 TCP segment”不能推出 record 数量。
- Error type: boundary confusion
- Fix strategy: 不重讲已正确的消息和 TCP 层，只追问“能否从一个 TCP segment 推出只有一个 TLS record”。
- Repair answer: 不能；同一个 TCP segment 只表示 TCP 一起运输这些内容，不能说明它们只占一个 TLS record，必须查看 TLS record header 才能确定边界。
- Result: Correct after targeted repair

### 证书链可信与域名匹配

- Topic: 证书信任链证明服务端公钥归属
- Question: 访问 `bank.example` 时收到一张由受信任根 CA 链签出、但只适用于 `evil.example` 的证书；客户端是否应接受，哪项验证通过、哪项失败？
- User answer: “能；因为是客户端本地信任的根证书。”
- Correct reasoning: 链到本地信任根只表示证书签发路径可以被信任；叶子证书的身份仍必须覆盖当前访问的 `bank.example`。本例证书链验证可通过，但 hostname/identity matching 失败，客户端必须拒绝。
- Error type: boundary confusion
- Fix strategy: 固定使用“谁签的”与“签给谁的”二问；只追问哪项检查阻止合法的其他域名证书冒充目标网站。
- Repair answer: 因为域名匹配失败，证件不属于目标身份。
- Result: Correct after targeted repair

## 2026-07-21

### ECDHE 临时私钥泄漏范围

- Topic: 用每次独立的临时私钥判断前向保密
- Question: 攻击者获得昨天服务端临时私钥 `b1` 和抓包中的客户端公钥 `A1`，能否恢复昨天的 `K1` 与今天的 `K2`？
- User answer: “不能，今天的 a 和 b 换了，无法计算今天的 `K2`。”
- Correct reasoning: 今天的临时私钥已更换，所以旧 `b1` 不能恢复 `K2`；但对于昨天的同一会话，攻击者已经具备 `A1` 和 `b1`，可以按服务端原公式计算 `K1 = A1^b1`。前向保密限制跨会话影响，不保护临时私钥已经泄漏的那一次会话。
- Error type: boundary confusion
- Fix strategy: 固定区分“同一会话”和“其他会话”；只要求补全 `K1 = A1^b1` 并判断该会话是否可恢复。
- Repair answer: `K1 = A1^b1`，攻击者能够恢复该次会话的 `K1`。
- Result: Correct after targeted repair

## 2026-07-22

### TCP D-SACK 误重传原因诊断

- Topic: 用 D-SACK 结合触发顺序诊断误重传原因
- Question: 原始数据延迟，后续段产生 3 个重复 ACK 并触发快速重传；重传副本先到、原始副本后到。判断是 ACK 丢失还是原始数据延迟，并说明原因。
- User answer: 正确判断“原始数据发生网络延迟”，并指出重传副本先到、原始副本后到；但随后说“问题在 ACK 路径”。
- Correct reasoning: 这次重传由 3 个重复 ACK 触发，而不是因 ACK 消失等待到 RTO；原始副本随后到达，说明原始数据在前向路径被延迟。若是 ACK 丢失，则原始数据早已正常到达，发送方通常因未获确认而在 RTO 后重传。
- Error type: boundary confusion
- Fix strategy: 固定先看重传触发器，再判断故障路径；只追问“3 个重复 ACK 对应前向数据延迟还是 ACK 返回路径丢失”。
- Repair answer: 这次重传由快速重传触发，因此问题在原始数据的前向路径，不是 ACK 返回路径。
- Result: Correct after targeted repair

### TCP 接收窗口左右边界

- Topic: 用 `RCV.NXT` 与 `RCV.WND` 定位可接收范围
- Question: `RCV.NXT=700`、`RCV.WND=100`；指出下一期待字节、区 4 起点，并判断序号 820 当前能否接收。
- User answer: 把下一期待字节答为 800；正确指出区 4 的第一个字节是 800，并判断序号 820 当前不能接收。
- Correct reasoning: `RCV.NXT` 本身就是下一连续期待字节和接收窗口左边界，因此下一期待 700；`RCV.NXT+RCV.WND=800` 是半开区间 `[700,800)` 的右边界，也是区 4 起点。
- Error type: boundary confusion
- Fix strategy: 固定“左边界 = 下一期待”和“右边界 = 窗口外起点”二分，只追问为什么下一期待是 700 而不是 800。
- Repair answer: 接收端下一期待字节是 700；800 正好是可接收半开区间的右边界，因此不属于当前可接收范围。
- Result: Correct after targeted repair

### TCP 快速重传降窗与快速恢复入口时刻

- Topic: 区分降窗赋值完成与 `ssthresh+3` 临时膨胀
- Question: 旧 `cwnd=16 MSS`；先执行 `cwnd=cwnd/2`，再执行 `ssthresh=cwnd`，正确顺序后两者各是多少？
- User answer: `cwnd=11 MSS`、`ssthresh=8 MSS`；同时正确识别反向顺序会使新 ACK 将 cwnd 弹回 16 MSS。
- Correct reasoning: 这两行执行完的瞬间是 `cwnd=8 MSS`、`ssthresh=8 MSS`；`cwnd=11 MSS` 是下一阶段进入快速恢复后再执行 `cwnd=ssthresh+3` 的结果。
- Error type: boundary confusion
- Fix strategy: 固定两个时刻：A 仅完成降窗赋值为 8/8；B 快速恢复入口临时膨胀为 11/8；只追问 A。
- Result: Repair pending

## 2026-07-23

### IPv4 分片的重组主体与丢片后果

- Topic: 先用 MTU 判断 IPv4 分片，再把重组责任放在目标主机
- Question: 一个 `4000` 字节的 IPv4 数据报经过 MTU 为 `1500` 字节的链路：是否需要分片，谁负责重组，丢失任意一片会怎样？
- User answer: “是；不负责重组；IP 层不会单独重传丢失的分片；是否重传由上层协议处理。”
- Correct reasoning: 因 `4000 > 1500` 需要分片；中间路由器不重组，只有最终目标主机重组；任意一片丢失，整个原始 IP 数据报就无法完整重组。IP 层不单独重传分片，上层协议决定后续恢复。
- Error type: boundary confusion
- Fix strategy: 用三个固定槽位补全“中间路由器不重组 / 最终目标主机重组 / 丢一片则整报文无法完整重组”。
- Repair answer: 中间路由器不负责重组；最终目标主机负责重组；任意一片丢失会导致整个原始 IP 数据报无法完整重组。
- Result: Correct after targeted repair

### IPv6 压缩遗漏组内前导零

- Topic: 把 IPv6 写成 8 组十六进制，并只用一次 `::` 压缩连续零组
- Question: 将 `2001:0db8:0000:0000:0000:0000:0000:0001` 按 IPv6 规则压缩成简写形式。
- User answer: `2001:0db8::0001`
- Correct reasoning: 该答案已合法地用一次 `::` 压缩连续全 0 组，但最简写法还要删除每组的前导 0：`0db8 -> db8`、`0001 -> 1`，得到 `2001:db8::1`。
- Error type: procedure confusion
- Fix strategy: 将 IPv6 缩写固定为两步：先删每组前导 0，再用一次 `::` 压缩最长连续全 0 组。
- Repair answer: `2001:db8::1`
- Result: Correct after targeted repair

### IPv6 通信类型遗漏无广播边界

- Topic: 按通信对象区分 IPv6 单播、组播、任播与无广播
- Question: A 只发给一台指定主机；B 发给加入某组的多台主机；C 多个服务节点共享一个地址，由路由送到最近的一个。分别属于什么 IPv6 类型？IPv6 是否还有广播地址？
- User answer: “A:单播；B：组播；C：任播”
- Correct reasoning: A、B、C 映射均正确；但 IPv6 没有广播地址，需要一对多时使用组播等机制。
- Error type: boundary confusion
- Fix strategy: 保留已正确的三类映射，只用“IPv4 有广播 vs IPv6 无广播、一对多用组播”对比修复。
- First repair answer: “定义了广播地址；组播”
- First repair result: Incorrect；一对多使用组播正确，但在提示后仍把 IPv6 误判为有广播地址
- Second repair prompt: IPv6____广播地址；需要一对多时使用____。
- Second repair answer: “IPv6 没有广播地址；需要一对多时，使用组播”
- Result: Correct after second targeted repair
