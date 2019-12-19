---
title: window.open()被拦截
date: 2019-12-19 15:25:43
tags: javaScript
---

项目里经常会碰到调用一个接口，返回一个新链接，然后我们要把这链接在一个新页面里打开的场景，但是如果直接在接口返回数据的时候直接`window.open(newUrl)`时往往会被浏览器拦截。

<!--more-->

#### 解决方案：

在接口调用之前先打开一个新页面，然后调用接口，当接口调用成功后，把返回的新链接加载到新窗口上。

```
async jump(){
	const newWindow = window.open()
	this.$loading.start()
	const { err, data: { data } } = await this.$service.index.jumpNewPage(params)
	this.$loading.done()
	newWindow.location.href = data.newUrl
} 
```

