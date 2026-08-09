# Review Schedule

Use this file for short, targeted spaced-review prompts.

## Template

```markdown
## YYYY-MM-DD

- Topic: <topic>
- Review timing: <same day / next day / 3 days / 1 week>
- Prompt: <one short recall, boundary, transfer, or diagnosis question>
- Target: <schema or weak boundary being tested>
- Result: <correct / partial / incorrect>
- Next review: <date>
```

## Default Intervals

- New weak concept: same day.
- Missed again: next day.
- Correct after repair: 3 days.
- Stable: 1 week.

## 2026-07-17

- Topic: HTTP 定义三问拆解
- Review timing: same day
- Prompt: HTTP 为什么不是“服务器到浏览器”的单向传输？请用请求和响应各说明一个方向。
- Target: 两点之间的双向传输边界
- Result: Correct after repair
- Next review: 2026-07-20

## 2026-07-18

- Topic: 用状态码和 Header 读懂请求-响应合同
- Review timing: 1 week
- Prompt: 给定一条状态行以及 `Content-Type`、`Content-Length`，分别说明处理结果、body 格式和 body 边界。
- Target: 状态码与 Header 的职责映射
- Result: Pending
- Next review: 2026-07-25

## 2026-07-19

- Topic: RFC 规范语义与实际实现
- Review timing: same day
- Prompt: RFC 规定 GET 用于获取资源，但代码把 GET 实现为删除；分别指出规范层和实际行为层。
- Target: 标准规定与具体实现的二层判断
- Result: Correct after prerequisite bridge
- Next review: 2026-07-22

## 2026-07-20

### 路由器接口与直连网络

- Topic: 路由器接口与直连网络
- Review timing: same day
- Prompt: 一台路由器左接口连接局域网 A，右接口连接局域网 B；目标主机在 B。路由器需要下一台路由器，还是能从右接口直接交付？
- Target: 建立“接口连接局域网”和“直连目标”的空间模型
- Result: Correct after prerequisite repair
- Next review: 2026-07-23

### 直连目标与下一跳

- Topic: 直连目标与下一跳
- Review timing: same day
- Prompt: R1 连接房间 A、B，R2 连接房间 B、C，目标在 C。R1 应直接交给目标，还是先交给 R2？
- Target: 先判断目标是否直连，再决定是否需要下一跳
- Result: Correct
- Next review: 2026-07-27

### IP 网络部分与目标房间

- Topic: IP 网络部分与目标房间
- Review timing: same day
- Prompt: R1 接口位于 `192.168.1.0/24` 和 `192.168.2.0/24`；目标改为 `192.168.3.50/24`。它是否与任一接口直连？
- Target: 用简单 `/24` 地址的网络部分识别目标房间
- Result: Correct on direct and non-direct cases
- Next review: 2026-07-27

### 最小路由表目录

- Topic: 最小路由表目录
- Review timing: same day
- Prompt: 路由项写着 `192.168.3.0/24 -> via R2 192.168.2.2, 右接口`。目标为 `192.168.3.50` 时，当前应交给谁、从哪个接口走？
- Target: 区分最终目标 IP、下一跳 IP 和出接口
- Result: Correct
- Next review: 2026-07-27

### 最终目标 IP 与下一跳 MAC

- Topic: 最终目标 IP 与下一跳 MAC
- Review timing: same day
- Prompt: R1 把包交给 R2 时，IP 目标应写服务器还是 R2；以太网目标 MAC 又应属于谁？
- Target: 内层 IP 记录最终目标，外层 MAC 记录当前下一跳
- Result: Correct again on 2026-07-23；准确回答目标 IP 为最终服务器 B、目标 MAC 为当前下一跳 R2
- Next review: 2026-07-30

### IPv4 是接口地址而非整机唯一编号

- Topic: 把 IPv4 读成 32 位接口地址
- Review timing: same day
- Prompt: 一台路由器有两个接口，分别连接 `192.168.1.0/24` 和 `10.0.0.0/8`；它通常至少需要几个 IP，这些 IP 配置在哪里？
- Target: IP 配置在网络接口或逻辑接口；多接口设备通常拥有多个 IP
- Result: Correct；回答至少需要两个 IP，并准确说明分别配置在两个接口上
- Next review: 2026-07-26

### A/B/C 分类号与地址边界

- Topic: 用前导位识别 A/B/C 类地址边界
- Review timing: same day
- Prompt: 一个 IPv4 地址最高三位是 `110`；它属于哪一类，分类号、网络号内容和主机号各占多少位？
- Target: C 类=`110`；分类号 3 位、网络号内容 21 位、主机号 8 位
- Result: Correct；准确回答 C 类、3 位分类号、21 位网络号内容、8 位主机号
- Next review: 2026-07-26

### 网络地址与广播地址

- Topic: 用主机位全 0/全 1 识别网络与广播地址
- Review timing: same day
- Prompt: 在 `192.168.1.0/24` 中，`192.168.1.0` 与 `192.168.1.255` 分别表示什么，能否分给普通主机？
- Target: 主机位全 0 是网络地址；主机位全 1 是广播地址；两者都不分给普通主机
- Result: Correct；准确回答网络本身与网络内所有主机，且两者不能分给普通主机；首个地址少写的 `1` 按笔误处理
- Next review: 2026-07-26

### 本地广播与直接广播

- Topic: 用目标网段区分本地广播与直接广播
- Review timing: same day
- Prompt: `192.168.0.10/24` 向 `192.168.1.255` 发送；这是本地广播还是直接广播，是否必须经过路由器才能到达目标网段？
- Target: 目标广播地址属于另一个网段，因此是直接广播；必须依赖路由器转发，且现实中常被禁用
- Result: Correct；准确判断为直接广播，并指出必须经过路由器才能到达远端 `192.168.1.0/24`；是否实际转发仍取决于路由器策略
- Next review: 2026-07-26

### D 类组播地址不是主机地址

- Topic: 把 D 类地址读成组标识而非主机地址
- Review timing: same day
- Prompt: 主机单播 IP 为 `192.168.1.20`，加入 `239.1.1.1` 组播组后，是否要把自己的 IP 改成 `239.1.1.1`；发送组播时目标 IP 应写什么？
- Target: 主机保留 `192.168.1.20`；`239.1.1.1` 是组标识，只在组播包的目标 IP 中使用
- Result: Correct；主机无需修改自己的单播 IP，发送组播时目标 IP 写 `239.1.1.1`
- Next review: 2026-07-26

### CIDR 前缀与网络地址

- Topic: 用 CIDR 前缀与掩码 AND 求网络地址
- Review timing: same day
- Prompt: 对 `192.168.7.83/24`，网络位和主机位各多少位，掩码是什么，网络地址是什么？
- Target: 24 位网络、8 位主机；`255.255.255.0`；`192.168.7.0`
- Result: Correct；准确回答 24 位网络、8 位主机、掩码 `255.255.255.0`、网络地址 `192.168.7.0`；多出的问号按输入符号处理
- Next review: 2026-07-26

### `/26` 块大小与子网边界

- Topic: 用块大小定位 `/26` 子网边界
- Review timing: same day
- Prompt: `192.168.1.130/26` 属于哪个子网？写出网络地址、广播地址和可用主机范围。
- Target: 网络 `192.168.1.128`；广播 `192.168.1.191`；可用 `192.168.1.129~192.168.1.190`
- Result: Correct；网络、广播和可用范围全部正确；按源文从 0 编号是子网 2，按从 1 计数是第 3 个子网
- Next review: 2026-07-26

### 公有、私有与环回地址

- Topic: 用作用域区分公有、私有与环回地址
- Review timing: same day
- Prompt: 分别判断 `10.8.0.3`、`172.32.0.1`、`127.0.0.1` 属于私有、公有还是环回地址。
- Target: `10.8.0.3` 私有；`172.32.0.1` 不在 `172.16/12` 内，按本章边界判为公有；`127.0.0.1` 环回
- Result: Correct；三项全部正确，且未将 `172.32.0.1` 误当成 `172.16/12` 私有段
- Next review: 2026-07-26

### 最长前缀匹配

- Topic: 按最长前缀匹配选择最具体路由
- Review timing: same day
- Prompt: 目标 IP 为 `10.1.2.10`，路由表同时有 `0.0.0.0/0`、`10.0.0.0/8`、`10.1.2.0/24`。应选哪一条，为什么？
- Target: 三条均能匹配该目标，但 `/24` 前缀最长、范围最具体；`/0` 只是全部更具体路由都未命中时的兜底
- Result: Correct；选择 `10.1.2.0/24`，并准确说明前缀越长、限定网络越小、路由越具体、优先级越高
- Next review: 2026-07-26

