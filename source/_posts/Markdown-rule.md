---
title: Markdown语法的简要规则
date: 2016-06-17 13:54:32
tags:
---

## 标题

在Markdown中，如果一段文字被定义为标题，只要在这段文字前加 `#` 号即可。

`# 一级标题`

`## 二级标题`

`### 三级标题`

以此类推，总共六级标题，建议在井号后加一个空格，这是最标准的 Markdown 语法。

## 列表

在Markdown中，列表的显示只需要在文字前加上 `-` 或 `*` 即可变为无序列表，有序列表则直接在文字前加 `1.` `2.` `3.` 符号要和文字之间加上一个字符的空格。

## 引用

如果你需要引用一小段别处的句子，那么就要用引用的格式。

`> 例如这样`

只需要在文本前加入 `>` 这种尖括号（大于号）即可。

## 代码

如果你只想高亮语句中的某个函数名或关键字，可以使用 `function_name()` 实现

通常编辑器根据代码片段适配合适的高亮方法，但你也可以用 \`\`\` 包裹一段代码，并指定一种语言

``` javascript
$(document).ready(function () {
    alert('hello world');
});
```

> 支持的语言
actionscript, apache, bash, clojure, cmake, coffeescript, cpp, cs, css, d, delphi, django, erlang, go, haskell, html, http, ini, java, javascript, json, lisp, lua, markdown, matlab, nginx, objectivec, perl, php, python, r, ruby, scala, smalltalk, sql, tex, vbscript, xml

也可以使用 4 空格缩进，再贴上代码，实现相同的的效果

    def g(x):  
        yield from range(x, 0, -1)  
    yield from range(x)
