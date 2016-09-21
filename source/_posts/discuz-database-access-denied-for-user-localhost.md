title: discuz 因数据库权限错误报 Access denied for user
date: 2014-05-17 21:07:12
tags:
- discuz
---
## 情景在现
笔者根据数据库用户权限最小化的原则分配给discuz的数据库用户权限为`select, insert, update, delete, index`, 但是安装的时候报`Access denied for user`错误，经过一番资料查找，得出可能是缺少`create, drop`权限

## 分析
discuz的数据库用户权限至少应该包括`select, insert, update, delete, index, create, drop, alter`, 如果缺少的话, 可能会报`Access denied for user`错误, 或者其他错误

## 参考链接
* http://www.itlearner.com/article/3946