### IPv4 分片的触发、重组与丢片后果

- Topic: 先用 MTU 判断 IPv4 分片，再把重组责任放在目标主机
- Review timing: same day
- Prompt: 一个 `4000` 字节的 IPv4 数据报要经过 MTU 为 `1500` 字节的以太网链路：是否需要分片，谁负责重组，丢失任意一片会怎样？
- Target: 超过 MTU，需要 IPv4 分片；仅最终目标主机重组，中间路由器不重组；丢一片就使整个原始 IP 数据报无法完整重组
- Result: Incomplete；已正确判断需要分片，并指出 IP 不单独重传分片、上层决定恢复；但未明确“中间路由器不重组，最终目标主机重组”，也未说明丢一片使整报文无法完整重组
- Repair prompt: 中间路由器____重组；只有____负责重组；任意一片丢失会导致整个原始 IP 数据报____。
- Repair result: Correct；准确补全“中间路由器不重组 / 最终目标主机重组 / 丢一片则整个原始 IP 数据报无法完整重组”；重复文字按输入重复处理
- Next review: 2026-07-26

### IPv6 十六进制表示与零组压缩

- Topic: 把 IPv6 写成 8 组十六进制，并只用一次 `::` 压缩连续零组
- Review timing: same day
- Prompt: 将 `2001:0db8:0000:0000:0000:0000:0000:0001` 按 IPv6 规则压缩成简写形式。
- Target: `2001:db8::1`；去除每组前导 0，并用一次 `::` 代替最长连续全 0 组
- Result: Partial；`2001:0db8::0001` 合法且已正确压缩连续零组，但未删除 `0db8` 和 `0001` 的组内前导 0
- Repair prompt: `2001:0db8::0001` 再应用“组内前导 0 可删除”，写出最简形式。
- Repair result: Correct；得到最简形式 `2001:db8::1`
- Next review: 2026-07-26

### IPv6 单播、组播、任播与无广播

- Topic: 按通信对象区分 IPv6 单播、组播、任播与无广播
- Review timing: same day
- Prompt: A 只发给一台指定主机；B 发给加入某组的多台主机；C 多个服务节点共享一个地址，由路由送到最近的一个。分别属于什么 IPv6 类型？IPv6 是否还有广播地址？
- Target: A 单播；B 组播；C 任播；IPv6 没有广播地址
- Result: Incomplete；A 单播、B 组播、C 任播全部正确，但漏答 IPv6 无广播地址
- Repair prompt: IPv6 是否定义了广播地址？如果需要一对多通信，本章使用哪种类型？
- Repair answer: “定义了广播地址；组播”
- Repair result: Incorrect on broadcast boundary；组播正确，但 IPv6 并未定义广播地址
- Second repair prompt: IPv6____广播地址；需要一对多时使用____。
- Second repair result: Correct；准确回答 IPv6 没有广播地址，一对多使用组播
- Next review: 2026-07-24

### IPv6 三类单播作用域

- Topic: 按能否越过路由器区分 IPv6 链路本地、唯一本地与全局单播
- Review timing: same day
- Prompt: A 同一链路内通信且不经路由器；B 企业内网通信；C 通过互联网全局可达。分别应选链路本地、唯一本地还是全局单播？写出 A、B 的前缀。
- Target: A 链路本地 `FE80::/10`；B 唯一本地 `FC00::/7`；C 全局单播
- Result: Correct；三种作用域映射及 A、B 前缀全部正确；“其他”符合源图对全局单播范围的归类
- Next review: 2026-07-26

### IPv6 固定基础首部与逐跳简化

- Topic: 用固定 40 字节基础首部简化 IPv6 逐跳处理
- Review timing: same day
- Prompt: 基础首部固定____字节；____首部校验和；选项由____指向扩展首部；中间路由器____IPv6 分片。
- Target: `40`；取消；`Next Header`；不做
- Result: Correct；四个边界全部正确，并明确使用“没有首部校验和”与“中间路由器不做 IPv6 分片”表述
- Next review: 2026-07-26

### IPv6 设计亮点与部署保证

- Topic: 把 IPv6 设计亮点与实际部署保证分层判断
- Review timing: same day
- Prompt: 判断并修正：A 无 DHCP 时 IPv6 主机一定无法获得地址；B IPv4 与 IPv6 可以直接互相理解；C 使用 IPv6 就会使所有流量自动加密并安全。
- Target: 三句均错；IPv6 支持自动配置，无 DHCP 也可获得地址；IPv4/IPv6 不直接兼容，需升级或过渡机制；IPv6 支持安全能力，但不等于所有流量自动加密或配置安全
- Result: Correct；三条边界全部准确，明确自动配置、不直接兼容与安全能力需实际部署的差异；`Pv6` 按输入遗漏处理
- Next review: 2026-07-26

### DNS 本地缓存链短路

- Topic: 用本地缓存链短路 DNS 查询
- Review timing: same day
- Prompt: 浏览器缓存未命中，但操作系统缓存已命中。后续是否还检查 `hosts`、请求本地 DNS 或访问根 DNS？查询在哪里结束？
- Target: 操作系统缓存直接返回；不再检查 `hosts`，也不请求本地 DNS 或根 DNS
- Result: Correct；准确回答不执行任何后续检查，查询在操作系统缓存命中处结束
- Next review: 2026-07-30

### DNS 根、TLD 与权威逐级问路

- Topic: 把 DNS 根和 TLD 读成指路者，把权威 DNS 读成最终答案源
- Review timing: same day
- Prompt: 补全 `www.server.com` 的问路链：客户端先问____ DNS；本地 DNS 问根 DNS，根 DNS 返回____的地址；该服务器再指向____ DNS；最后由____ DNS 返回最终 IP。
- Target: 本地 DNS；`.com` 顶级域 DNS；`server.com` 权威 DNS；权威 DNS
- Result: Correct；问路主体、根/TLD 指路和权威 DNS 返回最终 IP 的边界全部正确
- Next review: 2026-07-30

### ARP 下一跳 MAC 查询

- Topic: 路由先选下一跳 IP，ARP 再补当前链路的 MAC
- Review timing: same day
- Prompt: 主机 A 向远端 B 发包，当前下一跳为网关 R1。ARP 询问 R1 还是 B 的 MAC？请求在何处广播，谁响应，是否跨路由器？
- Target: 询问 R1 的 MAC；在 A 的当前链路广播；持有 R1 目标 IP 的接口响应；ARP 广播不跨路由器
- Result: Correct；查询对象、广播范围、响应者和不跨路由器四个边界全部正确
- Next review: 2026-07-30

### ARP/RARP 映射方向与缓存期限

- Topic: 用已知量方向区分 ARP 与 RARP，并记住 ARP 缓存有期限
- Review timing: same day
- Prompt: ARP 是已知____求____；RARP 是已知____求____，由预先登记映射的服务器回答；ARP 缓存是否永久有效？
- Target: IP；MAC；MAC；IP；ARP 缓存有期限，超期清除或刷新
- Result: Correct；ARP/RARP 方向与缓存有效期全部正确
- Next review: 2026-07-26

### DHCP DORA 顺序与 Discover 地址

- Topic: 用 DORA 四步把无地址客户端变成有租约主机
- Review timing: same day
- Prompt: 新主机没有 IP，写出 DORA 四个报文顺序，并写 Discover 的源 IP:端口和目标 IP:端口。
- Target: `Discover -> Offer -> Request -> ACK`；`0.0.0.0:68 -> 255.255.255.255:67`
- Result: Correct；DORA 顺序、步骤含义和 Discover 源/目标地址及端口全部正确
- Next review: 2026-07-26

### DHCP Offer 内容与租约续期

- Topic: 把 DHCP 配置读成有期限租约，并用 ACK/NACK 判断续租结果
- Review timing: same day
- Prompt: Offer 携带哪五类核心配置？租约快过期时客户端发什么？服务器回 ACK/NACK 后客户端如何处理旧地址？
- Target: 可租 IP、子网掩码、默认网关、DNS、租期；发 Request；ACK 延长租期，NACK 停止使用旧地址
- Result: Correct；Offer 五项配置、Request 续租、ACK/NACK 两条生命周期分支全部正确
- Next review: 2026-07-26

### DHCP 中继跨广播域

