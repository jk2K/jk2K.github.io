title: 自定义 ubuntu ssh 欢迎信息
date: 2015-09-14 16:34:16
tags:
- ubuntu
---
##步骤
1. 打开`/etc/update-motd.d`, 这个目录下面的文件会在登录成功后按照序号的顺序先后运行
```bash
cd /etc/update-motd.d
```

2. 我们找到`00-header`这个文件, 修改它, 在文件末尾添加如下代码
```bash
cat << "EOT"
  /\_/\
=( °w° )=
  )   (  //
 (__ __)//
EOT
```

3. 重新登录服务器就会看到如下效果啦
{% asset_img screenshot.png %}

##小提示
重新登录服务器后也许不会看到效果, 你再登录一次就肯定会看到啦, 就酱

##参考资料
* http://stackoverflow.com/questions/25214084/cant-get-ascii-art-to-echo-to-console
* http://www.cnphp6.com/archives/50433
