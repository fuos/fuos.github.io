---
title: 'Java后端必备技能(一):JVM'
tags:
  - Java
  - JVM
  - 面试
categories:
  - 面试
cover: 'https://fuos.github.io/picx-images-hosting/20240724/Javamust1.wigfg2hhx.webp'
abbrlink: fa087a3f
date: 2024-07-24 18:45:47
---

{% note orange 'fas fa-magic' %}

这是一个系列文章，完整列表在这里：

{% post_link Java技术面试必备技能 ' 🚀 Java后端面试必备技能列表' %}

Java Version：如果没有特别说明，那么默认Java8，hotspot虚拟机。

{% endnote %}

***

我写的东西会比较干，一点也不润~
“干”其实就是没那么多上下文，只有生硬的知识点。写这个些列的重点是为了记录📝知识点和最终结论，关于细节上的解释建议自行探索。

# Java和JVM的关系

## Java特点

1.Java是一种面向对象的服务端开发语言
2.Java所有的方法都必须写在类（class）中
3.Java是一种**编译+解释**型语言
4.在Java中，一切皆**对象**
5.在Java中，只有**值传递**，不存在引用传递
6.Java通过Java内存模型（JMM：原子性，可见性，有序性）保证多线程环境中数据一致性

## JVM特点

JVM的全称是`Java Virtual Machine`，即Java虚拟机，主要负责：
1.解释和执行字节码文件(.class)
2.管理程序内存（堆内存+栈内存）
3.提供垃圾回收机制
4.提供多线程支持

## Java平台无关性

Java语言是平台无关的，主要由下面三个方面给于保证：
1.Java语言规范：保证了Java基本类型在所有操作系统平台上的一致性
2.字节码.class：各操作系统平台使用统一的文件存储格式和JVM交互
3.JVM：保证了在不同操作系统和硬件平台上都能生成对应的二进制指令

## JDK/JRE/JVM关系

JRE：全称是`Java Runtime Environment`，它是运行Java程序所需要的最小组件，包含了JVM和Java核心类库。
JDK：全称是`Java Development Kit`，是开发Java程序的完整工具包，包含了JRE，以及其它Java开发和调试的工具。

JDK
├── JRE
│   ├── JVM
│   └── 核心类库
├── 编译器（javac）
├── 调试器（jdb）
└── 其他工具

# JVM运行时区域

## 组成部分

