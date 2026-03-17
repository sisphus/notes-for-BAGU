# 🧩 Java 集合框架八股文核心总结

## 一、Array vs ArrayList vs LinkedList

| 对比点   | Array（数组）         | ArrayList（动态数组）  | LinkedList（链表） |
| -------- | --------------------- | ---------------------- | ------------------ |
| 类型     | 基本数据结构          | List接口实现类         | List接口实现类     |
| 底层结构 | 连续内存数组          | 动态数组（Object[]）   | 双向链表           |
| 可变性   | 长度固定              | 可动态扩容（1.5倍）    | 不需扩容           |
| 元素类型 | 可存基本类型/引用类型 | 只能存对象（自动装箱） | 只能存对象         |
| 访问性能 | O(1) 随机访问快       | O(1) 随机访问快        | O(n) 顺序访问      |
| 插入删除 | 慢（需移动元素）      | 慢（中间插入需移动）   | 快（直接改引用）   |
| 内存占用 | 较小                  | 中等                   | 较大（多两个指针） |
| 使用场景 | 元素数量固定          | 查询多、增删少         | 增删多、查询少     |

📘 **口诀记忆：**

> “查用ArrayList，增删LinkedList，固定用Array。”

## 二、List vs Set

| 对比项       | List                          | Set                             |
| ------------ | ----------------------------- | ------------------------------- |
| 是否有序     | ✅ 有序（插入顺序）            | ❌ 无序（部分有序，如TreeSet）   |
| 是否允许重复 | ✅ 允许                        | ❌ 不允许                        |
| 访问方式     | 可用索引访问                  | 只能遍历访问                    |
| 常见实现     | ArrayList、LinkedList、Vector | HashSet、LinkedHashSet、TreeSet |

## 三、HashSet 的去重机制

**底层：基于 HashMap 实现**

- 元素作为 **key** 存入 HashMap；
- value 是一个固定对象（如 `PRESENT`）。

**判重逻辑：**

1. 调用对象的 `hashCode()` → 确定桶位置；
2. 若哈希冲突，调用 `equals()` 判断内容；
3. 若 `equals()` 为 true → 认为重复，不添加。

📘 **面试口诀：**

> “hashCode 定位桶，equals 决定相等。”

## 四、HashMap 核心原理

### 1️⃣ 底层结构（JDK 1.8）

> **数组 + 链表 + 红黑树（TreeBin）**

- 初始容量：16，负载因子：0.75
- 当链表长度 ≥ 8 且数组长度 ≥ 64 → 转为红黑树
- 当树节点数 < 6 → 退化回链表

### 2️⃣ 存储流程（put）

1. 计算 key 的 hash（扰动函数：`(h = key.hashCode()) ^ (h >>> 16)`）
2. 定位数组索引位置
3. 若桶为空 → 直接插入
4. 若冲突：
   - 比较 key 是否相等（hash + equals）
   - 不同 → 挂在链表尾部（JDK 8 用尾插法）
5. 若超过阈值（容量×负载因子）→ 扩容

### 3️⃣ 扩容机制

- 新容量 = 旧容量 × 2
- 重新计算位置（高位运算优化：元素要么 stay，要么移动到原索引+oldCap）
- 减少 rehash 开销

📘 **JDK 1.8 改进总结：**

| 项目      | JDK 1.7       | JDK 1.8                   |
| --------- | ------------- | ------------------------- |
| 冲突结构  | 链表          | 链表 + 红黑树             |
| 插入方式  | 头插法        | 尾插法                    |
| rehash    | 全部重算      | 高位判断优化              |
| hash 函数 | 直接 hashCode | 加扰动 `(h ^ (h >>> 16))` |

------

## 五、TreeMap vs HashMap

| 对比项     | HashMap                      | TreeMap                              |
| ---------- | ---------------------------- | ------------------------------------ |
| 底层结构   | 数组 + 链表 + 红黑树         | 红黑树                               |
| 是否有序   | ❌ 无序                       | ✅ 有序（自然顺序或自定义Comparator） |
| 复杂度     | 查找/插入 O(1)               | 查找/插入 O(log n)                   |
| null 键/值 | 允许一个 null 键，多 null 值 | ❌ 不允许 null 键                     |
| 适用场景   | 快速存取                     | 需排序/范围查询                      |

