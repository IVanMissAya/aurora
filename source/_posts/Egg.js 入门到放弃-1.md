---
title: Egg.js 教程-1
cover: "https://static.ivan.fun/blog/egg-banner.jpg"
index_img: "https://static.ivan.fun/blog/egg-banner.jpg"
tags:
  - Egg.js
  - Node
categories: -Node
abbrlink:
feature: true
date: 2021-07-16 19:00:00
---

# Egg.js 从入门到放弃-1

## Egg.js 是什么?

> - Egg.js 是 nodejs 的一个框架，它是基于 koa 框架的基础之上的上层框架，它继承了 koa 的，它可以帮助开发人员提高开发效率和维护成本。
> - Egg 约定了一些规则，在开发中，我们可以按照一套统一的约定进行应用开发，团队内部使用这种方式开发可以减少开发人员的学习成本。
> - Express 也是 Node.js 的一个框架，express 简单且扩展性强，但是 express 框架缺少了一些约定，不同的开发者会写出不同的代码，适合做个人项目，不太适合团队开发，而 Egg 它约定了一些规则，对整个团队开发效率会提高。

## 官网对 Egg 有如下特性

1. 可以基于 Egg 定制上层框架的能力。
2. 高度可扩展的插件机制。
3. 内置多进程管理。
4. 基于 koa 开发的，性能好。
5. 框架稳定，测试覆盖了高。
6. 渐进性开发。

# 快速入门

## 环境准备

- 操作系统：支持 macOS，Linux，Windows
- 运行环境：建议选择 LTS 版本，最低要求 8.x。

## 快速初始化

我们推荐直接使用脚手架，只需几条简单指令，即可快速生成项目（npm >=6.1.0）:

```
$ mkdir egg-example && cd egg-example
$ npm init egg --type=simple
$ npm i
```

此时目录结构如下：

```
egg-example
├── app
│   ├── controller
│   │   └── home.js
│   └── router.js
├── config
│   └── config.default.js
└── package.json
```

## 启动项目:

```
$ npm run dev
$ open http://localhost:7001
```

![](https://static.ivan.fun/blog/egg-912u3h.jpg)
