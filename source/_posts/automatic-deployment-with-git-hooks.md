title: 通过git hooks自动部署代码
date: 2014-05-23 12:36:13
tags:
- git
---
###准备条件
* 假设web目录为/www/example.com
*  本地git目录为/website_dev
* 公共git仓库地址为http://git.example.com/test/test.git
* git用户为username, 密码为password

###步骤
1.  在/www/example.com目录下执行以下命令
```sh
git clone http://git.example.com/test/test.git
git remote add product http://username:password@git.example.com/test/test.git
```
2. 在公共git仓库的hooks里创建post-receive(windows版本为post-receive.bat, linux版本才是post-receive)，在post-receive里添加代码
```
cd /www/example.com
git pull product master
```
3. 在本地git目录/website_dev下执行
```sh
git push product master
```
4. 搞定了, 请验证一下是否正确自动部署代码

###结语
1. 自动输入帐号密码
    ```sh
    http://username:password@git.example.com/test/test.git
    ```
    这种链接结构可以让git记住密码，不用每次提交或签出时都输入账号密码，username是git的账号名称，password是账号密码

2. post-receive里的代码请根据实际情况进行修改

3. 之前笔者的web服务器是linux的，代码部署用的是rsync, 还不错，挺好用的，现在web服务器换成windows的了，rsync在windows有一个移植版本，但需要收费，所以目前采用git自动部署方案
