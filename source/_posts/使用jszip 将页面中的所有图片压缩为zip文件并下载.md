---
title: JavaScript 使用JSZip保存文件压缩包并下载
cover: 'https://static.ivan.fun/blog/jszip.jpg'
index_img: 'https://static.ivan.fun/blog/jszip.jpg'
tags:
  - JavaScript
categories: '-JavaScript'
abbrlink: 3b99bdb9
date: 2020-11-11 17:00:00
---
## [JSZip](https://stuk.github.io/jszip/)
>jszip是一个用于创建、读取和编辑.zip文件的JavaScript库，且API的使用也很简单。

1. 官方给的栗子
```
// 初始化一个zip打包对象
var zip = new JSZip();
// 创建一个被用来打包的名为Hello.txt的文件
zip.file("Hello.txt", "Hello Worldn");
// 创建一个名为images的新的文件目录
var img = zip.folder("images");
// 这个images文件目录中创建一个base64数据为imgData的图像，图像名是smile.gif
img.file("smile.gif", imgData, {base64: true});
// 把打包内容异步转成blob二进制格式
zip.generateAsync({type:"blob"}).then(function(content) {
    // content就是blob数据，这里以example.zip名称下载    
    // 使用了FileSaver.js  
    saveAs(content, "example.zip");
});
```
2. 话不多说 上代码
```
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8">
		<title>Title</title>
		<script src="https://cdn.bootcdn.net/ajax/libs/jszip/3.3.0/jszip.js"></script>
	</head>
	<body>
		<img src="./img/desp.jpg" alt="">
		<img src="./img/headIcon.jpg" alt="">
	</body>

	<script>
		function getBase64Image(img) {
			let canvas = document.createElement("canvas");
			canvas.width = img.width;
			canvas.height = img.height;

			let ctx = canvas.getContext("2d");
			ctx.drawImage(img, 0, 0, img.width, img.height);
			let dataURL = canvas.toDataURL("image/jpeg");
			return dataURL;
		}

		let imgList = [...document.getElementsByTagName('img')]
		console.log(imgList)

		let buffer = imgList.map(getBase64Image)
		console.log('buffer', buffer)

		function saveAs(blob, name) {
			let a = document.createElement('a')
			let url = window.URL.createObjectURL(blob)
			a.href = url
			a.download = name
			a.click()
		}

		async function main() {
			let zip = new JSZip();
			let p = buffer.map(
				x => zip.file(Math.random().toString('36').slice(3) + '.jpeg', x.split(',')[1], {
					base64: true
				})
			)
			await Promise.all(p)
			zip.generateAsync({
				type: "blob"
			}).then(function(content) {
				// see FileSaver.js
				saveAs(content, "example.zip");
			});
		}

		main()
	</script>
</html>
```