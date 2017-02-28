---
title: CSS 绘制气泡
date: 2017-01-12 18:50:32
tags:
---
# 背景
CSS绘制气泡的方式，在文章[《纯CSS实现各类气球泡泡对话框效果》](http://www.zhangxinxu.com/wordpress/2010/03/%E7%BA%AFcss%E5%AE%9E%E7%8E%B0%E5%90%84%E7%B1%BB%E6%B0%94%E7%90%83%E6%B3%A1%E6%B3%A1%E5%AF%B9%E8%AF%9D%E6%A1%86%E6%95%88%E6%9E%9C/)中有主要的介绍，包括“字符法”与“边框法”。而当气泡具有边框线的时候，该方法会不适用，本文主要介绍在具有边框线效果的情况下如何绘制气泡。
# 思路
主要思路是通过添加元素或before after伪元素进行绘制。

# 过程

#### 绘制元素

```
&:before {
	content: ' ';
    width: 20*@unit;
    height: 20*@unit;
    background-color: @background;
    border: solid 1px @border;
    border-right-color: @background;
    border-bottom-color: @background;
}
```
 上述代码绘制出的效果是具有上边框线和左边框线的方块。

#### 对元素进行旋转和定位

```
&:before {
	position: absolute;
	top: -10*@unit;
    left: 50*@unit;
    transform: rotate(45deg);
}
```
#### 效果

<div id="bubble"></div>

<style type="text/css">
#bubble {
    position: relative;
	width: 150px;
	height: 100px;
    border: 1px solid gray;
    border-radius: 4px;
    background-color: red;
}
#bubble:before {
    content: ' ';
    position: absolute;
    width: 20px;
    height: 20px;
    top: -11px;
    left: 50px;
    background-color: red;
    border: solid 1px gray;
    border-right-style: none;
    border-bottom-style: none;
    transform: rotate(45deg);
}
</style>
                