- Topic: 用 DHCP 中继把客户端广播桥接到远端服务器
- Review timing: same day
- Prompt: 客户端与 DHCP 服务器不同网段；为何么客户端广播不能直达？中继收到广播后如何送给服务器，服务器回复后如何交给客户端？
- Target: 路由器默认不转发本地广播；中继收广播后单播给远端服务器；服务器回复中继，中继再在客户端链路广播/交付
- Result: Correct；能说明本地广播不跨路由器、中继向远端服务器发单播、服务器回复后由中继在客户端链路交付
- Next review: 2026-07-30

### NAPT 地址端口映射

- Topic: 用地址加端口映射理解 NAPT 共享公网 IP
- Review timing: same day
- Prompt: A 和 B 都用本地端口 1025 访问同一个 Web 服务器；若都转换成同一公网 IP，NAPT 至少还必须怎样改写，才能让回包不混淆？
- Target: 为两个连接分配不同的公网侧端口，并保存公网地址端口到私网地址端口的映射；回包按转换表还原
- Result: Correct；能说明不同公网侧端口用于区分连接，NAPT 保存转换表并在回包时恢复原私网 IP 和端口
- Next review: 2026-07-30

### NAPT 有状态转换的代价

- Topic: 从转换表状态推导 NAPT 的三个代价
- Review timing: same day
- Prompt: 一条经过 NAPT 的 TCP 连接仍在通信时，NAT 路由器突然重启。连接通常还能继续吗？为什么？
- Target: 不能正常继续；重启导致转换表丢失，回包失去对应的私网 IP/端口映射，已有 TCP 连接通常中断或重置
- Result: Correct；能从路由器重启导致转换表丢失，推导回包无法还原、既有 TCP 连接通常中断或重置
- Next review: 2026-07-30

### NAPT 无映射时的外部主动入站

- Topic: 从转换表状态推导 NAPT 的三个代价
- Review timing: same day
- Prompt: 公网主机知道 NAT 路由器的公网 IP，但没有既存或静态映射，能否直接主动连接某台指定内网服务器？为什么？
- Target: 通常不能；没有转换记录时，NAT 不知道公网地址端口应映射到哪台内网主机的哪个端口
- Result: Correct；能说明没有映射时 NAPT 无法判断公网流量对应哪台内网服务器
- Next review: 2026-07-30

### NAT 穿透与地址转换边界

- Topic: 用主动协调映射理解 NAT 穿透
- Review timing: same day
- Prompt: NAT 后的应用若希望公网对端联系自己，需要主动完成哪两件关键事？建立映射后 NAT 是否完全不再转换数据包？
- Target: 发现/获得公网侧地址端口并协调建立端口映射；不会，后续数据仍由 NAT 按该映射转换和转发
- Result: Correct；能说明应用主动获得公网侧 IP 和端口、协调建立映射，并指出建立后 NAT 仍按映射转换地址和端口
- Next review: 2026-07-30

### ICMP 反馈与 IP 承载

- Topic: 把 ICMP 当成 IP 的反馈与诊断通道
- Review timing: same day
- Prompt: 主机 A 发往 B 的 IP 包在 R2 处无法继续交付。谁生成什么 ICMP 报文？它如何到达 A？ICMP 会替 A 重传原包吗？
- Target: R2 生成 ICMP 目标不可达；ICMP 由 IP 承载并按普通路由返回 A；只报告失败原因，不负责重传原始包
- Result: Correct after narrow repair；已明确 R2 是发现交付失败并生成 ICMP 目标不可达的节点，同时保持 IP 返回和不重传边界
- Next review: 2026-07-30

### ICMP 查询报文与差错报文

- Topic: 把 ICMP 当成 IP 的反馈与诊断通道
- Review timing: same day
- Prompt: Echo Request、Echo Reply 和 Destination Unreachable 分别属于查询报文还是差错报文？
- Target: Echo Request/Reply 属于查询报文；Destination Unreachable 属于差错报文
- Result: Correct；Echo Request/Reply 归为查询报文，Destination Unreachable 归为差错报文
- Next review: 2026-07-30

### IGMP 工作边界与主机 IP

- Topic: 用 IGMP 管理本链路组员而不改主机 IP
- Review timing: same day
- Prompt: 主机加入 239.1.1.1 时，IGMP 工作在哪两个对象之间？主机单播 IP 是否改变？TTL 为什么通常为 1？
- Target: 成员主机与最后一跳路由器之间；单播 IP 不变；IGMP 只在本链路交换，所以 TTL 通常为 1
- Result: Correct；能说明 IGMP 的成员主机到最后一跳路由器边界、主机单播 IP 不变和 TTL=1 的原因
- Next review: 2026-07-30

### IGMP 常规查询与报告抑制

- Topic: 用随机延时和报告抑制压缩 IGMP 响应
- Review timing: same day
- Prompt: H1/H3 同组，收到发往 224.0.0.1 的查询后计时器分别为 2 秒/7 秒。谁报告？另一个怎样做？为什么路由器不会误判无人？
- Target: H1 先向组地址报告；H3 听到同组报告后抑制发送；一个报告已足以证明该链路至少还有一名组员
- Result: Correct；H1 先报告，H3 听到同组报告后抑制自己的报告；路由器已由一份报告确认本链路仍有成员
- Next review: 2026-07-30

### IGMPv2 离组与特定组查询

- Topic: 用特定组查询判断离组后链路是否仍有成员
- Review timing: same day
- Prompt: H1 离开 224.1.1.1 时离组报文发往哪里？路由器随后做什么？H3 响应与无人响应时分别如何处理转发？
- Target: 发往 224.0.0.2；路由器以 1 秒间隔发送 2 次特定组查询；H3 响应则继续转发，无响应超时后停止
- Result: Correct after narrow repair；补全 224.0.0.2、1 秒间隔和 2 次查询，并保留有响应继续、无响应停止两条分支
- Next review: 2026-07-30

### IP 章末综合迁移

- Topic: 从新主机入网追踪到跨网访问与失败反馈
- Review timing: same day
- Prompt: 新笔记本接入 192.168.1.0/24 后访问解析为 183.232.231.172 的域名；按 DHCP、DNS、CIDR/网关、ARP/MAC、路由、NAPT、ICMP 追踪全过程。
- Target: DHCP 给配置；DNS 给最终 IP；CIDR 判异网选网关；ARP 求网关 MAC；首帧目标 IP 是服务器、目标 MAC 是网关；逐跳路由；NAPT 改写私网 IP/端口；R2 不可达时以 IP 返回 ICMP 且不重传
- Result: Pending
- Next review: TBD

### R2 重复转发循环

- Topic: R2 重复转发循环
- Review timing: same day
- Prompt: R2 发现目标服务器位于自己的直连网段后，新以太网帧的目标 MAC 应属于谁？
- Target: 拆旧帧、保留目标 IP、查本地路由、为直连服务器制作新帧
- Result: Correct
- Next review: 2026-07-27

### 下一跳正确性的条件

- Topic: 下一跳正确性的条件
- Review timing: same day
- Prompt: R2 到目标网段的连接已断，但 R1 的路由表还指向 R2。R2 此时还是正确下一跳吗？为什么？
- Target: 下一跳正确依赖路由表与当前真实网络连接一致；过期或错误路由可能选错下一跳
- Result: Correct
- Next review: 2026-07-27

### 两跳转发迁移

- Topic: 两跳转发中的目标 IP 与目标 MAC
- Review timing: same day
- Prompt: 在一组新地址中，分别写出 R1 发往 R2 和 R2 发往直连服务器时的目标 IP 与目标 MAC
- Target: 两跳的目标 IP 都是最终服务器；目标 MAC 依次是 R2 和服务器
- Result: Deferred by topic switch
- Next review: When routing review resumes

### 交换机按目标 MAC 选择端口

- Topic: 交换机的已知单播转发
- Review timing: same day
- Prompt: 帧从端口 1 进入，目标是 `MAC_R1`，表中 `MAC_R1 → 端口 3`；交换机查看什么并从哪里发出？
- Target: 查看帧的目标 MAC，并从端口 3 原样转发
- Result: Correct
- Next review: 2026-07-27

### 交换机用源 MAC 学习端口

- Topic: 交换机 MAC 地址表学习
- Review timing: same day
- Prompt: 源 MAC 为 `MAC_A` 的帧从端口 1 进入，交换机应学习哪条记录？
- Target: `MAC_A → 端口 1`
- Result: Correct
- Next review: 2026-07-27

### HTTP GET/POST 规范语义与实际实现

- Topic: GET/POST 先看 RFC 语义，再看实际实现
- Review timing: 1 week
- Prompt: 一个 GET 接口每调用一次都新增资源；分别按 RFC 语义和实际实现判断安全性、幂等性。
- Target: 方法规范语义与接口真实行为的分层判断
- Result: Pending
- Next review: 2026-07-27

