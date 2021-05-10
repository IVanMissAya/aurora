---
title: Java处理emoji的方式
cover: 'https://static.ivan.fun/blog/java_logo.jpg'
index_img: 'https://static.ivan.fun/blog/java_logo.jpg'
tags:
  - Java
categories: -Java
abbrlink: ab91153b
date: 2020-04-14 10:00:00
---
# Java处理emoji的方式
## 问题由来: java后端获取微信小程序用户昵称含emoji时,显示乱码。

## 处理思路

- **过滤**
	1. 是当接收到客户端传过来得数据---->正则判断传过来的字符串中是否包含emoji---->如果包含emoji表情的话就通过正则把emoji表情过滤掉（或者把emoji表情替换成指定字符）---->仅保存过滤emoji表情后的字符串
	
	2. 使用 emoji-java. 首先导入依赖包
	
```   
  <dependency>
    <groupId>com.vdurmont</groupId>
    <artifactId>emoji-java</artifactId>
    <version>4.0.0</version>
  </dependency>
```
```
  然后使用 EmojiParser.removeAllEmojis(text); 这个方式移除字符中所有的emoji
```

- **后端编码处理**
  1. 通过emoji-java这个第三方提供的库进行处理,其中里面有两个常用的方法，**EmojiParser.parseToAliases()** 和 **EmojiParser.parseToUnicode()** 把emoji表情保存进数据库前调用parseToAliases()这个方法会把emoji表情转化成“:grinning:”这样的字符串，当想要把此表情从数据库里取出返回给客户端时则调parseToUnicode()这个方法。说明：如果客户端传过来的参数是有emoji表情+字符串组成，那么parseToAliases()这个方法只会处理表情，字符串部分会原封不动的存进数据库

- **数据库处理**
  1. 修改mysql数据库的配置，达到直接存储emoji表情的目的，程序无需再对emoji表情做额外处理（对mysql的版本有要求，5.7或以上版本）
 
```
修改database,table,column字符集：
```

```
  ALTER DATABASE database_name CHARACTER SET = utf8mb4 COLLATE=utf8mb4_unicode_ci;

  ALTER TABLE table_name CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

  ALTER TABLE table_name CHANGE column_name VARCHAR(191) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

- 编辑my.cnf文件，在对应章节添加如下内容：

[client]
default-character-set = utf8mb4
[mysql]
default-character-set = utf8mb4
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
init_connect='SET NAMES utf8mb4'
- 重启mysql服务即可

 