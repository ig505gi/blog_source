---
title: Effective javascript
categories:
  - 技术书籍笔记
tags:
  - JavaScript
  - Fronted
abbrlink: 41893
date: 2021-05-15 11:23:19
---

## chapter1 让自己习惯JavaScript
### 1.ES版本问题及strict mode
ES5引入了严格模式，使用字符串字面量作为指令是为了向前兼容，ES3看到`"use strict"`会直接丢掉。
为了解决严格模式和非严格模式的文件打包在一起的问题，可以用IIFE(Immmediately Invoked Function Expression)来解决

<!-- more -->

### 2.number是浮点型
JS中的数字只有`number`一种类型，是符合IEEE754制定的64位编码数字----doubles
`number`的位运算会隐式地转为32位整数，然后计算。

### 3. 当心隐式的强制转换
很多JS代码不会报错，而是会做强制的转换
```javaScript
3 + true // 4
"hello" + " world" // "hello world"
2 + "3" // "23"
"17" * "5" // 85
"8" | "1" // 9
```
尽量避免使用`valueOf()`
#### 3.1 真值运算(truthines)
JS有7个假值：
`false`/`0`/`-0`/`""`/`null`/`NaN`/`undefined`

### 4.原始类型优于封装对象
JS的5个原始类型
`boolean/number/string/null/undefined`

封装对象的方法，原始类型也可以使用，例如toUpperCase是String的原型对象的方法，而原始字符串可以直接调用：
`"hello".toUpperCase(); // "HELLO"`
这种隐式封装，每次调用都会产生一个新的String对象，因此对其设置属性是不起作用的。

### 5.避免对混合类型使用==运算符
当两个参数属于同一类型时，==和===是没有区别的
当使用==对不同的类型比较时，有强制类型转换规则，记忆比较麻烦，因此最好避免
![强制转换规则](强制转换规则.jpg)

### 6.了解分号插入的局限
JS有自动分号插入技术，因此不用写分号。
分号插入的规则：

1. 分号仅在}标记之前、一个或多个换行之后和程序输入的结尾被插入
2. 分号仅在随后的输入标记不能解析时插入

### 7.视字符串为16位的代码单元序列
![码单元大于等于2](码单元大于等于2.jpg)
因此，一个以上的音乐符号，在字符串中的长度是2
```javascript
"∮ clef".charCodeAt(0); // 55348(0xd834)
"∮ clef".charCodeAt(0); // 56606(0xdd1e)
"∮ clef".charAt(1) === " "; // false
"∮ clef".charAt(2) === " "; // true
```
## chapter2 变量作用域
### 8.尽量少用全局对象
简单理解：容易冲突
### 9.始终声明局部变量
虽然js不声明变量不会报错，但是这个变量直接变成了全局变量，要避免
### 10.避免使用with
with可以避免对对象的重复引用，但是不可靠性大大增加，避免使用
### 11.熟练掌握闭包
事实1. JS允许你引用在当前函数以外定义的变量
事实2. 即使外部函数已经返回，当前函数仍然可以引用在外部函数所定义的变量
事实3. 闭包可以更新外部的值

原理:JS的函数值在内部存储它们可能会引用的定义在其封闭作用域的变量。
```javaScript
function box() {
    var val = undefined;
    return {
        set: function(newVal) { val = newVal; },
        get: function() { return val; },
        type: function() { return typeof val; }
    }
}
var v = box();
b.type(); // "undefined"
b.set(98.6);
b.get(); // 98.6
b.type(); // "number"
```
这基本就是一个Class了，ES6的Class编译之后好像就是这样？
### 12.理解变量声明提升
JS不支持块级作用域，而是函数级作用域
变量提升：
```javaScript
function f() {
    // ...
    {
        // ...
        var x=/*...*/;
        // ...
    }
    // ...
}
// 会转化为以下：
function f() {
    var x;
    // ...
    {
        // ...
        x=/*...*/;
        // ...
    }
    // ...
}
```
异常处理的catch看似块级作用域，但是感觉更像函数，因此是个例外
### 13.使用立即调用的函数表达式创建局部作用域
闭包通过`引用`而不是`值`捕获它们的外部变量

