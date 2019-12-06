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

