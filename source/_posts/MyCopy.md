---
title: MyCopy
date: 2020-07-06 11:05:58
tags: 组件
---

Vue里实现一键复制方法

<!-- more  -->

```javascript
msgCopy (value) {
      if (!value) {
        this.$message.error('无复制内容');
        return;
      }
      // 动态创建 textarea 标签
      const textarea = document.createElement('textarea');
      // 将该 textarea 设为 readonly 防止 iOS 下自动唤起键盘，同时将 textarea 移出可视区域
      textarea.readOnly = 'readonly';
      textarea.style.position = 'absolute';
      textarea.style.left = '-9999px';
      // 将要 copy 的值赋给 textarea 标签的 value 属性
      textarea.value = value;
      // 将 textarea 插入到 body 中
      document.body.appendChild(textarea);
      // 选中值并复制
      textarea.select();
      // textarea.setSelectionRange(0, textarea.value.length);
      const result = document.execCommand('Copy');
      if (result) {
        this.$message.success('复制成功');
      }
      document.body.removeChild(textarea);
    }
```

