---
title:  "Nginx 负载均衡"
permalink: /nginx-and-tomcat-settings/
date: 2019-05-10

categories: [总结, 学习]

tags: [Nginx, Tomcat,DevOps, 负载均衡, 部署, 配置]
---

# Nginx 负载均衡

> 网易云课堂
> 极客时间

## 极客时间 Nginx 课程

Nginx 版本

nginx.org 开源版本

nginx.com 商业版本

tengine 淘宝开发的基于Nginx模块整合的软件

OpenResty 提供网关防火墙的Nginx模块整合的开源及商业软件 (openresty.org、openresty.com)

### 编译安装 Nginx

    $ yum install gcc pcre-devel zlib-devel -y

    $ ./configure --prefix=/usr/local/nginx

    $ make

    $ make install

    $ cd /usr/local/nginx

    $ ll

    $ cd sbin

    $ ./nginx

    $ ps -ef | grep nginx

    $ ./nginx -s stop

### http 模块

### nginx 升级

替换编译后的二进制文件即可

    $ cd /usr/local/nginx/sbin

    $ cp nginx nginx.old

    $ cp -r /usr/local/download/nginx/nginx-1.16.0/objs/nginx ./ -f

替换完成二进制文件之后再进行热部署

    $ ps -ef | grep nginx

    $ kill -USR2 pid

    $ ps -ef | grep nginx

再优雅的关闭老的进程

    $ kill -WINCH old_pid

    $ ps -ef | grep nginx

其中老的 master 进程不会自动退出，会一直保留，方便回退

### 日志切割

    $ cd /usr/local/nginx/logs/

GoAccess 实时日志分析

### SSL 安全协议





## 使用 Nginx 实现负载均衡

### Nginx 实现高并发高性能的原因

Apache 使用的是线程来处理请求

Nginx 使用的是事件驱动

模块化设计

### Nginx 的组成

1. Nginx 二进制可执行文件：由各模块源码编译出的一个文件
2. Nginx.conf 配置文件：控制 Nginx 的行文
3. access.log 访问日志：记录每一条 http 请求信息
4. error.log 错误日志：定位问题