好好理解下面三段代码：
```javaScript
function wrapElements(a) {
  var result = [], i, n;
  for(i = 0, n = a.length; i < n; i++) {
    result[i] = function() { return a[i]; };
  }
  return result;
}
var wrapped = wrapElements([10, 20, 30]);
var f = wrapped[0];
f(); // undefined
```
```javaScript
function wrapElements(a) {
  var result = [];
  for(var i = 0, n = a.length; i < n; i++) {
    result[i] = function() { return a[i]; };
  }
  return result;
}
var wrapped = wrapElements([10, 20, 30]);
var f = wrapped[0];
f(); // undefined
```
这个版本更具有欺骗性，但是由于变量提升，没有块级作用域，i仍然只有一个槽(slot)
想实现的效果：
```javaScript
function wrapElements(a) {
  var result = [], i, n;
  for(i = 0, n = a.length; i < n; i++) {
    (function() {
      var j = i;
      result[i] = function() { return a[j]; };
    })()
  }
  return result;
}
var wrapped = wrapElements([10, 20, 30]);
var f = wrapped[0];
f(); // 10
```
### 14.当心命名函数表达式笨拙的作用域
```javaScript
var f = function abs(a) {
  return a < 0 ? -a : a;
}
f(-2); // 2
abs(-3); // wrong 并没有abs这个变量
// 但是有些JS环境会产生abs，甚至分配多余的内存，都是不符合规范的
```

命名函数表达式的作用域在E3中会表示为一个对象，从而导致跟with语句一样的问题，作用域会受到`Object.prototype`的属性变化。
在E5中修复了这个问题

### 15.当心局部块函数生命笨拙的作用域
函数中可以嵌套函数，但是如果再把函数声明放在if语句块中呢？
在不同的JS运行环境中，可能有不同的解释，因此避免使用

### 16.避免使用eval创建局部变量
eval很强大，但使用起来有安全性，避免如下使用方式：
```javascript
function test(src) {
  eval(src);
  return y;
}

test("var y = 'local';");
```

推荐如下，这样不会对函数内部造成影响：
```javascript
function test(src) {
  (function() {eval(src);})()
  return y;
}

test("var y = 'local';");
```

### 17.间接调用eval函数优于直接调用
大多数函数只能访问定义它们所在的作用域，而eval函数具有访问调用它那时的整个作用域的能力。 （有点绕）

直接调用和间接调用：
```javascript
var x = 'global';
function test() {
  var x = 'local';
  return eval("x"); // 直接调用
}
test(); // "local"
```
```javascript
var x = 'global';
function test() {
  var x = 'local';
  var f = eval;
  return f("x"); // 间接调用
}
test(); // "local"
```

例子是有了，但是不太明白为什么后者优于前者

间接调用还有另外一种方式`(0, eval)(src)`

## chapter3 使用函数

### 18.理解函数调用、方法调用及构造函数调用
在面向对象中，三种概念是不同的。但是在JS中，只是单个构造对象的不同使用模式。
`方法就是特定对象调用的函数`
如果纯函数中使用this，会默认使用全局对象，但ES5中规定的严格模式不会这么做，会报错。
纯函数作为构造函数使用：
```javascript
function User(name, age) {
  this.name = name;
  this.age = age;
}

// 用new操作符能识别构造函数
const person = new User('Jack', 18);
person.name; // 'Jack'
```

### 19. 熟练掌握高阶函数
* 高阶函数是那些将函数作为参数或返回值的函数
* 熟悉掌握现有库中的高阶函数
* 学会发现可以被高阶函数所取代的常见的编码模式

### 20. 使用call方法来自定义接收者来调用方法
需要自定义接收者来调用函数时，也就是说`想改变函数中的this`

```javascript
// 不推荐的方法：
obj.temp = f;
f(arg1, arg2);
delete obj.temp
// 万一obj就有temp呢？
```
call方法支持传入一个接收者来调用函数，相当于改变了this

### 21. 使用apply方法通过不同数量的参数调用函数
当一个函数可接受可变参数时，用apply传入数组

```javascript
var scores = getAllScores(); // 返回一个数组
// average可接受可变参数
average.apply(null, scores);
```
### 22. 使用arguments创建可变参数的函数
如果提供一个便利的可变参数的函数，同时最好提供一个需要显式指定数组的固定参数版本，很容易实现：
```javascript
function average() {
  // average可接受可变参数
  averageOfArray(arguments);
}
```

