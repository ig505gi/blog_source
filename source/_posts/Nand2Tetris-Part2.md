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

## Week1 Overview和VM part1
### 0. Overview
软件层面，我们如何从高级编程语言一步步转化为机器语言
![高级编程语言到机器语言](week1/从高级到低级.png)


### 1. stack
在VM中 重要的数据结构stack
stack除了我们所熟知的push和pop方法，还有一些可以简化操作的方法
![Stack的方法](week1/stack-function.png)
* `add`: 将栈顶的两个值相加，并放回栈顶
* `neg`: 对栈顶的值求负，并放回栈顶
* `eq`: 比较栈顶的两个值，将是否相等的布尔值放回栈顶
* `or`: 对栈顶的两个值进行逻辑or的操作，并放回栈顶
以此类推。。
![Stack的方法2](week1/stack-function2.png)

### 2. Memory Segment
在内存中存放的值，会分不同的片段
![Memory Segment语法](week1/memory-segment.png)
一共有8种Segment：local, argument, static, constant, this, that, pointer, temp

Memory Segment和stack的push和pop方法，都属于抽象的概念。如何转化为我们的机器语言和RAM交互逻辑？
要借助指针的概念：
![指针的基本思想](week1/pointer.png)

抽象的stack machine：从push到指针再到Hack语言
![stack machine](week1/stack-machine.png)

## Week2 VM part2

![](week1/)