在Java8中，JVM运行时内存区域，主要由以下几个关键部分组成：
![jvmyxsncqy](https://fuos.github.io/picx-images-hosting/20240725/jvmyxsncqy.8ojlgi7lmm.svg)

### 程序计数器

作用是用来记录当前线程执行字节码指令的地址。每个线程都有它自己的程序计数器，它是线程私有的，是唯一不会OOM的内存区域。
如果当前线程执行的是Java方法，这个计数器中记录的是正在执行字节码指令的地址，如果执行的是native方法，这个计数器的值为undefined。

### Java虚拟机栈

作用是用来管理Java方法的调用和执行，每个线程都有它自己的虚拟机栈，它是线程私有的。
每个方法在执行的时候都会创建一个**栈帧**，用于存储局部变量表，操作数栈，动态链接，方法出口等信息。
![stack](https://fuos.github.io/picx-images-hosting/20240726/stack.7w6pzbio4o.svg)
方法的调用到执行完成的过程，对应着栈帧的入栈和出栈。

```bash
# -Xss<size>：设置每个线程的栈大小
java -Xss1m -jar myapp.jar
```

### 本地方法栈

作用与Java虚拟机栈类似，区别在于：Java虚拟机栈为Java方法服务，*本地方法栈为native方法服务*，一般都是由其他语言（c，c++）编写的，它是线程私有的。

### 堆

作用是存放对象实例和数组，在JVM启动时创建，是虚拟机管理内存中最大的一块区域，也是垃圾回收（GC）的主要区域。它是*所有线程共享*的。
```bash
# -Xms<size>: 设置初始堆大小
# -Xmx<size>: 设置最大堆大小
java -Xms512m -Xmx4g -jar myapp.jar
```

### 方法区

作用是存储已经被虚拟机加载的类信息、常量、静态变量、即时编译器（JIT）编译后的代码等数据。它是*所有线程共享*的。
方法区是Java虚拟机规范中规定的一块逻辑区域，不同版本、不同厂商的JVM实现方式都可能不同。

### 运行时常量池

作用是存放编译期生成的各种字面量和符号引用，以及运行期间动态生成的常量（String类的intern()方法），它是方法区的一部分，这部分内容将在类加载时或运行时被解析。

```java
// 什么是字面量和符号引用？
public class Example {
    public static void main(String[] args) {
        // 字面量：整数、字符串、布尔值
        int number = 42;                // 整数字面量 42
        String greeting = "Hello";      // 字符串字面量 "Hello"
        boolean flag = true;            // 布尔字面量 true

        // 符号引用：类、方法和字段
        Person person = new Person();   // 类符号引用 Person
        person.setName("John");         // 方法符号引用 setName 和字符串字面量 "John"
        String name = person.getName(); // 方法符号引用 getName
        System.out.println(greeting + ", " + name + "!"); // 方法符号引用 println 和字符串字面量 ", " 和 "!"
    }
}
```

### 直接内存

直接内存是操作系统的本地内存，区别于JVM管理的堆内存，也叫堆外内存。它的大小默认和最大堆内存相同，可以通过以下方式设置：

```bash
# -XX:MaxDirectMemorySize=<size>： 设置最大直接内存
java -XX:MaxDirectMemorySize=1g -jar myapp.jar
```

作用是提高Java NIO性能。Java NIO使用通道+缓冲区来提高IO性能，缓冲区可以使用直接内存，减少了数据在堆内存和操作系统间拷贝带来的开销。直接内存不是在JVM堆分配的，所以不受GC影响。

Java NIO中的`ByteBuffer`类提供了一个使用直接内存的示例。通过调用`ByteBuffer.allocateDirect(int capacity)`方法，可以分配一个直接缓冲区。

```java
// 分配一个容量为1024字节的直接缓冲区
ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
```

### 元空间

元空间（metaspace）是*方法区*的具体实现。元空间用于存储类的元数据信息，如类的名称，字段，方法，字节码，常量池等。他的优点是使用`本地内存`，可以自由扩展，只受限于操作系统，减少了OOM风险。
元空间初始大小默认值为21M，最大值受限于操作系统可用内存，但是也可以指定大小：

```bash
# -XX:MetaspaceSize： 设置初始元空间大小
# -XX:MaxMetaspaceSize：设置最大元空间大小
java -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=512m -jar myapp.jar
```

## Java变量类型

看完了上面的JVM运行时区域以及其作用，对于Java中变量类型在哪个区域应该比较清楚了：

```java
// 类变量（静态变量）：--- 方法区
public static int classVariable = 0;
// 类变量使用 static 关键字声明，属于类本身，由所有实例共享。

// 成员变量（实例变量）：--- 堆
public int instanceVariable;
// 成员变量属于类的实例，每个实例都有自己的副本。

// 局部变量：--- Java虚拟机栈
int localVariable = 10;
// 局部变量在方法内部声明，仅在方法执行期间存在。
```

# JVM垃圾回收（GC）

Java语言的一大特点是支持垃圾回收，你不需要手动编写垃圾清除代码去释放内存，这一块的工作由JVM进行管理。那既然这些事情都被JVM给做了，为什么我们还要去了解他的机制呢？这是因为垃圾回收期间会发生STW（Stop-The-World），使整个应用程序的线程都停止，直到GC完成，这会影响应用程序的响应时间和吞吐量。

## 谁是垃圾

JVM进行垃圾回收的第一步就是要判断*谁是垃圾*。Java 8通过可达性分析（GC Roots）来判断对象是否存活，死亡对象就是垃圾回收的目标。GC Roots是垃圾回收的起点，从GC Roots直接或间接引用的所有对象，都不会被垃圾回收。

### GC Roots

在Java 8中，GC Roots可以包括以下几类对象：
-   类：由系统类加载器加载的类；还包含对静态变量的引用
-   本地堆栈：存储在本地堆栈上的方法的局部变量和参数
-   活动 Java 线程：所有活动 Java 线程
-   JNI 引用：为 JNI 调用创建的本机代码 Java 对象；包含局部变量、JNI 方法的参数和全局 JNI 引用
-   用作同步监视器的对象
-   JVM 实现定义的特定对象，这些对象不会被垃圾回收。这些对象可能包含重要的异常类、系统类加载器或自定义类加载器
注意，JVM 没有关于哪些特定对象是 GC 根的文档

## 堆分代模型

堆是GC的主要区域，堆分代模型是根据对象的生命周期划分的：
![duifd](https://fuos.github.io/picx-images-hosting/20240726/duifd.8ad5qjvfsh.svg)


### 新生代
新生代占据整个堆内存的1/3，由Eden[80%]+Survivor（from[10%]、to[10%]）。
当eden区满时，会触发Minor GC，也叫Young GC，存活的对象会被移动到其中一个Survivor区，经过多次GC仍然存活的对象会被移到老年代。
新生代中对象生命周期短，需要快速回收，通常使用`标记-复制`垃圾回收算法。

### 老年代
老年代占据整个堆内存的2/3。
老年代存放的是经过多次Minor GC依然存活的对象，或者大的对象直接进入老年代。当老年代空间不足时触发Major GC。
老年代中对象生命周期长，垃圾回收耗时较长，通常使用`标记-整理`垃圾回收算法。

## GC触发条件

### Minor GC
主要用于新生代垃圾回收。当Java应用程序在Eden区分配新对象，而Eden区的空间不足以存放新的对象时，就会触发Young GC。

### Major GC
主要针对老年代垃圾回收。当老年代没有足够的空间存放新生代对象，就会触发Major GC；或者元空间不足，就会触发Full GC，间接触发Major GC。

### Full GC
对新生代+老年代垃圾回收。当老年代空间不足(比如无法保存大对象，无法支持Young GC后对象晋升)，元空间不足，或者调用System.gc()等。

### Mixed GC（仅适用于G1 GC）
对部分新生代+老年代垃圾回收。当老年代占用率达到一定阈值（默认45%）触发；或者定期出发。

## 垃圾收集器

垃圾收集器就是JVM回收垃圾的策略和算法，不同的版本默认策略不同。大致可以分为以下几类：
1.单线程🆚多线程
2.并行收集器🆚并发收集器
3.新生代收集器🆚老年代收集器🆚整堆收集器

| 垃圾回收器         | 串行/并行/并发 | 新生代/老年代 | 算法                     | 设计目标                    | 适用场景                                |
|--------------------|----------------|---------------|-------------------------|-----------------------------|-----------------------------------------|
| Serial             | 串行           | 新生代        | 标记-复制                | 简单、高效                  | 单核或小内存的客户端应用程序              |
| ParNew             | 并行           | 新生代        | 标记-复制                | 短暂停时间                  | 多核处理器，通常与CMS配合使用              |
| Parallel Scavenge  | 并行           | 新生代        | 标记-复制                | 高吞吐量                    | 后台计算、批处理任务，吞吐量优先的场景      |
| Serial Old         | 串行           | 老年代        | 标记-整理                | 简单、高效                  | 单核或小内存的客户端应用程序              |
| Parallel Old       | 并行           | 老年代        | 标记-整理                | 高吞吐量                    | 多核处理器，通常与Parallel Scavenge配合使用 |
| CMS                | 并发           | 老年代        | 标记-清除                | 低延迟                      | 响应时间要求高的服务端应用程序              |
| G1                 | 并发           | 新生代和老年代| 标记-整理和标记-复制      | 可预测的低停顿时间，适合大堆内存| 大堆内存，低停顿时间要求高的应用            |
| ZGC                | 并发           | 新生代和老年代| 标记-整理                | 超低停顿时间，适合超大堆内存 | 超大堆内存，低停顿时间要求高的应用程序      |

### 各版本垃圾收集器比较

**Java8**
新生代：Parallel Scavenge（多线程，标记-复制算法）-- 并行回收
老年代：Parallel Old（多线程，标记-整理算法）。  -- 并行回收

**Java9**
默认垃圾收集器为G1。同时标记CMS为deprecated，并在Java14彻底移除。
G1：将堆内存划分为多个大小相同的region，每个区域可以单独垃圾回收。支持自适应调优。

```bash
# G1调优常用参数
-XX:+UseG1GC # 启动G1
-XX:MaxGCPauseMillis=200 # 设置最大停顿时间
-XX:G1HeapRegionSize=32m # 设置每个region大小
-XX:MaxGCPauseMillis=200 # 设置年轻代GC最大停顿时间
-XX:InitiatingHeapOccupancyPercent=45 # 设置混合GC启动阈值
-XX:G1MixedGCCountTarget=8 #设置每次混合回收最大区域数
```

**Java11**
引入ZGC，设计目的是大内存低延迟垃圾回收。停顿时间不超过10ms，支持TB级别大堆。

### 老年代垃圾回收算法

CMS：`标记-清除`算法回收老年代。（并发回收，低延迟，可能产生内存碎片）

### 整堆垃圾回收算法

G1：`标记-复制`算法回收年轻代，`标记-整理`算法回收老年代。（并发回收，推荐4G以上堆内存使用，Java9可用）
ZGC：高吞吐量的同时保证最短的暂停时间。（并发回收，支持8MB~16TB级别的堆，Java11可用）

### 并行回收和并发回收区别

并行回收：关注吞吐量。多个垃圾收线程同时工作，应用线程被暂停
并发回收：关注STW时长。垃圾收集线程和应用线程同时工作，应用程序不用暂停