---
layout: post
category: memo
tagline: Ubuntu 安装 telnet
tags : [telnet, ubuntu]
---
{% include JB/setup %}

## Ubuntu的安装配置

#### 安装openbsd-inetd:
    sudo apt-get install openbsd-inetd

#### 安装telnetd:
    sudo apt-get install telnetd

#### 在etc/inetd.conf文件中可以看到这一行内容(如果没有这一行内容，就手动加上)：
    telnet stream tcp nowait root /usr/sbin/tcpd /usr/sbin/in.telnetd

#### 重启openbsd-inetd
    /etc/init.d/openbsd-inetd restart

#### 查看telnet运行状态
    netstat -a | grep telnet
#### 输出：
    tcp 0 0 *:telnet *:* LISTEN