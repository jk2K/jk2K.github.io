title: 用 git 和 git hooks 解决多地开发时 MySQL 同步问题
date: 2016-03-17 16:38:45
tags:
- git
---
## 需求
我经常在家和公司的不同电脑上开发一个`Web`项目, 代码托管可以用`GitLab`或者`GitHub`, 这解决了代码的多地同步问题,  但是`MySQL`一直没发现一个好的方式来同步。
目前`MySQL`多地同步的方案主要有以下几种
* 使用可远程连接的`MySQL`, 多地开发时直接连接这个数据库, 这个方案如果网速不好的话，就不合适 
* A 地使用`mysqldump`导出`sql`文件，发送给 B 地，B 地再恢复到`MySQL`中，这个方案手动操作的话很麻烦，费时
* 使用`git`和`git hooks`在提交和合并代码时自动相应的导出和恢复数据库, 这个方案自动化了`MySQL`的导出和恢复工作，简直完美

## 条件准备
* mysql 5.6+, 修改`my.cnf`文件, 在`[client]`下面添加
```ini
user=你的 MySQL 账号
password=你的 MySQL 密码
```
以解决`MySQL5.6 Using a password on the command line interface can be insecure`的问题
* git
* 坚果云(用于差量同步导出的`sql`文件)

## 动手干
1. 首先编辑`pre-commit`文件，这个钩子在代码`commit`之前可以执行脚本
    ```bash
    [your editor] /path/to/your/repo/.git/hooks/pre-commit
    ```

2. 写`pre-commit`钩子, 我们将要告诉系统去把我们的数据库`dump`到坚果云里面
    ```bash
    mysqldump --skip-extended-insert demo > /Users/lee/我的坚果云/demo.sql
    ```

3. 给`pre-commit`加上可执行权限
    ```bash
    chmod +x /path/to/your/repo/.git/hooks/pre-commit
    ```

4. 现在我们来写`post-merge`钩子，我们将要告诉系统在`git`合并代码的时候，把放在坚果云上的`sql`文件恢复到本地的`MySQL`数据库中
    ```bash
    [your editor] /path/to/your/repo/.git/hooks/post-merge
    ```
    在`post-merge`文件里写上
    ```bash
    mysql demo < ~/我的坚果云/demo.sql
    ```
    不要忘记给`post-merge`加上可执行权限啦
    ```bash
    chmod +x /path/to/your/repo/.git/hooks/post-merge
    ```

5. 就是这样，现在当你`commit`和`pull`代码时，`MySQL`数据库可以自动的在多个地方同步啦

## 总结
使用坚果云来同步导出的`sql`文件是因为坚果云在国内是唯一一家靠谱的云存储公司，其他国内的公司倒闭的倒闭，阉割的阉割，就是不让人省心
如果可以无障碍畅游网络的话，可以选择更好的`Dropbox`

享受生活吧

## 参考链接
* [Synchronizing a MySQL Database with Git and Git Hooks](http://ben.kulbertis.org/2011/10/synchronizing-a-mysql-database-with-git-and-git-hooks/)
