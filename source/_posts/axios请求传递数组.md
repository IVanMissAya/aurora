---
title: Axios请求传递数组的问题
cover: 'https://ivan-picgo.oss-cn-shenzhen.aliyuncs.com/blog/axios.jpg'
index_img: 'https://ivan-picgo.oss-cn-shenzhen.aliyuncs.com/blog/axios.jpg'
tags:
  - JavaScript
categories: '-JavaScript'
abbrlink: 6aa52416
date: 2021-03-15 18:00:00
---
# Vue 开发前端，向后台 springboot 传递数组，springboot 接收数组方式无法使用

-- @RequestParam("ids[]") String [] ids --

然后抛出以下异常，经过多轮验证发现@RequestParam("ids[]") 失效，无法使用

> Resolved [org.springframework.web.bind.MissingServletRequestParameterException]  Required String[] parameter 'ids[]' is not present]

## 解决方案

- ### 后端使用 @RequestParam(value="ids[]" String [] ids)

  ![](https://ivan-picgo.oss-cn-shenzhen.aliyuncs.com/blog/1615800526123sda.jpg)

  ```
  qs.stringify({ids: [1, 2, 3]}, { indices: false })
  //形式： ids=1&ids=2&id=3
  qs.stringify({ids: [1, 2, 3]}, {arrayFormat: ‘indices‘})
  //形式： ids[0]=1&aids1]=2&ids[2]=3
  qs.stringify({ids: [1, 2, 3]}, {arrayFormat: ‘brackets‘})
  //形式：ids[]=1&ids[]=2&ids[]=3
  qs.stringify({ids: [1, 2, 3]}, {arrayFormat: ‘repeat‘})
  //形式： ids=1&ids=2&id=3
  ```

- ### 后端不使用 @RequestParam(value="ids[]" String [] ids)

  1. 后端方法接收去除@RequestParam ，然后使用数组接收 例如：String[] ids
     ![](https://ivan-picgo.oss-cn-shenzhen.aliyuncs.com/blog/20190528035401142.png)

  2. 前端传递数组 axios 需要使用 URLSearchParams 包裹数组
     ![](https://ivan-picgo.oss-cn-shenzhen.aliyuncs.com/blog/20190528035506628.png)

  3. 请求头中参数传递显示为如图所示：无论是 axios 还是 ajax 只要请求头传递格式是这样的，后端用 String[] ids 接收就行了
     ![](https://ivan-picgo.oss-cn-shenzhen.aliyuncs.com/blog/20190528035628155.png)
