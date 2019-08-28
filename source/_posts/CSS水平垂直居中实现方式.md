---
title: CSS水平垂直居中实现方式
date: 2019-08-28 09:38:42
tags: CSS
---

水平垂直居中包括行内元素居中，以及块级元素居中

行内元素html结构

```
 <div class="outer">
    <span class="inner"></span>
  </div>
```

<!-- more -->

块级元素结构

```
 <div class="outer">
    <div class="inner"></div>
  </div>
```

基础样式

```
<style>
.outer {
    width: 400px;
    height: 400px;
    border: 1px solid red;
}
.outer .inner {
    width: 50px;
    height: 50px;
    border: 1px solid blue;
}
</style>
```

#### 水平居中

##### 1-行内元素（最简单 text-align: center）

```
.outer {
    text-align: center;
}
```

##### 1-块级元素（margin: auto）

当使用这种方式时，内部元素必须定义宽度，不然margin属性会无效

```
.outer .inner {
    margin: auto;
}
```

##### 2-块级元素（margin: auto + display: table）

前面这种方式需要为内部元素定义宽度，如果不想定义宽度，可以设置内部元素的display 为 table，它的宽度会由内部元素来撑开。

```
.outer .inner {
    margin: auto;
    display: table;
}
```

##### 3-块级元素（display: inline）

为内部元素设置display 为inline，将它转换为行内元素，再对父元素使用text-align: center 可以实现水平居中，缺点就是内部元素无法设置宽度。

```
.outer {
    text-align: center;
}
.outer .inner {
    display: inline;
}
```

###### 4-块级元素（display: inline-block）

方案三无法为内部元素设置宽度，但是采用inline-block，则可以为内部元素设置宽度。

```
.outer {
    text-align: center;
}
.outer .inner {
    display: inline-block;
}
```

##### 5-块级元素（float: left + transform）

这种方式不需要知道内部元素宽度。

```
.outer .inner {
    position: relative;
    left: 50%;
    transform: translateX(-50%);
}
```

##### 6-块级元素（负边距+绝对定位）

```
.outer {
    position: relative;
}
.outer .inner {
    position: absolute;
    left: 50%;
    margin-left: -25px;
}
```

##### 7-块级元素（flexbox）

```
用的最多的方式，但低版本浏览器会有兼容问题
.outer {
    display: flex;
    justify-content: center;  // 主轴上居中
}
```

#### 垂直居中

##### 1-行内元素（line-height）

外部元素设置line-height

```
.outer {
    line-height: 400px;
}
```

##### 1-块级元素（absolute + top + margin-top）

使用绝对定位将内部元素的顶部定位在中间，再通过margin-top 负值拉回高度，需要提前知道内部元素的高度

```
.outer {
    position: relative;
}
.outer .inner {
    position: absolute;
    top: 50%;
    margin-top: -25px;
}
```

##### 2-块级元素（absolute + margin:auto）

这种方式不需要知道内部元素的高度，兼容性也很好

```
.outer {
    position: relative;
}
.outer .inner {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
}
```

##### 3-块级元素（relative + transform）

前面水平居中的时候也出现过这种方式，也可以使用position: absolute方式，但要对应地将外部元素设置成position: relative

```
.outer .inner {
    position: relative;
    top: 50%;
    transform: translateY(-50%);
}
```

##### 4-块级元素（vertical-align + table-cell）

```
.outer {
    display: table-cell;
    vertical-align: middle;
}
```

##### 5-块级元素（vertical-align + inline-block）

原理是新建一个inner的兄弟元素，高度撑开整个容器，再对inner使用vertical-align: middle 使元素居中，不需要知道内部元素的高度

html结构

```
 <div class="outer">
    <div class="inner"></div>
    <div class="sibling"></div>
  </div>
.outer .inner {
    vertical-align: middle;
    display: inline-block;
}
.outer .slibing {
    height: 400px;
    display: inline-block;
    vertical-align: middle;
}
```

##### 5-块级元素（伪元素）

原理和上面的方式一样，只是通过伪元素去撑开高度

```
.inner {
    display: inline-block;
    vertical-align: middle;
}
.outer::before {
    content: '';
    height: 100%;
    display: inline-block;
    vertical-align: middle;
}
```

##### 6-块级元素（flexbox）

```
.outer {
    display: flex;
    align=items: center;
}
```

================================

补充：

还可以用transform: scale(0.5, 0.5)来实现

使用line-height垂直居中时，需要对自己行高进行修正，默认会继承下来的


```
.outer {
  display: flex;
  margin: auto
}
```