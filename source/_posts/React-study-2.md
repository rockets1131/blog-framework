---
title: React 源码学习(2) —— Virtual Dom 模型
date: 2017-02-28 19:32:04
tags: React
---
# 前言
本文从实际需求出发，分析前端模板直接渲染DOM的问题，继而得出 Virtual DOM 的解决方案。

之后，我们通过实现一个基本的 Virtual DOM ，给我们现有前端的编程提供一些新的思考。

关于 Virtual DOM ，将分为 3 篇文章：
1. 为什么需要 Virtual DOM 及其基本数据结构。
2. 当状态变化后，如何比较并记录 Virtual DOM 的差异。
3. 如何将差异应用到真正的DOM树上。

# 为什么需要 Virtual DOM

## Virtual Dom 快的前提
* Javascript很快
* DOM 很慢

当使用document.createElement()创建一个空的Element的时候（比如创建一个空的div），需要实现的接口非常非常多，并且还有不少嵌套引用。

这还只是一个空的 Element，啥内容也没有，就这么复杂。所以说DOM的操作非常慢是由于DOM设计得太复杂，没办法。

## Virtual Dom 的场景
当页面发生数据更新，需要重新渲染页面的时候。有一个非常直观的方法，可以大大降低页面更新的操作：一旦状态发生了变化，就用模版引擎重新渲染整个视图，然后用新的视图更换掉旧的视图，设置一下innerHTML就完事了。

最后的结论会是：对于局部的小视图的更新，没有问题（Backbone就是这么干的）；但是对于大型视图，如全局应用状态变更的时候，需要更新页面较多局部视图的时候，这样的做法不可取，并且可能会产生白屏现象。但是这里要明白和记住这种做法，因为后面你会发现，其实 Virtual DOM 就是这么做的，只是加了一些特别的步骤来避免了整棵 DOM 树变更。

标准的DOM节点非常庞大，而我们通常需要使用的数据并没有那么多。Virtual DOM 本质上就是在 JS 和 DOM 之间做了一个缓存。可以类比 CPU 和硬盘，既然硬盘这么慢，我们就在它们之间加个缓存：CPU（JS）只操作内存（Virtual DOM），最后的时候再把变更写入硬盘（DOM）。

这就是所谓的 Virtual DOM 算法。包括几个步骤：用 JavaScript 对象结构表示 DOM 树的结构；然后用这个树构建一个真正的 DOM 树，插到文档当中当状态变更的时候，重新构造一棵新的对象树。然后用新的树和旧的树进行比较，记录两棵树差异把2所记录的差异应用到步骤1所构建的真正的DOM树上，视图就更新了。

但是这里要明白和记住这种做法，因为后面会发现，其实 Virtual DOM 就是这么做的，只是加了一些特别的步骤来避免了整棵 DOM 树变更。另外一点需要注意的就是，上面提供的几种方法，其实都在解决同一个问题：维护状态，更新视图。在一般的应用当中，如果能够很好方案来应对这个问题，那么就几乎降低了大部分复杂性。

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
既然原来 DOM 树的信息都可以用 JavaScript 对象来表示，反过来，你就可以根据这个用 JavaScript 对象表示的树结构来构建一棵真正的DOM树。之前的章节所说的，状态变更->重新渲染整个视图的方式可以稍微修改一下：用 JavaScript 对象表示 DOM 信息和结构，当状态变更的时候，重新渲染这个 JavaScript 的对象结构。当然这样做其实没什么卵用，因为真正的页面其实没有改变。但是可以用新渲染的对象树去和旧的树进行对比，记录这两棵树差异。记录下来的不同就是我们需要对页面真正的 DOM 操作，然后把它们应用在真正的 DOM 树上，页面就变更了。这样就可以做到：视图的结构确实是整个全新渲染了，但是最后操作DOM的时候确实只变更有不同的地方。