### HTTP 强制缓存与协商缓存

- Topic: HTTP 缓存先强制缓存，后协商缓存
- Review timing: 1 week
- Prompt: 给定缓存未过期、过期后 ETag 相同、过期后 ETag 不同三种情况，说明是否发请求以及返回 304 或 200 的分支。
- Target: 强制缓存优先及协商缓存结果
- Result: Correct on conditional-validation transfer
- Next review: 2026-07-27

### HTTP 无状态与 Cookie

- Topic: HTTP/1.1 无状态与 Cookie 补状态
- Review timing: 1 week
- Prompt: 说明 HTTP 无状态的一个好处、一个连续业务问题，以及 Cookie 如何把请求关联到用户或 Session。
- Target: 无状态双刃剑与 Cookie 补偿机制
- Result: Pending
- Next review: 2026-07-27

### HTTP 明文的三类安全风险

- Topic: HTTP 明文的可调试性与三类安全风险
- Review timing: 1 week
- Prompt: 分别给出一个窃听、篡改、冒充场景，并指出对应的机密性、完整性、身份认证问题。
- Target: 三类安全风险边界
- Result: Pending
- Next review: 2026-07-27

### HTTP/1.1 长连接与响应队头阻塞

- Topic: HTTP/1.1 长连接、管道化与响应队头阻塞
- Review timing: 1 week
- Prompt: A、B 两个请求被管道化发送，A 慢而 B 快；解释为什么 B 仍需等待，以及长连接实际节省了什么。
- Target: 建连成本与响应队头阻塞的边界
- Result: Pending
- Next review: 2026-07-27

### HTTPS 三类风险与机制映射

- Topic: HTTPS 三类风险、三个安全目标与三类机制
- Review timing: 1 week
- Prompt: 将窃听、篡改、冒充分别映射到机密性、完整性、身份认证以及对应的 HTTPS 机制。
- Target: HTTPS 安全目标完整映射
- Result: Pending
- Next review: 2026-07-27

### TLS RSA 握手与会话密钥

- Topic: TLS 握手验证服务端并协商会话密钥
- Review timing: 1 week
- Prompt: 说明 RSA 握手中 pre-master key 的公钥加密、私钥解密方向，以及后续切换对称加密的原因。
- Target: 非对称密钥交换与对称数据加密的职责边界
- Result: Pending
- Next review: 2026-07-27

### HTTPS 证书信任链

- Topic: 证书信任链证明服务端公钥归属
- Review timing: same day
- Prompt: 访问 `bank.example` 时收到一张链到可信根、但只适用于 `evil.example` 的证书；分别判断证书链与域名匹配结果。
- Target: 可信签发链与目标域名身份匹配的独立边界
- Result: Correct after targeted repair
- Next review: 2026-07-23

### HTTPS 客户端信任边界

- Topic: HTTPS 可靠性依赖客户端正确验证证书
- Review timing: 1 week
- Prompt: 解释抓包工具导入根证书后为何能签发被浏览器接受的站点证书，以及这为何不是 TLS 算法被破解。
- Target: 受信任根证书注入与两条 TLS 连接
- Result: Pending
- Next review: 2026-07-27

### HTTP/2 TCP 层队头阻塞

- Topic: HTTP/2 多路复用仍受 TCP 层队头阻塞
- Review timing: 1 week
- Prompt: 两个 HTTP/2 Stream 复用一个 TCP 连接，其中一个早期 TCP 包丢失；解释为什么另一个 Stream 也会等待。
- Target: HTTP/2 应用层多路复用与 TCP 连续字节流的边界
- Result: Pending
- Next review: 2026-07-27

### HTTP/3 QUIC 独立 Stream

- Topic: HTTP/3 用 QUIC 独立 Stream 缓解队头阻塞
- Review timing: 1 week
- Prompt: QUIC Stream 1 丢包而 Stream 3 完整到达时，说明谁需要等待，并解释 HTTP/3 为什么不是裸 UDP。
- Target: QUIC 独立 Stream 与可靠传输层
- Result: Pending
- Next review: 2026-07-27

### HTTP/1.1 三轴优化分类

- Topic: 用“不发、少发、少传”定位 HTTP/1.1 优化
- Review timing: same day
- Prompt: 将新鲜缓存命中、20 个图标合成一张 Sprite、Brotli 压缩 JS 分别归入“不发、少发、少传”。
- Target: 先识别被减少的网络成本，再选择具体优化技术
- Result: Correct on first attempt
- Next review: 2026-07-27

### HTTP/1.1 代理内部改写重定向

- Topic: 把客户端重定向改成代理内部改写
- Review timing: same day
- Prompt: 比较“客户端收到 302 后再次请求”和“代理内部把 url1 改写为 url2”，指出减少的是哪段请求以及哪段请求仍可能存在。
- Target: 客户端可见往返与代理到源站访问的边界
- Result: Correct after boundary repair
- Next review: 2026-07-23

### HTTP/1.1 资源合并的收益与失效半径

- Topic: 用“请求收益 vs 失效半径”评估资源合并
- Review timing: same day
- Prompt: 10 个独立 JS 合并成一个 bundle 后，仅 1 个模块变化；说明请求收益和缓存更新代价。
- Target: 请求次数下降与整体 bundle 失效的双边判断
- Result: Correct on first attempt
- Next review: 2026-07-27

### HTTP/1.1 延迟请求与最终总请求数

- Topic: 把延迟请求看成首屏调度而非永久删除
- Review timing: same day
- Prompt: 首屏外 20 张图使用懒加载；分别判断用户不滚动和滚到底部时，首屏请求数与最终总请求数如何变化。
- Target: 首屏关键路径优化与永久减少请求的边界
- Result: Correct after numeric completion; full-scroll total is 23
- Next review: 2026-07-27

### HTTP/1.1 无损与有损压缩选择

- Topic: 按“是否必须完全恢复”选择无损或有损压缩
- Review timing: same day
- Prompt: 分别为 JavaScript 文件和商品照片选择无损或有损压缩，并用可恢复性要求解释。
- Target: 数据完整恢复要求与媒体质量交换的边界
- Result: Correct on first attempt with recoverability explanation
- Next review: 2026-07-27

### HTTP/1.1 内容编码协商方向

- Topic: 把 Accept-Encoding 与 Content-Encoding 读成能力清单和实际选择
- Review timing: same day
- Prompt: 客户端接受 gzip 和 br，服务器实际选择 br；分别写出请求字段和响应字段。
- Target: 客户端能力清单与服务器实际编码的方向
- Result: Correct on first attempt
- Next review: 2026-07-27

### HTTP/1.1 优化综合选择

- Topic: 按瓶颈组合“不发、少发、少传”并核算代价
- Review timing: same day
- Prompt: 为重复静态资源、多个小图标、首屏外大图和大体积 JavaScript 分别选择优化，并指出至少一项代价。
- Target: 从瓶颈到机制再到边界的完整选择链
- Result: Pending
- Next review: After learner attempt

### TLS 握手消息、record 与 TCP 分段边界

- Topic: 分清 Handshake message、TLS record、TCP segment 和 flight
- Review timing: same day
- Prompt: 同一个 TCP segment 中出现 `ServerHello`、`Certificate`、`ServerHelloDone`；分别判断有几个 handshake messages、可能有几个 TLS records，以及为什么不能由此固定推断 TCP 包数。
- Target: 协议语义、TLS 承载单位与 TCP 传输分段的分层计数
- Result: Correct after targeted repair
- Next review: 2026-07-23

### TLS 1.2 RSA-GCM 密码套件职责

- Topic: 从密码套件拆出密钥交换、身份认证和数据保护
- Review timing: same day
- Prompt: 将 `TLS_RSA_WITH_AES_128_GCM_SHA256` 中的 RSA、AES-128-GCM、SHA-256 分别映射到秘密建立、记录层保护与 PRF Hash。
- Target: 非对称密钥交换、AEAD 数据保护和密钥派生 Hash 的职责边界
- Result: Correct after prerequisite repair and near-transfer diagnosis
- Next review: 2026-07-23

### TLS AEAD 认证与独立 MAC

- Topic: 区分 AEAD 认证与独立 MAC
- Review timing: same day
- Prompt: AES-GCM record 的密文被改动时，接收方检查什么发现篡改？说明为什么这里不再需要独立 HMAC。
- Target: AEAD tag 与独立 MAC 的职责边界
- Result: Correct after prerequisite repair and near-transfer diagnosis
- Next review: 2026-07-23

### TLS 三个输入与记录层密钥派生

