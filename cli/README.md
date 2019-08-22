# Cloudbase CLI 简介

![npm (tag)](https://img.shields.io/npm/v/@cloudbase/cli) ![npm (tag)](https://img.shields.io/npm/v/@cloudbase/cli/beta)

云开发（Tencent Cloud Base，简称 TCB）是腾讯云为移动开发者提供的一站式后端云服务，可用于开发多种客户端，它帮助开发者统一构建和管理资源，免去了应用开发过程中繁琐的服务器搭建及运维、域名注册及备案、数据接口实现等繁琐流程，让开发者可以专注于业务逻辑的实现，而无需理解后端逻辑及服务器运维知识，开发门槛更低，效率更高。

Cloudbase CLI 是一个开源的命令行界面交互工具，用于帮助用户快速、方便的部署项目，管理 TCB 资源。

## 安装 Cloudbase CLI

### npm

```sh
npm install -g @cloudbase/cli
```

### yarn

```sh
yarn global add @cloudbase/cli
```

安装完成后，你可以使用 `tcb -V` 验证是否安装成功，如果输出了类似下面的版本号，则表明 Cloudbase CLI 被成功安装到您的计算机中。

```text
0.1.5
```

## 开通 TCB 服务

在所有开始之前，你需要登录 [TCB Web 控制台](https://console.cloud.tencent.com/tcb)，确保已经开通了 TCB 服务，并且已经创建了可以使用的环境。如果您不了解怎么创建环境，可以根据[此文档指导](https://cloud.tencent.com/document/product/876/34607)进行操作。

## 登录

您需要先登录您的腾讯云账号，在获取到您的授权之后，Cloudbase CLI 才能操作您的资源。Cloudbase CLI 提供了两种获取授权的方式：Web 控制台授权和云 API 密钥授权。

### Web 控制台授权

在您的终端中输入下面的命令

```sh
tcb login
```

Cloudbase CLI 会自动打开 Web 控制台获取授权，您需要点击同意授权按钮允许 Cloudbase CLI 获取授权。如您没有登录，您需要登录后才能进行此操作。

### 云 API 密钥授权

> **注意**：腾讯云 API 密钥可以操作您名下的所有腾讯云资源，请妥善保存和定期更换密钥，当您更换密钥后，请及时删除旧密钥。

首先您需要到腾讯云官网获取[云 API 密钥](https://console.cloud.tencent.com/cam/capi)，然后在终端中输入下面的命令：

```sh
tcb login --key
```

回车后，请按提示输入云 API 密钥的 SecretId 和 SecretKey 既可完成登录。

## 创建项目与部署

### 1. 初始化

您可以使用下面的命令创建一个项目，创建项目时 Cloudbase CLI 根据您输入的项目名创建一个文件夹，并写入相关的配置和模板文件。

```sh
tcb init
```

### 2. 编写函数

为了规范使用，所有 Node 和 PHP 函数都统一存放在 `functions` 目录下，并以函数名作为文件夹名称，如 `functions/tcb/index.js`。**对于 Java 函数时，则需要将 jar 文件名修改为函数名称，放在 `functions` 目录下即可，如 `functions/tcb.jar`**。

例如，创建一个 Node.js 函数 app，下面是 `functions/app/index.js` 的内容

```js
'use strict';

exports.main = (event, context, callback) => {
  console.log('Hello World');
  console.log(event);
  console.log(context);
  callback(null, event);
};
```

### 3. 修改配置

项目配置存储在 `tcbrc.json` 文件中，默认生成的函数配置为 Node 语言相关的配置，其他语言如 PHP，Java 等需要修改对应的 `handler(运行入口) 和 runtime(运行时)`，参考 [tcbrc.json 文件说明部分](/cli/config/)

```json
{
  "envId": "xxx",
  "functions": [
    {
      "name": "app",
      "config": {
        "timeout": 5,
        "envVariables": {
          "key": "value"
        },
        "vpc": {
          "subnetId": "subnet-xxx",
          "vpcId": "vpc-xxx"
        },
        "runtime": "Nodejs8.9"
      },
      "triggers": [
        {
          "name": "myTrigger",
          "type": "timer",
          "config": "0 0 2 1 * * *"
        }
      ],
      "params": {},
      "handler": "index.main"
    }
  ]
}
```

### 4. 部署函数

最后，在项目根目录下（tcbrc.json 所在目录）运行 `tcb functions:deploy` 命令，即可部署 app 函数

```sh
tcb functions:deploy app
```

## 使用说明

在介绍文档中，默认省略了环境 Id，默认在 `.tcbrc.json` 文件所在目录使用 Cloudbase CLI命令。

## 关于 TCB 项目

TCB 项目是和 TCB 环境资源关联的实体，TCB 项目聚合了云函数、数据库、文件存储等服务，您可以在 TCB 项目中编写函数，存储文件，并通过 `cloudbase cli` 快速的操作您的云函数、文件存储、数据库等资源。

TCB 项目文件结构：

```
.
├── _gitignore
├── functions // 云函数目录
│   └── app
│       └── index.js
└── tcbrc.json // tcb 项目配置文件
```

## 所有命令

使用 `tcb -h` 查看所有可用命令

```bash
tcb -h
```

```
Usage: tcb [options] [command]

Options:
  -V, --version                                                                     output the version number
  -h, --help                                                                        output usage information

Commands:
  init [options]                                                                    创建并初始化一个新的项目
  login [options]                                                                   登录腾讯云账号
  logout                                                                            登出腾讯云账号
  env:list                                                                          列出云开发所有环境
  env:create <alias>                                                                创建新的云环境
  env:domain:list [envId]                                                           列出环境的安全域名列表
  env:domain:create <domain> [envId]                                                添加环境安全域名，多个以斜杠 / 分隔
  env:domain:delete [envId]                                                         删除环境的安全域名
  env:login:list [envId]                                                            列出环境登录配置
  env:login:config [envId]                                                          配置环境登录方式
  functions:deploy [options] [functionName] [envId]                                 创建云函数
  functions:code:update <functionName> [envId]                                      创建云函数
  functions:list [options] [envId]                                                  展示云函数列表
  functions:delete [functionName] [envId]                                           删除云函数
  functions:detail [functionName] [envId]                                           获取云函数信息
  functions:log [options] <functionName> [envId]                                    打印云函数日志
  functions:config:update [functionName] [envId]                                    更新云函数配置
  functions:trigger:create [functionName] [envId]                                   创建云函数触发器
  functions:trigger:delete [functionName] [triggerName] [envId]                     删除云函数触发器
  functions:invoke [functionName] [params] [envId]                                  触发云函数
  functions:copy [options] <functionName> <newFunctionName> [envId] [targentEnvId]  创建云函数
  server:deploy [name]                                                              部署 node 服务
  server:log [options] <name>                                                       查看日志
  server:reload <name>                                                              重启 node 服务
  server:stop <name>                                                                停止应用
  server:show                                                                       查看状态
```

### 编程式使用

Cloudbase CLI 支持作为单独的 Node 模块使用

```js
const Client = require('@cloudbase/cli');
// 如果已使用 tcb login 登录过，可以不传入 secretId、secretKey 值
const client = new Client(secretId, secretKey);

// 列出云开发所有环境
client.env
  .list()
  .then(function(data) {
    console.log(data);
  })
  .catch(function(err) {});
```

编程式 API 接口详情：[API 接口](docs/api.md)

`env:login:config` 命令用于配置环境的登录方式，如绑定微信公众平台等。
