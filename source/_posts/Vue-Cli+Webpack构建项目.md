---
title: Vue-Cli + Webpack 构建项目
cover: 'https://static.ivan.fun/blog/vue.jpg'
index_img: 'https://static.ivan.fun/blog/vue.jpg'
tags:
  - Vue
categories: -Vue.js
abbrlink: 4e7a873
date: 2020-04-03 08:00:00
---

# **Vue-Cli + Webpack 构建项目** #

## 1、检查是否安装了node，node -v
![](https://static.ivan.fun/blog/nodeV.jpg)

## 2、npm镜像替换成国内淘宝cnpm镜像
	
	- 获取原本的镜像地址
    - npm get registry 

	- 设成淘宝cnpm镜像地址
	- npm config set registry http://registry.npm.taobao.org/
	
## 3、全局安装Vue-cli
	
	-cnpm install vue-cli -g

## 4、全局安装webpack
	
	-cnpm install webpack -g

## 5、初始化构建项目
	
	- vue init webpack vuedemo

## 6、补充一些项目的基本信息
![](https://static.ivan.fun/blog/vueinit.jpg)

## 7、进入项目文件夹 查看package.json
![](https://static.ivan.fun/blog/1585723639(1).jpg)
	
	-npm run dev || npm run start 启动项目

![](https://static.ivan.fun/blog/rundev.jpg)

## 8、访问启动地址
	
	-http://localhost:8081/#/

![](https://static.ivan.fun/blog/vuedemo.jpg)