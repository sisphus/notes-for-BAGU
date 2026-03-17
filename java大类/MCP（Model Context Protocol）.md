### 面试浓缩包：MCP - 模型上下文协议】



“MCP，全称是 **Model Context Protocol (模型上下文协议)**。

它是一个由 Anthropic (Claude的开发商) 牵头并开源的**行业标准**，旨在解决一个核心问题：**如何让 AI 大模型（LLMs）安全、标准地与外部世界（如工具、数据库、API）进行交互。**

您可以把它理解为**“AI 的 USB-C 接口”**。

------



#### 1. 为什么需要 MCP？（它解决了什么痛点？）



- **没有 MCP 之前：** 如果我想让一个 AI 助手连接到我的“企业数据库”或“Gmail API”，我需要为这个 AI 专门写一个定制的“胶水代码”（Custom Integration）。如果我换一个 AI 模型，我就得重写一遍。这导致了严重的**生态碎片化**。
- **有了 MCP 之后：** 我只需要让我的“企业数据库”提供一个标准的 **MCP 服务端 (Server)**。任何支持 MCP 协议的 AI 应用（如 IDE、聊天机器人）都可以作为**客户端 (Client)** 自动发现并使用它。**一次开发，处处可用。**

#### 2. 它的核心架构（Client / Server）

MCP 的架构非常清晰，它借鉴了“语言服务器协议”(LSP) 的思想，分为“客户端”和“服务端”：

- **MCP 服务端 (Server)：**
  - 这就是你的“工具”或“数据源”。
  - 例如：一个“GitHub 服务端”会暴露“拉取代码”、“提交pr”等功能。一个“数据库服务端”会暴露“执行SQL查询”功能。
- **MCP 客户端 (Client)：**
  - 这就是你的“AI 应用”或“Agent”。
  - 例如：你的 IDE、一个聊天窗口、或一个 AI 智能体。
- **通信：** 客户端（AI）通过标准的 MCP 协议（通常是 HTTP 或 stdio）向服务端（工具）发起请求。



#### 3. 核心功能（“原语” Primitives）

面试官可能会问：“AI 通过 MCP 能获取什么？” 答案是三种“原语”(Primitives)：

1. **工具 (Tools)：**
   - 这是**最核心**的。它允许 AI **“执行动作”**。
   - 服务端暴露一系列函数（如 `send_email(to, body)` 或 `query_database(sql)`），AI 可以调用这些函数来完成真实世界的任务。
2. **资源 (Resources)：**
   - 这为 AI 提供了**“只读的上下文”**。
   - 例如：AI 可以读取 `get_file_content()` 来获取代码库的上下文，或者 `get_database_schema()` 来了解如何编写 SQL。
3. **提示 (Prompts)：**
   - 这为 AI 提供了“**如何使用工具的指导”**。
   - 服务端可以提供一些模板化指令（如：“当我让你分析代码时，你应该先调用 `get_file_content()` ...”），这极大地提高了 AI 使用工具的准确性。

#### 4. 总结（为什么它对面试重要）

在面试中提到 MCP，表明你理解 AI Agent 的一个**核心瓶颈**和**未来趋势**：

- **瓶颈：** 单纯的 RAG 只是“开卷考试”，让 AI 去“读”资料。但 RAG 无法让 AI 去“做”事情。
- **未来：** MCP 则是 **RAG 的进化**。它不仅让 AI 能“读”，还赋予了 AI **“写”（执行工具）** 的能力。
- **一句话总结：** **MCP 是实现“AI 智能体 (Agent)”大规模落地的关键基础设施**，它将 LLM 从一个“聊天机器人”转变为一个可以操作软件和数据的“超级助理”。"

## 二、底层原理架构

MCP 的核心是一个 **双向流式通信协议**（bidirectional streaming protocol），
 采用 **JSON-RPC over WebSocket / stdio / HTTP/2** 的方式进行通信。

### 1️⃣ 核心组成部分

| 组件                         | 说明                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| **Client（模型端）**         | 大语言模型（如 GPT-5）或其宿主（如 ChatGPT、VSCode 插件）    |
| **Server（MCP 服务端）**     | 实际持有资源或工具的程序，比如数据库查询器、文件系统、API 网关 |
| **Protocol Layer（协议层）** | 用 JSON-RPC 定义的通信规范，管理请求/响应/事件               |
| **Resource & Tool Schemas**  | 用 JSON Schema 描述可访问的资源和函数签名                    |
| **Session Context**          | 维持模型的上下文状态（例如用户身份、已加载的资源等）         |

