---
title: 阿里云 CDN + OSS 解决方案
cover: 'https://static.ivan.fun/blog/cdn_timg.jpg'
index_img: 'https://static.ivan.fun/blog/cdn_timg.jpg'
tags:
  - CDN
  - OSS
categories: '-OSS,CDN'
abbrlink: 7d87ea3f
feature: true
date: 2020-07-15 17:30:00
---
# 阿里云 CDN + OSS 解决方案

## 前言

***直接使用阿里云的OSS+CDN的方案有几大好处:***

- 成本低廉。OSS+CDN部署自己的网站每个月的花费远比自己买ECS服务器部署网站花费要少得多
- 大幅降低运维成本。直接使用现成的云服务了，无需花精力去维护ECS了。
- 极高的可用性。无论阿里云的OSS还是CDN，都已经做好了高可用性，几乎可以保证网站始终可访问
- 极高的访问速度。ECS带宽毕竟有限的，高带宽意味着超高的费用。但是用OSS+CDN这种天然分布式的架构部署网站很轻松的解决了带宽问题，极大地提升了用户的访问体验。

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7140115851/p50521.png)

## 步骤

1. 添加二级域名 
   假设你已经有一台服务器和自己的域名，现在我们首先要做的是添加一个二级域名，作为静态资源域名，这样不用全站cdn，这里我设置为 test.ivan.fun ，在域名管理中添加二级解析。
   ![](https://static.ivan.fun/blog/addSonYu.jpg)
   ![](https://static.ivan.fun/blog/sonYu2.jpg)
   ![](https://static.ivan.fun/blog/sonYu3.jpg)
2. 添加 OSS 服务
   - 进入 oss 控制台，点击右侧的新建 bucket
   ![](https://img-blog.csdn.net/20171013102152849?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjgwMTgyODM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
   点击左侧新建的 bucket ,获取 access_key 和 access_sercet 之后,上传图片到 oss 的方法可以参考文档,或者我的另外一篇博客 ： [【微信小程序】上传文件到阿里云OSS](https://www.ivan.fun/posts/cf1bf869/) , 值得一提的是，oss 不是免费的 。
   - 进入bucket 域名管理 添加刚创建的子域名,选择自动添加CNAME记录
   ![](https://static.ivan.fun/blog/addSon.jpg)
3. 添加 CDN
   ![](https://static.ivan.fun/blog/cdn1.jpg)

## 验证
打开 CMD  直接 ping 刚才创建的 二级域名的地址 如下图所示 则配置成功
![](https://static.ivan.fun/blog/pingsuccess.jpg)
然后 二级域名访问oss里的图片 能直接访问则配置成功

## 总结
实现流程
1. 先添加、配置二级域名
2. oss里bucket 域名管理配置域名 这是cdn访问 自动添加cname
3. 去域名解析 在二级域名里 添加 cdn的cname配置