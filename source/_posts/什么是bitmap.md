---
title: 什么是bitmap
tags:
  - 数据结构
  - 面试
  - 布隆过滤器
categories:
  - 面试
cover: https://fuos.github.io/picx-images-hosting/20240820/913AC0c950493225AEB55a495Db79669.1e8j5x8ohr.webp
abbrlink: 51635cb9
date: 2024-08-20 11:28:32
---

在了解bitmap之前，先看下Java中的8个基本数据类型：
![javabasicdatatype](https://fuos.github.io/picx-images-hosting/20240824/javabasicdatatype.7p3j9tjnal.webp)
可以看到，不同数据类型，占用的存储空间也是不一样的。这里主要有两个单位：byte和bit。
## byte
`byte`就是平常说的*字节*，是计算机中*基本存储单位*。可以看到除了boolean，其他基本数据类型都是以byte作为基本存储单位的。计算机在处理数据时，也是以字节来处理和存储的，一个字节通常有8位构成。以无符号数为例，一个字节可以表示从`00000000`到`11111111`的二进制数，即 0 到 255 之间的整数。

## bit
`bit`就是平时说的*位*，也叫*比特*，是计算机中*最小数据单位*，只有两个值：`0`和`1`，可以表示`true`和`false`，上面的boolean占位就是1bit。我们都知道计算机的CPU有32位和64位，这里的位就是bit，你也可以说64位CPU每个时钟周期能处理8字节数据。1byte = 8 bit。

##  字节和表示范围
从上面的图可以看出，int和float都是4个字节，但是都知道int和float能表示的数值范围是不同的。这是因为字节只是规定了数据类型的长度，他们俩都是32位的，但是对于这32位的定义确是不同的：
###  int
`int` 使用32位直接表示一个整数值。
 -   最高位是符号位，`0` 表示正数，`1` 表示负数。
 -   剩余的31位用于存储数值的二进制补码表示。
###  float
`float` 使用IEEE 754标准的单精度浮点格式，由3部分组成：
 - 符号位（Sign bit）：1位，表示正数（0）或负数（1）。
 - 指数位（Exponent）：8位，用于表示数的指数部分，使用偏移量（通常为127）编码。
 - 尾数（Mantissa/Significand）：23位，表示有效数字部分（实际上是24位，因为默认情况下，最高位是1，但不存储）。

> 这里仅仅是用int和float举例，主要是为了说明不同的字节长度和数据结构，组成了不同的数据类型。

##  bitmap
了解了`bit`，现在来看看`bitmap`，也就是平时说的位图，它是用于存储和操作**位**信息的数据结构的*概念术语*，并不是一个具体的类或者数据结构，你可以使用byte[]，int[]，BitSet等方式实现位图。我们刚才说过bit只有两个值0和1，BitSet就是通过二进制位来表示和存储数据的。
###  使用int和bit存储数字13
下面请看一个例子，我将用他来说明bitmap是如何节省存储空间的：假设有一个数字13，我们要怎么存呢，我们首先想到可以用int，用Java表示就是：
```java
public class IntExample {
    public static void main(String[] args) {
        int a = 13; // 直接使用int存储数字13
        System.out.println("Int value: " + a);
    }
}
```
int有4字节，也就是32位，他在内存中存储的结构示意图如下：
![inta](https://fuos.github.io/picx-images-hosting/20240825/inta.5fkirdn1as.webp)
`13` 的二进制表示是 `00000000 00000000 00000000 00001101`，因为 13 = 8 + 4 + 1，对应的二进制位是 2³ + 2² + 2⁰。
你会发现他的空间有很大的浪费，实际上真正使用的空间只有4bit，即低位的1101，如果我们不使用`int`类型存储，我们我们使用bit存储，那就可以节省空间，用Java来表示下：
```java
import java.util.BitSet;

public class BitSetExample {
    public static void main(String[] args) {
        BitSet bitSet = new BitSet(4); // 创建一个长度为4的BitSet
        // 将数字13转换为二进制并存储在BitSet中
        // 13的二进制表示是1101，对应BitSet的第0, 2, 3位为true
        bitSet.set(0); // 设置第0位为true, 对应2^0
        bitSet.set(2); // 设置第2位为true, 对应2^2
        bitSet.set(3); // 设置第3位为true, 对应2^3
        
        System.out.println("BitSet: " + bitSet); // 输出BitSet内容
        System.out.println("BitSet value as integer: " + bitSetToInt(bitSet)); // 将BitSet转为整数输出
    }

    // 将BitSet转为整数的辅助方法
    public static int bitSetToInt(BitSet bitSet) {
        int value = 0;
        for (int i = 0; i < bitSet.length(); i++) {
            if (bitSet.get(i)) {
                value += (1 << i);
            }
        }
        return value;
    }
}
```
上面用到了Java中`BitSet`类，他是用来操作和管理位的集合。我们通过自己记录数字的二进制值存储到bit中，实现了数据的写入和读取，而且只用了4个bit。
###  为什么要定义基本数据类型
那为什么Java还要定义int是4字节32位呢？不是直接用bit也能存？那是因为不同数据类型规定了数字存储的上限和下限，虽然在低位会浪费空间，但是整个int类型大小是有规定的，方便计算机寻址和计算，如果单纯使用bit去存取，就需要自己编写辅助方法对bit进行操作和管理，增加了复杂度。可以理解为基本数据类型是在bit之上标准化的封装📦。
###  bitmap到底怎么用
bitmap不是一个类，他是一个概念，一个技术术语。上面例子中用到的*BitSet是bitmap的具体实现*。下面我们用一个bitmap表示32个数，并表示0-32之间的单数：
![bit032](https://fuos.github.io/picx-images-hosting/20240825/bit032.4uav52sl01.webp)
从0开始，每个位置的顺序就代表一个数，这个位置上的值位0说明不存在，1则说明存在。图上绿色部分表示🈶️，下标数字就是具体的值。
我们用4个byte，也就是1个int的长度表示了16个单数，如果用int存储，那么需要4byte * 16 = 64byte = 64 * 8 = 512bit。
***所以使用bitmap是比较节省存储空间的。***
## QQ号去重问题
QQ号使用`unsigned int`存储，在C++或者C中，他的存储范围是0-2^4*8-1，即0到4,294,967,295。实际上QQ号的范围是10001到4,294,967,295，40亿多个。
我们现在计算下这40亿QQ号码使用int和bit存储空间占用各是多少，大致的估算下，就不去掉前面的号码了，也没多少个：
####  使用int
把40亿个int型数存起来需要的空间：40亿✖️4byte✖️8bit=1280亿bit  大约是 14.9GB
![image](https://fuos.github.io/picx-images-hosting/20240825/image.41xznvu825.webp)
####  使用bit
其实就是要表示0到`2^32-1`个数，使用`2^32-1`个bit存储，大约是0.5GB
![bj1](https://fuos.github.io/picx-images-hosting/20240825/bj1.9rjbzgbu8g.webp)
可以看到通过位图可以节省很多的空间，基本上单机就能做去重。在Java中没有unsigned int，存储范围是`−2^31`到`2^31−1`，所以最大整数位是2,147,483,647，真要存40亿需要用long类型。下面是一个示例代码，示范如何设置对应的bit位置，如何去重：
```Java
import java.util.BitSet;

public class QQNumberDeduplication {
    public static void main(String[] args) {
        // 计算需要的 BitSet 的大小。2^32 - 1 是最大的 QQ 号减去最小的 QQ 号10001得到偏移量，再加1。
        int bitSetSize = (int) (Integer.MAX_VALUE - 10001 + 1);
        BitSet bitSet = new BitSet(bitSetSize);

        // 示例 QQ 号码列表
        int[] qqNumbers = {10001, 10002, 2147473647, 10001, 10003};

        // 去重处理
        for (int qq : qqNumbers) {
            int index = (int) (qq - 10001);
            if (!bitSet.get(index)) {
                bitSet.set(index);
                System.out.println("添加 QQ 号：" + qq);
            } else {
                System.out.println("QQ 号 " + qq + " 已存在，跳过。");
            }
        }
    }
}
```
##  总结
本文从Java基本数据类型出发，讲述了byte和bit的关系，以及bitmap的概念，使用BitSet解决了大量QQ号去重的经典问题。`BitSet` 更适合表示一组布尔标志或需要按位操作的场景，而不是简单的整数存储。后面将说说bitmap的典型应用-`布隆过滤器`的原理和使用场景。