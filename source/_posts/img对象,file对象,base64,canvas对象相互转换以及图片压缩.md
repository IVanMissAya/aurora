---
title: img 对象，file 对象，base64，canvas 对象相互转换以及图片压缩
cover: 'https://ivan-picgo.oss-cn-shenzhen.aliyuncs.com/blog/markus-spiske-xekxE_VR0Ec-unsplash.jpg'
index_img: 'https://ivan-picgo.oss-cn-shenzhen.aliyuncs.com/blog/markus-spiske-xekxE_VR0Ec-unsplash.jpg'
tags:
  - JavaScript
categories: '-JavaScript'
abbrlink: b7bc5b86
date: 2021-03-13 18:00:00
---
# img 对象，file 对象，base64，canvas 对象相互转换以及图片压缩

先上一张图

![](https://ivan-picgo.oss-cn-shenzhen.aliyuncs.com/blog/1475680-20190312204416645-1976367767.png)

## 该图片是 js 几乎所有图片类型的转换方式了。接下来讲讲几种常用的类型转换：

1. ### urltoImage(url,fn) 会通过一个 url 加载所需要的图片对象，其中 url 参数传入图片的 url , fn 为回调方法,包含一个 Image 对象的参数，代码如下：

   ```
   function urltoImage (url,fn){
     var img = new Image();
     img.src = url;
     img.onload = function(){
       fn(img);
     }
   };
   ```

2. ### imagetoCanvas(image) 会将一个 Image 对象转变为一个 Canvas 类型对象，其中 image 参数传入一个 Image 对象，代码如下：

   ```
   function imagetoCanvas(image){
     var cvs = document.createElement("canvas");
     var ctx = cvs.getContext('2d');
     cvs.width = image.width;
     cvs.height = image.height;
     ctx.drawImage(image, 0, 0, cvs.width, cvs.height);
     return cvs ;
   };
   ```

3. ### canvasResizetoFile(canvas,quality,fn) 会将一个 Canvas 对象压缩转变为一个 Blob 类型对象；其中 canvas 参数传入一个 Canvas 对象; quality 参数传入一个 0-1 的 number 类型，表示图片压缩质量; fn 为回调方法，包含一个 Blob 对象的参数;代码如下：

   ```
   function canvasResizetoFile(canvas,quality,fn){
     canvas.toBlob(function(blob) {
       fn(blob);
     },'image/jpeg',quality);
   };
   ```

   这里的 Blob 对象表示不可变的类似文件对象的原始数据。 Blob 表示不一定是 JavaScript 原生形式的数据。 File 接口基于 Blob ，继承了 Blob 的功能并将其扩展使其支持用户系统上的文件。我们可以把它当做 File 类型对待，其他更具体的用法可以参考 MDN 文档。

4. ### canvasResizetoDataURL(canvas,quality) 会将一个 Canvas 对象压缩转变为一个 dataURL 字符串,其中 canvas 参数传入一个 Canvas 对象; quality 参数传入一个 0-1 的 number 类型，表示图片压缩质量;代码如下：

   ```
   methods.canvasResizetoDataURL = function(canvas,quality){
     return canvas.toDataURL('image/jpeg',quality);
   };
   ```

5. ### filetoDataURL(file,fn) 会将 File （ Blob ）类型文件转变为 dataURL 字符串,其中 file 参数传入一个 File （ Blob ）类型文件; fn 为回调方法，包含一个 dataURL 字符串的参数;代码如下：

   ```
   function filetoDataURL(file,fn){
      var reader = new FileReader();
      reader.onloadend = function(e){
        fn(e.target.result);
      };
      reader.readAsDataURL(file);
   };
   ```

6. ### dataURLtoImage(dataurl,fn) 会将一串 dataURL 字符串转变为 Image 类型文件,其中 dataurl 参数传入一个 dataURL 字符串, fn 为回调方法，包含一个 Image 类型文件的参数，代码如下：

   ```
   function dataURLtoImage(dataurl,fn){
      var img = new Image();
      img.onload = function() {
        fn(img);
      };
      img.src = dataurl;
   };
   ```

7. ### dataURLtoFile(dataurl) 会将一串 dataURL 字符串转变为 Blob 类型对象，其中 dataurl 参数传入一个 dataURL 字符串,代码如下：

   ```
   function dataURLtoFile(dataurl) {
      var arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
      while(n--){
        u8arr[n] = bstr.charCodeAt(n);
      }
      return new Blob([u8arr], {type:mime});
   };
   ```

> ## 以上 7 种转换基本可以覆盖所有类型转换了，下面看下 JS 等比压缩图片的办法：

```
function proDownImage(path,imgObj) { // 等比压缩图片工具
  //var proMaxHeight = 185;
  var proMaxHeight=300;
  var proMaxWidth = 175;
  var size = new Object();　
  var image = new Image();　
  image.src = path;　
  image.attachEvent("onreadystatechange",
  function() { // 当加载状态改变时执行此方法,因为img的加载有延迟
    if (image.readyState == "complete") { // 当加载状态为完全结束时进入
      if (image.width > 0 && image.height > 0) {
        var ww = proMaxWidth / image.width;
        var hh = proMaxHeight / image.height;　
        var rate = (ww < hh) ? ww: hh;
        if (rate <= 1) {　
          alert("imgage width*rate is:" + image.width * rate);
          size.width = image.width * rate;
          size.height = image.height * rate;
        } else {
          alert("imgage width is:" + image.width);　　
          size.width = image.width;　　
          size.height = image.height;　　　
        }　
      }
    }
    imgObj.attr("width",size.width);
    imgObj.attr("height",size.height);
  });
}
```
