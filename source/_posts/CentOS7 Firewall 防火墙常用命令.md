---
title: CentOS7 防火墙Firewall常用命令
cover: 'https://static.ivan.fun/blog/firewall.jpg'
index_img: 'https://static.ivan.fun/blog/firewall.jpg'
tags:
  - CentOS
categories: '-CentOS'
abbrlink: 11396c1e
date: 2020-06-10 16:30:00
---
# CentOS7 防火墙Firewall常用命令

1. ### firewalld的基本使用
	- 启动： ``` systemctl start firewalld ```
	- 查看状态：``` systemctl status firewalld ```
	- 停止： ``` systemctl disable firewalld ```
	- 禁用： ``` systemctl stop firewalld ```
	
2. ### systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。
	- 启动一个服务： ``` systemctl start firewalld.service ```
	- 关闭一个服务： ``` systemctl stop firewalld.service ```
	- 重启一个服务： ``` systemctl restart firewalld.service ```
	- 显示一个服务的状态： ``` systemctl status firewalld.service ```
	- 在开机时启用一个服务： ```systemctl enable firewalld.service ```
	- 在开机时禁用一个服务： ``` systemctl disable firewalld.service ```
	- 查看服务是否开机启动： ``` systemctlis-enabled firewalld.service ```
	- 查看已启动的服务列表： ``` systemctllist-unit-files|grep enabled ```
	- 查看启动失败的服务列表： ``` systemctl--failed ```

3. ### 配置firewalld-cmd
	- 查看版本： ``` firewall-cmd --version ```
	- 查看帮助： ``` firewall-cmd --help ```
	- 显示状态： ``` firewall-cmd --state ```
	- 查看所有打开的端口： ``` firewall-cmd --zone=public --list-ports ```
	- 查看所有打开的端口： ``` firewall-cmd --list-all ```
	- 更新防火墙规则： ``` firewall-cmd --reload ```

4. ### 端口操作
	- 添加 ``` firewall-cmd --zone=public --add-port=80/tcp --permanent   （--permanent永久生效，没有此参数重启后失效）```
	- 重新载入 ``` firewall-cmd --reload ```
	- 查看 ``` firewall-cmd --zone=public --query-port=80/tcp ```
	- 删除 ``` firewall-cmd --zone=public --remove-port=80/tcp --permanent ```
	- ***针对IP开放端口***
	- ``` firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.142.166" port protocol="tcp" port="6379" accept" ```
	- ``` firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0.233" accept" ```
	- ***删除某个ip***
	- ``` firewall-cmd --permanent --remove-rich-rule="rule family="ipv4" source address="192.168.1.51" accept" ```
	
	 