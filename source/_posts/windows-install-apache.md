title: windows 安装配置 Apache
date: 2014-04-08 16:07:22
tags:
- apache
---
## 环境
* [httpd-2.4.9-win64.zip](http://www.apachelounge.com/download/)
* WindowsServer 2008 R2

## 安装配置
1. 解压httpd-2.4.9-win64.zip到e:/software/Apache24，以下以e:/software/Apache24为apache的存放目录做说明，目录可以随意改
2. 打开conf/httpd.conf, 按照如下步骤修改
    * 找到`ServerRoot "c:/Apache24"`，改为
    `ServerRoot "e:/software/Apache24"`
    * 找到`#ServerName www.example.com:80`，修改为
    `ServerName localhost:80`
    * 找到`LoadModule access_compat_module modules/mod_access_compat.so`, 在其上一行添加
    `PHPIniDir "e:/software/php"`
    注意: 一定要添加在LoadModule那一段的前面,以便提前加载,不然windows下apache无法读取php.ini里的设置，这里假设php放在e:/software/php目录下，版本为php-5.5.11-Win32-VC11-x64.zip
    * 在LoadModule这段后面添加
    `LoadModule php5_module "e:/software/php/php5apache2_4.dll"`
    注意：由于apache版本的不同，这里不一定是`php5apache2_4.dll`，比如apache版本为2.2，这里就应该是`php5apache2_2.dll`
    * 找到`AddType application/x-gzip .gz .tgz`，在其下一行添加
    `AddType application/x-httpd-php .php`
    * 找到
    ```apache
    DocumentRoot "c:/Apache24/htdocs"
    <Directory "c:/Apache24/htdocs">
    ```
    修改为
    ```apache
    DocumentRoot "e:/software/Apache24/htdocs"
    <Directory "e:/software/Apache24/htdocs">
    ```
    htdocs是网站存放目录
    * 找到`DirectoryIndex index.html`，修改为
    `DirectoryIndex index.php index.html`
    * 保存关闭

3. 进入bin文件夹
* 将apache注册为windows服务
`httpd -k install -n apache`
以后就可以用以下命令操作apache
```bash
net start apache #启动apache
net stop apache #关闭apache
```
* 查看apache配置是否正确，可以执行
`httpd -n apache -t`

## 常见问题：
* 提示缺少msvcr100.dll，请安装[Visual C++ Redistributable 2010](http://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe)
