---
title: Egg.js 教程-3
cover: "https://static.ivan.fun/blog/egg-banner.jpg"
index_img: "https://static.ivan.fun/blog/egg-banner.jpg"
tags:
  - Egg.js
  - Node
categories: -Node
abbrlink:
feature: true
date: 2021-07-27 13:30:00
---

# Egg.js 从入门到放弃-3

## Egg-Sequelize

> 前面的章节中，我们介绍了如何在框架中通过 egg-mysql 插件来访问数据库。而在一些较为复杂的应用中，我们可能会需要一个 ORM 框架来帮助我们管理数据层的代码。而在 Node.js 社区中，**sequelize** 是一个广泛使用的 ORM 框架，它支持 MySQL、PostgreSQL、SQLite 和 MSSQL 等多个数据源。本章节我们会通过开发一个对 MySQL 中 users 表的数据做 CURD 的例子来一步步介绍如何在 egg 项目中使用 sequelize。

### 初始化项目

通过 npm 初始化一个项目:

```
$ mkdir sequelize-project && cd sequelize-project
$ npm init egg --type=simple
$ npm i
```

安装并配置 egg-sequelize 插件（它会辅助我们将定义好的 Model 对象加载到 app 和 ctx 上）和 mysql2 模块：

- 安装
  ```
  npm install --save egg-sequelize mysql2
  ```
- 在 `config/plugin.js` 中引入 egg-sequelize 插件
  ```
  exports.sequelize = {
  enable: true,
  package: 'egg-sequelize',
  };
  ```
- 在 `config/config.default.js` 中编写 sequelize 配置
  ```
  config.sequelize = {
  dialect: 'mysql',
  host: '127.0.0.1',
  port: 3306,
  database: 'egg-sequelize-doc-default',
  };
  ```

我们可以在不同的环境配置中配置不同的数据源地址，用于区分不同环境使用的数据库，例如我们可以新建一个 `config/config.unittest.js` 配置文件，写入如下配置，将单测时连接的数据库指向 `egg-sequelize-doc-unittest`。

```
exports.sequelize = {
  dialect: 'mysql',
  host: '127.0.0.1',
  port: 3306,
  database: 'egg-sequelize-doc-unittest',
};
```

完成上面的配置之后，一个使用 sequelize 的项目就初始化完成了。egg-sequelize 和 sequelize 还支持更多的配置项，可以在他们的文档中找到。

### 初始化数据库和 Migrations

接下来我们先暂时离开 egg 项目的代码，设计和初始化一下我们的数据库。首先我们通过 mysql 命令在本地快速创建开发和测试要用到的两个 database：

```
mysql -u root -e 'CREATE DATABASE IF NOT EXISTS `egg-sequelize-doc-default`;'
mysql -u root -e 'CREATE DATABASE IF NOT EXISTS `egg-sequelize-doc-unittest`
```

然后我们开始设计 users 表，它有如下的数据结构：

```
CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'primary key',
  `name` varchar(30) DEFAULT NULL COMMENT 'user name',
  `age` int(11) DEFAULT NULL COMMENT 'user age',
  `created_at` datetime DEFAULT NULL COMMENT 'created time',
  `updated_at` datetime DEFAULT NULL COMMENT 'updated time',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='user';
```

我们可以直接通过 mysql 命令将表直接建好，但是这并不是一个对多人协作非常友好的开发模式。在项目的演进过程中，每一个迭代都有可能对数据库数据结构做变更，怎样跟踪每一个迭代的数据变更，并在不同的环境（开发、测试、CI）和迭代切换中，快速变更数据结构呢？这时候我们就需要 `Migrations` 来帮我们管理数据结构的变更了。

sequelize 提供了 `sequelize-cli` 工具来实现 `Migrations`，我们也可以在 egg 项目中引入 sequelize-cli。

- 安装 sequelize-cli

  ```
  npm install --save-dev sequelize-cli
  ```

  在 egg 项目中，我们希望将所有数据库 Migrations 相关的内容都放在 `database` 目录下，所以我们在项目根目录下新建一个 `.sequelizerc` 配置文件：

  ```
  'use strict';
  const path = require('path');
  module.exports = {
    config: path.join(**dirname, 'database/config.json'),
    'migrations-path': path.join(**dirname, 'database/migrations'),
    'seeders-path': path.join(**dirname, 'database/seeders'),
    'models-path': path.join(**dirname, 'app/model'),
  };
  ```

