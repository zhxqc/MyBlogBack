---
title: 用Vue写一个二维码组件
date: 2019-08-16 16:50:16
tags: Vue
---

首先在项目的package.json里引入qrcode包，这个包我看4个月前还在维护，gitHub地址在 [这里](https://github.com/soldair/node-qrcode)

```
npm install qrcode --save
```

例子如下：

```vue
<template>
  <div
    style="text-align: right"
  >
    <canvas ref="qrCodeCanvas"></canvas>
  </div>
</template>
<script>
  import QRCode from 'qrcode'
  export default {
    name: 'PrintQRCode',
    props: {
      text: {
        type: String,
        default: ''
      }
    },
    watch: {
      text (nVal, oVal) {
        this.createQRCode()
      }
    },
    methods: {
      createQRCode () {
        QRCode.toCanvas(this.$refs.qrCodeCanvas, this.text, (error) => {
          if (error) {
            console.log(error)
          }
        })
      }
    }
  }
</script>
<style scoped>
  .btn-wrap {
    width: 260px;
    height: 260px;
    margin: 0 auto;
  }
</style>
```

例子调用的是toCanvas接口，这个接口接受三个参数，第一个参数是domElement元素，第二个是二维码扫描出的地址，第三个是错误处理回调函数。
页面上用一个canvas元素接收，显示出二维码。

效果如图：

![](https://user-gold-cdn.xitu.io/2019/5/17/16ac4b2acc51827c?w=146&h=127&f=png&s=3056)

以上
