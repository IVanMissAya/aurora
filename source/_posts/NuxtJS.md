---
title: NuxtJS && SSR
cover: "https://static.ivan.fun/blog/nuxt-banner.jpg"
index_img: "https://static.ivan.fun/blog/nuxt-banner.jpg"
tags:
  - NuxtJS
  - SSR
categories: "-NuxtJS"
abbrlink:
feature: true
date: 2021-07-01 10:00:00
---

##  [Nuxt.js](https://www.nuxtjs.cn/) 是什么？

> Nuxt.js 是一个基于 Vue.js 的通用应用框架。
> 通过对客户端/服务端基础架构的抽象组织，Nuxt.js 主要关注的是应用的 UI 渲染。
> Nuxt.js 预设了利用 Vue.js 开发服务端渲染的应用所需要的各种配置。
> NuxtJS 让你构建你的下一个 Vue.js 应用程序变得更有信心。这是一个 开源 的框架，让 web 开发变得简单而强大。

**1. 原理图**

![](https://static.ivan.fun/blog/v2-820d47e4c14d94d6b598f037def9e90a_720w.png)

**2. nuxt.js 的优势**

- 基于 Vue.js
- 自动代码分层
- 服务端渲染 SSR
- 强大的路由功能，支持异步数据
- 静态文件服务
- ES6/ES7 语法支持
- 打包和压缩 JS 和 CSS
- HTML 头部标签管理
- 本地开发支持热加载
- 集成 ESLint
- 支持各种样式预处理器： SASS、LESS、 Stylus 等

## SSR

![](https://static.ivan.fun/blog/aHR0cHM6Ly9pbWcudzNjdGVjaC5jb20vMS1qSmtFUXBnWjh3YVE1UC1XNWxoeHVRLnBuZw.png)

> 从图中可以看出，（这两种渲染方式的）区别主要在于出现首屏渲染的时机。对于 SSR 来说，服务器返回的是（结构相对完整的）HTML 文件，（通过解析 HTML 文件），浏览器就能渲染出页面。而对 CSR 来说，浏览器拿到的只是包含 JavaScript 代码的 HTML 文件，（换句话，在浏览器渲开始渲染出页面之前，需要动态创建 HTML 标签）。这也就意味着，SSR 可以让浏览器在边下载 JavaScript 文件的同时边渲染 HTML 页面，换句话说，浏览器再也不需要等到所有的 JavaScript 文件下载并执行完之后才去渲染页面啦。

### NuxtJs 安装

```
npx create-nuxt-app <项目名>
```

或者使用 Yarn:

```
yarn create nuxt-app <项目名>
```

### 目录结构

```
|-- .nuxt                            // Nuxt自动生成，临时的用于编辑的文件，build
|-- assets                           // 用于组织未编译的静态资源入LESS、SASS 或 JavaScript
|-- components                       // 用于自己编写的Vue组件，比如滚动组件，日历组件，分页组件
|-- layouts                          // 布局目录，用于组织应用的布局组件，不可更改。
|-- middleware                       // 用于存放中间件
|-- pages                            // 用于存放写的页面，我们主要的工作区域
|-- plugins                          // 用于存放JavaScript插件的地方
|-- static                           // 用于存放静态资源文件，比如图片
|-- store                            // 用于组织应用的Vuex 状态管理。
|-- .editorconfig                    // 开发工具格式配置
|-- .eslintrc.js                     // ESLint的配置文件，用于检查代码格式
|-- .gitignore                       // 配置git不上传的文件
|-- nuxt.config.json                 // 用于组织Nuxt.js应用的个性化配置，已覆盖默认配置
|-- package-lock.json                // npm自动生成，用于帮助package的统一性设置的，yarn也有相同的操作
|-- package-lock.json                // npm自动生成，用于帮助package的统一性设置的，yarn也有相同的操作
|-- package.json                     // npm包管理配置文件
```

### 启动项目

```
npm run dev
```

**现在我们的应用运行在 http://localhost:3000 上运行。**

> 注意：Nuxt.js 会监听 pages 目录中的文件更改，因此在添加新页面时无需重新启动应用程序。
