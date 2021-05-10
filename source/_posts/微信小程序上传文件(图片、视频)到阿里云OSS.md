---
title: 【微信小程序】上传文件到阿里云OSS
cover: 'https://static.ivan.fun/blog/oss_index.jpg'
index_img: 'https://static.ivan.fun/blog/oss_index.jpg'
tags:
  - 小程序
  - OSS
categories: '-小程序'
abbrlink: cf1bf869
date: 2020-07-02 17:30:00
---
# 微信小程序上传文件到阿里云OSS

## 小程序上传文件到OSS也是利用OSS提供的PostObject接口来实现表单文件上传到OSS

## 步骤1：配置Bucket跨域访问
###  客户端进行表单直传到OSS时，会从浏览器向OSS发送带有Origin的请求消息。OSS对带有Origin头的请求消息会进行跨域规则（CORS）的验证。因此需要为Bucket设置跨域规则以支持Post方法。

1. 登录OSS管理控制台。
2. 单击Bucket列表，之后单击目标Bucket名称。
3. 单击权限管理 > 跨域设置，在跨域设置区域单击设置。
4. 单击创建规则，配置如下图所示
![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0520621951/p12308.png)

## 步骤2：配置外网域名到小程序的域名白名单
1. 登录OSS管理控制台。
2. 单击Bucket列表，之后单击目标Bucket名称。
3. 存储空间概览页的访问域名区域查看Bucket域名。
![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0520621951/p62609.png)
4. 登录微信小程序平台，配置小程序的上传域名白名单。
![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0520621951/p62625.png)

## 步骤3：小程序端代码

***config.js***  //阿里云oss Bucket的配置信息
```
var fileHost="xxxx.aliyuncs.com(你的阿里云oss地址)"
var config = {
  //aliyun OSS config
  uploadImageUrl: `${fileHost}`, //默认存在根目录，可根据需求改
  AccessKeySecret: '填你自己的AccessKeySecret',
  OSSAccessKeyId: '填你自己的 OSSAccessKeyId',
  timeout: 87600 //这个是上传文件时Policy的失效时间
};
module.exports = config
```

***Base64,hmac,sha1,crypto*** 相关算法点击 [这里](https://github.com/IVanMissAya/weixinFileToAliYunOss)

### 参数说明

|参数|类型|必填|说明|
|--|:--:|:--:|:--:|
|filePath|String|是|微信本地的文件名|
|dir|String|是|保存到阿里云oss的目录，可填""(空字符串)|
|success|Function|否|上传成功的回调函数，返回值为保存的网络路径|
|fail|Function|否|上传失败的回调函数，返回值与官方uploadfile api文档一致|

### 代码实现

***UploadAliyun.js***
```
const env = require('../config.js'); //配置文件，在这里配置你的OSS keyId和KeySecret,timeout:87600;
//更好的做法是把这些信息放到服务器进行签名，防止信息泄露

const Base64 = require('./Base64.js');

require('./hmac.js');
require('./sha1.js');
const Crypto = require('./crypto.js');

const uploadFile = function (filePath, dir, successc, failc) {
    if (!filePath || filePath.length < 9) {
        wx.showModal({
            title: '图片错误',
            content: '请重试',
            showCancel: false,
        })
        return;
    }

    console.log('上传图片…');
    const aliyunFileKey = dir+filePath.replace('wxfile://', '');//我直接拿微信本地的名字当做传到服务器上的名字了，dir传的是images/，表示要传到这个目录下

    // const aliyunFileKey = fileW + '' + (new Date().getTime()) + '_' + objectId + '.mp4';
    //const aliyunFileKey = fileW 
    const aliyunServerURL = env.uploadImageUrl;//OSS地址，需要https
    const accessid = env.OSSAccessKeyId;
    //const policyBase64 = env.Policy;
    //const signature = env.Signature;
     const policyBase64 = getPolicyBase64();
     const signature = getSignature(policyBase64);//获取签名

    console.log('aliyunFileKey=', aliyunFileKey);
    console.log('aliyunServerURL',aliyunServerURL);
    wx.uploadFile({
        url: aliyunServerURL,
        filePath: filePath,
        name: 'file',//必须填file
        formData: {
            'key': aliyunFileKey,
            'policy': policyBase64,           
            'OSSAccessKeyId': accessid,
            'signature': signature,           
            'success_action_status': '200',
        },
        success: function (res) {
            if (res.statusCode != 200) {
                failc(new Error('上传错误:' + JSON.stringify(res)))
                return;
            }
            console.log('上传图片成功', res)
            successc(aliyunFileKey);
        },
        fail: function (err) {
            err.wxaddinfo = aliyunServerURL;
            failc(err);
        },
    })
}

const getPolicyBase64 = function () {
    let date = new Date();
    date.setHours(date.getHours() + env.timeout);
    let srcT = date.toISOString();
    const policyText = {
        "expiration": srcT, //设置该Policy的失效时间，超过这个失效时间之后，就没有办法通过这个policy上传文件了 
        "conditions": [
            ["content-length-range", 0, 5*1024 * 1024] // 设置上传文件的大小限制,5mb
        ]
    };

    const policyBase64 = Base64.encode(JSON.stringify(policyText));
    return policyBase64;
}

const getSignature = function (policyBase64) {
    const accesskey = env.AccessKeySecret;

    const bytes = Crypto.HMAC(Crypto.SHA1, policyBase64, accesskey, {
        asBytes: true
    });
    const signature = Crypto.util.bytesToBase64(bytes);

    return signature;
}

module.exports = uploadFile;
```

### 代码使用   

```
const uploadImage = require('../../utils/uploadAliyun.js');

//filePath为要上传的本地文件的路径
//images/为oss目录
uploadImage(filePath, "images/",
      function (res) {
        console.log("上传成功")
        //todo 做任何你想做的事
      }, function (res) {
        console.log("上传失败")
        //todo 做任何你想做的事
      }
```

基本上就是这样，有不明白的就留言吧~

---