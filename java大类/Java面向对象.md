# 🧩 Java 面向对象编程（OOP）核心八股总结

## 一、什么是 OOP（面向对象编程）

> OOP 是一种以“对象”为中心的编程思想，将程序划分为相互协作的对象，每个对象封装数据和行为。

### 🧠 OOP 三大特性

| 特性                     | 定义                                     | 关键点                                          | 好处                           |
| ------------------------ | ---------------------------------------- | ----------------------------------------------- | ------------------------------ |
| **封装 (Encapsulation)** | 把属性和方法封装在类中，对外提供受控访问 | `private` 修饰属性，`public` 提供 getter/setter | 隐藏实现、提高安全性与可维护性 |
| **继承 (Inheritance)**   | 子类继承父类成员，代码复用               | 通过 `extends` 实现单继承                       | 复用代码，体现“is-a”关系       |
| **多态 (Polymorphism)**  | 同一接口，不同实现                       | 父类引用指向子类对象，运行时绑定                | 扩展性强，符合开闭原则         |

📘 **三者关系：**

> 封装是基础 → 继承是手段 → 多态是目标。
>  没有良好的封装，继承容易破坏父类结构；继承是多态实现的前提。

## 二、封装（Encapsulation）

### 📌 定义

将数据（属性）和操作（方法）打包在一个类中，隐藏内部细节，只暴露公共接口。

### ✅ 实现方式

1. 成员变量设为 `private`；
2. 提供 `public` 的 getter/setter；
3. 控制访问修饰符（public/protected/private）；
4. 通过方法封装数据校验逻辑。

### 💡 示例：银行账户

```Java
class BankAccount {
    private double balance;

    public BankAccount(double initialBalance) {
        if (initialBalance >= 0)
            this.balance = initialBalance;
    }

    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }

    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance)
            balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

**优点：**

- 数据安全（防止外部直接修改）；
- 降低耦合；
- 提高可维护性；
- 支持模块化设计。

## 三、继承（Inheritance）

### 📌 定义

子类通过 `extends` 继承父类非私有成员，实现代码复用与层级结构。

### ✅ 特点

| 项目     | 内容                                             |
| -------- | ------------------------------------------------ |
| 关键字   | `extends`                                        |
| 单继承   | Java 只支持单继承                                |
| 访问权限 | 子类继承父类的 `public`、`protected` 成员        |
| 构造器   | 不会被继承，但会被隐式调用                       |
| 关键字   | `super` 调用父类构造或方法                       |
| 私有成员 | 不可继承，但可通过 public/protected 方法间接访问 |

### 💡 示例：

```
class Animal {
    protected String name;
    public void eat() { System.out.println("Animal eats"); }
}

class Dog extends Animal {
    public void bark() { System.out.println("Dog barks"); }
}
```

**优点：**

- 代码复用；
- 层级关系清晰；
- 逻辑自然贴近现实世界。

**缺点：**

- 父类修改可能影响所有子类（强耦合）；
- Java 单继承限制扩展性；
- 子类暴露父类实现（白盒复用）。

📘 **设计原则：**

> 继承用于表达“is-a”关系（狗是一种动物），
>  如果只是为了代码复用，应优先考虑 **组合（composition over inheritance）**。

## 四、多态（Polymorphism）

### 📌 定义

> 同一接口，不同实现。父类引用可指向子类对象，方法调用在运行时动态绑定。

### ✅ 实现条件（三要素）

1. 存在继承/接口实现关系；
2. 子类重写（Override）父类方法；
3. 父类引用指向子类对象。

### 💡 示例：

```
class Animal { void sound() { System.out.println("Animal sound"); } }
class Dog extends Animal { void sound() { System.out.println("Dog barks"); } }
class Cat extends Animal { void sound() { System.out.println("Cat meows"); } }

