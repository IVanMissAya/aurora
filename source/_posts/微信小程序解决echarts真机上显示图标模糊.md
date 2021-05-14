---
title: 【微信小程序】引用echarts 在真机上预览图表模糊的解决办法
cover: 'https://static.ivan.fun/blog/timg.jpg'
index_img: 'https://static.ivan.fun/blog/timg.jpg'
tags:
  - 小程序
categories: '-小程序 -Echarts'
abbrlink: 2633b48b
feature: true
date: 2020-04-26 09:30:00
---
# 【微信小程序】引用echarts 在真机上预览图表模糊的解决办法

1. 获取移动设备的像素比 获取系统信息 ==> wx.getSystemInfo()  API说明：**wx.getSystemInfo()**

```
const getPixelRatio = () => {
    let pixelRatio = 0
       wx.getSystemInfo({
          success: function (res) {
           pixelRatio = res.pixelRatio
      },
      fail: function () {
         pixelRatio = 0
     	}
     })
    return pixelRatio
}
```

2. 初始化图表的时候设置像素比 devicePixelRatio API说明：***echarts.init***
#### 案例

```
	 //获取像素比
      const getPixelRatio = () => {
        let pixelRatio = 0
        wx.getSystemInfo({
          success: function (res) {
            pixelRatio = res.pixelRatio
          },
          fail: function () {
            pixelRatio = 0
          }
        })
        return pixelRatio
      }
      // console.log(pixelRatio)
      var dpr = getPixelRatio()
        // 初始化图表
      const chart = echarts.init(canvas, null, {
        // renderer: 'svg',//微信小程序中不支持该设置
        width: width,
        height: height,
        devicePixelRatio: dpr
      });
      setOption(chart,this);


```
