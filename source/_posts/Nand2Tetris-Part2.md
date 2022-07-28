---
title: Nand2Tetris-Part2
categories:
  - CS网课笔记
tags:
  - Coursera
  - Computer Science
  - Full Stack
abbrlink: 28107
date: 2022-07-10 16:28:17
---

简介：Coursera网课笔记，已经在Part1用硬件造出了电脑，准备实现OS、VM、编译器、高级编程语言。
课程地址：https://www.coursera.org/learn/nand2tetris2
作业仓库：https://github.com/ig505gi/Nand2Tetris-homework

![课程概览，part2为上面的软件部分](week1/overview.png)
<!-- more -->

# **Week1 Overview和VM part1**
## 0. **Overview**
软件层面，我们如何从高级编程语言一步步转化为机器语言
![高级编程语言到机器语言](week1/从高级到低级.png)
本周的内容主要是VM的实现part1

### 0.1 VM Overview
VM中应该有四种命令：算术/逻辑、内存访问、分支、函数
![VM part1和part2](week1/VM-part1.png)
本周为part1，作为project7，只会涉及算术/逻辑、内存访问；而分支和函数则在week2作为part2，在project8中实现。


## 1. **Stack & Memory Segment concept**
### 1.1 Stack
在VM中，重要的对象stack，stack除了我们所熟知的push和pop方法，还有一些可以简化操作的方法
![Stack的方法](week1/stack-function.png)
* `add`: 将栈顶的两个值相加，并放回栈顶
* `neg`: 对栈顶的值求负，并放回栈顶
* `eq`: 比较栈顶的两个值，将是否相等的布尔值放回栈顶
* `or`: 对栈顶的两个值进行逻辑or的操作，并放回栈顶

以此类推。。（图中eq应该是x==y）
![Stack的方法2](week1/stack-function2.png)

### 1.2 Memory Segment
在内存中存放的值，会分不同的片段，push和pop主要是在stack和segment之间来回通信的操作。
![Memory Segment语法](week1/memory-segment.png)
一共有8种Segment：local, argument, static, constant, this, that, pointer, temp

## 2. **VM Implement**
### 2.1 指针：如何从VM code到Machine code
Memory Segment和stack的push和pop方法，都属于抽象的概念。如何转化为我们的机器语言和RAM交互逻辑？
要借助指针的概念：
![指针的基本思想](week1/pointer.png)
### 2.2 stack Implement
抽象的stack machine：从push到指针再到Hack语言
* 注意：stack的指针SP，一直指向栈顶。

![stack machine](week1/stack-machine.png)

### 2.3 local/argument/this/that Segment Implement
这四个segment的作用和分工：
* 方法的本地变量或入参放在local和argument中
* 方法可访问到的对象和数组放在this或that中；
* 注意，与stack不同:local/argument/this/that的指针, 一直指向base。

![local实现](week1/local.png)
图中的pop local 2用hack assembly实现：
```
// 必须借助其他内存空间才能完成
@2 // Addr = LCL + 2
D=A
@LCL
D=M+D
@R13
M=D
@SP // SP--, D=*SP
AM=M-1
D=M
@R13 // *Addr = D
A=M
M=D
```
push local 5实现：
```
@5 // addr = LCL + 5
D=A
@LCL
A=M+D
D=M
@SP // *SP = *addr
A=M
M=D
@SP // SP++
M=M+1
```
argument/this/that和local的实现是一致的

### 2.4 constant Segment Implement
constant没有pop操作，也在内存里没有对应的地址
`push constant i` 转化为 `*SP = i, SP++`

### 2.5 static Segment Implement
* static因为要保证去全局唯一，比较复杂，加上文件前缀；16~256
* 文件名+.+数字 确定一个唯一的Label

![static实现](week1/static实现.png)

### 2.6 temp Segment Implement
* 当编译高级语言的时候，可能会需要一些变量暂时存储，我们的VM只提供8个暂时变量 5~12，实现上几乎和local等是一样的。
* 不同点是LCL是指针，`@LCL`是指针操作，要拿出存在LCL位置的值在加上index；而temp是`@5`（5是temp的base），然后`5+index`。

### 2.7 pointer Segment Implement
* 注意：跟其他的不同点，该操作的是this或that指针，而其他操作的都是指针指向地址的值
* pointer为什么需要：之后写编译器的时候才会解释
![为什么要pointer segment](week1/为什么要pointer.png)
pointer的值只有0或1，当0的时候就是访问this，当1的时候，访问that
![pointer segment实现](week1/pointer-segment实现.png)

### 2.8 Hack平台的约定/标准
这些segment在哪里，在Hack平台都是约定好的
![Hack平台的标准VM Mapping](week1/标准VMmapping.png)
用约定好的标识符
![Hack平台的标准VM Mapping的标识符](week1/标准VMmapping2.png)

## 3 **VM translator**
可以用python或者java等语言去写，主要架构如下：
![VM translator架构](week1/translator架构.png)
其中parser的主要API：
![Parser的API](week1/parser类API.png)
![Parser的API补充](week1/parser类API2.png)
codeWriter的主要API：
![CodeWriter的API](week1/codeWriter类API.png)

## 4. **project7**
code now!
# **Week2 VM part2**

![](week1/)