### 23. 永远不要修改arguments对象
如下实现：
```javascript
function callMethod(obj, method) {
  var args = [].slice.call(arguments, 2);
  retrun obj[method].apply(obj, args);
}

var obj = {
  add: function(x + y) { return x + y; }
}

callMethod(obj, 'add', 17, 25);
```

### 24.使用变量保存arguments对象的引用
主要是注意函数嵌套时，arguments会变，要用外层的arguments时，需要先保存引用

### 25.使用bind方法提取具有确定接收者的方法
```javascript
var buffer = {
  entries: [],
  add: function(s) {
    this.entries.push(s);
  }
}

// wrong
var source = [1, 2, 3];
source.forEach(buffer.add); // entries undefined

// correct
source.forEach(buffer.add.bind(buffer));

// 注意！！
buffer.add === buffer.add.bind(buffer); // false
```
bind是安全的，不会修改add方法，bind方法创建了一个新的方法，里面this指向的buffer

### 26.使用bind函数进行函数柯里化
```javascript
function simpleURL(protocol, domain, path) {
  return protocol + '://' + domin + '/' + path;
}

//优化前
var urls = paths.map(function(path) {
  return simpleURL('http', siteDomain, path);
})

// 优化后
var urls = paths.map(simpleURL.bind(null, 'http', siteDomain));
```

### 27.使用闭包而不是字符串封装代码
这个正常人也不会用eval...

### 28.不要信赖函数对象的toString()方法
如题，因为在不同JS引擎中结果不同

### 29.避免使用非标准的栈检查工具
arguments.caller虽然能告诉你函数的调用者，如果用它来实现栈检查工具，在函数调用本身的情况下，会陷入无限循环

## Chapter4 对象和原型
虽然JS支持继承，但不像传统语言，继承不是基于类，而是基于原型。
### 30.31.32.prototype/getPrototypeOf()/\_\_proto\_\_

它们是对立但相关的访问器
* `C.prototype`用于建立由new C()c创建的对象的原型
* `Object.getPrototypeOf(obj)`是ES5中用来获取obj对象的原型对象的标准方法
* `obj.__proto__`是获取obj对象的原型对象的非标准方法

因此，尽量用`getPrototypeof()`,尽量不去修改`__proto__`

### 33.使构造函数与New操作符无关
如果不使用new而是直接：
```javascript
var u = User('Jack', '18');
```
如果内部用了严格模式，并给this.name赋值，会报错
所以内部函数要考虑到这种情况，判断this是否是undefined，主动new一个

### 34. 在原型中储存方法
如果在对象中储存方法，每个实例都会生成副本，而在原型中，只有一个

### 35. 闭包存储私有变量
对象中的属性很容易通过this获取，如果有私有变量可以利用闭包存储
```javascript
function User(name, passwordHash) {
  this.toString = function() {
    return 'User ' + name;
  };
  this.checkPassword = function(password) {
    return Hash(password) === passwordHash;
  }
}
```
但是会牺牲一些，如34的问题，不能将该方法存在原型中。

### 36. 只将实例状态保存在实例对象中

别保存在原型中，不然所有实例会共享，显而易见

### 37. this变量的隐式绑定问题

经常遇到，this的作用域是由最近的封闭函数确定，解决该办法有三种方法

1. `arr.map((v) => this.outerFunc(v), this)` 有很多函数接受第二个参数绑定this
2. `var self = this; arr.map(...` 在外部把this保存一下
3. `arr.map((v) => this.outerFunc(v).bind(this))` 使用bind

### 38. 在子类中的构造函数调用父类的构造函数

假设飞船SpaceShip是游戏中Actor的子类，
```javascript
function SpaceShip(scene, x, y) {
  Actor.call(this, scene, x, y);
  this.point = 0;
}
```
先调用Actor的构造函数，使Actor的实例属性添加到新对象中
后续作为一个正确的子类，SpaceShip的原型必须继承Actor.prototype
```javascript
// ✔️right
SpaceShip.prototype = Object.create(Actor.prototype);

// ❌ wrong
SpaceShip.prototype = new Actor();
```
上述正确做法避免调用了Actor的构造函数。

### 39.不要重用父类的属性名
如题 理解很简单

### 40.避免继承标准类
如果继承标准类，其行为会被特殊的内部标记属性[[Class]]破坏
如果child继承了array，
child是object，不会有array的行为

### 41.将原型视为实现细节
对象是接口，原型是实现 (？如何理解)