- Topic: 用三个输入逐级派生记录层密钥
- Review timing: same day
- Prompt: 监听者知道两个 Hello random，但不知道 `pre_master_secret`；判断他能否推导 `master_secret`，并说明公开与秘密输入的边界。
- Target: `ClientRandom + ServerRandom + pre_master_secret -> master_secret -> key_block`
- Result: Public-vs-secret input boundary correct; directional key_block expansion pending
- Next review: After learner attempt

### TLS DH 公开信道共享秘密

- Topic: 让双方在公开信道上算出同一秘密
- Review timing: same day
- Prompt: DH 双方没有发送最终共享秘密 `K`；分别说明客户端和服务端各用哪一个本地私有值、哪一个对方公开值计算 `K`。
- Target: 公开值交换与本地共享秘密计算的边界
- Result: Correct after targeted repair; only `A、B` are exchanged and `K` is computed locally without transmission
- Next review: 2026-07-24

### TLS 临时私钥与前向保密

- Topic: 用每次独立的临时私钥判断前向保密
- Review timing: same day
- Prompt: 攻击者保存旧 ECDHE-RSA 流量后，只获得服务端长期 RSA 证书私钥；说明为什么这不足以恢复旧会话共享秘密。
- Target: 长期身份密钥与单次 ECDHE 临时私钥的生命周期边界
- Result: Correct after targeted repair; `A1 + b1` recovers the compromised session's `K1`, while independently generated private values protect `K2`
- Next review: 2026-07-24

### TLS 从 DH 到 ECDHE 的结构迁移

- Topic: 把 DH 协商骨架映射到椭圆曲线
- Review timing: same day
- Prompt: 已知 `QC = dC G`、`QS = dS G`，分别写出客户端和服务端计算共享点 `Z` 的公式。
- Target: 自己的私有标量乘对方公开点，并得到相同的 `dC dS G`
- Result: Pending
- Next review: After learner attempt

## 2026-07-21

### TCP 连接身份与运行状态

- Topic: 把 TCP 连接看成双向可靠字节流的状态共识
- Review timing: same day
- Prompt: 判断“TCP 连接就是一个四元组”是否准确；分别说明四元组的职责，以及连接还要维护哪些状态。
- Target: 四元组标识连接 vs Socket、序列号、窗口构成连接状态
- Result: Correct on first attempt
- Next review: 2026-07-28

### TCP 字节流与应用消息边界

- Topic: 把 TCP 连接看成双向可靠字节流的状态共识
- Review timing: same day
- Prompt: 客户端一次 `write` 写入 10 个字节；服务端下一次 `read` 是否一定恰好返回这 10 个字节？
- Target: TCP 保证有序字节流，但不保留应用层 write/read 消息边界
- Result: Correct on first attempt
- Next review: 2026-07-28

### TCP 与 UDP 的应用语义选择

- Topic: 根据消息边界、可靠性和时延容忍度选择 TCP 或 UDP
- Review timing: same day
- Prompt: 实时语音中的旧包迟到后已无播放价值；选择 TCP 或 UDP，并从时效性和丢包处理解释。
- Target: 不靠快慢标签，而按迟到数据价值与可靠性需求选择传输协议
- Result: Correct on first attempt
- Next review: 2026-07-28

### TCP 三次握手的双向 ISN 确认

- Topic: 用三次确认双方状态推导 TCP 建连
- Review timing: same day
- Prompt: 第二次握手 `SYN+ACK, seq=y, ack=x+1` 中，`seq` 与 `ack` 分别承担什么任务？
- Target: 服务端发送自己的 ISN，同时确认客户端 SYN 与 client ISN
- Result: Correct on first attempt；第二、第三次握手的确认方向与状态迁移均已稳定
- Next review: 2026-07-28

### TCP 第三次确认否决历史连接

- Topic: 用第三次确认阻止历史连接过早建立
- Review timing: same day
- Prompt: 新连接使用 `seq=100`，却收到 `ack=91`；判断这是当前响应还是旧连接响应，并决定继续 ACK 还是发送 RST。
- Target: 用确认号不匹配识别旧 SYN，并在服务端正式建连前否决历史连接
- Result: Correct on first attempt
- Next review: 2026-07-28

### TCP 三次握手的最小确认次数

- Topic: 用双向 ISN 确认解释三次是最小次数
- Review timing: same day
- Prompt: 为什么服务端的 ACK 与 SYN 可以合并，而客户端最后确认服务端 SYN 的 ACK 不能删除？
- Target: 四次可以合并为三次；两次无法确认服务端 ISN且会过早建立连接
- Result: Correct on first attempt
- Next review: 2026-07-28

### TCP 用变化 ISN 隔离历史数据

- Topic: 用变化的 ISN 隔离同四元组的新旧连接
- Review timing: same day
- Prompt: 固定 ISN 时，旧连接延迟数据恰好落入新连接接收窗口会怎样？变化 ISN 为什么能降低风险？
- Target: 通过改变新连接序列号空间，降低历史段被新连接误收的概率
- Result: Correct on first attempt
- Next review: 2026-07-28

### TCP MSS 与 IP 分片重传范围

- Topic: 用 MSS 将重传粒度留在 TCP 层
- Review timing: same day
- Prompt: 一个大 TCP 段被 IP 分片后丢一片，与 TCP 先按 MSS 分段后丢一段，分别需要重传什么范围？
- Target: IP 无法单独重传丢失分片；TCP 按 MSS 分段能缩小重传粒度
- Result: Correct on first attempt；2026-07-23 在 IP 章迁移中再次正确选择先按 MSS 分段，并说明丢一段只重传对应的较小 TCP 段
- Next review: 2026-07-30

### TCP 握手丢包的重传责任

- Topic: 先找谁在等确认，判断握手丢包后的重传者
- Review timing: same day
- Prompt: 第三次握手纯 ACK 丢失后，它会不会自行重传？真正由谁重传什么报文？
- Target: 纯 ACK 不单独重传；服务端重传仍在等待确认的 SYN+ACK
- Result: Correct on first attempt；已迁移到第二次握手丢失时的双端重传
- Next review: 2026-07-28

### TCP SYN 队列与 Accept 队列

- Topic: 用 SYN 队列到 Accept 队列定位服务端建连阶段
- Review timing: same day
- Prompt: 服务端收到 SYN 后、收到第三次 ACK 前后，连接分别在哪个队列、处于什么状态？
- Target: SYN_RCVD 半连接在 SYN 队列；ESTABLISHED 已完成连接在 Accept 队列
- Result: Correct after targeted completion；已正确迁移到未调用 accept 仍可完成握手并停留 Accept 队列
- Next review: 2026-07-24

### TCP SYN 攻击占用半连接队列

- Topic: 用半连接资源占用解释 SYN 攻击
- Review timing: same day
- Prompt: 伪造源 IP 的 SYN 从不完成第三次 ACK；它占用哪个队列、停在哪个状态，为什么不能进入另一个队列？
- Target: SYN_RCVD 半连接持续占用 SYN 队列，缺少 ACK 无法进入 Accept 队列
- Result: Correct on first attempt
- Next review: 2026-07-28

### TCP SYN Cookie 无状态验证

- Topic: 用 SYN Cookie 将半连接状态延迟到 ACK 验证后
- Review timing: same day
- Prompt: SYN 队列满时 cookie 放在哪个字段，服务端何时才创建连接并放入 Accept 队列？
- Target: cookie 编码进服务端 ISN；验证第三次 ACK 后才分配已完成连接状态
- Result: Correct on first attempt
- Next review: 2026-07-28

### TCP 四次挥手的双向半关闭

- Topic: 把四次挥手拆成两个方向各自关闭
- Review timing: same day
- Prompt: 主动方发送 FIN 后，被动方为什么先 ACK 而不一定立刻 FIN？此时被动方还能否继续发送数据？
- Target: 一个 FIN 只关闭一个发送方向；被动方处理完剩余数据后再关闭自己的方向
- Result: Correct after state completion；双向半关闭与 LAST_ACK/TIME_WAIT 状态均已稳定
- Next review: 2026-07-24

### TCP 四次挥手丢包的重传责任

- Topic: 用待确认的 FIN 推导四次挥手丢包
- Review timing: same day
- Prompt: 第四次 ACK 丢失后谁重传什么？TIME_WAIT 一端再次收到 FIN 后做什么？
- Target: 被动方重传 FIN；主动方再次 ACK 并重置 2MSL 计时
- Result: Correct on first attempt
- Next review: 2026-07-24

### TCP TIME_WAIT 的可靠收尾