## 六、HashMap vs HashSet vs Hashtable

| 比较项        | HashMap                             | HashSet                       | Hashtable            |
| ------------- | ----------------------------------- | ----------------------------- | -------------------- |
| 存储内容      | 键值对                              | 单个元素                      | 键值对               |
| 底层结构      | 数组 + 链表 + 红黑树                | 基于 HashMap                  | 数组 + 链表          |
| 是否线程安全  | ❌ 否                                | ❌ 否                          | ✅ 是（synchronized） |
| 是否允许 null | ✅ 允许一个 null 键、多 null 值      | ✅ 允许 null                   | ❌ 不允许             |
| 替代推荐      | ConcurrentHashMap（高性能线程安全） | Collections.synchronizedSet() | 弃用（老旧类）       |

📘 **记忆口诀：**

> “HashMap快，Hashtable老；线程安全用ConcurrentHashMap。”

## 七、线程安全集合类

| 分类   | 实现类                        | 是否线程安全            | 说明                 |
| ------ | ----------------------------- | ----------------------- | -------------------- |
| List   | ArrayList ❌                   | Vector ✅                | Vector 同步开销大    |
| Set    | HashSet ❌                     | CopyOnWriteArraySet ✅   | 写时复制             |
| Map    | HashMap ❌                     | ConcurrentHashMap ✅     | 分段锁/桶级锁        |
| Queue  | ArrayDeque ❌                  | ConcurrentLinkedQueue ✅ | 无锁队列             |
| 工具类 | Collections.synchronizedXxx() | ✅                       | 外层加全局锁，性能低 |

## 八、Vector vs ArrayList

| 对比项   | Vector        | ArrayList |
| -------- | ------------- | --------- |
| 线程安全 | ✅（方法加锁） | ❌         |
| 扩容机制 | 2倍           | 1.5倍     |
| 性能     | 慢            | 快        |
| 使用场景 | 多线程        | 单线程    |

📘 **推荐：**

> 单线程：用 ArrayList
>  多线程：用 `Collections.synchronizedList()` 或 `CopyOnWriteArrayList`

## 九、Collection 接口 vs Collections 工具类

| 名称        | 类型   | 功能                                       |
| ----------- | ------ | ------------------------------------------ |
| Collection  | 接口   | 是所有集合类的父接口                       |
| Collections | 工具类 | 提供静态方法（排序、同步化、不可变集合等） |

常见方法：

```
Collections.sort(list);
Collections.reverse(list);
Collections.synchronizedList(list);
Collections.unmodifiableList(list);
```

------

## 🔟 HashMap 线程不安全原因与解决方案

**不安全原因：**

- 多线程下 put 可能导致数据覆盖；
- JDK 1.7 头插法会引发链表反转 → 死循环；
- 扩容 rehash 时可能丢数据。

**线程安全方案：**

1. `Collections.synchronizedMap(map)` → 全局锁，性能差；
2. `ConcurrentHashMap` → 分段锁/Node级CAS，性能高；
3. 或手动加锁：`synchronized(map)`。

## 🧠 面试记忆总结（口诀）

| 场景         | 推荐集合          | 理由                    |
| ------------ | ----------------- | ----------------------- |
| 频繁查询     | ArrayList         | 动态数组，随机访问 O(1) |
| 频繁插入删除 | LinkedList        | 链表结构，插删 O(1)     |
| 不允许重复   | HashSet           | 基于 HashMap            |
| 需要排序     | TreeSet / TreeMap | 基于红黑树              |
| 键值映射     | HashMap           | 快速查找                |
| 多线程       | ConcurrentHashMap | 高并发安全              |

# 🧠 Java 集合框架 — 进阶篇八股文总结

## 一、ConcurrentHashMap 原理（核心高频🔥）

### 1️⃣ JDK 1.7 实现：分段锁（Segment）

- 结构：**Segment 数组 + HashEntry 数组**
- Segment 相当于一个小型的 HashMap，每个 Segment 维护一把独立锁。
- 默认 16 个 Segment，可并发写入 16 个区域，减少锁竞争。
- 缺点：Segment 数量固定，扩容复杂，内存浪费。

### 2️⃣ JDK 1.8 实现：取消分段锁（改为 Node + CAS + synchronized）

**核心结构：**