### 42.避免使用轻率的猴子补丁
猴子补丁是指，假如Array缺少一个split方法，那么直接`Array.prototype.split = ...`创建一个方法。。

如果非要用，可以考虑封装在一个function中，调用给函数的代码才会修改原型

## Chapter5 数组和字典
### 43.使用Object的直接实例构造轻量级字典

其实就是常用的做法，把一个Object直接当成字典来用
但是文中指出了避免对其原型上增加方法，不然会导致错误(原型污染)

### 44.使用null原型防止原型污染
用Object.create(null)创建空对象，不容易被污染（？）
还是会在属性查找的时候，出现污染现象

### 45.使用hasOwnProperty避免原型污染
这样不会拿到加在原型上的方法
更严谨的：你不知道对象上的hasOwnProperty是否被覆盖了，可以这么办
```javascript
var hasOwn ={}.hasOwnProperty;
hasOwn.call(obj, 'Alice');
```

### 46.使用数组而不要使用字典来储存有序集合
很简单明了，对象的key是无序的

### 47.绝不要在Object.prototype中增加可枚举的属性

如果想增加方法给原型，会污染for...in方法，
以下方式可以解决这个问题：
```javascript
Object.defineProperty(Object.prototype, 'allKeys', {
  value: function() {
    var result = [];
    for (var key in this) {
      result.push(key);
    }
    return result;
  },
  writable: true,
  enumerable: false,
  configurable: true
})
```

### 48.避免在枚举之间修改对象
* 使用for...in的时候一定不要修改可枚举对象，会出现意外情况。
* 可改用for循环或者while
* 为了在一个可变的枚举对象中遍历，可以考虑再用一个额外的数组去保存信息

### 49.数组迭代优先使用for循环而不是for...in
```javascript
var scores = [98, 74, 85, 77, 93, 100, 89];
var total = 0;
for(var score in scores) {
    total += score;
}
var mean = total / scores.length;
mean; // ?
```
第一层❌：以上代码看似求平均值，得出88；
第二层❌：for...in拿的是index，所以是0+1+2。。。total=21,mean=3;
实际✔️：for...in拿的确实是index，但是是string，所以得到了total='0123456', 强制类型转化为了123456/7,得到17636.57....

解决：用普通for循环即可
```javascript
// 更好的代码
for(var i = 0, n = scores.length; i < n; i++) {...}
```
提前计算出n，避免长度变化的影响

### 50.迭代方法优于循环

* 因为自己写for循环很容易写错初始条件，尽量用数组的forEach,map,filter,every,some等迭代方法。
* 同样的，对自己定义的抽象对象，也可以尽量实现迭代方法去遍历。

### 51.在类数组对象中复用数组方法

举个例子：
```javascript
function highlight() {
    [].forEach.call(arguments, function(widget){
        widget.setBackground('yellow');
    });
}
```
像arguments、DOM中的Nodelist，都没有继承JS的Array原型，但是是类数组

类数组定义：
![类数组定义](类数组定义.png)
这个例子不满足第二条：`var arraylike = {0: 'a', 1: 'b', length: 2}`
因为，length不能自动变化

但是Array的几乎所有方法和length变化无关，因此arraylike可以用，除了concat

### 52.数组字面量优于数组构造函数
使用这种字面量语法：`var list = [1, 2, 3]`
优于这种构造函数语法：`var list = new Array(1, 2, 3)`

因为Array可能有人修改，可能有人包装过。。。
(说得有道理，但是不常见)


## Chapter6 库和API设计

### 53.保持一致的约定
* 变量命名和函数签名尽量一致
* 参数顺序尽量一致，比如先宽度，后高度
* 优秀的库都有完善的文档，如果文档保持一致，用户面对一些常见的任务就不用不停得看文档

### 54.将undefined看作“没有值”
* 避免使用undefined表示某一非特定值
* 不要用undefined表示特定标志
* 提供的默认参数用undefined检查，而不是arguments.length
* 在允许0、NaN、''存在的情况，避免用真值测试(width || 300)

### 55.接受关键字参数的选项对象
多参数虽然简单，但参数多的时候难以记忆:
`new Person(18, 'Bob')`
选项对象，不用记忆顺序：
`new Person({age: 18, name: 'Bob'})`

