---
title: CentOS部署项目
cover: 'https://static.ivan.fun/blog/CentOS.jpg'
index_img: 'https://static.ivan.fun/blog/CentOS.jpg'
tags:
  - CentOS
categories: '-CentOS'
abbrlink: b02bb2ee
date: 2020-03-14 08:00:00
---

# 阿里云服务器部署web项目 CentOS7+JDK1.8+Tomcat8+MySQL5.7
## 1 准备工作
### 1.1 查看Linux服务器版本
```
[lishuai@svr1 ~]$ lsb_release -a
LSB Version:	:core-4.1-amd64:core-4.1-noarch
Distributor ID:	CentOS
Description:	CentOS Linux release 7.4.1708 (Core) 
Release:	7.4.1708
Codename:	Core
```
若提示未该命令，则执行以下命令进行安装
```
yum install -y redhat-lsb
```
Linux系统分为CentOS、Ubuntu、Debian，此处为64位CentOS7。
### 1.2 Linux系统介绍 
#### 1.2.1 CentOS系统
非常多的商业公司部署在生产环境上的服务器都是使用CentOS系统，CentOS是从Redhat源代码编译重新发布版，CentOS去除很多与服务器功能无关的应用，系统简单但非常稳定，命令行操作可以方便管理系统和应用，并且有帮助文档和社区的支持。
#### 1.2.2 Ubuntu系统
Ubuntu系统有着靓丽的用户界面，完善的包管理系统，强大的软件源支持，丰富的技术社区，并且Ubuntu对计算机硬件的支持优于CentOS和Debian，兼容性强，Ubuntu应用非常多，但是对于服务器操作系统来说，并不需要太多的应用程序，需要的是稳定，操作方便，维护简单的系统。如果你需要在服务器端使用图形界面，Ubuntu是一个不错的选择，你需要注意的是，图形界面占用的内存非常大，而内存越大的VPS（Virtual Private Server 虚拟专用服务器）价格也越高。
#### 1.2.3 Debian系统
Debian系统也非常适合做服务器操作系统，与Ubuntu比较，它没有太多的花哨，稳定压倒一切，对于服务器操作系统来说是一条不变的真理，Debian这个Linux系统，底层非常稳定，内核和内存的占用都非常小，在小内存的VPS就可以顺畅运行Debian,比如128M的内存，但Debian的帮助文档和技术资料比较少，对于小内存，首选Debian，对于非常熟悉Linux系统的VPS高手，首选Debian。

## 2 安装JDK
### 2.1 查看是否已安装JDK
```
[lishuai@svr1 ~]$ yum list installed | grep java
java-1.8.0-openjdk.x86_64          1:1.8.0.171-7.b10.el7               @updates 
java-1.8.0-openjdk-headless.x86_64 1:1.8.0.171-7.b10.el7               @updates 
javapackages-tools.noarch          3.4.1-11.el7                        @base    
python-javapackages.noarch         3.4.1-11.el7                        @base    
tzdata-java.noarch                 2018e-3.el7                         @updates
```
可以看到该环境已安装JDK1.8，若没有安装可执行以下命令进行安装。
### 2.2 查看yum库里有哪些版本可以安装
```
[lishuai@svr1 ~]$ yum -y list java*
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Installed Packages
java-1.8.0-openjdk.x86_64                                                                                     1:1.8.0.171-7.b10.el7                                                                      @updates
java-1.8.0-openjdk-headless.x86_64                                                                            1:1.8.0.171-7.b10.el7                                                                      @updates
javapackages-tools.noarch                                                                                     3.4.1-11.el7                                                                               @base   
Available Packages
java-1.6.0-openjdk.x86_64                                                                                     1:1.6.0.41-1.13.13.1.el7_3                                                                 base    
java-1.6.0-openjdk-demo.x86_64                                                                                1:1.6.0.41-1.13.13.1.el7_3                                                                 base    
java-1.6.0-openjdk-devel.x86_64                                                                               1:1.6.0.41-1.13.13.1.el7_3                                                                 base    
java-1.6.0-openjdk-javadoc.x86_64                                                                             1:1.6.0.41-1.13.13.1.el7_3                                                                 base    
java-1.6.0-openjdk-src.x86_64                                                                                 1:1.6.0.41-1.13.13.1.el7_3                                                                 base    
java-1.7.0-openjdk.x86_64                                                                                     1:1.7.0.191-2.6.15.4.el7_5                                                                 updates 
java-1.7.0-openjdk-accessibility.x86_64                                                                       1:1.7.0.191-2.6.15.4.el7_5                                                                 updates 
java-1.7.0-openjdk-demo.x86_64                                                                                1:1.7.0.191-2.6.15.4.el7_5                                                                 updates 
java-1.7.0-openjdk-devel.x86_64                                                                               1:1.7.0.191-2.6.15.4.el7_5                                                                 updates 
java-1.7.0-openjdk-headless.x86_64                                                                            1:1.7.0.191-2.6.15.4.el7_5                                                                 updates 
java-1.7.0-openjdk-javadoc.noarch                                                                             1:1.7.0.191-2.6.15.4.el7_5                                                                 updates 
java-1.7.0-openjdk-src.x86_64                                                                                 1:1.7.0.191-2.6.15.4.el7_5                                                                 updates 
java-1.8.0-openjdk.i686                                                                                       1:1.8.0.181-3.b13.el7_5                                                                    updates 
java-1.8.0-openjdk.x86_64                                                                                     1:1.8.0.181-3.b13.el7_5                                                                    updates 
java-1.8.0-openjdk-accessibility.i686
```

