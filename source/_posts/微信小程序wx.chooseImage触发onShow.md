---
title: 【微信小程序】wx.chooseImage方法Bug
cover: 'https://static.ivan.fun/blog/choImage.jpg'
index_img: 'https://static.ivan.fun/blog/choImage.jpg'
tags:
  - 小程序
categories: '-小程序'
abbrlink: 1e2f5eda
date: 2020-06-12 16:00:00
---
# 微信小程序wx.chooseImage方法会引发底层Bug

在微信小程序中调用**wx.chooseImage**方法选择图片，选择完图片之后页面会莫名的跳转，找了很久才发现原因。

研究发现调用**wx.chooseImage**方法之后会触发入口文件**app.js**中的**onLaunch**、**onShow**方法，然后再触发当前页面的**onHide**、**onShow**方法。逻辑如下：

```
app.onLaunch();
app.onShow();
page.onHide();
page.onShow();
```

这四个方法中肯定有业务逻辑，如果不做特殊的处理会导致无法预估的问题。解决办法如下：

1. page外全局定义开关变量

```
var switch;
Page({

})
```

2. onshow事件中：

```
if (switch) {
    switch = false;
    return;
}
```

3. 在你需要调用 chooseImage 之前，将这个开关变量设置为 true

```
switch = true
wx.chooseImage({})
```