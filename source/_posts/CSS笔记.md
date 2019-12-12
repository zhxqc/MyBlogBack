---
title: CSS笔记
date: 2019-12-06
tags: CSS
---

# CSS盒模型

当对一个文档进行布局的时候，浏览器的渲染引擎会根据标准之一的CSS基础框盒模型，将所有的元素表示为一个个矩形的盒子。CSS决定这些盒子的大小、位置以及属性（例如颜色，背景，边框尺寸等）。

每个盒子由四个部分（或称区域）组成，其效用由它们各自的边界（Edge）所定义：

​	Content 、Padding、Border、Margin

<!--more-->

# CSS术语和概念

## 长度单位:

CSS中的长度单位有时间单位（如s，ms），还有角度单位（如deg，rad等），但是最常见的自然还是长度单位（如px，em等）。需要注意的是，诸如2%后面的百分号%不是长度单位，因为2%就是一个完整的值，就是一个整体，就像0.02是一个值那样，2%也是一个值。

```
<number> + 长度单位 = <length>
```

如果细分，长度单位又可以分为相对长度单位和绝对长度单位。

1. 相对长度单位。相对长度又分为相对字体长度单位和相对视区长度单位。
   - 相对字体长度单位，如em，ex，还有CSS3的rem和ch（字符0的宽度）
   - 相对视区长度单位，如vh、vw、vmin和vmax。
2. 绝对长度单位：最常见的就是px，还有pt、cm、mm、pc等了解一下就可以了

## 选择器

选择器是瞄准目标元素的东西。

- 类选择器：指以"."开头的选择器。很多元素可以应用同一个类选择器。
- ID选择器：以“#”开头，权重很高。ID一般指向唯一元素。但是在CSS中，ID央视出现在多个不同的元素上并不会只渲染第一个，而是雨露均沾。但是显然不推荐这么做。
- 属性选择器：指含有[]的选择器，形如`[title]:{}、[title="css-world"]`等
- 伪类选择器：一般指前面有个英文冒号":"的选择器，如:first-child或:last-child等
- 伪元素选择器：就是有两个连续冒号的选择器，如::first-line、::first-latter、::before、::after

## 关系选择器

关系选择器是指根据与其他元素的关系选择元素的选择器，常见的符号有空格、>、~，还有+等，这些都是非常常用的选择器。

- 后代选择器：选择所有合乎规则的后代元素。空格连接。
- 相邻后代选择器：仅仅选择合乎规则的儿子元素，孙子、重孙元素忽略，因此又称“子选择器”。>连接。适用于IE7以上版本。
- 兄弟选择器：选择当前元素后面的所有合乎规则的兄弟元素。~连接。适用于IE7以上版本。
- 相邻兄弟选择器：仅仅选择当前元素相邻的那个合乎规则的兄弟元素。+连接。适用于IE7以上版本。

# 流、元素与基本尺寸

HTML常见的标签有`<div>`、`<p>`、`<li>`和`<table>`以及`<span>`、`<img>`、和`<em>`等。虽然标签种类繁多，但通常我们就把它们分为两类：块级元素（block-level element）和内联元素（inline element）。

## 块级元素

“块级元素”对应的英文是block-level element，常见的块级元素有<div>、<li>和<table>等。需要注意是，“块级元素”和“display为block的元素”不是一个概念。例如，<li>元素默认的display值是list-item，<table>元素默认的display值是table，但是它们均是“块级元素”，因为它们都符合块级元素的基本特征，也就是一个水平流上只能单独显示一个元素，多个块级元素则换行显示。

每个元素都有两个盒子，外在盒子和容器盒子，外在盒子控制元素是可以一行显示还是只能换行显示，内联盒子控制宽高、内容呈现等。

按照display属性值的不同，值为block的元素的盒子实际由外在的“块级盒子”和内在的“块级容器盒子”组成，值为inline-block的元素则由外在的“内联盒子”和内在的“块级容器盒子”组成。

display属性值是inline-block的元素既能和图文一行显示，又能直接设置width/height了吧！因为有两个盒子，外面的盒子是inline级别，里面的盒子是block级别。实际上，如果遵循这种理解，display:block应该脑补成display:block-block，display:table应该脑补成display:block-table，我们平时的写法实际上是一种简写。

