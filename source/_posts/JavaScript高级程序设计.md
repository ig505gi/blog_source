---
title: JavaScript高级程序设计
tags:
  - Fronted
  - JavaScript
  - 读书笔记
abbrlink: 53497
date: 2022-03-21 22:04:08
---

## Chapter 1-7 JS的基础知识
### 1 JS的历史
* JS包括ECMA、DOM、BOM
...

### 2 HTML
* HTML的发展，HTML中如何用JS
...
<!-- more -->
### 3 语法
* Number类型的上下限
...
### 4 变量、作用域
* 执行环境是什么，「没有块级作用域」
* 垃圾回收、接触引用

### 5 引用类型
### 6 面向对象
* 设计模式：工厂模式、继承模式
* 原型链

### 7 私有变量
* 闭包
* 模仿块级作用域
* 模块模式 & 单例模式

## Chapter 8 BOM
### 1 window对象
* frame框架，每一层都有个window对象，top指向最高层级的window
* 窗口位置
* 窗口大小
* 导航和打开窗口
* 间歇调用和超时调用(setTimeout/setInterval)
* 系统对话框(alert/prompt/confirm/print/find)

### 2 location对象
* 既是windows的对象，又是document的对象
* location的属性：
![location属性](location属性.png)
* 每次修改location的属性(hash除外)页面都会以新url重新加载
* replace方法不能生成历史记录，也不能返回刚刚的网页

### 3 navigator对象
* 有属性浏览器版本信息、名称、插件、环境等
* 注册处理程序`registerContentHandler()`和`registerProtocolHandler()`方法，跟RSS和在线电子邮件有关

### 4 screen对象
能够拿到一些屏幕的硬件信息：PPI、像素、刷新率等，对于编程意义不大
### 5 history对象
安全的：开发者是不能知道用户访问过那些网页的
方法：go/back/forward

## Chapter 10-12 DOM
DOM（文档对象类型）是针对HTML和XML文档的一个API。
DOM 可以将任何HTML或XML文档描绘成一个由多层节点构成的结构。节点分为几种不同的类型。如下多种：
### 1 节点层次
#### 1.1 Node类型
* 其它节点都继承自Node类型，每个节点都有一个`nodeType`的属性，记录了其类型
* 每个节点都有这样或那样的关系。每个节点都有一个`childNodes`属性，其中保存着一个`NodeList`对象
* 每个节点都有一个`parentNode`属性，该属性指向文档树中的父节点
![Node类型查询属性](Node类型查询属性.png)
* 常用方法`appendChild()`。用于向`childNodes`列表的末尾添加一个节点。
* 常用方法`insertBefore()`。这个方法接受两个参数:要插入的节点和作为参照的节点。
* 常用方法`replaceChild()`。这个方法接受两个参数:要插入的节点和要替换的节点。
* 其他方法`cloneNode()`可以深浅复制；`normalize()`处理文档树中的文本节点。
#### 1.2 Document类型
* document对象是HTMLDocument(继承自Document类型)的一个实例，表示整个HTML页面。
* document对象是Window对象的一个属性，可以将其作为全局属性访问。
* document对象的属性`nodeType`值为9。
* document对象的属性`documentElement`、`firstChild`和`childNodes[0]`的值相同，都指向\<html>元素。
* document作为HTMLDocument实例，有一些不同于Document类型的属性：`title`、`URL`、`domain`、`referrer`。其中title和domain是可以设置的，domain只能修改三级域名(www)
* Document类型都有的方法：`getElementById()`和`getElementsByTagName()`;HTMLDocument类型独有的方法是`getElementsByName()`。
* document有一些集合可以方便拿到一些HTMLCollection：`document.links`拿到所有带href的\<a>元素、`document.forms`拿到所有\<form>元素、`document.images`拿到所有\<img>元素
* `document.implementation.hasFeature("XML", "1.0")` 可以检测DOM的功能及版本号
* document对象具有输出流写入网页的功能：`write()`、`writeln()`、`open()`、`close()`
#### 1.3 Element类型
* 所有的HTML元素都是HTMLElement类型，其继承自Element类型，添加了一些Element没有的属性：id、titile、className等；可以直接像这样`div.id`**通过属性访问**对其属性赋值和取值
* `getAttribute()`、`setAttribute()`、`removeAttribute()`是三个常用的方法
* H5规范规定，自定义特性需要有`data-`前缀；**通过getAttribute方法**可以拿到自定义特性的值；除了IE可以直接用`div.myAttr`这样**通过属性访问**拿到特性的值，其他浏览器都**不可以**
* style属性：**通过getAttribute方法**拿到的是CSS文本，**通过属性访问**拿到的是对象
* onClick属性：**通过getAttribute方法**拿到的是响应代码的字符串，**通过属性访问**拿到的是JS函数
* 通过setAttribute()方法设置的特性，统一会变成小写
* 想要遍历元素的特性，可以使用attributes属性`element.attributes`
* `var div = document.createElement("div");`
#### 1.4 Text类型
* 文本节点，parentNode是一个Element类型；通过`nodeValue`属性或`data`属性访问文本内容，是一致的
* 常用方法：`appendData()` `deleteData()` `insertData()` `replaceData()` `splitText()` `substringData()`
* `div.firstChild.nodeValue = 'aa<s>';` 输出的结果是转义过的，是`aa&lt;s&gt;`
* `document.createTextNode()`创建新文本节点，但是如果不指定其父元素，在页面上是看不到的；

#### 1.5-1.9 其它类型
* Comment类型：parentNode可能是Document或Element类型；有除了splitText以外的Text类型的全部方法
* CDATASection：类型只针对基于XML的文档
* DocumentType类型：在Web浏览器中并不常用，仅有 Firefox、Safari 和 Opera 支持它
* DocumentFragment类型：只有它在文档中没有对应的标记，可以当做一个仓库。比如如果向文档中渲染三个连续的元素，先放到仓库里，可以避免重复渲染三次
* Attr类型：不是DOM文档树的一部分，是元素的特性

### 2 DOM扩展
#### 2.1 选择符API
* querySelector()
* querySelectorAll()
* msMatchesSelector()

...

### 3 DOM2和DOM3
DOM1主要定义的是HTML和XML文档的底层结构
#### DOM2的模块
* DOM2级核心(DOM Level 2 Core):在1级核心基础上构建，为节点添加了更多方法和属性。
* DOM2级视图(DOM Level 2 Views):为文档定义了基于样式信息的不同视图。
* DOM2级事件(DOM Level 2 Events):说明了如何使用事件与 DOM 文档交互。
* DOM2级样式(DOM Level 2 Style):定义了如何以编程方式来访问和改变 CSS 样式信息。
* DOM2级遍历和范围(DOM Level 2 Traversal and Range):引入了遍历 DOM 文档和选择其特定部分的新接口。
* DOM2级HTML(DOM Level 2 HTML):在1级HTML基础上构建，添加了更多属性、方法和新接口。

...

## Chapter 13 事件
JS和HTML之间的交互是通过事件实现的。
