---
title: 深入React技术栈-Chapter1-2
categories:
  - 技术书籍笔记
tags:
  - React
  - Fronted
  - JavaScript
abbrlink: 3925
date: 2021-11-29 22:36:03
---

## 1.React基础
### React生命周期
![react生命周期](react-lifecircle.jpg)
<!-- more -->
### 无状态组件
1. 无状态组件挂载时只是函数的调用，没有创建实例。
2. 因此findDOMNode和refs都不能用，同时建议，这两个方法尽量别用，会打破封装性。
3. 在能用无状态组件的情况下，尽量用无状态组件

### React之外的DOM操作
尽量不用DOM操作为了保证封装性，但有时有一些操作没办法避免
1. H5的video的play和input的forcus
2. 点击其他区域合上该组件
3. 计算DOM的尺寸
自己的理解：用hooks解决以上2，3问题，保证了一定的封装性？


## 2.事件
### 合成事件的实现机制
事件委派和自动绑定
1. 事件委派：有一个统一的事件监听层，并不会把事件处理函数直接绑定到真实的节点上。
2. 自动绑定：React组件中，每个方法的上下文都会指向该组件的实例，即this自动指向当前组件。

### 三种手动绑定方法
如果用ES6 classes或者纯函数，就没自动绑定了，需要手动绑定：

1.bind方法绑定
```javaScript
return <button onClick={this.handleClick.bind(this, 'tes')}>Test</button>
```
2.构造器内声明
class中的方法不用箭头函数，就必须绑定
```javaScript
// 在constructor中：
this.handleClick = this.handleClick.bind(this);

// 在 class中
handleClick(e) {
    ...
}
```
3.箭头函数
如果使用箭头函数，可以直接这样：
```javaScript
class App extends Component {
    const handleClick = (e) => {
        console.log(e);
    }
    render() {
        return <button onClick={this.handleClick}>Test</button>
    }
}
```
### React合成事件的限制
React 的合成事件系统只是原生 DOM 事件系统的一个子集。它仅仅实现了 DOM Level 3 的事件接口，并且统一了浏览器间的兼容问题。有些事件 React 并没有实现，或者受某些 限制没办法去实现，比如 window 的 resize 事件

### React合成事件和JS原生事件的对比
#### 1. 事件传播与阻止事件传播
原生DOM事件三阶段：1.事件捕获 2.事件处理 3.事件冒泡

而React没有实现事件捕获，因为其在开发中意义不大 (?)
#### 2.事件类型
React 合成事件的事件类型是 JavaScript 原生事件类型的一个子集
#### 3.事件绑定方式
不多说了
#### 4.事件对象
原生 DOM 事件对象在 W3C 标准和 IE 标准下存在着差异。在低版本的 IE 浏览器中，只能使用 window.event 来获取事件对象。而在 React 合成事件系统中，不存在这种兼容性问题，在事件处理函数中可以得到一个合成事件对象。

## 3.表单
### 受控组件
React 受控组件更新 state 的流程:

(1) 可以通过在初始 state 中设置表单的默认值。

(2) 每当表单的值发生变化时，调用 onChange 事件处理器。

(3) 事件处理器通过合成事件对象 e 拿到改变后的状态，并更新应用的 state。

(4) setState 触发视图的重新渲染，完成表单组件值的更新。

## 4.CSS
### CSS模块化
#### CSS模块化的问题
1. `全局污染`:CSS 使用全局选择器机制来设置样式，优点是方便重写样式。缺点是所有的 样式都是全局生效，样式可能被错误覆盖，因此产生了非常丑陋的 !important，甚至 inline !important 和复杂的选择器权重计数表1 ，提高犯错概率和使用成本。Web Components 标准中的 Shadow DOM 能彻底解决这个问题，但它把样式彻底局部化，造成 外部无法重写样式，损失了灵活性。
2. `命名混乱`:由于全局污染的问题，多人协同开发时为了避免样式冲突，选择器越来越复 杂，容易形成不同的命名风格，很难统一。样式变多后，命名将更加混乱。
3. `依赖管理不彻底`:组件应该相互独立，引入一个组件时，应该只引入它所需要的 CSS 样 式。现在的做法是除了要引入 JavaScript，还要再引入它的 CSS，而且 Saas/Less 很难实现 对每个组件都编译出单独的 CSS，引入所有模块的 CSS 又造成浪费。JavaScript 的模块化 已经非常成熟，如果能让 JavaScript 来管理 CSS 依赖是很好的解决办法，而 webpack 的 css-loader 提供了这种能力。
4. `无法共享变量`:复杂组件要使用 JavaScript 和 CSS 来共同处理样式，就会造成有些变量 在 JavaScript 和 CSS 中冗余，而预编译语言不能提供跨 JavaScript 和 CSS 共享变量的这种 能力。
5. `代码压缩不彻底`:由于移动端网络的不确定性，现代工程项目对 CSS 压缩的要求已经到 了变态的程度。很多压缩工具为了节省一个字节,会把 16px 转成 1pc，但是这对非常长的 类名却无能为力

#### CSS与JS变量共享
```css
/* config.scss */ 
$primary-color: #f40;
:export {
  primaryColor: $primary-color;
}
```
```javascript
/* app.js */
import style from 'config.scss';
// 会输出 #F40 
console.log(style.primaryColor);
```
#### css Modules使用技巧
1. 不适用选择器，只用class名来定义样式
2. 不层叠多个class，只用一个class把所有样式定义好
3. 通过composes组合来实现复用
4. 不嵌套

