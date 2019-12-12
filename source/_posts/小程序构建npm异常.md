---
title: 小程序构建npm异常
date: 2019-12-12 21:05:54
tags: 微信小程序
---

按照官网的[npm支持]('https://developers.weixin.qq.com/miniprogram/dev/devtools/npm.html')步骤来做，第一步就卡住，报错“没有找到可以构建的npm包”，原因是没有npm初始化...这时候我们第一时间再微信开发者工具里npm init，然后发现报错找不到标识符，此时不禁骂了一句MMP，然后打开系统的终端，找到文件位置，npm init，一路回车，进入下一关

<!--more-->

正确步骤：

1. 打开终端，找到文件位置，执行`npm init`，一路回车。

2. 执行你要引入的包的安装命令，eg：

   ```
   npm i @vant/weapp -S --production
   ```

3. 在微信开发者工具里，点击 工具->构建npm。此时有的包不好，会报错“未找到npm包的入口文件”，这是因为入口文件需要打包成index，如果有的包没打包成index，就找不到

4. 详情里面：选中`使用npm模块`