------

### 2️⃣ 通信机制（底层工作流）

整个流程可以抽象为 4 步：

1. **连接建立（Handshake）**

   - Client 与 MCP Server 建立通信通道（可通过 `stdio`、`socket`、`WebSocket`）。
   - Server 返回其可用的“资源目录”（Resource Catalog）与“工具列表”（Tool Manifest）。

2. **能力声明（Capabilities Declaration）**

   - Server 通过 JSON Schema 声明自己支持的操作，如：

     ```c
     {
       "name": "getWeather",
       "parameters": {
         "city": {"type": "string"}
       },
       "returns": {
         "type": "object",
         "properties": {
           "temperature": {"type": "number"}
         }
       }
     }
     ```

3. **调用与响应（Invocation）**

   - 当模型推理中识别到需要外部信息（例如“查一下天气”），
      它向 MCP Server 发出一个 JSON-RPC 请求：

     ```c
     {
       "jsonrpc": "2.0",
       "method": "call_tool",
       "params": {
         "tool": "getWeather",
         "arguments": {"city": "Melbourne"}
       },
       "id": 1
     }
     ```

   - MCP Server 执行逻辑后返回结果：

     ```c
     {
       "jsonrpc": "2.0",
       "result": {"temperature": 22.5},
       "id": 1
     }
     ```

4. **上下文注入（Contextualization）**

   - 模型自动将返回值注入到自身上下文，用于后续推理或回答。

------

## 三、MCP 的设计理念（核心思想）

| 目标             | 对应机制                                     |
| ---------------- | -------------------------------------------- |
| **可移植性**     | 任何遵守协议的模型或工具都能互操作           |
| **安全性**       | 所有调用都可审计、可沙箱执行                 |
| **上下文一致性** | 模型可在跨会话保持状态（如缓存用户设置）     |
| **多模态支持**   | 可扩展到图像、音频、视频流工具               |
| **开发者友好**   | 工具注册机制类似“API manifest”，简化插件开发 |

------

## 四、与传统插件系统的区别

| 对比项   | MCP                                  | 传统插件（如 ChatGPT Plugin, API 调用） |
| -------- | ------------------------------------ | --------------------------------------- |
| 通信协议 | JSON-RPC 双向流                      | REST / HTTPS 单向请求                   |
| 连接方式 | 可长连接、可多协议                   | HTTP(S) 请求响应模式                    |
| 状态管理 | 内置上下文、可跨会话                 | 无状态、每次重建上下文                  |
| 安全模型 | 权限分级 + 沙箱机制                  | 依赖外部 API 控制                       |
| 兼容性   | 模型无关（GPT、Claude、Gemini 均可） | 平台绑定（如仅 ChatGPT）                |

------

## 五、实际应用示例

以 VSCode 插件为例，MCP 可以这样工作：

1. 模型连接到一个本地 MCP Server。

2. MCP Server 提供工具：

   - `read_file(path)`
   - `run_tests()`
   - `commit_changes(msg)`

3. 模型可以执行如下逻辑：

   > “Run the unit tests, fix failing lines, and commit the changes.”

模型通过协议调用 MCP 工具完成自动开发任务。

------

## 六、底层实现要点（技术细节）

1. **消息格式**：基于 JSON-RPC 2.0
    支持 `request`、`response`、`notification` 三种类型。
2. **传输层抽象**：独立于网络协议，可运行在：
   - WebSocket
   - stdin/stdout
   - TCP/Unix socket
3. **Schema Validation**：所有输入输出都经 JSON Schema 验证。
4. **Session Context**：允许存储与检索上下文变量（例如用户身份、历史记录）。
5. **可扩展事件机制**：支持订阅机制（如“文件系统变化”、“任务完成”）。

------

## 七、总结

**底层原理一句话概括：**

> MCP 是一个让模型能以标准、可审计的方式调用外部世界的双向通信协议，
>  其核心是 JSON-RPC 流式通信 + Schema 定义的工具接口 + 安全上下文管理。