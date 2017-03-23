---
title: React 源码学习(2) —— Virtual Dom 模型
date: 2017-02-28 19:32:04
tags: React
---
# 前言
本文通过实现一个基本的 Virtual DOM ，使我们深入理解 React Virtual DOM 算法，给我们现有前端的编程提供一些新的思考。

# 为什么需要 Virtual DOM
当我们使用模板或者其他 MVVM 框架的时候，一旦数据发生了变化，就用模版引擎重新渲染整个视图，然后设置一下innerHTML，用新的视图更换掉旧的视图。然后就完事了。

听到这样的做法，经验丰富的你一定第一时间意识这样的做法会导致很多的问题。最大的问题就是这样做会很慢，因为即使一个小小的状态变更都要重新构造整棵 DOM，性价比太低。

最后的结论会是：对于局部的小视图的更新，没有问题（Backbone就是这么干的）；但是对于大型视图，如全局应用状态变更的时候，需要更新页面较多局部视图的时候，这样的做法不可取。但是这里要明白和记住这种做法，因为后面你会发现，其实 Virtual DOM 就是这么做的，只是加了一些特别的步骤来避免了整棵 DOM 树变更。

标准的DOM节点非常庞大，而我们通常需要使用的数据并没有那么多。Virtual DOM 本质上就是在 JS 和 DOM 之间做了一个缓存。可以类比 CPU 和硬盘，既然硬盘这么慢，我们就在它们之间加个缓存：CPU（JS）只操作内存（Virtual DOM），最后的时候再把变更写入硬盘（DOM）。

# Virtual DOM 的数据结构
构建一套简单的 Virtual DOM 模型并不复杂， 只需要具备 DOM 标签的基本元素即可：
* 标签
* 节点熟性，包括样式、属性、事件等
* 子节点
* id

基本结构如下：
```js
{
    tagName: 'p', // tag的名字
    properties: { // 节点包含属性
        style: {
            color: '#fff'
        }
    },
    children: [], // 子节点
    key: 1// 该节点的唯一表示，后面会讲有啥用
}
```
比如：
```js
var element = {
  tagName: 'ul', // 节点标签名
  props: { // DOM的属性，用一个对象存储键值对
    id: 'list'
  },
  children: [ // 该节点的子节点
    {tagName: 'li', props: {class: 'item'}, children: ["Item 1"]},
    {tagName: 'li', props: {class: 'item'}, children: ["Item 2"]},
    {tagName: 'li', props: {class: 'item'}, children: ["Item 3"]},
  ]
}
```
上面对应的HTML写法是：
```html
<ul id='list'>
  <li class='item'>Item 1</li>
  <li class='item'>Item 2</li>
  <li class='item'>Item 3</li>
</ul>
```