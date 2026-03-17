# 💾 Java I/O 流与 I/O 模型 八股文核心总结

------

## 一、Java I/O 基础概念

### 🧩 1. IO 的定义

> IO（Input/Output）是 Java 用于处理输入和输出数据的机制。所有数据的读写在 Java 中都被抽象为“流”（Stream）。

- **输入流 (InputStream / Reader)**：从外部设备（文件、网络、键盘）读入数据到程序。
- **输出流 (OutputStream / Writer)**：从程序输出数据到外部设备。

📘 Java 的 I/O 流是 **面向流（Stream）** 的，流是一种**顺序读写**的数据传输方式。

### 🧩 2. 按数据类型划分

| 类型       | 基类                       | 单位                 | 适用场景                     |
| ---------- | -------------------------- | -------------------- | ---------------------------- |
| **字节流** | InputStream / OutputStream | 字节 (8 位)          | 图片、视频、音频、二进制文件 |
| **字符流** | Reader / Writer            | 字符 (16 位 Unicode) | 文本文件、XML、JSON          |

📘 **字符流 = 字节流 + 编码解码机制**。
 当需要处理中文或其他非 ASCII 文本时，推荐使用字符流。

### 🧩 3. 按流向划分

| 流向   | 含义                 | 示例                         |
| ------ | -------------------- | ---------------------------- |
| 输入流 | 从外部设备读入程序   | FileInputStream、FileReader  |
| 输出流 | 从程序写出到外部设备 | FileOutputStream、FileWriter |

### 🧩 4. 常见类与功能分层

| 分类   | 字节流类                                   | 字符流类                        | 功能                          |
| ------ | ------------------------------------------ | ------------------------------- | ----------------------------- |
| 文件流 | FileInputStream / FileOutputStream         | FileReader / FileWriter         | 文件读写                      |
| 缓冲流 | BufferedInputStream / BufferedOutputStream | BufferedReader / BufferedWriter | 提高性能，减少系统调用        |
| 转换流 | InputStreamReader / OutputStreamWriter     | —                               | 字节 ↔ 字符 转换              |
| 数据流 | DataInputStream / DataOutputStream         | —                               | 读写 Java 基本类型            |
| 对象流 | ObjectInputStream / ObjectOutputStream     | —                               | 序列化与反序列化              |
| 打印流 | PrintStream / PrintWriter                  | —                               | 格式化输出（如 `System.out`） |

------

### 🧩 5. I/O 总结（高频面试答法）

> Java 的 IO 流体系本质上是对 **数据读写的抽象**。
>  通过 **字节流 / 字符流 × 输入 / 输出** 四个维度组合，实现对各种数据传输场景的统一处理。

## 二、I/O 模型与发展演进

| 模型 | 全称             | 特点                     | 使用场景                   |
| ---- | ---------------- | ------------------------ | -------------------------- |
| BIO  | Blocking I/O     | 同步阻塞：每连接一个线程 | 小并发（文件、传统Socket） |
| NIO  | Non-blocking I/O | 同步非阻塞：单线程多通道 | 中高并发（网络服务）       |
| AIO  | Asynchronous I/O | 异步非阻塞：回调机制     | 极高并发、低延迟（NIO.2）  |

------

### 1️⃣ BIO（阻塞 I/O）

- 每个客户端连接都占用一个独立线程；
- 当线程执行 `read()` 或 `write()` 时会阻塞；
- 线程等待期间不能执行其他任务；
- 实现简单，但线程开销大，不适合高并发。

**应用：** 文件流、低并发 Socket 通信。

------

### 2️⃣ NIO（非阻塞 I/O）

- 基于 **Channel（通道）**、**Buffer（缓冲区）** 和 **Selector（选择器）**。
- 单线程可通过 **Selector** 同时监听多个通道的事件（如连接、读、写）。
- 非阻塞模式下，`read()` 不会卡死，若无数据返回 0。

📘 **核心组件：**

| 组件         | 作用                                          |
| ------------ | --------------------------------------------- |
| **Channel**  | 数据传输通道（FileChannel、SocketChannel）    |
| **Buffer**   | 存储数据的缓冲区，支持读写切换（flip、clear） |
| **Selector** | 事件选择器，一个线程管理多个通道              |

🧩 **工作原理：**

1. 打开通道（Channel）
2. 注册到选择器（Selector）
3. 不断轮询 select()，判断哪个通道准备好
4. 处理读写事件（read/write）
5. 重复循环（Reactor 模式）

**优点：**

- 单线程处理多连接；
- CPU 利用率高；
- 可扩展性强。

**缺点：**

- 编程复杂；
- 需要手动管理 Buffer。

------

### 3️⃣ AIO（异步 I/O）

- Java 7 引入（NIO.2），核心类：
  - `AsynchronousSocketChannel`
  - `AsynchronousServerSocketChannel`
- 操作完全异步：线程发起 I/O 请求后立即返回；
- 完成后系统回调 `CompletionHandler`；
- 不阻塞线程、资源利用率最高。

**应用：**

- 分布式系统；
- 高性能异步服务器；
- 实时计算、海量并发。

------

### 4️⃣ 三种 I/O 模型对比

| 特性       | BIO          | NIO                  | AIO                                   |
| ---------- | ------------ | -------------------- | ------------------------------------- |
| 阻塞模式   | 阻塞         | 非阻塞               | 异步                                  |
| 编程复杂度 | 简单         | 中等                 | 高                                    |
| 线程模型   | 一连接一线程 | 一个线程处理多个连接 | 回调通知，无需等待                    |
| 性能       | 低并发       | 中高并发             | 极高并发                              |
| Java 包    | java.io      | java.nio             | java.nio.channels.AsynchronousChannel |

