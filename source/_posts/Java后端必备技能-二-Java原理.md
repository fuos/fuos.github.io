---
title: 'Java后端必备技能(二):Java原理'
tags:
  - Java
  - 面试
categories:
  - 面试
abbrlink: fc5e4d
date: 2024-07-29 09:29:47
cover: https://fuos.github.io/picx-images-hosting/20240729/cover2.54xnvmb0z0.webp
---

{% note orange 'fas fa-magic' %}

这是一个系列文章，完整列表在这里：

{% post_link Java技术面试必备技能 ' 🚀 Java后端面试必备技能列表' %}

Java Version：如果没有特别说明，那么默认Java8，hotspot虚拟机。

{% endnote %}

***

这一篇讲下你编写的`.Java`文件，最终是如何被翻译成`机器码`在CPU上执行的，顺便讲明白了四个常问面试八股：
 1. Java源代码如何翻译成机器码？ --- Java编译原理
 2. Java源代码在JVM中是如何运行的？ --- Java执行过程
 3. Java类是如何加载到JVM的？ --- JVM类加载机制
 4. Java如何保证类加载的唯一性和安全性？ --- Java双亲委派模型
# 高级语言
Java是一门高级开发语言。不是说learning Java OR be a Javaer高级，也不是说哪种开发语言好哪种不好，而是*语言的抽象程度*。记住一句话：越需要考虑操作系统的编程语言级别越低。
## 举个例子
在[stackoverflow](https://stackoverflow.com/questions/3468068/whats-the-difference-between-a-low-level-midlevel-and-high-level-language)看到一个很好的回答，他通过对比不同开发语言特点，非常直观的让你感受到开发语言的高级和低级：
![img_20240729170224](https://fuos.github.io/picx-images-hosting/20240729/img_20240729170224.54xnw20s32.webp)
## 各自特点
### 低级语言
一般都需要较长的开发时间，对操作系统和硬件指令有一定的要求，需要直接对底层操作，代码执行效率高。
### 高级语言
对开发人员更友好，通常借助高层抽象底座（比如JVM）提高开发效率，但是代码执行效率不如低级语言。
# Java编译原理
编译，就是把便于人书写、理解、记录的高级语言`翻译`成计算机能够运行的低级语言。
> Javaer执行 -> Java源代码（.java） -> Java字节码（.class）-> 机器码（01代码） -> OS执行
## Java源代码
请使用宇宙最强[IDEA](https://www.jetbrains.com/idea/)开发Java项目！！！你编写的Java代码是一个TestDemo.java文件，这就是源码，一般放在src目录下。
这个文件实际上就是一种**高级语言**表达方式，他符合Java语言规范，他通过Java语法表达程序要做什么。但是这个.java对于JVM来讲它是不能执行的，他只能执行.class文件。
所以接下来就需要看下Java源码是如何变成字节码文件的：
TestDemo.java  ➡️  TestDemo.class
## Java字节码
一个.java文件是通过执行编译命令生成.class文件的。当我们编写完.java文件，在IDEA中点击`run`或者`debug`后，如果代码没有错误，那程序就会在自动编译后直接运行。下面就是编译之后的.class文件，如果你使用IDEA打开会发现就是.java里面的内容，因为IDEA会反编译：
![image](https://fuos.github.io/picx-images-hosting/20240729/image.b8t0267iv.webp)
编译的过程实际上是执行了`javac`，可以指定很多指令。对于使用IDEA编译来说，他自动做了很多其他工作，比如分析、加载项目配置，构建类路径，调用`javac`编译，执行增量编译等。
## javac的原理
执行javac后，源代码被编译成.class文件，也就是字节码文件，字节码文件是JVM执行的文件，由JVM再解释执行（翻译成机器码）。下面这幅图，描述了.java变成.class都做了哪些事：
![byyl](https://fuos.github.io/picx-images-hosting/20240729/byyl.7p3i8t4gyh.svg)
整个编译过程，由前端翻译和后端翻译组成：
### 前端编译
执行javac命令，经过词法分析、语法分析、语义分析等，生成字节码（.class）。字节码是JVM的中间表示，平台无关，便于移植。
### 后端编译
由JVM内部执行，负责优化字节码并转换为机器码，以提高运行时执行效率。具体来讲，JVM通过`解释器`逐条执行字节码指令，将字节码翻译成相应的机器码执行，并维护运行状态（程序计数器，本地变量表，操作数栈等）。在解释器执行过程中，JVM会监测字节码执行频率，识别热点代码（频繁执行的代码），使用`JIT编译器`将其直接编译成*机器码*并*优化*（内联分析分析、逃逸分析等），放在内存中供后续直接使用。
## 生成机器码
无论是`解释器`还是`JIT编译器`，最后都会将字节码生成机器码，最终在CPU上面执行，包含具体的寄存器操作和方法调用等。
# Java执行过程
看完上面的内容，你已经知道了一个.java文件首先要经过`前端编译`生成字节码（.class），然后再由JVM经过`后端编译`生成机器码，实现高级语言到低级语言的转变，最终运行在CPU上。这个过程大概可以用下面这幅图表示：
![jvm0919](https://fuos.github.io/picx-images-hosting/20240729/jvm0919.39l33h9glp.svg)
## 执行过程
整个执行过程大致可以描述为三个阶段：
1.`.java`源代码被编译为`.class`文件（**前端编译**）
2.类加载器`ClassLoader`负责将`.class`文件动态加载到JVM运行时数据区域。（上篇有详细讲解JVM各区域作用）
3.执行引擎对字节码文件`.class``解释和执行，并进行内存管理、垃圾回收、栈帧管理等，期间会通过本地方法接口（JNI）调用本地方法库，执行优化代码等。（**后端编译**）
> **tips：**
> JVM通过本地方法接口（JNI）可以访问本地方法库，本地方法库一般都不是java代码写的，可能是C、C++等，提供了操作系统和系统资源相关的方法。
## 类加载器
上面提到：JVM通过类加载器加载.class文件，那加载过程是怎样的呢？首先放出一幅图，下面再解释：
![class0919](https://fuos.github.io/picx-images-hosting/20240730/class0919.6ik715ynfx.svg)
### 类加载过程
类加载过程是将.class文件导入JVM并准备好执行的过程。类的加载主要在图中前三个阶段：加载➡️链接➡️初始化，各阶段的作用如下：
**加载**
*查找和导入类文件*：JVM通过类的全限定名（如com.example.MyClass）来查找对应的类文件（.class文件）。
*生成Class对象*：将类文件的二进制数据读入内存，并在内存中创建一个java.lang.Class对象来表示这个类。
**链接**  --- 分为三个步骤
*验证*：确保类文件的字节码符合JVM规范，并且不会破坏JVM的安全性。验证过程包括格式验证、语义验证等。
*准备*：为类的静态变量分配内存，并将其初始化为默认值（如零值或空值）。此时不执行任何Java代码，只是分配内存。
*解析*：将类、方法、字段等符号引用转换为直接引用。符号引用是指在类文件中使用的字符串形式的引用，而直接引用是指在内存中的实际地址。
**初始化**
执行类构造器`<clinit>`方法，包括静态变量的初始化和静态代码块的执行。
### 类生命周期
上面的图完整的展示了类的生命周期：从类的加载到类的销毁：
*加载➡️链接➡️初始化➡️使用➡️销毁*。
### 双亲委派模型
类加载过程中需要保证不会重复加载，还要保证核心类库加载的安全性，这些要如何保证呢？⬇️⬇️⬇️

Java类加载器通过双亲委派模型来查找和加载类文件，确保类的*唯一性和安全性*。
![sqwp0919](https://fuos.github.io/picx-images-hosting/20240730/sqwp0919.2a4zrca31s.svg)
双亲委派模型要求除了顶层的启动类加载器外，其余的类加载器都应当有自己的父类加载器。
Java采用双亲委派模型来加载类，即类加载器在加载一个类时，首先会委托给父类加载器加载。如果父类加载器无法找到该类，才会尝试自己加载。这种机制*可以避免类的重复加载，并确保核心类库的安全*。