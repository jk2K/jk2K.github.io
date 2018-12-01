title: 如何安装托管在私有 GitLab 实例上的 composer 包?
date: 2018-01-27 12:54:19
tags:
- PHP
---
## composer 包安装方式有哪几种?
一般来说开源的 PHP 包都会放在 [packagist.org](https://packagist.org/), Composer 直接安装就行

私有包有以下几种安装方式
- 在 [packagist.com](https://packagist.com/) 购买付费服务, 不用自己维护, 省心
- 搭建 [Satis](https://github.com/composer/satis) 实例, 相当于搭建了私有的 packagist.org, 需要自己维护服务器
- 购买了 [GitHub 企业版](https://enterprise.github.com/), 将包托管在企业版上
- 搭建 [GitLab 社区版](https://about.gitlab.com/) 实例, 将包托管在这上面

可以根据各自情况选择合适的方案, 笔者在这篇文章中只讲解如何安装托管在私有 GitLab 实例上的包

## 环境信息
- 假设私有 GitLab 实例网址为 `http://git.demo.com`
- 要安装的包名为 `demo/fly`

## 配置 composer 以使用 GitLab API
因为网络原因, 项目里一般会把 vendor 目录也加入版本管理, 某个依赖如果有 `.git` 目录会造成依赖不会被加入到 git 中

Composer 支持使用 `--prefer-dist` 参数, 只下载相应的 zip 包, 不 clone 源码, 这样下载下来的依赖不会带有`.git`目录

但只有在使用 GitLab API 的前提下, `--prefer-dist` 参数才会生效, 否则下载下来的依赖还是会带有 `.git` 目录

### 修改 composer 全局 config
- 文件路径: `~/.composer/config.json`
- 因为 git.demo.com 使用 HTTP 协议, 所以`secure-http`要设为`false`
- 设置 `gitlab-domains`, 告诉 Composer 相应的域名可以使用 GitLab API

内容修改为如下
```json
{
    "config": {
        "secure-http": false,
        "gitlab-domains": [
            "git.demo.com"
        ]
    }
}
```

### 修改 composer 全局用户认证信息
- 文件路径: `~/.composer/auth.json`
- 设置 token 后, 才有权限访问 GitLab API

去 `http://git.demo.com/profile/personal_access_tokens` 新建一个 `personal access token`

内容修改为如下
```json
{
    "gitlab-token": {
        "git.demo.com": "your person access token in git.demo.com"
    }
}
```

## 安装步骤
以 `demo/fly` 为例

### 添加 repository
修改 `composer.json`, 添加如下代码
```json
"repositories": [
  {
    "type": "vcs",
    "url": "http://git.demo.com/demo/fly.git"
  }
],
```

告诉 composer 去哪找 `demo/fly` 依赖

### 安装依赖
```bash
composer require --prefer-dist demo/fly
```


## 参考资料
- [composer repository 文档](https://getcomposer.org/doc/05-repositories.md)
- [composer gitlab-token 文档](https://getcomposer.org/doc/06-config.md#gitlab-token)
- [Why composer clone my private gitlab instead only download it](https://github.com/composer/composer/issues/6016#issuecomment-270001655)
- [Composer Update Fails due to Github Authorization](https://github.com/composer/composer/issues/3542#issuecomment-113846131)
- [使用 Satis 创建 Composer 私有库](https://www.greatcl.com/2016/09/03/create-private-composer-repo-with-satis/)
