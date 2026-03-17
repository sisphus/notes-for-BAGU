# 🚀 **RocketMQ 八股 · 第一阶段（基础版）**

适合刚开始复习，5 分钟看完全部基础概念。

------

# ✅ **1. RocketMQ 是什么？有什么特点？（必考）**

**一句话：** RocketMQ 是阿里开源的高性能分布式消息队列，用于解耦、削峰、异步。

**特点：**

1. **高吞吐 + 低延迟**（顺序写 CommitLog，零拷贝 sendfile）
2. **分布式，可扩展**（Broker 可水平扩容）
3. **强一致、高可靠**（同步刷盘 / 同步复制）
4. **天然支持顺序消息**
5. **支持百万级消息堆积**

# ✅ **2. RocketMQ 的基本组件有哪些？（必考）**

记住四件套：

| 组件                       | 作用                       |
| -------------------------- | -------------------------- |
| **NameServer**             | 注册中心，保存 Broker 路由 |
| **Broker（Master/Slave）** | 存消息的地方               |
| **Producer**               | 发消息                     |
| **Consumer**               | 收消息（Push/Pull）        |

**Broker 是唯一真正负责存储的核心。**

# ✅ **3. 主题 Topic、队列 Queue、消息 Message 是什么？**

RocketMQ 的核心：
 一个 Topic = 多个 Queue
 Queue 是真正存消息的位置。

**为什么这样设计？**
 为了并发消费，每个 Queue 由一个消费者线程消费。

# 🚀 **第二阶段（面试常问）**

# ✅ **4. RocketMQ 的消息怎么存储？（高频必问）**

RocketMQ 的存储由三个文件组成：

1. **CommitLog（消息本体）**
2. **ConsumeQueue（消费队列的索引）**
3. **IndexFile（按 key 查询）**

📌 **重点：CommitLog 是顺序写文件 → RocketMQ 高性能的根本原因。**

------

# ✅ **5. RocketMQ 支持顺序消息吗？怎么实现的？**

支持 **全局顺序** 和 **分区顺序**。

**原理：**

- **同一个 Queue 内是顺序的**（文件顺序写）
- 所以只要某个业务的消息都落到同一个 Queue → 顺序消息

生产端用 hash 选择队列：

```
producer.send(msg, (mqs, msg, arg) -> {
    int orderId = (Integer) arg;
    int index = orderId % mqs.size();
    return mqs.get(index);
}, orderId);
```

------

# 🚀 **第三阶段（深入但还算简单）**

# ✅ **6. RocketMQ 如何保证消息不丢？**

依赖三层保证：

### **1. Producer（发送可靠性）**

- 同步发送（sync）→ 几乎不丢
- 异步发送（async）→ 有回调
- Oneway → 最容易丢

### **2. Broker（存储可靠性）**

- **刷盘策略：**
  - 同步刷盘（可靠）
  - 异步刷盘（性能快，但可能丢 1ms 数据）
- **主从复制：**
  - 同步复制 → 写入 Master & Slave 才算成功
  - 异步复制 → 有风险（主挂了会丢）

### **3. Consumer（消费可靠性）**

- 消费失败会 **自动重试**，除非超过次数进入 DLQ

------

# ✅ **7. RocketMQ 一条消息会不会被重复消费？为什么？**

会。

原因：

1. Producer 重试导致发了多次
2. Broker 写成功但客户端没收到 ack
3. Consumer 消费成功但 ack（offset 提交）失败

🏆 **RocketMQ 是“至少一次消费”，不是 Exactly Once。**

解决：业务端幂等。

------

# 🚀 **第四阶段（中等面试题）**

# ✅ **8. RocketMQ 如何保证消息顺序？（面试超爱问）**

一句话：

> 生产者：同一个业务的消息落到同一个 queue
>  消费者：一个队列只被一个消费线程消费

------

# ✅ **9. RocketMQ 如何实现延迟消息？**

RocketMQ 使用 **TimerWheel（时间轮）**
 非定时调度器，而是按层级轮盘时间推进。

------

# 🚀 **第五阶段（进阶面试题）**

# ✅ **10. RocketMQ 消息堆积怎么办？**

从三个维度回答：

### ✔ 健康检查：

- 查看 Broker offset
- 确认消费者线程数
- 查看消费耗时

### ✔ 消费者优化：

- 增大消费线程数
- 开启批量消费
- 提升业务逻辑性能

### ✔ Broker 层：

- 扩容队列（提高消费并行度）

------

# 🚀 **第六阶段（高频八股总结）**

下面是快速记忆版：

