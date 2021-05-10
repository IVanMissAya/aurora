---
title: 搭建 Node.js 环境
cover: 'https://static.ivan.fun/blog/post_5.jpg'
index_img: 'https://static.ivan.fun/blog/post_5.jpg'
tags:
  - Node.js
  - Window 10
categories: -前端
abbrlink: 27ec92d2
date: 2020-03-12 08:00:00
---

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时。 

## Node

### 配置本地 node 环境
  1.<font color=#c7254e>node</font>[官网](https://nodejs.org/en/ "官网")下载。
 ![](https://static.ivan.fun/blog/node.js1.jpg)

  2.默认安装
``` 
  $ node -v
  $ npm -v
```
  ![](https://static.ivan.fun/blog/node.js2.jpg)

#### 修改
``` 
  $ npm config set registry http://registry.npm.taobao.org/   //修改淘宝源
```
#### 还原
``` 
   $ npm config set registry https://registry.npmjs.org/   //还原默认下载源
```

#### 查看
``` 
   $ npm config get registry    //查看下载源
```
 ![](https://static.ivan.fun/blog/node.js3.jpg)