- Topic: 用 TIME_WAIT 保证最后 ACK 可补发
- Review timing: same day
- Prompt: 最后 ACK 丢失且主动方已删除状态时，重传 FIN 到达会收到什么？为什么不是优雅关闭？
- Target: TIME_WAIT 保留状态以再次 ACK；立即关闭只能回 RST，导致被动方看到连接重置
- Result: Correct on first attempt
- Next review: 2026-07-24

### TCP TIME_WAIT 隔离历史报文

- Topic: 用 2MSL 隔离旧连接的延迟报文
- Review timing: same day
- Prompt: 同四元组新连接可能如何误收旧连接延迟段？2MSL 等待解决的是什么时间窗口？
- Target: 让旧连接两个方向的在途报文自然消亡，降低新连接误收历史数据的风险
- Result: Correct on first attempt
- Next review: 2026-07-24

### TCP TIME_WAIT 与 CLOSE_WAIT 诊断

- Topic: 用谁先发 FIN 诊断 TIME_WAIT 与 CLOSE_WAIT
- Review timing: same day
- Prompt: 服务端大量 CLOSE_WAIT 说明它是哪种关闭角色？应优先调 TIME_WAIT 参数还是排查应用 close？
- Target: CLOSE_WAIT 属于被动关闭方；优先排查应用为何未发送本方 FIN
- Result: Correct on first attempt
- Next review: 2026-07-28

### TCP TIME_WAIT 原因与优化边界

- Topic: 先找主动关闭原因，再评估 TIME_WAIT 优化
- Review timing: same day
- Prompt: Nginx 在 keepalive_timeout 到期后产生大量 TIME_WAIT，谁主动发 FIN？先强制 RST 还是先评估长连接超时策略？
- Target: 服务端定时器触发主动关闭；先查连接策略与业务负载，不用牺牲优雅关闭的 RST 盲目跳过 TIME_WAIT
- Result: Correct after repair；已掌握先调整连接策略、再确认资源瓶颈、最后才评估 RST；已修复 RST 是改变本次 close 路径、避免进入 TIME_WAIT，而非删除已有状态
- Next review: 2026-07-25

### TCP close(fd) 的 OS/Network 分层

- Topic: 用 close(fd) 桥接应用、OS 与 TCP 状态机
- Review timing: same day
- Prompt: 应用调用 close(sockfd) 后，谁实际组装并发送 TCP FIN？
- Target: close 是应用进入内核的系统调用接口；内核 Socket/TCP 协议栈执行报文发送与状态迁移
- Result: Correct on first prerequisite check
- Next review: 2026-07-29

### TCP 进程崩溃与主机失联

- Topic: 用“内核还在不在”区分进程崩溃与主机失联
- Review timing: same day
- Prompt: 服务进程崩溃与整机断电，哪一种仍能由本机内核发 FIN？另一种为什么不能？
- Target: 进程崩溃时内核仍在并回收 Socket，可发 FIN；整机断电时内核也不再运行，无法发送关闭报文
- Result: Correct on first attempt
- Next review: 2026-07-29

### TCP keepalive 的三种探测结果

- Topic: 用 TCP keepalive 探测结果区分正常、重启与不可达
- Review timing: same day
- Prompt: 保活探测发往已重启且丢失旧连接状态的主机，与仍处于断电/不可达的主机，分别会得到什么结果？
- Target: 重启主机能回应但无旧连接状态，因此回 RST；断电/不可达时无响应，探测次数耗尽后内核向应用报告连接死亡
- Result: Correct on first attempt
- Next review: 2026-07-29

### HTTP Keep-Alive、TCP keepalive 与应用超时

- Topic: 按“解决什么问题”区分三种近似机制
- Review timing: same day
- Prompt: 将多请求复用一条 TCP、内核探测死连接、Nginx 空闲 60 秒主动关闭，分别映射到 HTTP Keep-Alive、TCP SO_KEEPALIVE、keepalive_timeout。
- Target: HTTP Keep-Alive 负责连接复用；TCP SO_KEEPALIVE 由内核探测死对端；Nginx keepalive_timeout 是应用层空闲回收策略
- Result: Correct on first attempt
- Next review: 2026-07-29

### TCP 监听 Socket 与已连接 Socket

- Topic: 分开监听 Socket 与已连接 Socket 的职责
- Review timing: same day
- Prompt: listenfd=3，accept 返回 connfd=7；读客户端数据应用哪个 fd，另一个继续负责什么？
- Target: 用 connfd=7 与该客户端 read/write；listenfd=3 保持监听并继续接收其他新连接
- Result: Correct on first attempt
- Next review: 2026-07-29

### TCP listen backlog 的现代 Linux 语义

- Topic: 把 backlog 定位为尚未被 accept 领取的已完成连接容量
- Review timing: same day
- Prompt: listen(fd, 128) 且 somaxconn=64；backlog 限制哪个队列，有效上限近似多少，其中连接处于什么状态？
- Target: 现代 Linux 语义下限制 Accept 队列；有效上限近似 min(128,64)=64；队列中是已完成握手、尚未被 accept 的 ESTABLISHED 连接
- Result: Correct on first attempt
- Next review: 2026-07-29

### TCP 无 listen 建连的特殊边界

- Topic: 把“没有 listen 也能建连”限定在主动打开特例
- Review timing: same day
- Prompt: 普通客户端连未监听服务端，与两端同时主动 connect，哪个通常失败，哪个可能在无 listen 时建连？
- Target: 普通未监听端口无被动打开状态，通常拒绝/回 RST；双方同时主动打开是文章的无 listen 特例
- Result: Correct on first attempt
- Next review: 2026-07-29

### TCP 章末综合诊断

- Topic: 用“状态 -> 协议/应用责任 -> 首查入口”完成迁移
- Review timing: same day
- Prompt: 同机同时出现伪造源 SYN_RCVD、CLOSE_WAIT、对齐 keepalive_timeout 的 TIME_WAIT、已断电客户端的 ESTABLISHED；逐项判断责任与首查入口。
- Target: SYN_RCVD -> SYN 队列/第三 ACK 缺失/SYN flood/syncookies；CLOSE_WAIT -> 被动关闭/应用未 close/查代码路径；TIME_WAIT -> 服务端主动超时关闭/先查连接策略；失联 ESTABLISHED -> 无 FIN/开启 TCP keepalive 或应用心跳
- Result: Pending
- Next review: After learner attempt

## 2026-07-22

### TCP 累计 ACK 与快速重传

- Topic: 根据累计 ACK 反馈判断快速重传
- Review timing: same day
- Prompt: `Seq2` 丢失，接收端依次收到 `Seq1、Seq3、Seq4、Seq5`；逐次写 ACK，并指出快速重传在哪个反馈到达时触发。
- Target: `ACK2` 表示下一期待 `Seq2`；原始 ACK 后的 3 个重复 `ACK2` 触发快速重传，不等待 RTO。
- Result: Correct on first attempt；四次反馈均为 `ACK2`，其中后 3 个是重复 ACK，第三个重复 ACK 触发快速重传。
- Next review: 2026-07-29

### TCP SACK 与 D-SACK 边界

- Topic: 用 SACK 定位缺口，用 D-SACK 识别重复接收
- Review timing: same day
- Prompt: 累计 `ACK=5000` 时又报告 `SACK=3500~4000`；判断是普通 SACK 还是 D-SACK，并解释该范围发生了什么。
- Target: 该范围已被累计 ACK 覆盖，再次报告说明它被重复接收，因此是 D-SACK。
- Result: Correct on first attempt；利用累计 ACK 已覆盖该区间，正确判断为 D-SACK，并说明 `3500~4000` 被重复接收。
- Next review: 2026-07-29

### TCP D-SACK 误重传原因诊断

- Topic: 用 D-SACK 结合触发顺序诊断误重传原因
- Review timing: same day
- Prompt: 原始 `1000~1499` 延迟，后续段产生 3 个重复 ACK 并触发快速重传；重传副本先到、原始副本后到，最终出现 D-SACK。判断是 ACK 丢失还是原始数据延迟。
- Target: 触发器是 3 个重复 ACK 而非 RTO，且原始副本后到，因此原始数据只是网络延迟，快速重传造成两个副本都到达。
- Result: Correct after targeted repair；最终明确由快速重传触发，故障在原始数据的前向路径，不是 ACK 返回路径。
- Next review: 2026-07-25

### TCP RTT 波动与动态 RTO

