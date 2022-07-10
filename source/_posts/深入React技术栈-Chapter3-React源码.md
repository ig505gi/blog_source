---
title: 深入React技术栈-Chapter3-React源码
tags:
  - React
  - Fronted
  - JavaScript
  - 读书笔记
abbrlink: 28293
date: 2022-02-21 08:47:40
---

## 1. 初探React源码
React源码的组织结构
![react目录](react目录.jpg)
<!-- more -->
而renderers目录结构是这样的
![react-renderers目录](react-renderers目录.jpg)

client是客户端操作dom的一些方法；server是服务端渲染的实现和方法；event包含有一套自定义的事件插件系统；作为React核心的reconciler，称为协调器，管理组件的实现、生命周期、setState、DOM diff算法;管理Virtual DOM

## 2. Virtual DOM 模型

### ReactNode
Virtual DOM的节点叫作ReactNode
```javascript
type ReactNode = ReactElement | ReactFragment | ReactText;
type ReactElement = ReactComponentElement | ReactDOMElement;
```

// 差一些

### ReactDOMComponent
1. 属性的更新，包括更新样式、更新属性、处理事件等;
2. 子节点的更新，包括更新内容、更新子节点，此部分涉及diff算法。

// 差很多