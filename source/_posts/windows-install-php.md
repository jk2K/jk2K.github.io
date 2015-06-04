title: windows安装配置php
date: 2014-04-08 16:11:19
tags:
- php
---
##环境：
* [php-5.5.11-Win32-VC11-x64.zip](http://windows.php.net/download/)
* WindowsServer 2008 R2

##说明：
* Non Thread Safe和Thread Safe版本的区别
Thread Safe是线程安全，执行时会进行线程（Thread）安全检查，以防止有新请求就启动新线程的CGI执行方式而耗尽系统资源。Non Thread Safe是非线程安全，在执行时不进行线程（Thread）安全检查。
* VC6、VC9和VC11的区别
由不同的编译器编译而成,VC6是由Visual Studio 6编译而成，VC9是由Visual Studio 2008编译而成，VC11是由Visual Studio 2012编译而成，版本越新，性能和稳定性越好，所以推荐用VC11
* php.ini-development和php.ini-production的区别
这两个都可以更名为php.ini, 只不过php.ini-development里的配置更适合开发用，php.ini-production里的配置更适合发布产品时用，例如当网站正式上线运营时，应该将php.ini-production更名为php.ini

##安装步骤
1. 解压php-5.5.11-Win32-VC11-x64.zip，得到php.ini-development, 将其更名为php.ini
2. 打开php.ini，修改以下参数
    * 找到date.timezone，设置时区为中国时区
`date.timezone = PRC`
    * 找到`; extension_dir = "ext"`，改为如下样子，取消前面的;注释符
`extension_dir = "e:/software/php/ext"`
    * 找到
    ```
    ;extension=php_bz2.dll
    ;extension=php_curl.dll
    ;extension=php_fileinfo.dll
    ;extension=php_gd2.dll
    ;extension=php_gettext.dll
    ;extension=php_gmp.dll
    ;extension=php_intl.dll
    ```
    按照需要来加载这些扩展库，笔者一般加载这些
    ```
    ;远程抓取页面
    extension=php_curl.dll
    ;图像处理
    extension=php_gd2.dll
    ;多字节字符串处理
    extension=php_mbstring.dll
    ;操作mysql数据库
    extension=php_mysqli.dll
    ;ssl加密传输
    extension=php_openssl.dll
    ;使用内存缓存，加速网站
    extension=php_memcache.dll
    ```
    加载某个扩展库，只需取消行首的`;`符，如果没有该扩展库，请查看ext文件夹下是否有该扩展库，如果有，按照格式手动添加即可，如果没有该扩展库，需手动下载扩展库文件到ext文件夹下，最后按照格式手动添加
    每次改动php.ini文件里的配置时，都需要重启apache才能生效

3. 保存退出
4. 测试php是否安装配置成功, 可以执行php解压目录下的php.exe

##常见问题：
* 提示缺少msvcr110.dll
请安装[Visual C++ Redistributable 2012](http://download.microsoft.com/download/9/C/D/9CD480DC-0301-41B0-AAAB-FE9AC1F60237/VSU4/vcredist_x64.exe)