- Topic: 用平滑 RTT 与波动设置动态 RTO
- Review timing: same day
- Prompt: 路径 RTT 多数为 `100ms`、偶尔为 `180ms`；判断固定 `RTO=100ms` 的问题，并指出除 SRTT 外还需跟踪什么。
- Target: 正常抖动会被误判为丢包并触发无谓重传；还要用 `DevRTT` 跟踪波动，为 RTO 留出动态安全余量。
- Result: Correct on first attempt；指出固定 RTO 会误重传未丢数据，并正确补充 `DevRTT` 表示 RTT 波动。
- Next review: 2026-07-29

### TCP 连续超时的指数退避

- Topic: 用指数退避控制连续超时的重传频率
- Review timing: same day
- Prompt: 初始 `RTO=200ms`，连续两次超时；写出后续等待间隔，并解释为什么不能保持固定 200ms。
- Target: 第一次超时后 400ms，再次超时后 800ms；连续超时表明网络状况差，翻倍可避免高频重传进一步加剧拥塞。
- Result: Correct on first attempt；正确推导 `200 -> 400 -> 800ms`，并说明指数退避用于减少拥塞网络中的注入频率。
- Next review: 2026-07-29

### TCP 滑动窗口与累计确认

- Topic: 用滑动窗口与累计确认把停等变成流水线
- Review timing: same day
- Prompt: 窗口内连续发送 3 段，`ACK600` 丢失但后续 `ACK700` 到达；判断 700 前数据是否已确认，以及是否需因 ACK600 丢失而重传。
- Target: `ACK700` 累计确认 700 之前的连续数据，已覆盖 ACK600，因此不需仅因 ACK600 丢失而重传。
- Result: Correct on first attempt；正确说明 `ACK700` 覆盖 700 前连续数据，因此无需仅因 `ACK600` 丢失而重传。
- Next review: 2026-07-29

### TCP 发送窗口四区与三个变量

- Topic: 用三个发送变量定位滑动窗口四区
- Review timing: same day
- Prompt: `SND.UNA=32`、`SND.NXT=46`、`SND.WND=20`；计算未确认字节数、发送窗口右边界和可用窗口。
- Target: 未确认 `46-32=14` 字节；右边界 `32+20=52`；可用窗口 `20-(46-32)=6` 字节。
- Result: Correct on first attempt；正确计算未确认 14 字节、右边界 52 和可用窗口 6 字节，并明确 52 是区 4 的第一个字节。
- Next review: 2026-07-29

### TCP ACK 推动发送窗口右移

- Topic: 区分 ACK 推动 `SND.UNA` 与发送动作推动 `SND.NXT`
- Review timing: same day
- Prompt: `SND.UNA=32`、`SND.NXT=46`、`SND.WND=20`；累计确认 `32~36`，窗口不变且暂不发送新数据，求更新后的 UNA、NXT、右边界和可用窗口。
- Target: `SND.UNA=37`；`SND.NXT=46`；右边界 `37+20=57`；可用窗口 `20-(46-37)=11` 字节。
- Result: Correct on first attempt；正确区分 ACK 只推动 UNA，得到 `UNA=37`、`NXT=46`、右边界 57、可用窗口 11 字节。
- Next review: 2026-07-29

### TCP 接收窗口两个变量

- Topic: 用 `RCV.NXT` 与 `RCV.WND` 定位可接收范围
- Review timing: same day
- Prompt: `RCV.NXT=700`、`RCV.WND=100`；指出下一期待字节、区 4 起点，并判断序号 820 当前能否接收。
- Target: 下一期待 700；可接收范围 `[700,800)`，即 700~799；区 4 起点 800；820 在窗口外，当前不能接收。
- Result: Correct after targeted repair；修正为下一期待 700，并明确 800 是不包含在可接收半开区间内的右边界。
- Next review: 2026-07-25

### TCP 收发窗口只约等于

- Topic: 用 Window 通告时延区分接收端真实窗口与发送端视图
- Review timing: same day
- Prompt: 真实 `RCV.WND` 已从 100 增至 300，但新窗口通告仍在网络中；发送端当前按哪个值限制发送，为什么两端不完全相等？
- Target: 发送端暂按已知的旧通告 100；新的 Window 值到达后才更新，因此发送端持有的是有传播时延的接收窗口快照。
- Result: Correct on first attempt；明确发送端暂按旧值 100，并用窗口通告的传播时延解释两端视图不完全同步。
- Next review: 2026-07-29

### TCP rwnd 反馈接收应用速度

- Topic: 从接收缓冲占用计算 rwnd，并定位流量控制保护对象
- Review timing: same day
- Prompt: 接收缓冲容量 360 字节；收到 140 字节后应用读走 40 字节。求剩余占用和应通告的 rwnd，并说明保护接收端还是网络。
- Target: 未读占用 `140-40=100` 字节；`rwnd=360-100=260` 字节；保护接收端缓冲区，网络拥塞由后续拥塞控制处理。
- Result: Correct on first attempt；正确算出未读占用 100、`rwnd=260`，并明确保护对象是接收端。
- Next review: 2026-07-29

### TCP 未读堆积与零窗口

- Topic: 连续追踪未读占用和 rwnd 收缩
- Review timing: same day
- Prompt: 缓冲容量 360，当前未读 100、rwnd 260；再收 180 且不读，再收 80 且不读，写出两次 rwnd，并说明 rwnd=0 的含义。
- Target: 未读占用依次为 280、360；rwnd 依次为 80、0；零窗口表示接收缓冲已无可用空间，发送端暂停普通数据发送。
- Result: Correct on first attempt；正确给出 `rwnd=80、0`，并说明零窗口表示暂停普通数据发送。
- Next review: 2026-07-29

### TCP 接收缓冲突降与窗口收缩危险

- Topic: 用通告时延诊断操作系统缩小缓冲区造成的丢包
- Review timing: same day
- Prompt: 发送端仍认为可用 220，接收端真实 rwnd 已因缩缓存变成 100，新通告未到；发送 180 后能否全收，为什么，安全缩减顺序是什么？
- Target: 不能；发送端按旧窗口发送 180，但真实窗口只能容纳 100，超出部分/报文会被丢弃；应先收缩并通告窗口，等待发送端停止占用将被回收的空间，再减少缓冲区。
- Result: Correct on first attempt；指出 180 超过真实窗口 100，并正确给出先通告收窗、等待生效、再缩缓冲区的顺序。
- Next review: 2026-07-29

### TCP 持续计时器与窗口探测

- Topic: 打破非零窗口通告丢失造成的双方互等
- Review timing: same day
- Prompt: 发送端收到 rwnd=0 后，接收端后来发送 rwnd=200 的 ACK 但丢失；持续计时器超时后做什么，探测回复为 0 或 200 时分别如何处理？
- Target: 发送窗口探测；接收端在 ACK 中回报当前窗口；若仍为 0，重启持续计时器继续探测；若为 200，更新窗口并恢复普通发送。
- Result: Correct on first attempt；正确说明发送探测、仍为 0 则继续探测、变为 200 则恢复普通发送。
- Next review: 2026-07-29

### TCP 糊涂窗口的接收端抑制

- Topic: 用窗口阈值避免接收端频繁通告极小空间
- Review timing: same day
- Prompt: 接收缓冲 360、MSS 100；可用空间 80 时通告 80 还是 0，增至 120 后能否重新开窗？
- Target: 阈值 `min(100,180)=100`；80 小于阈值，通告 0；120 已达到 MSS，可以重新打开并通告非零窗口。
- Result: Correct on first attempt；正确判断 80 时通告 0，120 达到 MSS 后可重新开窗。
- Next review: 2026-07-29

### TCP Nagle 与 TCP_NODELAY

