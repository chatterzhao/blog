---
title: "常见问题"
date: "2019-09-09"
permalink: "faq"
sidebar: "auto"
---

这里罗列的是腾讯云开发产品使用过程中，用户提问最频繁的问题。若你在这里无法找到问题的答案，请访问 [TencentCloudBase Issues](https://github.com/TencentCloudBase/blog/issues)，提交你的问题。

## 怎样在云开发的云函数中使用当前环境的资源？

云函数中初始化云开发实例时（`wx-server-sdk` 以及 `tcb-admih-node`）, 缺省 `env` 参数时，将默认访问**第一个创建**的环境。在云函数中一般建议使用当前的环境来初始化云开发实例。

在 `wx-server-sdk` 中，可以是使用 `.getWXContext` 和 `.updateConfig` 方法来做到动态失败当前环境变量：

```js
const cloud = require('wx-server-sdk')
cloud.init()
exports.main = async (event) => {
  const { ENV, OPENID, APPID } = cloud.getWXContext()
  // 更新默认配置，将默认访问环境设为当前云函数所在环境
  cloud.updateConfig({
    env: ENV
  })
  // ...
  return {
    ENV,
    OPENID,
    APPID,
  }
}
```

在 `tcb-admin-node` 中, 可以使用 `.getCurrentEnv` 方法获取当前环境：

```js
const tcb = require('tcb-admin-node')

exports.main = async (event) => {
  let env = tcb.getCurrentEnv()
  tcb.init({
    env
  })
  return event
}
```

## 示例问题 2

回答1