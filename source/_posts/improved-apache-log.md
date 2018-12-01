title: 改进 Apache 日志
date: 2014-06-11 18:33:42
tags:
- apache
---
## 方案
### 让log按照日期来存储
```apache
ErrorLog "|bin/rotatelogs.exe -l logs/discuz.dev.error.%Y.%m.%d.%H_%M_%S.log 5M"
CustomLog "|bin/rotatelogs.exe -l logs/discuz.dev.access.%Y.%m.%d.log 86400" common
```
    其中86400是指86400秒，正好是1天，表示按照1天的间隔来存储日志
    5M是指当错误日志容量大于5M时, 新建一个日志文件来存储
    rotatelogs.exe的参数l是指使用本地时间代替GMT时间作为时间基准, 避免记录的时间有错误

### 过滤不必要的日志
日志中一般没有必要记录静态的资源文件请求，比如图片、js、css等，将这些请求过滤后能大幅减少日志记录行数
首先要保证一个叫setenvif的mod已经安装并enable，然后在VirtualHost的配置文件中添加
```apache
<FilesMatch "\.(png|gif|jpe?g|js|css|swf)$">
	SetEnv dontlog 1
</FilesMatch>
```
其作用是过滤静态的资源文件请求，dontlog表示一个变量，将这些过滤器的信息存储在这个变量中，最后在CustomLog行尾添加env=!dontlog，如
```apache
CustomLog "|bin/rotatelogs.exe -l logs/discuz.dev.access.%Y.%m.%d.log 86400" common env=!dontlog
```

### 自定义日志内容

```apache
CustomLog "|bin/rotatelogs.exe -l logs/intra.access.%Y.%m.%d.log 86400" "%h %t %V \"%r\" %>s %b \"%{Referer}i\" \"%{User-agent}i\"" env=!dontlog
```
%h ip
%t 时间
%V 访问的域名
\"%r\" 访问的方法,路径
%>s 状态码
%b 返回给客户端的字节数
\"%{Referer}i\" 访问来源
\"%{User-agent}i\" user-agent

## 参考链接
* https://sdjkx.me/2013/10/apache-log-optimization/
* http://blog.csdn.net/luhao/article/details/2969388
* http://works.jinbuguo.com/apache/menu22/programs/rotatelogs.html