如果在style文件中使用id选择器、伪类和标签选择器，不会被转换或者加前缀

#### react-css-modules库
可以避免重复输入styles.**
```javascript
import React, { Component } from 'react';
import classNames from 'classnames';
import CSSModules from 'react-css-modules';
import styles from './dialog.css';

class Dialog extends Component { 
  render() {
    const cx = classNames({
      confirm: !this.state.disabled, 
      disabledConfirm: this.state.disabled,
    });
    return (
      <div styleName="root">
        <a styleName={cx}>Confirm</a>
        // ... 
      </div>
    ); 
  }
}
export default CSSModules(Dialog, styles);
```
使用 CSS Modules，容易使用 :global 去解决特殊情况，使用 react-css-modules 可写成 <div className="global-css" styleName="local-module"></div>，这种形式轻松对应全局和局部;

## 5.React组件
### 组件通信
* 父向子通信：属性
* 子向父通信：回调
* 跨级通信：在父组件中定义ChildContext,子组件可以直接用this.context拿到对应的值；context可以存在很多级
* 无嵌套关系通信：使用Events模块的事件订阅和发布来实现

### 组件间抽象

#### mixin
##### JS中的mixin
mixin是为了弥补一些老的OOP语言不能实现多重继承的缺点，而采用的另外一个方法
```javascript
// TODO 有空自己实现一下
```
##### React中的mixin
1. 适用于createClass的情况。。比较老了，不再细讲
2. 使用ES6 Classes和decorator，用法：
```javascript
import React, { Component } from 'React';
import { mixin } from 'core-decorators';
const PureRender = { 
  shouldComponentUpdate() {}
};
const Theme = { 
  setTheme() {}
};
@mixin(PureRender, Theme)
class MyComponent extends Component {
  render() {} 
}
```
但是mixin也有命名冲突、破坏原组件的封装性等问题

#### 高阶组件
解释：React组件被包裹，高阶组件会返回一个增强的React组件。
实现高阶组件有两种方式：属性代理和反向继承
##### 属性代理
```javascript
import React, { Component } from 'React';
const MyContainer = (WrappedComponent) => 
  class extends Component {
    render() {
      return <WrappedComponent {...this.props} />;
    }
  }
```
当然，也可以使用装饰器语法。
调用顺序
didmount→HOC didmount→(HOCs didmount)→(HOCs will unmount)→HOC will unmount→unmount

##### 反向继承
```javascript
const MyContainer = (WrappedComponent) => 
  class extends WrappedComponent {
    render() {
      return super.render();
    }
  }
```
特点：渲染劫持，控制state
(大部分高阶组件都应该限制读取或增加state，防止混乱)

#### 组合式组件
![组合式组件](compose.jpg)
组合式的方式意图打破这种关联，寻求单元化，通过颗粒度更细的 基础组件与抽象组件共有交互与业务逻辑的高阶组件，使组件更灵活，更易扩展，也使我们能够 完成对于基础组件的自由支配。

### 组件性能优化
#### 纯函数的定义
1. 给定相同的输入，总返回相同的输出
2. 过程没有副作用
3. 没有额外的状态依赖
理解：
1.好理解。2.入参有个对象，函数内是该对象的引用，对其进行改变，就是所谓的产生了副作用。可以用immutable的概念去解决，用lodash的cloneDeep。3.在方法内不使用共享变量，内部所有变量只在方法的生命周期内存活，拒绝闭包？

#### PureRender
重新实现shouldComponentUpdate方法，对props和state做浅比较，值对比引用(深比较实在太昂贵了)
以下类型，无论如何都会触发pureRender的重渲染：
1. props直接设置为对象或数组
```javascript
<Account style={{color: 'black'}} />
```
因为每次都是重新生成了一个新对象，数组同理；解决：先定义一个变量即可。
2. 设置props方法并通过事件绑定在元素上
```javascript
<input onChange={this.handleChange.bind(this)} />
```
每次会重新生成新函数对象(箭头函数也同理？)。解决：bind放在constructor内
3. 设置子组件
```javascript
<Item>
  <span>Arcthur</span>
<Item/>

// 翻译后 每次都生成了新对象
<Item
children={React.createElement('span', {}, 'Arcthur')}
/>
```
解决：使用Item组件的父组件也要pureRender

#### Immutable
引用赋值的问题：容易改变，复杂系统有隐患。
深拷贝：浪费内存
1. Immutable Data
Immutable Data被创建后，不能更改。如果对原数据修改，会生成一个新的Immutable对象。
Immutable.js库就是这样的理念
2. Immutable的优点
复杂度降低；节省了内存，因为节点是共享的；重做/撤销、复制/粘贴都可以实现，老数据都存着呢；并发安全，对于JS来说好像无关紧要，单线程。。；拥抱函数式编程，其本就是函数式编程的概念。
3. 缺点
api还是和原生map array有区别
4. Immutable 与 PureRender
用immutable的`is`去改造shouldComponentUpdate即可

#### key
`Array<Element>`渲染时，每一个item都要唯一的key，切不建议使用index，建议使用id或shortid

#### 性能检测工具react-addons-perf
使用`Perf.start()`和`Perf.stop()`分析

## 6.动画
后续添加
## 7.自动化测试
后续添加