------

## 三、I/O 性能优化与缓冲机制

### 🧩 BufferedReader vs BufferedInputStream

| 对比项 | BufferedReader           | BufferedInputStream    |
| ------ | ------------------------ | ---------------------- |
| 流类型 | 字符流                   | 字节流                 |
| 单位   | 字符（char）             | 字节（byte）           |
| 场景   | 文本文件                 | 二进制文件             |
| 性能   | 读取文本更快（内含解码） | 读取字节更快（无编码） |

📘 **原因：**

- `BufferedReader` 内部使用字符缓冲，减少多次 IO 调用；
- 读取文本时避免频繁的字节到字符转换；
- 若是二进制文件（图片、音频）→ 用 `BufferedInputStream` 更合适。

------

## 四、NIO 核心机制详解

### 1️⃣ Channel（通道）

> 双向数据传输通道，取代传统单向流。

常见类型：

- FileChannel（文件）
- SocketChannel（TCP 客户端）
- ServerSocketChannel（TCP 服务端）
- DatagramChannel（UDP）

### 2️⃣ Buffer（缓冲区）

> 存放数据的容器。
>  关键属性：

- capacity（容量）
- position（当前位置）
- limit（读写上限）

**常用类型：** ByteBuffer、CharBuffer、IntBuffer 等。

📘 **常用操作：**

```
buffer.put(data);
buffer.flip();   // 切换为读模式
buffer.get();
buffer.clear();  // 清空并切换为写模式
```

### 3️⃣ Selector（选择器）

> 实现多路复用，一个线程监听多个通道。

**流程：**

1. 注册 Channel 到 Selector；
2. 设定监听事件（OP_READ, OP_WRITE, OP_CONNECT）；
3. 调用 `select()` 阻塞等待；
4. 通过 `selectedKeys()` 获取事件；
5. 处理对应的通道。

📘 **核心思想：**

> Reactor 模式 — “事件驱动 + 回调执行”。

------

## 五、序列化与反序列化

### 🧩 1. 概念

- **序列化（Serialization）**：将对象转为字节流，用于存储或网络传输。
- **反序列化（Deserialization）**：从字节流还原为对象。

------

### 🧩 2. 实现机制

#### ✅ 步骤：

1. 类实现 `java.io.Serializable`（标记接口）；
2. 使用 `ObjectOutputStream.writeObject(obj)` 序列化；
3. 使用 `ObjectInputStream.readObject()` 反序列化。

#### ✅ 示例：

```java
class Person implements Serializable {
    private static final long serialVersionUID = 1L;
    String name;
    int age;
}

Person p = new Person();
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("p.dat"));
oos.writeObject(p);
oos.close();

ObjectInputStream ois = new ObjectInputStream(new FileInputStream("p.dat"));
Person p2 = (Person) ois.readObject();
ois.close();
```

------

### 🧩 3. 关键点

| 特性               | 说明                                             |
| ------------------ | ------------------------------------------------ |
| `serialVersionUID` | 标识类版本，反序列化时比对用                     |
| `transient`        | 关键字修饰的字段不会被序列化                     |
| `static`           | 不会被序列化（属于类）                           |
| `Externalizable`   | 可自定义序列化逻辑（writeExternal/readExternal） |

------

### 🧩 4. 使用场景

- 对象持久化；
- 网络传输（如 RPC、Socket 通信）；
- 缓存中存储对象。

------

## 六、面试速答总结 🔥

| 面试题                                            | 答案要点                                     |
| ------------------------------------------------- | -------------------------------------------- |
| Java IO 有哪几种分类？                            | 按方向（输入输出）、按数据类型（字节字符）   |
| 字节流与字符流区别？                              | 前者处理字节数据，后者处理字符文本（含编码） |
| BIO、NIO、AIO 区别？                              | 阻塞/非阻塞/异步，性能依次提升               |
| Selector 的作用？                                 | 单线程管理多通道，事件驱动模型               |
| 为什么 BufferedReader 比 BufferedInputStream 快？ | 减少字符解码与系统调用次数                   |
| Java 如何实现序列化？                             | 实现 Serializable 接口，ObjectOutputStream   |
| serialVersionUID 作用？                           | 类版本控制，防止反序列化失败                 |
| transient 修饰符作用？                            | 排除字段不被序列化                           |

------

## 七、终极记忆导图（IO全景思维图）

```
Java IO
├── IO流分类
│   ├── 按方向：输入流 / 输出流
│   ├── 按单位：字节流 / 字符流
│   ├── 按功能：文件流、缓冲流、对象流、转换流
│
├── IO模型
│   ├── BIO（阻塞） → 一连接一线程
│   ├── NIO（非阻塞） → 单线程多连接（Channel+Selector）
│   └── AIO（异步） → 回调机制、异步完成
│
├── NIO核心
│   ├── Channel：双向通道
│   ├── Buffer：数据容器
│   └── Selector：多路复用
│
├── 序列化机制
│   ├── Serializable / Externalizable
│   ├── ObjectOutputStream / ObjectInputStream
│   ├── serialVersionUID / transient
│
└── 性能优化
    ├── 缓冲流（Buffered）
    ├── NIO替代BIO
    └── 内存映射文件 / 异步通道
```