| 问题            | 答案 1 句总结                          |
| --------------- | -------------------------------------- |
| RocketMQ 组件？ | NameServer、Broker、Producer、Consumer |
| 为什么高性能？  | CommitLog 顺序写 + 零拷贝              |
| 顺序消息？      | 同队列 + 单线程消费                    |
| 不丢消息？      | 同步发送 + 同步刷盘 + 同步复制         |
| 重复消费？      | Exactly-Once 做不到，业务幂等          |
| 延迟消息？      | 时间轮                                 |
| 消息堆积？      | 扩线程、批消费、扩 queue               |

### 为什么要用 RocketMQ？

（面试官可能会问的第一个问题）在面试中，你可以这样回答：在这个项目中，我们引入 RocketMQ 这样的消息中间件，主要是为了解决两个核心问题：系统解耦和异步处理，从而提升系统的整体性能和可扩展性。

举个项目中的具体例子：文件删除。

•同步处理的痛点：当一个用户删除文件时，后台不仅要删除数据库里的用户文件关联记录，还要检查这个物理文件是否还有其他用户在引用。如果没有引用了，就需要去磁盘上把它真正地删掉。磁盘 I/O 操作是非常耗时的，如果把这些操作全部放在删除文件的那个接口里同步执行，接口的响应时间会变得很长，用户就会感觉“卡顿”。

•异步处理的优势：为了优化用户体验，我们采用异步化的思想。删除文件的接口只做最核心、最快速的业务（比如，把文件的删除状态置为“已删除”），然后发布一个“文件已被删除”的事件到 RocketMQ 中。接口可以立刻返回，告诉用户“删除成功”。而真正耗时的物理文件清理工作，由另一个专门的消费者服务去监听 RocketMQ 里的消息，然后在后台慢慢处理。

这样一来，核心业务（文件删除接口）和非核心业务（物理文件清理）就被解耦了，并且通过异步执行，保证了核心接口的高性能。

### 2. 项目中是如何用 RocketMQ 的？（代码层面解读）

整个流程可以分为三大部分：定义通道 -> 发送消息（生产） -> 接收消息（消费）。

第一步：定义消息通道 (Channel)你可以把 Channel 理解为生产者和消费者之间沟通的“管道”。在 RocketMQ 中，它通常对应一个Topic。

// /Users/weileipeng/Desktop/RPan/server/src/main/java/com/imooc/pan/server/common/stream/channel/PanChannels.java

```java
public interface PanChannels {
// ... 其他通道

// 定义了物理删除文件的输入和输出通道名称
String PHYSICAL_DELETE_FILE_INPUT = "physicalDeleteFileInput";
String PHYSICAL_DELETE_FILE_OUTPUT = "physicalDeleteFileOutput";

// ...

// @Input 注解表示这是个输入通道（消费者用）
@Input(PanChannels.PHYSICAL_DELETE_FILE_INPUT)
SubscribableChannel physicalDeleteFileInput();

// @Output 注解表示这是个输出通道（生产者用）
@Output(PanChannels.PHYSICAL_DELETE_FILE_OUTPUT)
MessageChannel physicalDeleteFileOutput();

// ...
    }
```
最后，在项目启动类 RPanServerLauncher.java 上，有一个关键注解 @EnableBinding(PanChannels.class)，它会告诉 Spring Cloud Stream 框架：“请帮我把 PanChannels 接口里定义的这些通道都创建并绑定好”。

第二步：发送消息（生产者）

当某个业务需要发送消息时，它会调用一个生产者（Producer）接口。我们来看一个场景：当一个文件被确认要物理删除时，系统需要发出一个通知。这个动作通常发生在另一个消费者 ShareStatusChangeConsumer 或者一个定时任务里（比如清理回收站）。当它处理完逻辑删除后，会构造一个 FilePhysicalDeleteEvent 事件，然后通过 IStreamProducer 发送出去。

```java
// 这是一个简化的例子，实际代码中发送物理删除事件的地方可能在其他服务中
// 比如，在逻辑删除文件的服务操作完成后，会发送这个事件

// 1. 注入生产者
@Autowired
@Qualifier(value = "defaultStreamProducer")
private IStreamProducer producer;

// 2. 构造事件对象
List<RPanUserFile> filesToDelete = ... // 获取到需要被物理删除的文件列表
FilePhysicalDeleteEvent event = new FilePhysicalDeleteEvent(filesToDelete);

// 3. 发送消息
// 指定要发送到哪个通道 (PHYSICAL_DELETE_FILE_OUTPUT)，并把事件作为消息体(payload)
producer.sendMessage(PanChannels.PHYSICAL_DELETE_FILE_OUTPUT, event);

```

