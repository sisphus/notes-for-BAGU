# 🧠 Java 基础八股文高频考点总结

## 1️⃣ Java 数据类型（重点）

**两大类：**

- ✅ 基本数据类型（Primitive Type）
- ✅ 引用数据类型（Reference Type）

### 🧩 基本数据类型（8种）

| 分类 | 类型    | 位数 | 默认值   | 取值范围 / 特点 |
| ---- | ------- | ---- | -------- | --------------- |
| 整数 | byte    | 8    | 0        | -128 ~ 127      |
| 整数 | short   | 16   | 0        | -32768 ~ 32767  |
| 整数 | int     | 32   | 0        | -2³¹ ~ 2³¹-1    |
| 整数 | long    | 64   | 0L       | -2⁶³ ~ 2⁶³-1    |
| 浮点 | float   | 32   | 0.0f     | 约7位有效数字   |
| 浮点 | double  | 64   | 0.0d     | 约15位有效数字  |
| 字符 | char    | 16   | '\u0000' | Unicode编码     |
| 布尔 | boolean | 1    | false    | 仅 true / false |

**注意：**

- 整数和浮点数统称为 **数值类型**。
- 运算时 `byte、short、char` 自动提升为 `int`。
- `boolean` 不参与类型转换。
- 表达式结果会自动提升为最大类型。

### 🧩 引用数据类型

- 类（Class）
- 接口（Interface）
- 数组（Array）
- 枚举（Enum）

> 存储的是对象的“引用地址”，而非对象本身。

## 2️⃣ final 关键字（高频考点）

| 修饰目标 | 特点                 | 示例 / 含义                          |
| -------- | -------------------- | ------------------------------------ |
| 类       | 不能被继承           | `public final class String {...}`    |
| 方法     | 不能被重写，但可继承 | 通常用于模板方法模式                 |
| 变量     | 只能赋值一次         | 修饰成员变量、局部变量、静态变量均可 |

📘 **注意：**

- `final` 不能修饰构造器。
- `final` 修饰引用类型 → 地址不可变，但对象内容可变。
- `final` 与 `abstract` 不能共存。
- 常量命名一般全大写，用下划线分隔，如：`MAX_VALUE`。

## 3️⃣ 自动装箱与拆箱

| 概念     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| 自动装箱 | 基本类型 → 包装类（`Integer a = 10;` 等价于 `Integer.valueOf(10)`） |
| 自动拆箱 | 包装类 → 基本类型（`int b = a;` 等价于 `a.intValue()`）      |

📘 **注意事项：**

1. 装箱/拆箱会有性能开销；
2. 若包装类为 `null` 拆箱会导致 `NullPointerException`；
3. Integer 常量池：[-128, 127] 区间内共用对象。

## 4️⃣ == 与 equals() 区别

| 比较对象 | ==           | equals()                 |
| -------- | ------------ | ------------------------ |
| 基本类型 | 比较值       | 不可用                   |
| 引用类型 | 比较内存地址 | 可被重写（默认也是地址） |

📘 常见例子：

```
String s1 = new String("abc");
String s2 = new String("abc");
System.out.println(s1 == s2);       // false
System.out.println(s1.equals(s2));  // true
```

## 5️⃣ 访问修饰符

| 修饰符         | 同类 | 同包 | 子类(跨包) | 其他包 |
| -------------- | ---- | ---- | ---------- | ------ |
| public         | ✅    | ✅    | ✅          | ✅      |
| protected      | ✅    | ✅    | ✅          | ❌      |
| 默认(无修饰符) | ✅    | ✅    | ❌          | ❌      |
| private        | ✅    | ❌    | ❌          | ❌      |

## 6️⃣ static 与非 static 区别

| 对比项   | static变量         | 非static变量       |
| -------- | ------------------ | ------------------ |
| 存储位置 | 方法区（类加载时） | 堆内存（实例化时） |
| 生命周期 | 随类加载/卸载      | 随对象创建/销毁    |
| 访问方式 | 类名.变量名        | 对象.变量名        |
| 是否共享 | ✅ 所有对象共享     | ❌ 每对象独立       |

## 7️⃣ 方法重载（Overloading）与重写（Overriding）

| 对比项   | 重载 Overloading             | 重写 Overriding  |
| -------- | ---------------------------- | ---------------- |
| 发生位置 | 同类中                       | 父子类中         |
| 参数     | 不同                         | 相同             |
| 返回值   | 可不同（不能仅靠返回值区分） | 必须相同或协变   |
| 修饰符   | 任意                         | 不能缩小可见范围 |
| 多态类型 | 编译时多态                   | 运行时多态       |

## 8️⃣ 异常处理机制

**关键词：** `try-catch-finally`、`throws`、`throw`

**执行流程：**

```java
try {
    // 可能抛异常的代码
} catch (Exception e) {
    // 处理异常
} finally {
    // 一定执行（资源释放）
}
```

📘 **说明：**

- `throw`：手动抛出异常；
- `throws`：声明该方法可能抛出的异常；
- `finally`：一定会执行（除非 System.exit()）。

## 9️⃣ 泛型（Generics）

**作用：**

- 类型安全
- 代码复用
- 避免强制类型转换

**示例：**

```java
class Box<T> {
    T value;
    void set(T v) { value = v; }
    T get() { return value; }
}
```

**高级用法：**

- 泛型方法：`<T> void print(T t)`
- 上界：`<? extends Number>`
- 下界：`<? super Integer>`
- 类型擦除：运行时擦除类型信息（变成 Object）

## 🔟 String 不可变性（面试必问）

**原因：**

1. `String` 内部 `char[] value` 被 `final` 修饰；
2. 没有修改内容的方法；
3. 每次修改返回新对象；
4. 保证线程安全、安全性、性能（字符串常量池）。

**对比：**

| 类            | 可变性   | 线程安全 | 性能 | 场景           |
| ------------- | -------- | -------- | ---- | -------------- |
| String        | ❌ 不可变 | ✅ 安全   | 较低 | 常量、少修改   |
| StringBuffer  | ✅ 可变   | ✅ 同步   | 一般 | 多线程修改     |
| StringBuilder | ✅ 可变   | ❌ 非同步 | 较高 | 单线程频繁修改 |

## 11️⃣ Lambda 表达式

**语法：**
 `(参数) -> { 方法体 }`

**示例：**

```
List<String> list = List.of("A","B","C");
list.forEach(s -> System.out.println(s));
```

**优势：**

- 简化匿名类；
- 支持函数式编程；
- 与 Stream API 结合使用。

## 12️⃣ Optional 类

**作用：** 解决空指针异常（NPE）

**示例：**

```
Optional<String> name = Optional.ofNullable("Tom");
name.ifPresent(System.out::println);
String result = name.orElse("Anonymous");
```

**方法：**

- `isPresent()` 是否有值
- `orElse()` 若为空返回默认值
- `map()` / `flatMap()` 链式操作

## 13️⃣ JDK 8 新特性（高频）

| 特性          | 简述                               |
| ------------- | ---------------------------------- |
| Lambda 表达式 | 简化匿名内部类                     |
| Stream API    | 声明式集合操作                     |
| Optional 类   | 避免 NPE                           |
| 新日期API     | `LocalDate`, `LocalDateTime`       |
| 默认方法      | 接口中带实现方法                   |
| 函数式接口    | `@FunctionalInterface` 支持 Lambda |
| 方法引用      | `System.out::println` 简化 Lambda  |