* 用选项对象使API可读性提高，减少记忆
* 一般所有的选项对象都是可选的
* 用extend函数(lodash.merge也可以吧？)抽象从选项对象中取值的逻辑

### 56.避免不必要的状态

函数内部不保存状态，输入参数相同时，输出也相同。个人理解也就是纯函数。
更加模块化、减少不同部分的影响、使代码更易于阅读。
一个著名的有状态的API是Web的Canvas库，一开始学的时候确实费劲。。

### 57.使用结构类型设计灵活的接口

举个例子，以下代码是我们将实现的功能：
```javascript
// Wiki.formats.MEDIAWIKI为格式化器
var app = new Wiki(Wiki.formats.MEDIAWIKI);
// Wiki将格式化函数存储在了wiki实例对象的内部
function Wiki(format) {
    this.format = format;
}
// 每次渲染页面时，会调用Wiki的displayPage方法，内部实现如下：
Wiki.prototype.displayPage = function(source) {
    var page = this.format(source);
    var title = page.getTitle();
    var author = page.getAuthor();
    var output = page.toHTML();
    // ...
}
```

#### 方法一：使用类去实现格式化器

创建一个Page基类，再实现一个继承Page的MWPage类(其他格式也都为Page的子类)，然后MEDIAWIKI则是一个返回MWPage实例的一个“工厂函数”，代码如下：
```javascript
function MWPage(source) {
    Page.call(this, source);
    // ...
}

// MWPage extends Page
MWPage.prototype = Object.create(Page.prototype);

MWPage.prototype.getTitle = /* ... */;
MWPage.prototype.getAuthor = /* ... */;
MWPage.prototype.toHTML = /* ... */;

Wiki.formats.MEDIAWIKI = function(source) {
    return new MWPage(source);
}
```

确实是OOP的做法，但问题是调用的3个方法都是自己实现的，并未从Page继承任何有用的代码。

#### 方法二：结构类型
```javascript
Wiki.formats.MEDIAWIKI = function(source) {
  // ... 
  return {
    getTitle: function() { /* ... */ },
    getAuthor: function() { /* ... */ },
    toHTML: function() { /* ... */ },
  };
};
```
只要displayPage方法结构正确，具有预期的`getTitle`、`getAuthor`、`toHTML`方法，那么这个格式化器就OK；比PAGE类更加灵活。

这种接口有时候称为结构类型或鸭子类型，优雅的轻量的编程模式。

### 58.区分数组对象和类数组对象
有这么一个方法，支持入参为 一个数组或一个对象，也就是实现了方法的重载。那么就必须在方法内部去判断这个入参是数组还是对象，这时候就要考虑类数组的概念了（51条），类数组是一个对象但是可以被视为数组，所以如何区分？因此引出了如下规则：
* API绝不应该重载与其他类型有重叠的类型
不完美的方案：`if (x instanceof Array)`。跨frame通信时，会有多个Array的原型副本，是不同的。
更好的方案：`if (Array.isArray(x))`。

### 59.避免过度的强制转换
如题所示，本人几乎不去用强制类型转换。
* 避免强制类转换和重载的混用
* 考虑防御性地监视非预期的输入(对入参进行类型判断)
如果用TS多了，这种问题基本上不会有。。

### 60.支持方法链
无状态的API的好的例子：String的`replace`方法、Array的`map`、`filter`方法。
有状态的API也值得支持，在更新对象时返回this。
```javascript
element.setBackgroundColor("yellow")
       .setColor("red")
       .setFontWeight("bold");
```

## Chapter7 并发
### 61.不要阻塞I/O事件队列
* 主要是用异步函数和回调去避免阻塞。
* JavaScript并发地接收事件，但会使用一个事件队列按序地处理程序
* 最重要的原则：`绝不要在应用程序事件队列中使用阻塞I/O的API`

### 62.在异步序列中使用嵌套或命名的回调函数
* 使用嵌套或者命名的回调函数按顺序地执行多个异步操作。（合理的处理回调地狱。。）
* 尝试在过多的嵌套的回调函数和尴尬的命名的非嵌套回调函数之间取得平衡。
* 避免将可被并行执行的操作顺序化

### 63.当心丢弃错误
* 写共享的错误处理函数来避免复制和粘贴错误处理代码
* 确保异步操作的所有错误情况都处理了，避免丢弃错误

