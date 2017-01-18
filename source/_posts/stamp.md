---
title: 邮票锯齿边框实现
date: 2016-08-03 11:20:58
tags: CSS3
css: stamp
---
## 背景

在实现抵用券样式的时候，需要实现类似于邮票的锯齿边框效果，如下图：
<img src="css/images/page/stamp.jpg" style="width:300px" />

之前的实现方式是通过锯齿的png图作为border然后重复作为实现。这样做的缺点是：

* 配色需要与抵用券的背景颜色保持一致，可扩展性较差。
* 锯齿的半径固定，不同分辨率的客户端下，显示效果不一致，适配性较弱。

有没有方法能够直接用css样式直接实现呢？

## 初步思路

初步思路就是在背景的边框上每隔一段间距画圆，圆的圆心位于边框上,就可以实现邮票锯齿效果。

### radial-gradient 实现圆点背景

`radial-gradient` 径向渐变是从一个点向四周的颜色渐变。W3C标准语法如下：


> radial-gradient( \[ \[ < shape > || < size > \] \[ at < position > ]? , | at < position > ,]? < color-stop > \[ , < color-stop > ]+);

#### 主要参数：

`position`
position与background-position或者transform-origin类似。如缺省，默认为中心点。其值主要有以下几种：

* < length >：用长度值指定径向渐变圆心的横坐标或纵坐标。可以为负值。
* < percentage >：用百分比指定径向渐变圆心的横坐标或纵坐标。可以为负值。
* left：设置左边为径向渐变圆心的横坐标值。
* center：设置中间为径向渐变圆心的横坐标值或纵坐标。
* right：设置右边为径向渐变圆心的横坐标值。
* top：设置顶部为径向渐变圆心的纵标值。
* bottom：设置底部为径向渐变圆心的纵标值。

`shape`
渐变的形状。圆形"circle"或椭圆形"ellipse"。默认值为椭圆。

* circle：如果"size"和"length"大小相等，那么径向渐变是一个圆形。
* ellipse：如果"size"和"length"大小不相等，那么径向渐变是一个椭圆形。

`size`
渐变的尺寸大小。

`color-stop`
表示确定位置的固定色值，包含一个< color >值加上可选的位置值（相对虚拟渐变射线的< percentage >或者< length >长度值）。 百分比值0%，或者长度值0，表示渐变中心点；百分比值100%表示渐变射线与边缘形状相交的点。 其间的百分比值线性对应渐变射线上的点。

### 基本用法

```css
div {
    width: 300px;
    height: 300px;
    background-image: radial-gradient(white, red);
}
```
实现效果
<div id="example1"></div>

"size"缺省值为farthest-corner，即渐变到达最远边框。

当颜色的终止值和下一个颜色初始值相同，或下一个颜色初始值为0时，即可画出圆形。
```css
div{
    width: 200px;
    height: 200px;
    background-image: radial-gradient(white 50px, red 0);
    background-size: 300px 300px;
}
```
实现效果
<div id="example2"></div>

利用这种方式，在一个边框重复，即可实现邮票锯齿边框效果。我们设置：
```css
div {
    background-image: radial-gradient(white 5px, transparent 0);
    background-size: 20px 20px;
    background-position: -10px 0;
    background-repeat: repeat-y;
}
```
实现效果
<div id="example3"></div>

其中圆点半径为 "5px" ，锯齿图片大小为 "background-size 20px 20px" ，并向左偏移 "10px" ，在y轴重复，即可实现左边边框的锯齿效果。
## 进一步扩展

目前主要实现了一边锯齿的效果。那么，如何扩展实现4边锯齿效果呢？
我们需要解决一个问题：如何在布局大小不确定的情况下绘制右边以及下边边框？

### CSS3的calc()

calc()可以理解为一个函数function。calc是英文单词calculate(计算)的缩写，是css3的一个新增的功能，用于动态给元素的border、margin、padding、font-size和width等属性设置动态值。
你可以给一个div元素，使用百分比、em、px和rem单位值计算出其宽度或者高度，比如说“width:calc(50% + 2em)”，这样一来你就不用考虑元素div的宽度值到底是多少，而把这个烦人的任务交由浏览器去计算。

### 通过calc()计算右边及下边边框的位置

布局大小不确定的情况下，我们需要确定右边以及下边边框的 `background-position` 。
由于当前的 `background-size` 为20px。因此右边的边框的 `background-position` 的x轴需增加10px；而下边边框的 `background-position` 的y轴需增加10px。
因此，右边和下边边框的 `background-position` 的计算公式分别为 `calc(100%+10px) 0`, `0 calc(100%+10px)` 。
从而得到自适应布局大小的邮票边框样式：

```css
div {
    width: 200px;
    height: 200px;
    background: red;
    background-image: radial-gradient(white 5px, transparent 0),
                      radial-gradient(white 5px, transparent 0),
                      radial-gradient(white 5px, transparent 0),
                      radial-gradient(white 5px, transparent 0);
    background-size: 20px 20px;
    background-position: -10px 0, calc(100%+10px) 0, 0 -10px, 0 calc(100%+10px);
    background-repeat: repeat-y, repeat-y, repeat-x, repeat-x;
}
```
最终效果为：
<div id="example4"></div>





