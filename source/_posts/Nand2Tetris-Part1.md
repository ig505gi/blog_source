---
title: Nand2Tetris Part1
tags: 
  - Coursera
  - Computer Science
  - 网课笔记
  - Full Stack
abbrlink: 8986
date: 2022-06-18 12:05:49
---

简介：Coursera网课笔记，从一个门电路开始，搭建一台电脑。
课程地址：https://www.coursera.org/learn/build-a-computer
作业仓库：https://github.com/ig505gi/Nand2Tetris-homework

![课程概览，part1为硬件部分](week1/overview.png)
<!-- more -->

## Week1 布尔运算和逻辑门
### 1.1 运算法则
![布尔运算法则](week1/booleanIdentities.png)

### 1.2 NAND
任何一个布尔函数都可以用只含有AND和NOT操作符的表达式来表示；而NAND是NOT和AND的结合，因此：
`任何一个布尔函数都可以用只含有NAND操作符的表达式来表示`
![NAND的证明](week1/NAND.png)

### 1.3 XOR
![XOR真值表和门电路图](week1/XOR.png)
![XOR的HDL实现](week1/XOR-HDL.png)
ps: HDL: Hardware Design Language

### 1.4 Multiplexor和Demultiplexor
HDL中的subBuses：
![subBuses](week1/subBuses.png)
Multiplexor非常有用，可以从多条数据中选出其中的一条
![Multiplexor](week1/Multiplexor.png)
Demultiplexor可以从一条数据中按照规律解析成多条数据
![Demultiplexor](week1/Demultiplexor.png)
## Week2 加法和算术逻辑单元ALU

### 2.1 半加器和加法器
#### 半加器真值表：
| 输入输出 | a | b | sum | carry |
| ---- | ---- | ---- | ---- | ---- |
| 值 | 0 | 0 | 0 | 0 |
| 值 | 0 | 1 | 1 | 0 |
| 值 | 1 | 0 | 1 | 0 |
| 值 | 1 | 1 | 0 | 1 |
#### 全加器真值表和接口
![全加器](week1/fullAdder.png)

### 2.2 负数和补位
从左起第一位为0的为正数，为1的为负数，x+(-x)=0，如果不考虑进了一位，也就是2^n
![负数的计算](week1/negative.png)

### 2.3 ALU
CPU里的核心计算单元，该项目里设计的比较简单
![ALU概览](week1/ALU.png)
![ALU真值表](week1/ALU真值表.png)

### Week3 内存
### 3.1 Sequential Logic & DFF
序列逻辑可以在一个位置存放所有的信息(这句怎么理解？)
![Sequential Logic](week3/SequentialLogic.png)
DFF: Data Filp Flop
特点：1.自己需要保存一个状态，2.该状态取决于上一个时间unit的输入
![DFF](week3/DFF.png)

### 3.2 Register寄存器
#### 1-Bit Register
内部用一个DFF和一个Mux可以实现。
![1-Bit Register](week3/1-bitRegister.png)
16个1-Bit Register组合在一起成为一个16位寄存器

### 3.3 RAM
Register堆在一起组成RAM，图中的小三角代表是一个Sequential chip
![RAM](week3/RAM.png)
读：设置address为i就好
写：设置address为i，in为v，load为1
#### RAM16K
我们的电脑内存是16K的，如何用HDL来写？
首先实现RAM8.png，tips:
![RAM8实现tips](week3/RAM8.png)
8个16bit-Register组成RAM8,8个RAM8组成RAM64。。。。。
![RAM16K实现](week3/RAM16K.png)

### 3.4 Counter
计数器，内部用DFF实现，每过一个时间单位，能自动加一
![Counter](week3/Counter.png)

## Week4 机器语言
### 4.1 Machine Language Overview
一门机器语言应该有哪些成分？操作符，逻辑运算，流程控制，寻址功能，IO。
* 如果硬件复杂，可以实现乘除法，机器语言可以包含乘除法，如果硬件简单，可以只有加法；
* 同样，如果硬件复杂，可以设计负数，浮点型数据类型。如果硬件简单，只有正数

ps: 这里强调tradeoff，一些功能到底设计在硬件层面还是软件层面，由设计者决定。

### 4.2 Hack Computer
Hack电脑认识的机器语言叫Hack语言，由两位教授设计的。
#### 硬件
![Hack电脑的硬件部分](week4/Hardware.png)
#### 软件
![Hack电脑的软件部分](week4/Software.png)
#### 控制和寄存器
* ROM中加载了Hack语言
* reset按钮一按，程序开始执行

三个寄存器用来实现Hack语言：A M D，目前先不用管CPU里如何设计，第五章会讲
![Hack电脑中的Register](week4/registers.png)

### 4.3 Hack Language
汇编语言时人能看懂的语言，转化成二进制才是机器认识的机器语言
Hack语言含有A命令和C命令
#### A命令
![A命令定义](week4/A1.png)
![A命令转化二进制](week4/A2.png)
#### C命令
![C命令定义](week4/C1.png)
![C命令转化二进制](week4/C2.png)

### 4.3 IO
#### 屏幕
RAM中有一块专门管屏幕的内存screen memory map，hack电脑用512X256像素的黑白屏
![屏幕和RAM](week4/Screen.png)
### 键盘
RAM中有一个16bit-Register管键盘
![键盘和RAM](week4/KBD.png)
![键盘上的键对应的code](week4/KBD-character-set.png)

### 4.4 Hack program
如果程序不终止，有人在ROM后面写入了恶意命令，就会被执行。
如何终止程序：写一个无限循环即可
```
// line 6
@6
0;JMP
```
#### 内置标识符
为了方便编程，内置了一些标识符
![内置标识符](week4/builtInSymbols.png)
#### 分支
使语言更具有可读性，一般为大写字母
![分支](week4/Branch.png)
#### 变量
也是更具有可读性，不用在其他地方声明，一般为小写字母，而分支需要在其他地方声明
![变量](week4/Variable.png)
#### 迭代
用以上功能实现迭代的功能
![迭代](week4/Iteration.png)
#### 指针
A寄存器实际上就是一个指针的功能
![指针](week4/Points.png)


## Week5 电脑架构
### 5.1 整体架构图
![架构图](week5/架构图1.png)
![架构图细节](week5/架构图2.png)

### 5.2 CPU内部具体实现
非常优雅，值得细品
![](week5/CPU实现.png)

## Week6 汇编器Assembler
用汇编器将汇编语言变成机器语言，可以用高等语言去写，前提是有其他电脑被造出来了。
如果没有电脑，只能手工去费时费力的去转化。

汇编器的架构：
![汇编器架构](week6/Assembler.png)