### 64.对异步循环使用递归
想要实现的效果：一个文件下载完，继续下另一个，但给的是一个urls。
```javascript
function downloadOneAsync(urls, onsuccess, onfailure) {
  var n = urls.length;
  function tryNextURL(i) {
    if (i >= n) {
      onfailure("all downloads failed");
      return;
    }
    downloadAsync(urls[i], onsuccess, function() {
      tryNextURL(i + 1);
    });
  }
  tryNextURL(0);
}
```
* 循环不能异步
* 使用递归在时间循环的单独轮次中执行迭代
* `在回调函数中执行递归，不会导致栈溢出`

### 65.不要在计算时阻塞事件队列
JS代码在进行长时间计算时，会使web页面无响应。
#### 方案1：Worker API
有一个技术可以解决Web平台的Worker API，以下场景，比如一个要搜索大量可移动距离的人工智能游戏，如果在单线程环境下，会卡顿无响应。
```javascript
// main.js
// 产生一个新的线程独立的事件队列的并发执行线程。
var ai = new Worker("ai.js");

// 主线程需要ai的数据时，通过postMessage的方式交互
var userMove = /* ... */;
// 需要重新计算用户移动
ai.postMessage(JSON.stringify({
  userMove: userMove
}));

// 处理worker线程的响应
ai.onMessage = function(event) {
  excuteMove(JSON.parse(event.data).computerMove);
}
```
AI的代码
```javascript
self.onMessage = function(event) {
  var userMove = JSON.parse(event.data).userMove;
  var computerMove = computeNextMove(userMove);
  self.postMessage(JSON.stringify({
    computerMove: computerMove
  }));

  function computeNextMove(userMove) {
    // ...
  }
}
```

#### 方案2：分解算法。组成可管理的工作块
搜索社交网络图的算法，改造前的算法while循环代价过高，会阻塞。如果用方案1，还要复制整个网络图的状态或在worker中存储网络图的状态，传递消息来更新和查询网络。
```javascript
// 改造前
Member.prototype.inNetwork = function(other) {
  var visited = {};
  var worklist = [this];
  while (worklist.length > 0) {
    var member = worklist.pop();
    // ...
    if (member === other) { // found
      return true
    }
    // ...
  }
  return false
}

// 改造后
Member.prototype.inNetwork = function(other, callback) {
  var visited ={};
  var worklist = [this];
  function next() {
    if (worklist.length === 0) {
      callback(false);
      return;
    }
    var member = worklist.pop();
    // ...
    if (member === other) { // found
      callback(true);
      return;
    }
    // ...
    setTimeout(next, 0); // schedule the next iteration
  }
  setTimeout(next, 0); // schedule the first iteration
}
```
利用回调函数拿到异步查询的结果，查询过程中不会阻塞。
但如果在一个`应用程序事件队列`中只执行算法的一个迭代，有点杀鸡用牛刀，可以稍微调整下，在next函数中加一个循环，`每进行10次循环再setTimeout(next, 0)`

### 66.使用计数器来执行并行操作
并行发生的事件，回调函数的执行顺序是未知的，注意返回结果的顺序。
解决：用数组的index去保存结果(JS不会报index出边界的错误。。)，用计数器保证所有成功的时候回调。
```javascript
function downloadAllAsync(urls, onsuccess, onerror) {
  var pending = urls.length;
  var results = [];
  if (pending === 0) {
    setTimeout(onsuccess.bind(null, result), 0);
    return;
  }

  urls.forEach(function(url, i) {
    downloadAsync(url, function(text) {
      if (result) {
        result[i] = text;
        pending--;
        if (pending === 0) {
          onsuccess(result);
        }
      }
    }, function(error) {
      if (result) {
        result = null;
        onerror(error);
      }
    })
  });
}
```

### 67.绝不要同步地调用异步的回调函数
* 即使可以立即得到数据，也绝不要同步地调用异步回调函数。
* 同步地调用异步的回调函数扰乱了预期的操作序列，并可能导致意想不到的交错代码。
* 同步地调用异步回调函数可能导致栈溢出或错误地处理异常
* 使用异步的API，比如setTimeout去调度异步回调函数

### 68.使用promise模式清洁异步逻辑
promise经常用，就不细说了
* promise代表最终值，即并行操作完成时最终产生的结果
* 使用promise组合不同的并行操作
* 使用promise模式的API避免数据竞争
* 在要求有意的竞争条件时使用select(早期的API吧，后期有race)