---
title: audio播放音频报错
date: 2019-10-14 11:40:53
tags: Vue
---
## Uncaught (in promise) DOMException: The play() request was interrupted by a call to pause().

从chrome50开始，对`<video>`或`<audio>`元素的play()调用返回一个promise。
一个异步返回单个结果的函数，如果执行成功，Promise实现，play事件也同时执行

#### 错误发生的过程为：

1. media.play() 开始异步加载video/audio内容

2. media.pause() 在video/audio没有准备好时中断加载。

3. media.paly()此时进行继续播放，报错

<!-- more -->

#### 解决方案：

```
<audio
      controls="controls"
      preload="none"
      :src="require('../assets/voice/tip.mp3')" //vue 模板中 audio 的 src 跟 img 不同，不会自动转换路径，需要改成 require格式
      ref="tip"
></audio>
...

tipPlay () {
        const audio = this.$refs.tip
        audio.load()
        const playPromise = audio.play()
        if (playPromise !== undefined) {
          playPromise.then(_ => {
            audio.play()
          }).catch(error => {
            throw new Error(error.msg)
          })
        }
      }
```

