---
title: CDN 如何通过 HTTP 头配置跨域资源共享（CORS）
cover: "https://static.ivan.fun/blog/cdnBanner.jpeg"
index_img: "https://static.ivan.fun/blog/cdnBanner.jpeg"
tags:
  - CDN
  - CORS
categories: -CDN 
abbrlink:
feature: true 
date: 2021-07-13 20:00:00
---

# CDN 如何通过 HTTP 头配置跨域资源共享（CORS）

1. 登录 CDN 控制台。
2. 在域名管理页面，选择需要配置 CORS 功能域名右侧的管理。
3. 单击缓存配置，选择自定义 HTTP 响应头，单击添加。
   ![](https://onekb.oss-cn-zhangjiakou.aliyuncs.com/51061015/be38e546-4961-475c-86a4-3775b0c73683.png)
4. 进入 **自定义 HTTP 响应头** 页面，请按照以下内容进行选择，设置指定允许的跨域请求的来源，然后单击**确定**保存配置
   |**参数**|**示例**|
   |--|:--:|
   响应头操作|增加|
   自定义响应头参数|Access-Control-Allow-Methods|
   响应头值|GET,POST,PUT (说明：如果您需要同时添加 POST、GET、PUT，请使用英文逗号（,）隔开。)
   是否允许重复|不允许

   - 允许表示允许重复，即源站返回的头会保留，同时会加上一个同名的头
   - 不允许表示不允许重复，即源站返回的头会被新配置的同名头覆盖。
   - 本文以不允许重复为例，现场可根据实际环境而定。 |

     ![](https://onekb.oss-cn-zhangjiakou.aliyuncs.com/51061015/73ce708f-617d-48f8-96c4-829443beab70.jpg)

## 更多信息

> 以下为配置跨域资源共享（CORS）的更多说明：

- 目前仅支持配置一条白名单域名。
- 若使用 OSS 作为源站，OSS 与 CDN 控制台同时配置 CORS，CDN 的配置将覆盖 OSS。
- 若源站为本地服务器或 ECS 实例，建议先进行动静分离，静态文件使用 CDN 加速，CDN 控制台配置的 CORS 功能，仅对静态文件生效。
