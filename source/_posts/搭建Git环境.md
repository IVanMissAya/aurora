---
title: 搭建 Git 环境
cover: 'https://static.ivan.fun/blog/post_7.jpg'
index_img: 'https://static.ivan.fun/blog/post_7.jpg'
tags:
  - Git
  - CentOS
categories: -运维
abbrlink: c8814d8f
date: 2020-03-10 08:00:00
---
GitHub就是一个免费托管开源代码的远程仓库。但是对于某些视源代码如生命的商业公司来说，既不想公开源代码，又舍不得给GitHub交保护费，那就只能自己搭建一台Git服务器作为私有仓库使用。

## Git 

### 搭建远程Git私库
  1.登录到远程服务器，推荐使用Xshell 6

  2.安装 git
  
``` 
  $ git --version // 如无，则安装
  $ yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
  $ yum install -y git
```

  3.创建用户并配置其仓库
``` 
  $ useradd git
  $ passwd git   //设置密码
  $ mkdir /git   //创建git文件
  $ chown -R git:git /git   //给Git用户添加权限
  $ su git   //这步很重要，不切换用户后面会很麻烦
  $ cd /git
  $ mkdir -p projects/blog   //项目存在的真实目录
  $ mkdir repos && cd repos
  $ git init --bare blog.git   //创建一个裸露的仓库
  $ cd blog.git/hooks
  $ vim post-receive   //创建 hook 钩子函数，输入了内容如下
```
``` 
  #!/bin/sh
  git --work-tree=/git/projects/blog --git-dir=/git/repos/blog.git checkout -f
```
  添加完毕后修改权限
``` 
  $ chmod +x post-receive
  $ exit // 退出到 root 登录
  $ chown -R git:git /git/repos/blog.git // 添加权限
```

  4.测试git仓库是否可用，另找空白文件夹
``` 
  $ git clone git@server_ip:/git/repos/blog.git
```
  如果能把空仓库拉下来，就说明 git 仓库搭建成功了
  ![](https://static.ivan.fun/blog/git1.jpg)
 
  5.建立ssh信任关系，在本地电脑打开 <font color=#c7254e>Git Bash Here</font> 运行
``` 
  $ ssh-copy-id -i C:/Users/ASUS/.ssh/id_rsa.pub git@server_ip
  $ ssh git@server_ip // 测试能否登录
```
  注：此时的 ssh 登录 git 用户不需要密码！否则就有错，请仔细重复步骤 3-5

  6.为了安全起见禁用 git 用户的 shell 登录权限，从而只能用 git clone，git push 等登录

``` 
  $ cat /etc/shells // 查看 git-shell 是否在登录方式里面
  $ which git-shell // 查看是否安装
  $ vim /etc/shells
  添加上2步显示出来的路劲，通常在 /usr/bin/git-shell
```

    
  手动建立~/git-shell-commands/no-interactive-login文件
  切换到Git用户

``` 
  $ su git
  $ chsh -s /usr/bin/git-shell
  $ mkdir $HOME/git-shell-commands
  $ cat >$HOME/git-shell-commands/no-interactive-login <<\EOF
  #!/bin/sh
  printf '%s\n' "Hi $USER! You've successfully authenticated, but I do not"
  printf '%s\n' "provide interactive shell access."
  exit 128
  EOF
  $ chmod +x $HOME/git-shell-commands/no-interactive-login
```

  root权限修改 vim /etc/passwd中的权限

``` 
  // 将原来的
  $ git:x:1001:1001::/home/git:/bin/bash
  // 修改为
  $ git:x:1001:1001::/home/git:/usr/bin/git-shell
```


### 配置SSH免密登录
把本地电脑的公钥放到服务器的/home/git/.ssh/authorized_keys文件里（如果没有此文件可自行创建）