> 数组 + 链表 + 红黑树 + CAS + synchronized(桶锁)

| 机制               | 说明                                               |
| ------------------ | -------------------------------------------------- |
| 无分段锁           | 直接用 Node 数组存储数据                           |
| CAS + synchronized | CAS 用于无锁插入；桶冲突时锁住该桶节点（非全表锁） |
| 链表树化           | 链表长度 ≥ 8 且 table 长度 ≥ 64 时 → 树化为红黑树  |
| 树退化             | 链表节点数 < 6 时 → 退化回链表                     |
| 扩容               | 多线程协作扩容，避免单线程阻塞                     |

📘 **时间复杂度：**

- 插入、查找：平均 O(1)，最坏 O(log n)

📘 **线程安全机制总结：**

- JDK 1.7：分段锁（Segment锁）
- JDK 1.8：桶级锁 + CAS，无需整体加锁

### 3️⃣ 面试速答模板：

> “ConcurrentHashMap 1.8 之后摒弃了 Segment 分段锁，采用了 **CAS + synchronized 桶级锁**。在插入冲突时只锁定链表或红黑树的头节点，扩容时多个线程可协作迁移数据，极大提升了并发性能。”

## 二、CopyOnWriteArrayList 与 CopyOnWriteArraySet

### 1️⃣ 基本原理

- “写时复制（Copy On Write）”：写操作不直接修改原数组，而是：
  1. 先复制一份新数组；
  2. 修改新数组；
  3. 替换原数组引用。

```java
public boolean add(E e) {
    ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] newArr = Arrays.copyOf(array, array.length + 1);
        newArr[array.length] = e;
        array = newArr;
        return true;
    } finally {
        lock.unlock();
    }
}
```

### 2️⃣ 特点

| 特性     | 说明                           |
| -------- | ------------------------------ |
| 读操作   | 不加锁，读取快且线程安全       |
| 写操作   | 加锁 + 复制数组，性能低但安全  |
| 适合场景 | 读多写少，如白名单、缓存配置表 |
| 缺点     | 占内存大，写操作开销大         |

📘 **底层：**

- CopyOnWriteArraySet 内部其实使用 CopyOnWriteArrayList 实现（去重靠 `contains()`）。

 ## 三、BlockingQueue（阻塞队列）



### 1️⃣ 定义

> 支持阻塞的队列接口，常用于生产者-消费者模型。
>  当队列为空时，`take()` 会阻塞；当队列满时，`put()` 会阻塞。

### 2️⃣ 常见实现类

| 实现类                | 底层结构            | 特点                    |
| --------------------- | ------------------- | ----------------------- |
| ArrayBlockingQueue    | 数组                | 有界队列（容量固定）    |
| LinkedBlockingQueue   | 链表                | 可指定容量，默认无限    |
| PriorityBlockingQueue | 堆（优先级队列）    | 自动按优先级排序        |
| DelayQueue            | 优先队列 + 定时任务 | 任务到期才能取出        |
| SynchronousQueue      | 无容量              | 一进一出，线程直接交接  |
| LinkedTransferQueue   | 链表                | 高性能无界队列（JDK7+） |

### 3️⃣ 常用方法

| 方法         | 功能                            |
| ------------ | ------------------------------- |
| `put(E e)`   | 队列满则阻塞                    |
| `take()`     | 队列空则阻塞                    |
| `offer(E e)` | 插入成功返回true，失败返回false |
| `poll()`     | 取元素，无则返回null            |

📘 **应用场景：**

- 线程池任务队列
- 生产者/消费者模型
- 延迟任务执行

------

## 四、ThreadLocal（线程本地变量）

### 1️⃣ 作用

每个线程都维护自己的变量副本，解决多线程共享数据冲突问题。

```
ThreadLocal<Integer> local = new ThreadLocal<>();
local.set(10);  // 每个线程独立的值
Integer val = local.get();
```

### 2️⃣ 实现原理

- 每个线程（Thread对象）内部有一个 `ThreadLocalMap`；
- key = ThreadLocal对象引用；
- value = 当前线程的局部变量值；
- 不同线程拥有各自独立的 ThreadLocalMap。

📘 **优点：**

- 避免加锁；
- 提高并发性能；
- 常用于保存用户会话信息、事务ID、数据库连接。