- 初始化 Migrations 配置文件和目录

  ```
  npx sequelize init:config
  npx sequelize init:migrations
  ```

  执行完后会生成 database/config.json 文件和 database/migrations 目录，我们修改一下 database/config.json 中的内容，将其改成我们项目中使用的数据库配置：

  ```
  {
  "development": {
    "username": "root",
    "password": null,
    "database": "egg-sequelize-doc-default",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "egg-sequelize-doc-unittest",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
  }
  ```

  此时 sequelize-cli 和相关的配置也都初始化好了，我们可以开始编写项目的第一个 Migration 文件来创建我们的一个 users 表了。

  ```
  npx sequelize migration:generate --name=init-users
  ```

  执行完后会在 database/migrations 目录下生成一个 migration 文件(${timestamp}-init-users.js)，我们修改它来处理初始化 users 表：

  ```
    module.exports = {
    // 在执行数据库升级时调用的函数，创建 users 表
    up: async (queryInterface, Sequelize) => {
    const { INTEGER, DATE, STRING } = Sequelize;
    await queryInterface.createTable('users', {
    id: { type: INTEGER, primaryKey: true, autoIncrement: true },
    name: STRING(30),
    age: INTEGER,
    created_at: DATE,
    updated_at: DATE,
  });
  },// 在执行数据库降级时调用的函数，删除 users 表
    down: async queryInterface => {
    await queryInterface.dropTable('users');
    },
  };
  ```

- 执行 migrate 进行数据库变更

  ```
  # 升级数据库

  npx sequelize db:migrate

  # 如果有问题需要回滚，可以通过 `db:migrate:undo` 回退一个变更

  # npx sequelize db:migrate:undo

  # 可以通过 `db:migrate:undo:all` 回退到初始状态

  # npx sequelize db:migrate:undo:all
  ```

  执行之后，我们的数据库初始化就完成了

### 编写代码

现在终于可以开始编写代码实现业务逻辑了，首先我们来在 app/model/ 目录下编写 user 这个 Model：

```
'use strict';
module.exports = app => {
  const { STRING, INTEGER, DATE } = app.Sequelize;

  const User = app.model.define('user', {
    id: { type: INTEGER, primaryKey: true, autoIncrement: true },
    name: STRING(30),
    age: INTEGER,
    created_at: DATE,
    updated_at: DATE,
  });

  return User;
};
```

这个 Model 就可以在 Controller 和 Service 中通过 `app.model.User` 或者 `ctx.model.User` 访问到了，例如我们编写 `app/controller/users.js`：

```
// app/controller/users.js
const Controller = require('egg').Controller;

function toInt(str) {
  if (typeof str === 'number') return str;
  if (!str) return str;
  return parseInt(str, 10) || 0;
}

class UserController extends Controller {
  async index() {
    const ctx = this.ctx;
    const query = { limit: toInt(ctx.query.limit), offset: toInt(ctx.query.offset) };
    ctx.body = await ctx.model.User.findAll(query);
  }

  async show() {
    const ctx = this.ctx;
    ctx.body = await ctx.model.User.findByPk(toInt(ctx.params.id));
  }

  async create() {
    const ctx = this.ctx;
    const { name, age } = ctx.request.body;
    const user = await ctx.model.User.create({ name, age });
    ctx.status = 201;
    ctx.body = user;
  }

  async update() {
    const ctx = this.ctx;
    const id = toInt(ctx.params.id);
    const user = await ctx.model.User.findByPk(id);
    if (!user) {
      ctx.status = 404;
      return;
    }

    const { name, age } = ctx.request.body;
    await user.update({ name, age });
    ctx.body = user;
  }

  async destroy() {
    const ctx = this.ctx;
    const id = toInt(ctx.params.id);
    const user = await ctx.model.User.findByPk(id);
    if (!user) {
      ctx.status = 404;
      return;
    }

    await user.destroy();
    ctx.status = 200;
  }
}

module.exports = UserController;
```

最后我们将这个 controller 挂载到路由上：

```
// app/router.js
module.exports = app => {
  const { router, controller } = app;
  router.resources('users', '/users', controller.users);
};
```

> 针对 users 表的 CURD 操作的接口就开发完了。
