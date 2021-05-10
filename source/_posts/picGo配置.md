---
title: 图床工具的使用 - PicGo
cover: 'https://static.ivan.fun/blog/picgo2-1024x576.png'
index_img: 'https://static.ivan.fun/blog/picgo2-1024x576.png'
tags:
  - 图床
categories: -软件工具
abbrlink: 9afd02e1
date: 2020-04-14 10:20:00
---
# PicGo介绍
## 这是一款图片上传的工具，目前支持微博图床，七牛图床，阿里云，腾讯云，又拍云，GitHub等图床，未来将支持更多图床。

这是一款图片上传的工具，目前支持 **微博图床**，**七牛图床**，**腾讯云**，**又拍云**，**阿里云OSS**，**GitHub**等图床，未来将支持更多图床。

![](https://static.ivan.fun/blog/3146329-a818a6851f18d336.webp)

下面的链接下载最新的版本
```
https://github.com/Molunerfinn/PicGo/releases
```
注意：mac 系统选择 dmg 下载，windwos 选择 .exe系统，如果不是下载安装包，想看源码的话，可以选择 git clone https://github.com/Molunerfinn/PicGo.git 克隆到本地

![](https://static.ivan.fun/blog/15194389-3e994af556f91ac4.webp)

## 创建自己的图床

1.创建GitHub图床之前，需要注册/登陆GitHub账号 申请Github账号很简单，这里就不演示了

2.创建Repository

![](https://static.ivan.fun/blog/1586830250.jpg)

3.生成一个 accessToken 用于操作 Github Repository
![](https://static.ivan.fun/blog/findSetting.jpg)
![](https://static.ivan.fun/blog/generatorToken.jpg)
![](https://static.ivan.fun/blog/findSetting.jpg)
![](https://static.ivan.fun/blog/3146329-2cddeffa8fe35933.webp)

3. 配置图床
![](https://static.ivan.fun/blog/picgoSet.jpg)

- 设定仓库名的时候，是按照“账户名/仓库名的格式填写”
- 分支名统一填写“master”
- 将之前的Token黏贴在这里
- 存储的路径可以按照我这样子写，就会在repository下创建一个“img”文件夹
- 自定义域名的作用是，在上传图片后成功后，PicGo会将“自定义域名+上传的 图片名”生成的访问链接，放到剪切板上https://raw.githubusercontent.com/用户名/RepositoryName/分支名，，自定义域名需要按照这样去填写

4. 快捷键相关设置
![](https://static.ivan.fun/blog/codeShort.jpg)

```
注：可以将快捷键设置为ctrl+shift+c
```

### 总结 
将上面的步骤都设置好之后，就可以让自己的Markdown文档飞起来了，每次截图之后，都可以按一下ctrl+shift+c，这样就会将剪切板上面的截图转化为在线网络图片链接，简直就是爽的不要不要的！！