# CSS定位

笔记来源：[CSS定位详解](http://www.ruanyifeng.com/blog/2019/11/css-position.html)

## 一、postion属性的作用

postion属性用来指定一个元素在网页上的位置，一共有5种定位方式：

- static
- relative
- fixed
- absolute
- sticky  (2017年浏览器才支持)

## 二、static属性值

static是postion的默认值。这时浏览器会按照源码的顺序决定每个元素的位置，这时 top、bottom、left、right这四个属性无效。

## 三、relative，absolute、fixed

relative、absolute、fixed这三个属性值有一个共同点，都是相对于某个基点的定位，不同之处仅仅在于基点不同。这三种定位都不会影响其他元素的位置，因此元素之间可能会产生重叠。

### 3.1 relative属性值

relative表示，相对于默认位置（即static时的位置）进行偏移，即定位基点是元素的默认位置。

它必须搭配top、bottom、left、right这四个属性一起使用，用来指定偏移的方向和距离

```
div{
	postion: relative;
	top: 20px;
}
```

### 3.2 absolute属性

absolute表示，相对于上级元素（一般是父元素）进行偏移，即定位基点是父元素。

它有一个重要的限制条件：父元素的postion不能是static（默认）定位，否则定位基点就会变成整个网页的根元素html。另外，absolute定位也必须搭配top、bottom、left、right这四个属性一起使用。

```
<div id="father">
	<div id="son"></div>
</div>
#father{
	postion: relative;
}
#son{
	postion: absolute;
	top: 20px;
}
```

注意： absolute定位的元素会被“正常的页面流”忽略，即在“正常的页面流”里，该元素所占的空间为0，周边元素不受影响。

### 3.3 fixed属性

fixed表示，相对于浏览器窗口进行偏移，即定位基点是浏览器窗口。这会导致元素的位置不随页面滚动而变化，好像固定在网页上一样。

 它如果搭配`top`、`bottom`、`left`、`right`这四个属性一起使用，表示元素的初始位置是基于视口计算的，否则初始位置就是元素的默认位置。 

```
div{
	postion: fixed;
	top: 0;
}
```

 上面代码中，`div`元素始终在视口顶部，不随网页滚动而变化。 

## 四、sticky属性值

`sticky`跟前面四个属性值都不一样，它会产生动态效果，很像`relative`和`fixed`的结合：一些时候是`relative`定位（定位基点是自身默认位置），另一些时候自动变成`fixed`定位（定位基点是视口）。

 因此，它能够形成"动态固定"的效果。比如，网页的搜索工具栏，初始加载时在自己的默认位置（`relative`定位）。 

 页面向下滚动时，工具栏变成固定位置，始终停留在页面头部（`fixed`定位），等到页面重新向上滚动回到原位，工具栏也会回到默认位置。 

`sticky`生效的前提是，必须搭配`top`、`bottom`、`left`、`right`这四个属性一起使用，不能省略，否则等同于`relative`定位，不产生"动态固定"的效果。原因是这四个属性用来定义"偏移距离"，浏览器把它当作`sticky`的生效门槛。

它的具体规则是，当页面滚动，父元素开始脱离视口时（即部分不可见），只要与`sticky`元素的距离达到生效门槛，`relative`定位自动切换为`fixed`定位；等到父元素完全脱离视口时（即完全不可见），`fixed`定位自动切换回`relative`定位。

请看下面的示例代码。（注意，除了已被淘汰的 IE 以外，其他浏览器目前都支持`sticky`。但是，Safari 浏览器需要加上浏览器前缀`-webkit-`。）

```
#toolbar {
  position: -webkit-sticky; /* safari 浏览器 */
  position: sticky; /* 其他浏览器 */
  top: 20px;
}
```

上面代码中，页面向下滚动时，`#toolbar`的父元素开始脱离视口，一旦视口的顶部与`#toolbar`的距离小于`20px`（门槛值），`#toolbar`就自动变为`fixed`定位，保持与视口顶部`20px`的距离。页面继续向下滚动，父元素彻底离开视口（即整个父元素完全不可见），`#toolbar`恢复成`relative`定位。

[sticky属性Demo](https://jsbin.com/fegiqoquki/edit?html,css,output)