- Topic: 根据未确认数据和 MSS 决定小数据立即发送还是缓存
- Review timing: same day
- Prompt: MSS 1000、窗口 2000；已有 200 字节未确认，又写入 200 字节。Nagle 下立即发还是缓存，哪些事件可放行，TCP_NODELAY 有何变化？
- Target: 先缓存；收到之前数据的 ACK，或累计数据达到 MSS 且窗口至少允许 MSS 时发送；TCP_NODELAY 关闭 Nagle 的合并等待，使小数据更快发出但增加小包开销。
- Result: Learner reported overloaded before attempting；已拆为单一判断，不计作错误。
- Next review: Same day after prerequisite bridge
- Repair prompt: Nagle 开启，已有一小段数据未确认，又产生一小块不足 MSS 的数据；现在立即发送还是先缓存？
- Repair target: 先缓存，因为当前已有未确认数据。
- Bridge answer: 先放进发送缓存。
- Bridge result: Correct；已掌握第一道状态判断。
- Next prompt: 缓存中有 200 字节，前一批数据的 ACK 已到，但缓存仍不足 MSS；现在发送还是继续等待？
- Next target: 发送；ACK 到达是 Nagle 的一个放行条件，不要求此时缓存必须达到 MSS。
- ACK-release answer: 发送。
- ACK-release result: Correct；已掌握第一个放行条件。
- MSS-release prompt: 前一小段仍未确认，但缓存已达到 MSS，且可用窗口至少为 MSS；发送一个 MSS 还是继续等 ACK？
- MSS-release target: 发送一个 MSS；“缓存达到 MSS 且窗口允许”是无需等待 ACK 的另一条放行路径。
- MSS-release answer: 发送一个 MSS。
- MSS-release result: Correct；已掌握第二个放行条件。
- TCP_NODELAY prompt: SSH 按键只产生几个字节且强调快速回显；保留 Nagle 还是逐 Socket 启用 TCP_NODELAY，收益和代价是什么？
- TCP_NODELAY target: 启用 TCP_NODELAY 关闭 Nagle 等待，降低小消息延迟；代价是更多小包和更高首部/网络开销。
- TCP_NODELAY answer: 在该 Socket 上启用 TCP_NODELAY；小字节更快发出，但承担小包首部开销。
- Result: Correct after overload repair；Nagle Schema 已完成。
- Next review: 2026-07-25

### TCP rwnd 与 cwnd 双约束

- Topic: 从较小窗口定位接收端或网络瓶颈
- Review timing: same day
- Prompt: `rwnd=12 MSS`、`cwnd=4 MSS`；求 `swnd`，判断当前瓶颈，并说明两个窗口分别保护谁。
- Target: `swnd=min(12,4)=4 MSS`；cwnd 更小，当前主要受网络承载能力限制；rwnd 保护接收端缓冲，cwnd 保护共享网络。
- Result: Correct on first attempt；正确计算 4 MSS、定位网络瓶颈，并区分两个窗口的保护对象。
- Next review: 2026-07-29

### TCP 慢启动的指数增长

- Topic: 从每个 ACK 增 1 MSS 推导每 RTT 近似翻倍
- Review timing: same day
- Prompt: 教学例初始 cwnd=1 MSS，每个 ACK 增 1 MSS；前三轮 ACK 全回后 cwnd 依次是多少，属于线性还是指数增长？
- Target: `1 -> 2 -> 4 -> 8 MSS`；每轮可发送并确认的段数也随 cwnd 增加，所以按 RTT 观察近似翻倍，是指数增长。
- Result: Correct on first attempt；正确给出 `2、4、8 MSS`，并判断为指数增长。
- Next review: 2026-07-29

### TCP ssthresh 与拥塞避免

- Topic: 达到慢启动门限后切换为线性增长
- Review timing: same day
- Prompt: `ssthresh=8 MSS`、`cwnd=8 MSS`；选择算法，并计算 8 个 ACK 每个增 `1/8 MSS` 后的 cwnd，说明是否停止增长。
- Target: 因 `cwnd>=ssthresh`，进入拥塞避免；8 个 ACK 合计增加 1 MSS，cwnd 约为 9 MSS；增长没有停止，只是变成线性。
- Result: Correct on first attempt；正确选择拥塞避免，算出一轮后约为 `9 MSS`，并说明增长未停止。
- Next review: 2026-07-29

### TCP RTO 超时后的保守降窗

- Topic: 用超时信号重设 ssthresh 与 cwnd
- Review timing: same day
- Prompt: 按文章教学模型，超时前 `cwnd=16 MSS`；RTO 超时后的 `ssthresh`、`cwnd` 各是多少，下一阶段是什么？
- Target: `ssthresh=8 MSS`，`cwnd=1 MSS`，然后重新慢启动。
- Result: Correct on first attempt；正确算出 `ssthresh=8 MSS`、`cwnd=1 MSS`，并判断重新进入慢启动。
- Next review: 2026-07-29

### TCP 快速重传后的温和降窗

- Topic: 用三个重复 ACK 区分快速恢复与超时重启
- Review timing: same day
- Prompt: 快速重传前 `cwnd=16 MSS`；三个重复 ACK 到达后，进入快速恢复前的 `cwnd`、`ssthresh` 各是多少，下一阶段是什么？
- Target: `cwnd=8 MSS`，再令 `ssthresh=8 MSS`；接下来进入快速恢复，而非重新慢启动。
- Result: Correct on first attempt；正确算出 `cwnd=8 MSS`、`ssthresh=8 MSS`，并选择进入快速恢复。
- Next review: 2026-07-29

### TCP 快速恢复的临时窗口膨胀

- Topic: 用重复 ACK 记账快速恢复中的已离网数据
- Review timing: same day
- Prompt: 进入快速恢复前 `ssthresh=8 MSS`；先根据 3 个重复 ACK 计算入口 cwnd，再计算后续又来 2 个重复 ACK 后的 cwnd。
- Target: 入口 `cwnd=ssthresh+3=11 MSS`；后续每个重复 ACK 加 1，两个后为 `13 MSS`。
- Result: Correct on first attempt；正确算出入口 `cwnd=11 MSS`，随后两个重复 ACK 后为 `13 MSS`。
- Next review: 2026-07-29

### TCP 用新 ACK 退出快速恢复

- Topic: 撤销临时窗口膨胀并回到拥塞避免
- Review timing: same day
- Prompt: 快速恢复中 `ssthresh=8 MSS`、临时 `cwnd=13 MSS`；收到确认新数据的 ACK 后，cwnd 变为多少，后续进入哪个阶段？
- Target: 新 ACK 说明缺口已修复，将 `cwnd=ssthresh=8 MSS`，退出快速恢复并进入拥塞避免。
- Result: Correct on first attempt；正确将 `cwnd` 还原为 `8 MSS`，并选择进入拥塞避免。
- Next review: 2026-07-29

### TCP 读者问答：为何是 1/cwnd

- Topic: 用每 RTT 总增量排除 1/ssthresh
- Review timing: same day
- Prompt: `ssthresh=8 MSS`、`cwnd=16 MSS`，一轮约 16 个 ACK；分别用每 ACK 加 `1/16` 和 `1/8 MSS` 计算一轮总增量，判断哪个符合线性增长。
- Target: `16×1/16=1 MSS`，符合每 RTT 约加 1；`16×1/8=2 MSS`，且 cwnd 继续变大时总增量还会增大，所以分母应为 cwnd。
- Result: Correct on first attempt；正确算出 `1 MSS` 与 `2 MSS`，并说明 ssthresh 是阶段门限而非当前 ACK 数量的基准。
- Next review: 2026-07-29

### TCP 读者问答：快速重传降窗顺序

- Topic: 先减半 cwnd，再把结果存入 ssthresh
- Review timing: same day
- Prompt: 旧 `cwnd=16 MSS`；分别推演正确顺序和反向顺序得到的 cwnd/ssthresh，再判断新 ACK 结束恢复时的 cwnd。
- Target: 正确顺序得 `cwnd=8、ssthresh=8 MSS`；反向顺序得 `ssthresh=16、cwnd=8 MSS`，新 ACK 又会将 cwnd 设回 16，使拥塞降窗效果被撤销。
- Result: Partial；正确识别反向顺序会使 cwnd 弹回 16，但将正确顺序两行执行完时的 cwnd 与快速恢复入口 `ssthresh+3=11` 混淆。
- Repair prompt: 只执行 `cwnd=cwnd/2` 和 `ssthresh=cwnd`，不执行 `+3`；旧 cwnd=16 时两者各是多少？
- Next review: Same day after repair

### TCP MSS 前置桥接

- Topic: 区分 MSS、MTU 与发送窗口
- Review timing: same day
- Prompt: MSS=1000，应用有 2500 字节；MSS 限制整条连接还是单个 TCP 段，这些数据怎样分段？
- Target: MSS 限制单个 TCP 段的数据载荷，不限制连接总量；可分为 1000、1000、500 字节三个 TCP 数据段。
- Result: Correct after prerequisite bridge；正确说明单段上限，并给出 `1000、1000、500` 三段。
- Next review: 2026-07-25

### HTTP/2 从外部优化上限识别协议重设计

- Topic: 区分 HTTP/1.1 外部优化与 HTTP/2 协议内生改造
- Review timing: same day
- Prompt: 页面已把 100 张小图合成一张 Sprite，但同一 HTTP/1.1 连接里一个慢响应仍挡住后续响应；继续合图能否解决，为什么？
- Target: 不能；合图减少请求次数，却不改变同一连接的响应顺序约束。要消除这一层阻塞，需要 HTTP/2 用 Stream 和 Frame 改变并发传输模型。
- Result: Pending
- Next review: After learner answer
