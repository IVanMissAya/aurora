---
title: 【微信小程序】使用ECharts绘制水波图
cover: 'https://static.ivan.fun/blog/water-cover.jpg'
index_img: 'https://static.ivan.fun/blog/water-cover.jpg'
tags:
  - 小程序
categories: '-小程序 -Echarts'
abbrlink: 3140581a
feature: true
date: 2020-06-06 17:30:00
---
# 微信小程序使用ECharts绘制水波图

## 效果图
![](https://static.ivan.fun/blog/liqud-water.jpg)

### 需要用到的插件 ECharts 的微信小程序版本 GitHub地址: https://github.com/ecomfe/echarts-for-weixin 
### 话不多说 上代码

***wxml***
```
<view class="container">
	<view class="canvasArea">
		<ec-canvas id="dispace_charts" canvas-id="dispace_charts" ec="{{ ec_dispace_charts }}"></ec-canvas>
	</view>
</view>
```

***wxss***
```
.container {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: pink;
}

.canvasArea {
  width: 300rpx;
  height: 300rpx;
}

.canvasArea canvas {
  width: 300rpx;
  height: 300rpx;
}
```
***js***
```
import * as echarts from '../ec-canvas/echarts';
import * as liquidFill from '../ec-canvas/echarts-liquidfill.min';
const app = getApp()
Page({
  data: {
    ec_dispace_charts: {
      lazyLoad: true
    },
  },
  onLoad: function () {
    // 获取组件
    this.ecComponent = this.selectComponent('#dispace_charts');
    this.initCharts(0.7);
  },
  initCharts: function (value) {
    this.ecComponent.init((canvas, width, height, dpr) => {
      // 获取组件的 canvas、width、height 后的回调函数
      // 在这里初始化图表
      const chart = echarts.init(canvas, null, {
        width: width,
        height: height,
        devicePixelRatio: dpr // new
      });
      let data = [value, value, value, ];
      chart.setOption(setOption(value, data));
      // 将图表实例绑定到 this 上，可以在其他成员函数（如 dispose）中访问
      this.chart = chart;
      // 注意这里一定要返回 chart 实例，否则会影响事件处理等
      return chart;
    });
  }
})

function setOption(value, data) {
  const option = {
    title: {
      text: (value * 100).toFixed(0) + '{a|%}',
      textStyle: {
        fontFamily: 'Microsoft Yahei',
        fontWeight: 'normal',
        color: '#fff',
        rich: {
          a: {
            fontSize: 18,
          }
        }
      },
      x: 'center',
      y: '40%'
    },
    series: [{
      type: 'liquidFill',
      radius: '80%',
      center: ['50%', '50%'],
      //  shape: 'roundRect',
      data: data,
      backgroundStyle: {
        color: {
          type: 'linear',
          x: 1,
          y: 0,
          x2: 0.5,
          y2: 1,
          colorStops: [{
            offset: 1,
            color: 'rgba(68, 145, 253, 0)'
          }, {
            offset: 0.5,
            color: 'rgba(68, 145, 253, .25)'
          }, {
            offset: 0,
            color: 'rgba(68, 145, 253, 1)'
          }],
          globalCoord: false
        },
      },
      outline: {
        borderDistance: 0,
        itemStyle: {
          borderWidth: 5,
          borderColor: {
            type: 'linear',
            x: 0,
            y: 0,
            x2: 0,
            y2: 1,
            colorStops: [{
              offset: 0,
              color: 'rgba(69, 73, 240, 0)'
            }, {
              offset: 0.5,
              color: 'rgba(69, 73, 240, .25)'
            }, {
              offset: 1,
              color: 'rgba(69, 73, 240, 1)'
            }],
            globalCoord: false
          },
          shadowBlur: 10,
          shadowColor: '#000',
        }
      },
      color: {
        type: 'linear',
        x: 0,
        y: 0,
        x2: 0,
        y2: 1,
        colorStops: [{
          offset: 1,
          color: 'rgba(255.0,0,0)'
        }, {
          offset: 0.5,
          color: 'rgba(266, 69, 0,.5)'
        }, {
          offset: 0,
          color: 'rgba(255, 99, 71,1)'
        }],
        globalCoord: false
      },
      label: {
        normal: {
          formatter: '',
        }
      }
    }]
  };
  return option
}
```