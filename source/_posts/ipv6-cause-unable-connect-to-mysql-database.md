title: ipv6 导致无法访问 MySQL 数据库
date: 2012-04-28 13:24:21
tags:
- ipv6
---
## 环境
* xp sp3系统
* PHP 5.2.6
* Apache httpd-2.2.22-win32-x86-no_ssl
* MySQL 5.5.20.0

## 情景再现
前天我在学校的网络中心里安装配置好一个bt程序后，回到寝室里发现数据库不能连接，mysql数据库报错为`Could not connect to the specific instance MySQL Error Number 2003，Can't connect to MySQL server on 'localhost' (10061)，If you want to check the network connection, please click the Ping button`

当时我就纳闷了，好好的数据库怎么突然就无法连接了呢，于是就在网上寻找解决方案，试过很多方法，但是都没能解决我这个问题，我发现好多人都是直接在网上复制答案来给别人回答问题，根本就没有真正考虑这个问题的原因

比如把`my.ini`文件复制到`windows/system`文件夹里,这就是一种典型的治标不治本的方法，并且还无法解决这个问题，此外如果把那个文件复制到`windows/system`里，万一下次mysql的my.ini文件更改了，怎么办？再复制一次？其实完全可以在系统变量里指定my.ini的路径，这样的话是否一劳永逸？

我觉得我们解决问题的原则是，既要知道原因，又要知道如何更好地解决它。

## 解决方案
打开mysql的`my.ini`文件，在mysqld的部分里添加`bind-address=127.0.0.1`（在`port=3306`下面）这段代码

## 分析
原因是我在网络中心的时候安装了ipv6协议，mysql默认是以ipv4地址访问的，当我们安装ipv6协议时，mysql就默认以ipv6地址访问，所以我们才无法连接到数据库，这个问题一般出现在xp系统里，xp系统默认是不安装ipv6协议的，win7默认安装，所以win7不会出现这种问题
