---
title: 如何并发访问 Apache
date: 2014-08-02 16:08:20 +0800
categories: [技术]
tags:
- apache
---
### 需求
需要同时访问某个链接, web服务器是apache

### 条件准备
apache需加载mod_status模块
1. 打开apache配置文件httpd.conf, 搜索mod_status, 找到如下代码, 取消前面的#注释符
```apache
#LoadModule status_module modules/mod_status.so
```
2. 配置查看apache服务状态的页面地址, 在httpd.conf最后一行添加如下代码
```apache
<location /server-status>
	sethandler server-status
	order deny,allow
	allow from all
</location>
```
3. 访问http://localhost/server-status?refresh=1
即可查看apache服务状态(其中1代表每秒刷新1次)

### 实验过程
* 实验1: 最开始想用浏览器来并发访问链接, 用的是chrome浏览器，同时打开多个标签页来访问, 额，看了半天，发现即使打开很多个标签页，apache只开启一个线程来处理访问请求，这样就导致多个标签页的访问请求是排队来处理的, 没有实现并发访问的需求

* 实验2: 开启多个浏览器窗口来访问链接, 结果如实验1，apache只开启一个线程来处理访问请求

* 实验3: 打开多个浏览器隐身窗口和浏览器正常窗口来访问链接，结果发现对于多个隐身窗口，apache只安排一个线程来处理访问请求，对于多个正常窗口来，apache也只安排一个线程来处理访问请求，也就是说，打开多个浏览器隐身窗口和正常窗口，apache只会开启两个线程来处理访问请求

* 实验4: 通过apache自带的性能测试工具ab来实现并发访问，实验成功，满足并发访问请求，ab使用举例如下
```bash
ab -n10 -c5 http://www.baidu.com/
```
	其中参数n代表总共要访问多少个请求
参数c代表并发访问多少个请求
最后的参数代表要访问的网址