📘 **缺点（内存泄漏风险）：**

- ThreadLocalMap 的 key 使用弱引用；
- 若 ThreadLocal 对象被 GC 回收，但 ThreadLocalMap 未清理，value 会滞留（泄漏）。

**解决：**

- 用完后显式调用 `remove()`：

```
local.remove();
```

## 五、Concurrent 集合家族速查表

| 类名                  | 线程安全方式       | 底层结构             | 适用场景     |
| --------------------- | ------------------ | -------------------- | ------------ |
| ConcurrentHashMap     | CAS + synchronized | 数组 + 链表 + 红黑树 | 高并发Map    |
| CopyOnWriteArrayList  | 写时复制           | 数组                 | 读多写少     |
| CopyOnWriteArraySet   | 写时复制           | 数组                 | 去重版List   |
| ConcurrentLinkedQueue | 无锁 + CAS         | 链表                 | 高并发队列   |
| BlockingQueue         | 锁/条件变量        | 多种结构             | 生产者消费者 |
| ConcurrentSkipListMap | 跳表（有序）       | 链表+索引层          | 有序Map      |
| ConcurrentSkipListSet | 跳表               | LinkedSkipList       | 有序Set      |

------

## 六、内存模型与可见性补充（JUC 面试常挂钩）

**JMM（Java Memory Model）三大特性：**

| 特性   | 解释                                 | 举例                       |
| ------ | ------------------------------------ | -------------------------- |
| 可见性 | 一个线程修改变量，另一个线程能否看到 | `volatile` 保证            |
| 有序性 | 指令重排序的禁止                     | `volatile`、`synchronized` |
| 原子性 | 操作不可被中断                       | `synchronized`、`Lock`     |

📘 **volatile 关键字作用：**

1. 保证可见性；
2. 禁止指令重排；
3. 不保证原子性。

## 七、面试高频追问速记🔥

| 题目                                           | 要点速答                                                     |
| ---------------------------------------------- | ------------------------------------------------------------ |
| ConcurrentHashMap 为什么快？                   | 局部锁 + CAS + 树化结构 + 分段扩容                           |
| CopyOnWriteArrayList 适合什么场景？            | 读多写少，如配置缓存、白名单                                 |
| ThreadLocal 为什么会内存泄漏？                 | ThreadLocalMap 的 key 是弱引用，但 value 不是；不手动 remove 会残留 |
| ConcurrentHashMap 为什么不用 ReentrantLock？   | 局部 synchronized 更轻量 + CAS 更快                          |
| BlockingQueue 的底层实现？                     | 通过锁 + Condition 控制 put()/take() 阻塞唤醒                |
| ConcurrentSkipListMap 与 TreeMap 区别？        | TreeMap 红黑树、单线程；SkipList 多层链表、有序且线程安全    |
| HashMap 的线程安全版本有哪些？                 | `Collections.synchronizedMap()`、`ConcurrentHashMap`         |
| JDK 1.8 ConcurrentHashMap 扩容如何多线程协作？ | 使用 transferIndex 分配任务区间，多个线程并行迁移桶数据      |

## 八、实战类场景题（中级必问）

### 🧩 1️⃣ 生产者-消费者模型实现：

```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);

Thread producer = new Thread(() -> {
    try {
        for (int i = 0; i < 10; i++) {
            queue.put("item-" + i);
            System.out.println("Produced: item-" + i);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

Thread consumer = new Thread(() -> {
    try {
        while (true) {
            String item = queue.take();
            System.out.println("Consumed: " + item);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

producer.start();
consumer.start();
```

------

## 九、终极记忆口诀表

| 场景           | 推荐集合              | 特点                 |
| -------------- | --------------------- | -------------------- |
| 高并发读写 Map | ConcurrentHashMap     | 分桶 + CAS + 树化    |
| 高并发队列     | ConcurrentLinkedQueue | 无锁队列             |
| 任务调度       | DelayQueue            | 时间到期触发         |
| 读多写少       | CopyOnWriteArrayList  | 写时复制             |
| 有序高并发 Map | ConcurrentSkipListMap | 跳表结构             |
| 一进一出队列   | SynchronousQueue      | 无缓冲，线程直接交接 |
| 线程本地变量   | ThreadLocal           | 每线程独立副本       |