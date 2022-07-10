---
title: 零基础Java-慕课学习笔记
tags:
  - Mooc
  - Java
  - 网课笔记
  - Backend
abbrlink: 43762
date: 2018-12-24 14:53:23
---

简介：慕课网课笔记，从零学习Java语法
课程地址：https://www.icourse163.org/course/ZJU-1001541001?tid=1003042007
作业仓库：https://github.com/ig505gi/JAVA-Course


## 1.IDE使用技巧
Alt+/ 补全

control+/ 注释某一行 （mac下 是 command）

<!-- more -->

shift+↑ 选中光标移动行

eclipse右上角可以切换java模式和debug模式

debug时，step over是跳过函数，step into是进入函数

## 2.输出和输入
### 2.1 string与数字类型转化demo
`system.out.println(2+3+“=2+3=”+2+3)；`

控制台输出：
`5=2+3=23`

### 2.2 格式化输出
println输出回车

print不输出回车

输出两位有效数字
`System.out.printf("%.2f", 7.128741)`

### 2.2 输入
`Scanner in = new Scanner(System.in);`

`in.next()` -->读入一个单词，结束的标志位空格(包括tab和换行)

`in.nextLine()` -->读入一整行

`in.nextInt()` --> 读入一个整型

## 3.定义常量
final

## 4.结合关系及优先级

一般从左向右，而赋值运算从右向左

判断优先级很低，最后做

## 5.强制类型转换

`System.out.println((int)(170.239));`

## 6.最大的数
20！不能算，最大到2e31-1

## 7.循环
### 7.1 for
for循环的（；；）每个分号之间都可以空着，也可以用逗号链接多个句子，是唯一一处可以用逗号链接构成长句子的地方

### 7.2 break/continue
要离开多重循环的时候，可以在需要的for前加上一个限定词 如OUT

然后在多重循环里面 break OUT；即可结束多重循环

continue 同理也可以这样使用
### 7.3 for-each循环
```
int[] nums = new int[2]
for(int item : nums){ item = 1;}
```
理解：1.for-each循环的格式与C#不同，要记住。 2。取出item赋值不会改变原数组的值，只有nums[i]调用赋值才能改变
## 8. 数组
### 8.1 数组索引、长度
编译器不检查数组的索引是否有效，只有运行的时候才会出错超出索引范围

获取num[10]数组的长度：`num.length`

### 8.2 数组变量、数组复制
`int[] b = a` 该行代码使得b指向了a所指向的数组，改变a[i]的同时，b[i]也会变，反之也会变

b称之为数组变量，如果比较数组变量，比较的是两个数组变量是否管理一个数组

## 9.字符串
### 9.1 char
char类型是单个字符
```
char ch = 'a';
char c = '字';
```
java用的Unicode编码，汉字也是一个字符

`ch++` --> `b`

char类型加1得到ASCII码表后面的一个字符

`char ch='\u0041'` == `char ch=65` == `char ch='A'`

代表16进制的ASCII码值，即65 -->A

字符可以做加减法，得到的值是int

字符可以比较大小，比较的是ASCII值

### 9.2 逃逸字符
`\b` 回退一格，在Eclipse里面不会执行，需要到console才行。并且只是光标回退一格，接下来的输出从这里开始，不会删除只会被覆盖

`\t` 到下一个表格位
```
System.out.println("123\tabc")
System.out.println("12\tabc")
```
输出：
```
123     abc
12      abc
```

`\r` 回车 `\n` 换行，来源老式打印机，现在使用可能差不多？

## 10.包裹类型
int --> Integer, char --> Character

包裹类型的意义：可以用点运算符，eg：

`Integer.Max_VALUE` 得到int型的最大范围

`Character.toLowerCase('A')` --> a, 可以使用许多方法

## 11.String
### 11.1 定义
注意：String 的S是大写，和其他类型不一样，而是和包裹类型一样

`String s = new String("Hello world")`

### 11.2 比较
String的定义和数组一样需要用new，也就说明了和数组一样定义的是管理者，还不是所有者

因此，`s == "Hello world"`返回的是false

需要用 `s.equals("Hello world")` 返回的是true

### 11.3 索引

用`s.length()` 可以获取长度

遍历的时候跟数组一样 `s[index]`  但是不能用for-each循环

### 11.4 常用操作
`s.compareTo("Hello worlf")` --> 返回 `-2` 一个字符一个字符比较，返回比它小多少

`s.indexOf('e')` --> 返回 `1`

`s.indexOf("ell")` --> 返回 `1` 开始的index

`s.indexOf('a')` --> 返回 `-1`

`s.trim()` 去掉两边的空格

`s.startsWith(t)` 是否以t开始

`s.replace(c1,c2)` 用c2代替c1

`s.toLowerCase()` 变成小写

### 11.5 StringBuffer
```
String re = ""
re += "first"
re += "second"
```
这样的代码对系统开销很大,因为String是一种不可以修改的对象，每一次“+=”操作都会产生一个String对象  
***优化***
```
StringBuffer sb = new StringBuffer()；
sb.append("first");
sb.append("second");
String re = sb.toString();
```

ps：
* 主要记录基本语法和之前学过的语言不太一样的知识点，或者容易忘记的知识点
* 网课老师为浙大翁恺教授，绝对男神！！不仅会教Java，还会教一些很Geek的tips，很实用！
* 在18年学习的，但是未发布在个人博客，2022年整理并同步到个人博客
