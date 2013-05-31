---
layout: post
category: memo
tagline: Ubuntu 安装配置 ftp
tags : [ftp, ubuntu]
---
{% include JB/setup %}

### ubuntu安装ftp服务器

#### 1: 安装vsftpd

    ~$ sudo apt-get install vsftpd

#### 2: 配置vsftpd
##### 修改vsftpd的配置文件。此类配置文件通常位于 /etc 目录下。

    ~$ sudo gedit /etc/vsftpd.conf

#### 重新启动vsftpd：
    sudo service vsftpd start