当 sendMessage 被调用时，Spring Cloud Stream 框架会：

1.将 event 对象序列化（比如转成 JSON 字符串）。

2.找到 PHYSICAL_DELETE_FILE_OUTPUT 这个通道绑定的 RocketMQ Topic。

3.把消息发送到这个 Topic 里。

第三步：接收并处理消息（消费者）

现在消息已经在 RocketMQ 的 Topic 里了，需要有消费者来处理它。项目中的 FilePhysicalDeleteEventConsumer.java 就是扮演这个角色的。

// /Users/weileipeng/Desktop/RPan/server/src/main/java/com/imooc/pan/server/common/stream/consumer/file/FilePhysicalDeleteEventConsumer.java



```java
@Component
public class FilePhysicalDeleteEventConsumer extends AbstractConsumer {
// ... 注入了各种 Service

/**
 * 关键点：@StreamListener 注解
 * 它声明了 physicalDeleteFile 方法正在监听 PHYSICAL_DELETE_FILE_INPUT 通道。
 * 当该通道（对应的 RocketMQ Topic）有新消息时，此方法会被自动调用。
 */
@StreamListener(PanChannels.PHYSICAL_DELETE_FILE_INPUT)
public void physicalDeleteFile(Message<FilePhysicalDeleteEvent> message) {
    // 打印日志、做一些空消息判断
    if (isEmptyMessage(message)) {
        return;
    }
    printLog(message);

    // 从消息中取出真正的事件对象
    FilePhysicalDeleteEvent event = message.getPayload();
    List<RPanUserFile> allRecords = event.getAllRecords();
    if (CollectionUtils.isEmpty(allRecords)) {
        return;
    }

    // --- 开始执行真正耗时的业务 ---
    // 1. 找出真正没有被引用的文件ID
    List<Long> realFileIdList = findAllUnusedRealFileIdList(allRecords);
    if (CollectionUtils.isEmpty(realFileIdList)) {
        return;
    }

    // 2. 从数据库删除物理文件记录 (r_pan_file)
    List<RPanFile> realFileRecords = iFileService.listByIds(realFileIdList);
    if (!iFileService.removeByIds(realFileIdList)) {
        // 如果删除失败，再发一个错误日志消息到RocketMQ
        producer.sendMessage(PanChannels.ERROR_LOG_OUTPUT, new ErrorLogEvent(...));
        return;
    }

    // 3. 调用存储引擎，从磁盘上删除文件
    physicalDeleteFileByStorageEngine(realFileRecords);
}

// ... 其他私有方法
}
```
总结与面试建议当面试官问你时，你可以把上面这个“物理删除文件”的例子完整地讲一遍：

1.背景：为了优化用户删除文件的响应速度，我们将耗时的物理文件清理工作设计为异步执行。

2.技术选型：我们使用 Spring Cloud Stream 集成 RocketMQ 来实现这个异步架构。

3.实现流程：

首先，在 PanChannels 中定义了消息的输入/输出通道，并用 @EnableBinding 开启绑定。

•当文件需要被物理删除时，生产者服务会创建一个 FilePhysicalDeleteEvent 事件，并通过 IStreamProducer 将其发送到 PHYSICAL_DELETE_FILE_OUTPUT 通道。

•Spring Cloud Stream 底层会将这个消息发送到 RocketMQ 的一个特定 Topic。•FilePhysicalDeleteEventConsumer 类中有一个方法使用 @StreamListener 监听 PHYSICAL_DELETE_FILE_INPUT 通道。

•当 Topic 中有新消息时，该方法被触发，它会解析消息内容，然后执行一系列数据库和磁盘的清理操作，完成最终的物理删除。

4.优点：通过这套机制，我们成功地将前端操作与后端耗时任务解耦，保证了系统核心链路的高性能和高可用性。

额外加分项（并发问题）你还可以主动提出这个设计中潜在的并发问题和解决方案，也就是我们之前讨论过的分布式锁：“在这个消费逻辑中，isUnused 方法（检查文件引用计数）和 iFileService.removeByIds（删除数据库记录）这两个步骤并非原子操作。在高并发下，如果两个服务实例同时消费同一个文件的删除事件，可能会同时判断文件为‘未使用’，导致重复删除等问题。为了解决这个问题，我们在 isUnused 方法内部引入了基于 Redis 的分布式锁，确保在检查和删除期间，只有一个实例能够操作特定的物理文件ID，从而保证了数据的一致性。”这样一说，不仅体现了你对消息队列的理解，还展示了你在分布式系统设计中的深度思考，面试效果会非常好。



