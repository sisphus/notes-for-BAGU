# Weakness Profile

Record repeated mistakes or important gaps. Do not record every small hesitation.

## Template

```markdown
## Topic: <topic>

- Weakness: <specific weakness>
- Evidence: <what the learner said or did>
- Error type: <missing prerequisite / concept misunderstanding / procedure confusion / boundary confusion / overloaded working memory / surface-level memorization / transfer failure>
- Fix strategy: <how future tutoring should repair it>
- Status: Active
```

## Topic: RFC 规范语义与实际实现

- Weakness: 尚未形成“标准规定应该怎样”和“代码实际上怎样”的二层判断。
- Evidence: 学习者在 GET/POST 语义题前主动回复 `missing prerequisite。RFC 规范语义`。
- Error type: missing prerequisite
- Fix strategy: 先用交通规则类比建立规范层与行为层，再用一个不符合 RFC 语义的 GET 接口做分类。
- Status: Resolved 2026-07-19

## Topic: 路由表下一跳为何可信

- Weakness: 缺少“局域网、路由器接口、直连目标、相邻路由器”的空间模型，因此直接讲路由表和逐跳承诺造成严重抽象负荷。
- Evidence: 学习者先报告无法理解下一跳为何正确，随后明确反馈“太抽象了，我严重缺少前置知识”。
- Error type: missing prerequisite
- Fix strategy: 暂停路由表、前缀、动态路由和多路由器链路；先用“一台路由器连接两个房间”建立接口与直连网络模型，只检查目标是否能从对应接口直接交付。
- Progress: 2026-07-20 已能正确判断直连与非直连目标、用简单 `/24` 网络部分识别目标房间、读取路由表将远端目标交给 R2、区分最终目标 IP 与逐跳 MAC、从 R2 视角在直连服务器处结束转发循环，并判断链路断开后旧路由指向的 R2 不再是正确下一跳；只剩一组新地址的完整迁移检查。
- Status: Active

## Topic: AEAD 认证与独立 MAC

- Weakness: 尚未形成“独立 MAC 另算认证码”和“AEAD 同时输出密文与认证 tag”的机制边界。
- Evidence: 在诊断 `TLS_RSA_WITH_AES_128_GCM_SHA256` 的记录认证职责前，学习者主动报告 `missing prerequisite：AEAD 认证与独立 MAC是什么？`。
- Error type: missing prerequisite
- Fix strategy: 暂停密码套件迁移题；先用“独立加密组件 + MAC 组件”对比“GCM 一体化输出密文 + tag”，只检查篡改由谁发现。
- Progress: 2026-07-20 已能识别 AES-GCM 的 authentication tag 负责发现篡改，并明确该 GCM 套件不需要 SHA-256 为每条 record 另算独立 MAC；等待在完整密码套件诊断中说明原因。
- Status: Resolved 2026-07-20；已在完整密码套件诊断中准确区分 GCM 的 AEAD 职责与 SHA-256 的 PRF 职责。

## Topic: 证书链可信与域名匹配

- Weakness: 把“证书链能追溯到本地信任根”过度推广成“该证书可以证明任意网站身份”。
- Evidence: 面对 `bank.example` 收到仅适用于 `evil.example` 的可信链证书时，学习者回答“能；因为是客户端本地信任的根证书”。
- Error type: boundary confusion
- Fix strategy: 使用“谁签的 vs 签给谁的”二问；链验证检查签发来源，hostname matching 检查证书身份是否覆盖当前域名。
- Status: Resolved 2026-07-20；已明确由域名匹配失败阻止合法的其他域名证书冒充目标身份。

## Topic: ECDHE 临时私钥泄漏范围

- Weakness: 把前向保密过度推广成“即使某次会话的临时私钥泄漏，该次会话也无法恢复”，尚未稳定区分同一会话与其他会话的影响范围。
- Evidence: 已知攻击者拥有昨天的 `A1` 和 `b1` 时，只判断今天的 `K2` 无法计算，遗漏昨天的 `K1 = A1^b1` 可以恢复。
- Error type: boundary confusion
- Fix strategy: 使用二格对比：`A1 + b1 -> K1` 可恢复；`A2 + b1` 缺少 `b2`，不能恢复 `K2`。
- Status: Resolved 2026-07-21；已补全 `K1 = A1^b1` 并确认同一会话可恢复、独立的其他会话不受牵连。

## Topic: MSS、MTU 与发送窗口

- Weakness: 缺少 MSS 是“单个 TCP 段最大数据载荷”的前置表示，导致 Nagle 的“缓存达到 MSS”条件无法理解。
- Evidence: 在 Nagle 的 MSS 放行题前，学习者主动询问“什么是 MSS”。
- Error type: missing prerequisite
- Fix strategy: 暂停 Nagle；先用 `MTU = IP 头 + TCP 头 + MSS 数据` 和 `2500 -> 1000 + 1000 + 500` 建立单段上限，再恢复 Nagle。
- Status: Resolved 2026-07-22；已明确 MSS 限制单个 TCP 段的数据载荷，并正确完成 2500 字节的分段。

## Topic: IPv6 无广播边界

- Weakness: 已能正确区分 IPv6 单播、组播和任播，但仍把 IPv4 的广播模型迁移到 IPv6，未稳定记住 IPv6 没有广播地址。
- Evidence: 首次类型映射漏答广播边界；在明确提示“IPv6 没有广播地址”后，仍回答“定义了广播地址”。
- Error type: boundary confusion
- Fix strategy: 固定使用“IPv4 有广播 vs IPv6 无广播；IPv6 一对多用组播”对比，并在次日用一句填空复查。
- Progress: 2026-07-23 第二次针对性修复后，已正确回答 IPv6 无广播、一对多用组播；等待 2026-07-24 无提示复查后再判断是否解决。
- Status: Active