### 2.3 安装JDK1.8
```
[lishuai@svr1 ~]$ yum -y install java-1.8.0-openjdk*
```

## 3 安装Tomcat

### 3.1 下载Tomcat
打开Tomcat官网`http://tomcat.apache.org`，在左侧栏的Download里选择版本，我选择的是Tomcat 8；之后选择最新版本8.5.33，选择下载tar.gz包。
![](https://static.ivan.fun/blog/Tomcat8.5.33.png)
也可以通过链接`http://ftp.tsukuba.wide.ad.jp/software/apache/tomcat/tomcat-8/v8.5.33/bin/apache-tomcat-8.5.33.tar.gz`直接下载该版本的Tomcat。
### 3.2 安装Tomcat
将压缩包上传到Linux服务器，可使用`rz`命令上传

解压
```
[lishuai@svr1 ~]$ tar -xvzf apache-tomcat-8.5.33.tar.gz
```
修改端口和编码，Tomcat默认端口号为8080

```
#进入Tomcat目录下的conf目录
[lishuai@svr1 ~]$ cd apache-tomcat-8.5.33/conf

#打开server.xml
[lishuai@svr1 conf]$ vi server.xml

#修改端口号
<Connector port="8548" protocol="HTTP/1.1"
               connectionTimeout="20000" URIEncoding="UTF-8"
               redirectPort="8443" />
```
### 3.3 常用命令
```
#启动Tomcat 在Tomcat解压目录的bin目录下执行
#启动完成后可在logs路径下的查看启动日志
#通过访问url[http://address:port]，若显示Tomcat信息则启动成功
[lishuai@svr1 bin]$ sh startup.sh
Using CATALINA_BASE:   /home/lishuai/apache-tomcat-8.5.33
Using CATALINA_HOME:   /home/lishuai/apache-tomcat-8.5.33
Using CATALINA_TMPDIR: /home/lishuai/apache-tomcat-8.5.33/temp
Using JRE_HOME:        /usr
Using CLASSPATH:       /home/lishuai/apache-tomcat-8.5.33/bin/bootstrap.jar:/home/lishuai/apache-tomcat-8.5.33/bin/tomcat-juli.jar
Tomcat started.

#停止Tomcat 在Tomcat目录的bin目录下执行
[lishuai@svr1 bin]$ sh shutdown.sh
Using CATALINA_BASE:   /home/lishuai/apache-tomcat-8.5.33
Using CATALINA_HOME:   /home/lishuai/apache-tomcat-8.5.33
Using CATALINA_TMPDIR: /home/lishuai/apache-tomcat-8.5.33/temp
Using JRE_HOME:        /usr
Using CLASSPATH:       /home/lishuai/apache-tomcat-8.5.33/bin/bootstrap.jar:/home/lishuai/apache-tomcat-8.5.33/bin/tomcat-juli.jar

#查看端口状态
#Tomcat启动后的端口状态
[lishuai@svr1 bin]$ netstat -an | grep 8548
tcp        0      0 0.0.0.0:8548            0.0.0.0:*               LISTEN
#Tomcat刚停止后的端口状态
[lishuai@svr1 bin]$ netstat -an | grep 8548
tcp        0      0 172.31.13.141:36474     172.31.13.141:8548      TIME_WAIT
```
### 3.4 注意事项
若通过url`[http://address:port]`访问已经启动的Tomcat，显示错误`500 Internal Privoxy Error`，是因为阿里云服务器需要配置安全组规则开放相应的端口才能访问。

## 4 安装MySQL
### 4.1 选择MySQL版本
#### 4.1.1 直接下载MySQL
登录MySQL官网`https://www.mysql.com/`，如下图所示，选择MySQL版本，下载与Linux系统相对应的MySQL。此例选择的是MySQL5.7，Linux是64位CentOS 7，而CentOS是从Redhat源代码编译重新发布的。
![](https://static.ivan.fun/blog/MySQL-1.png)
![](https://static.ivan.fun/blog/MySQL-2.png)
![](https://static.ivan.fun/blog/MySQL-3.png)
或者直接打开链接`https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-server-5.7.23-1.el7.x86_64.rpm`进行下载

或者在Linux服务器使用命令下载MySQL
```
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-server-5.7.23-1.el7.x86_64.rpm
```
#### 4.1.2 通过MySQL源安装
如下图所示，在MySQL官网选择相应的MySQL源进行下载。（在官网找了半天也只能找到MySQL最新版本8.0的源，故截图中的为8.0版本，演示用的是5.7）
![](https://static.ivan.fun/blog/MySQL2-1.png)
![](https://static.ivan.fun/blog/MySQL2-2.png)
或者直接打开链接`https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm`进行下载

或者在Linux服务器使用命令下载MySQL源
```
wget https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
```

### 4.2 安装MySQL
使用MySQL源安装MySQL
```
#下载MySQL源安装包
wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
#安装MySQL
yum localinstall mysql57-community-release-el7-8.noarch.rpm
#验证安装是否成功
yum repolist enabled | grep "mysql.*-community.*"
```

#### 常用命令

启动MySQL
```
[lishuai@svr1 bin]$ systemctl start mysqld
```
停止MySQL
```
[lishuai@svr1 bin]$ systemctl stop mysqld
```
查看MySQL状态
```
#MySQL启动后的状态
[lishuai@svr1 bin]$ systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2018-09-06 09:49:57 CST; 5h 43min ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 30715 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 30692 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 30719 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─30719 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid
#MySQL停止后的状态
[lishuai@svr1 bin]$ systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: inactive (dead) since Thu 2018-09-06 15:34:35 CST; 25s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 30715 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 30692 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 30719 (code=exited, status=0/SUCCESS)
```
### 4.3 修改root用户密码
MySQL启动后，会发现使用root用户登陆需要密码，但是安装的全过程并没有提示输入密码，所以需要设置跳过密码验证后登陆root用户并修改密码。

* 在`/etc/my.cnf`文件中[mysqld]下添加`skip-grant-tables`，如下所示
```
[mysqld]
skip-grant-tables
```
* 添加后重新启动MySQL，然后使用root用户登录，并修改root用户密码，如下所示
```
[lishuai@svr1 ~]$ mysql -u root
#切换到mysql数据库
mysql> use mysql;
#修改root用户密码
mysql> update mysql.user set authentication_string = password('123456') where user='root';
mysql> quit;
```
* 修改完密码，将`/etc/my.cnf`文件中的`skip-grant-tables`删除，并重启MySQL，再次登陆就需要进行密码验证了
```
[lishuai@svr1 ~]$ mysql -u root -p 123456
```

### 4.4 初始化数据库
创建数据库
```
mysql> create database testdb;
```
执行sql脚本
```
mysql> source db.sql;
```

## 5 web项目部署

### 5.1 打war包
我使用的是Intellij IDEA，对maven项目进行打包

选择Maven Projects，选择相应的project，然后选择Lifecycle -> package，即可生成war包。
![](https://static.ivan.fun/blog/Maven-1.png)

### 5.2 发布war包

* 将war包上传到tomcat目录下的webapps
* 修改tomcat目录conf文件夹内的server.xml文件，在Host元素内添加子元素Context，内容如下
```
<Context docBase="UsechainService.war" path="/" debug="0" privileged="true" reloadable="true"/>
```
docBase的内容是你的war包名字，path是你要设置的访问路径。
* 启动Tomcat，在浏览器输入url`http://address:port/UsechainService`进行访问

若启动服务出现问题或者无法访问到请到Tomcat的logs目录下查看日志。