public class Test {
    public static void main(String[] args) {
        Animal a1 = new Dog();
        Animal a2 = new Cat();
        a1.sound();  // Dog barks
        a2.sound();  // Cat meows
    }
}
```

### ✅ 分类

| 类型       | 特征                    | 绑定时间 |
| ---------- | ----------------------- | -------- |
| 编译时多态 | 方法重载（Overloading） | 编译期   |
| 运行时多态 | 方法重写（Overriding）  | 运行期   |

### ⚙️ 好处与限制

| 优点             | 限制                         |
| ---------------- | ---------------------------- |
| 扩展性、可维护性 | 父类引用不能访问子类特有方法 |
| 提高代码灵活性   | 性能略低（动态绑定）         |

📘 **设计思想：**

> 多态体现了 OCP（开闭原则）：“对扩展开放，对修改关闭”。

## 五、类与对象的关系

| 项目     | 类          | 对象         |
| -------- | ----------- | ------------ |
| 定义     | 模板或蓝图  | 类的实例     |
| 存储     | 方法区      | 堆内存       |
| 内容     | 属性 + 方法 | 实际数据     |
| 创建方式 | 通过类定义  | `new` 实例化 |

> 类是“设计图”，对象是“实物”。
>  多个对象可由同一个类模板创建，互相独立。

## 六、抽象类 vs 接口（Interface）

| 对比项   | 抽象类                       | 接口                                     |
| -------- | ---------------------------- | ---------------------------------------- |
| 关键字   | `abstract class`             | `interface`                              |
| 抽象方法 | 可有也可无                   | Java 8 之前必须有（全抽象）              |
| 具体方法 | 可以有                       | Java 8 起可有默认（`default`）与静态方法 |
| 成员变量 | 可定义任意变量（非 final）   | 只能定义 `public static final` 常量      |
| 构造器   | 可有                         | 不可有                                   |
| 继承     | 单继承（`extends`）          | 多实现（`implements`）                   |
| 接口继承 | 不支持多继承                 | 支持接口多继承                           |
| 适用场景 | 表示“is-a”关系，模板方法模式 | 表示“can-do”能力（规范/功能）            |

📘 **选择原则：**

- 抽象类：关注“是什么”（类型层次），有共同状态；
- 接口：关注“能做什么”（行为约定），强调规范；
- 若仅定义行为规则 → 接口；
- 若要共享实现、状态、构造 → 抽象类。

## 七、构造函数与重载（Constructor & Overloading）

### 📌 定义

构造函数用于在对象创建时初始化状态，与类名相同、无返回值。

### ✅ 重载（Overloading）

> 多个构造函数参数列表不同，实现多种初始化方式。

```java
public class Person {
    private String name; private int age;

    public Person() { this("未知", 0); }
    public Person(String name) { this(name, 0); }
    public Person(String name, int age) {
        this.name = name; this.age = age;
    }
}
```

📘 **注意：**

- 若定义了有参构造，编译器不会自动生成无参构造；
- 可用 `this()` 调用本类其他构造函数；
- 子类构造器会隐式调用 `super()`。

## 八、static 静态成员与实例成员

| 类型     | 属于 | 存储位置 | 调用方式      |
| -------- | ---- | -------- | ------------- |
| 静态变量 | 类   | 方法区   | `类名.变量`   |
| 实例变量 | 对象 | 堆内存   | `对象.变量`   |
| 静态方法 | 类   | 方法区   | `类名.方法()` |
| 实例方法 | 对象 | 堆       | `对象.方法()` |

📘 **规则：**

- 静态方法不能访问非静态成员；
- 实例方法可访问静态与非静态；
- 静态成员所有实例共享。

## 九、final 关键字用法总结

| 用法     | 含义         | 示例                 |
| -------- | ------------ | -------------------- |
| 修饰变量 | 变量值不可变 | `final int x = 10;`  |
| 修饰方法 | 不可被重写   | `final void show()`  |
| 修饰类   | 不可被继承   | `final class String` |

📘 **说明：**

- `final` 修饰引用类型 → 地址不可变，内容可变；
- 常量命名规范：全大写 + 下划线；
- 常用于不可变类（如 `String`）。

## 🔟 面试高频追问与速答模板

| 问题                       | 面试速答                                                     |
| -------------------------- | ------------------------------------------------------------ |
| 封装、继承、多态三者关系？ | 封装是基础，继承是手段，多态是目标。                         |
| 为什么提倡组合优于继承？   | 组合是黑盒复用，耦合低，可灵活扩展。继承是白盒复用，强耦合且单继承限制。 |
| 抽象类 vs 接口的选择？     | 抽象类用于“类型共享与模板”，接口用于“功能规范与多继承”。     |
| 多态的前提是什么？         | 继承关系 + 方法重写 + 父类引用指向子类对象。                 |
| 父类构造器何时调用？       | 子类构造前自动调用 `super()`。                               |
| 静态变量和实例变量区别？   | 静态属于类共享，实例属于对象独立。                           |
| final类能否继承？          | 不行；final方法不能重写，final变量不可修改。                 |