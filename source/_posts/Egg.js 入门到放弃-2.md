---
title: Egg.js 教程-2
cover: "https://static.ivan.fun/blog/egg-banner.jpg"
index_img: "https://static.ivan.fun/blog/egg-banner.jpg"
tags:
  - Egg.js
  - Node
categories: -Node
abbrlink:
feature: true
date: 2021-07-21 20:00:00
---

# Egg.js 从入门到放弃-2

> 上文说到 快速创建、启动项目 请求接口 拿到数据。本章我们来学习 Egg.js 连接数据库

## Egg-Mysql

> 在 Web 应用方面 MySQL 是最常见，最好的关系型数据库之一。非常多网站都选择 MySQL 作为网站数据库。框架提供了 **egg-mysql** 插件来访问 MySQL 数据库。这个插件既可以访问普通的 MySQL 数据库，也可以访问基于 MySQL 协议的在线数据库服务。

### 安装与配置

安装对应的插件 egg-mysql ：

```
npm i --save egg-mysql
```

开启插件：

```
// config/plugin.js
exports.mysql = {
  enable: true,
  package: 'egg-mysql',
};
```

在 `config/config.${env}.js` 配置各个环境的数据库连接信息。

### 单数据源

如果我们的应用只需要访问一个 MySQL 数据库实例，可以如下配置：

```
// config/config.${env}.js
exports.mysql = {
  // 单数据库信息配置
  client: {
    // host
    host: 'mysql.com',
    // 端口号
    port: '3306',
    // 用户名
    user: 'test_user',
    // 密码
    password: 'test_password',
    // 数据库名
    database: 'test',
  },
  // 是否加载到 app 上，默认开启
  app: true,
  // 是否加载到 agent 上，默认关闭
  agent: false,
};
```

使用方式：

```
await app.mysql.query(sql, values); // 单实例可以直接通过 app.mysql 访问
```

### 多数据源

如果我们的应用需要访问多个 MySQL 数据源，可以按照如下配置：

```
exports.mysql = {
  clients: {
    // clientId, 获取client实例，需要通过 app.mysql.get('clientId') 获取
    db1: {
      // host
      host: 'mysql.com',
      // 端口号
      port: '3306',
      // 用户名
      user: 'test_user',
      // 密码
      password: 'test_password',
      // 数据库名
      database: 'test',
    },
    db2: {
      // host
      host: 'mysql2.com',
      // 端口号
      port: '3307',
      // 用户名
      user: 'test_user',
      // 密码
      password: 'test_password',
      // 数据库名
      database: 'test',
    },
    // ...
  },
  // 所有数据库配置的默认值
  default: {

  },

  // 是否加载到 app 上，默认开启
  app: true,
  // 是否加载到 agent 上，默认关闭
  agent: false,
};
```

使用方式：

```
const client1 = app.mysql.get('db1');
await client1.query(sql, values);

const client2 = app.mysql.get('db2');
await client2.query(sql, values);
```

### 动态创建

我们可以不需要将配置提前申明在配置文件中，而是在应用运行时动态的从配置中心获取实际的参数，再来初始化一个实例。

```
// {app_root}/app.js
module.exports = app => {
  app.beforeStart(async () => {
    // 从配置中心获取 MySQL 的配置
    // { host: 'mysql.com', port: '3306', user: 'test_user', password: 'test_password', database: 'test' }
    const mysqlConfig = await app.configCenter.fetch('mysql');
    app.database = app.mysql.createInstance(mysqlConfig);
  });
};
```


