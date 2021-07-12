---
title: CentOS7.5 Nginx 反向代理 部署 Nuxt.js
cover: "https://static.ivan.fun/blog/nuxtBuild.jpg"
index_img: "https://static.ivan.fun/blog/nuxtBuild.jpg"
tags:
  - CentOS
  - NuxtJS
  - NodeJs
categories: "-NuxtJS"
abbrlink:
feature: true
date: 2021-07-12 19:00:00
---

# CentOS7.5 Nginx 反向代理 部署 Nuxt.js

## 1. 安装 NodeJS

- 确认服务器有 nodejs 编译及依赖相关软件，如果没有可通过运行以下命令安装。
  ```
    yum -y install gcc gcc-c++ openssl-devel
  ```
- 下载 NodeJS 源码包并解压 进入要存放下载资源的目录，本文存放在/usr/local/src/目录下。然后执行命令下载源码包
  ```
    wget https://nodejs.org/dist/v10.14.2/node-v10.14.2-linux-x64.tar.xz
  ```
- 解压
  ```
    xz -d node-v10.14.2-linux-x64.tar.xz
    tar -xf node-v10.14.2-linux-x64.tar
  ```
- 为安装的包添加软连接（这样就可以在全局使用了）
  ```
    ln -s ~/node-v10.14.2-linux-x64/bin/node /usr/bin/node
    ln -s ~/node-v10.14.2-linux-x64/bin/npm /usr/bin/npm
  ```
- 测试是否安装成功 进入 node 目录下的 bin 目录，执行 ls 命令：
  ```
    cd node/bin && ls
  ```
- 会看到 node 和 npm，使用如下命令：

  ```
    node -v
  ```

  如此图出现，表明 Node 安装成功
  ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fecad3e5d49846c7a6ddfe92b1fe4093~tplv-k3u1fbpfcp-zoom-1.image)

  ### 为 npm 添加淘宝镜像

  ```
  npm config set registry https://registry.npm.taobao.org
  npm config get registry
  ```

## 2. 安装 nginx

- 如果直接使用 yum install nginx 安装报错，找不到 nginx 安装源则执行下面命令添加 nginx 安装源
  ```
    rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
  ```
- 安装 nginx
  ```
    yum install -y nginx
  ```

## 3. 安装 pm2 开启进程守护

- PM2 是 node 进程管理工具，可以利用它来简化很多 node 应用管理的繁琐任务，如性能监控、自动重启、负载均衡等，而且使用非常简单
- 安装前提：

  1. Node.js 环境
  2. npm

     - 全局安装，npm install -g pm2
     - 进入到对应的目录执行：
       ```
       pm2 start npm --name "SSR-service" -- run start  #SSR-service的名称是我们在package中的项目名称
       ```
     - 保存当前进程状态，pm2 save
     - 生成开机自启动服务，pm2 startup
     - 查看启动项，systemctl list-unit-files | grep enable
     - pm2 基本命令如下：

       ```
        pm2 list # 查看当前正在运行的进程
        pm2 start all # 启动所有应用
        pm2 restart all # 重启所有应用
        pm2 stop all # 停止所有的应用程序
        pm2 delete all # 关闭并删除所有应用
        pm2 logs # 控制台显示所有日志

        pm2 start 0 # 启动 id 为 0 的指定应用程序
        pm2 restart 0 # 重启 id 为 0 的指定应用程序
        pm2 stop 0 # 停止 id 为 0 的指定应用程序
        pm2 delete 0 # 删除 id 为 0 的指定应用程序

        pm2 logs 0 # 控制台显示编号为 0 的日志
        pm2 show 0 # 查看执行编号为 0 的进程
        pm2 monit jsyfShopNuxt # 监控名称为 jsyfShopNuxt 的进程

       ```

## 4. 部署

1. NuxtJS 打包部署
   **npm run build** 打包应用，打包完成后，我们将这几个文件夹：
   ```
    .nuxt
    static
    nuxt.config.js
    package.json
   ```
   传到服务器空间里，然后在服务器上部署运行：
   ```
    npm install
    npm start
   ```
2. 配置 nginx 代理监听 3000 端口，package 打包时端口 3000（默认 可修改）  
   找到 nginx 配置文件：nginx.conf
   ```
    server {
        listen       80;
   	    server_name  test.ssr.net;
        root D:/nginx/html;
        location / {
            proxy_pass http://127.0.0.1:3000 ;
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
   ```
   启动 nginx
   输入域名即可访问