# 1. 什么是分布式锁？（概念理解）

“在单体应用中，当多个线程需要访问同一个共享资源时，我们可以用 Java 的 synchronized 或 ReentrantLock 来保证同一时间只有一个线程能操作，这叫本地锁。但在分布式系统中，我们的应用被部署在多台服务器上，每个服务器都是一个独立的 JVM。这时，本地锁就失效了，因为它锁不住其他服务器上的线程。如果不加控制，多台服务器同时修改同一份数据，就会造成数据错乱，这就是竞态条件（Race Condition）。”

“分布式锁就是为了解决这个问题而生的。它是一种跨服务器、跨进程的锁，能确保在整个分布式集群中，同一时刻只有一个节点能够获得锁，并执行关键代码（临界区），从而保证数据的一致性。”

“实现分布式锁有多种方式，比如基于数据库、Zookeeper 或 Redis。在这个项目中，我们选择使用 Redis，因为它性能非常高（基于内存），并且它提供了一些原子命令，比如 SETNX (SET if Not eXists)，非常适合用来实现分布式锁。基本思想就是：谁能成功在 Redis 中创建一个特定的 key，谁就获得了锁。”

# 2. 项目中哪里用到了分布式锁？（问题定位）

接下来，你需要给出一个项目中的具体场景来证明你不是在空谈理论。FilePhysicalDeleteEventConsumer.java 就是一个绝佳的例子。

你可以这样描述：“在我们的 RPan 项目中，有一个典型的场景需要用到分布式锁，那就是文件的物理删除。”

业务背景：当用户删除文件时，为了提升接口响应速度，我们并没有直接从磁盘上删除文件（这是一个耗时的 I/O 操作）。我们采用的是异步处理：接口只是将文件标记为“已删除”，然后发送一个 FilePhysicalDeleteEvent 消息到 RocketMQ。后台有一个专门的消费者服务 (FilePhysicalDeleteEventConsumer) 负责监听这个消息，并执行真正的物理删除。

并发问题（Race Condition）：这个消费者的核心逻辑是：

•首先，检查这个物理文件在数据库中是否还有其他用户的引用（通过 isUnused 方法执行 SELECT COUNT(*)）。•如果引用计数为 0，就删除数据库中的物理文件记录，并从磁盘上删除文件。

“我们很快意识到这里存在一个并发风险：假设有两个用户同时删除了同一个热门文件的最后两个引用。那么，集群中的两台服务器（比如服务器A和服务器B）可能会同时收到这个文件的删除消息。它们会几乎在同一时刻去执行 isUnused 方法，并且查询到的引用数都为 0。这样一来，两个服务实例都会认为自己应该执行删除操作，从而导致对同一个文件进行重复删除，引发系统错误和异常。”

问题本质：这个问题本质上是一个“检查再执行（Check-Then-Act）”的竞态条件。检查引用数和执行删除这两个步骤，在分布式环境下不是一个原子操作。

# 3. 如何解决？（分布式锁的实现）

这是展示你动手能力的关键部分。你需要说明如何用代码解决这个问题。

“为了解决这个并发问题，我们引入了基于 Redis 的分布式锁，来确保‘检查-删除’这个过程的原子性。具体的实现如下：”1.引入依赖：我们在 pom.xml 中加入了 r-pan-lock-redis 模块（或者像 Redisson 这样的优秀客户端库），它为我们提供了分布式锁的便捷实现。

2.改造关键代码：我们修改了 FilePhysicalDeleteEventConsumer.java 中的 isUnused 方法。在执行数据库查询之前，先获取一个与该文件绑定的锁。

代码逻辑解释：

•锁的 Key：我们为每一个物理文件（realFileId）都生成一个唯一的 lockKey。

这样，对同一个文件的操作就会竞争同一个锁，而对不同文件的操作则互不影响。

•尝试加锁：使用 lock.tryLock() 来尝试获取锁。它会尝试在5秒内获取锁，如果成功，该锁的持有时间为10秒（自动过期，防止因服务宕机导致死锁）。

•如果实例A获取了锁，它就可以安全地执行数据库查询。

•此时如果实例B也来尝试获取同一个锁，tryLock() 会在等待后返回 false，实例B就知道有其他人在处理了，于是直接跳过，避免了并发冲突。

•释放锁：最关键的一步是，在 finally 代码块中释放锁。这能保证即使业务代码出现异常，只要获取了锁，就一定会被释放，避免了“死锁”的发生。
