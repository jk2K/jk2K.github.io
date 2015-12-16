title: 为 apache 运行用户创建 ssh key
date: 2015-09-21 17:49:00
tags:
- ubuntu
---
##解决方案
###方案1, 自动
`sudo -u apache ssh-keygen -t rsa`

###方案2, 手动
你必须复制`root`用户生成的`key`到`apache`用户的`.ssh`文件夹下, 软链接是不行的, 必须复制, 复制, 复制, 重要的事情说三遍

假设`apache`的`homedir`是`/var/www`(检查`/etc/passwd`), `root`用户生成的私钥(`key`)是`id_rsa-git`

```sh
mkdir -p /var/www/.ssh/
cp /root/.ssh/id_rsa-git /var/www/.ssh/id_rsa
```
无需复制公钥(`public key`)

####注意
* 默认使用的私钥名称是`id_rsa`或`id_dsa`, 所以你复制过来的私钥名称得是这个
* `apache`现在默认的运行用户为`daemon`, 他的`homedir`是`/usr/sbin`, 你可能得改成其他目录才能生效, 可以改成`home/daemon`

```sh
chown -R apache:apache /var/www/.ssh
chmod 0700 /var/www/.ssh
chmod 0600 /var/www/.ssh/id_rsa
```

##需求说明
apache 在执行`git`相关命令时报`Could not create directory '/usr/sbin/.ssh'`

##参考链接
* http://stackoverflow.com/questions/7306990/generating-ssh-keys-for-apache-user
