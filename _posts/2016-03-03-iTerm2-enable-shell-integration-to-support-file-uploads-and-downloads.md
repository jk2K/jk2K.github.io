---
title: 让 iTerm2 支持拖拽上传文件和右键下载文件
date: 2016-03-03 11:11:39  +0800
categories: [技术]
tags:
- macOS
---
## 条件准备
* [iTerm2 version 3 nightly builds](https://iterm2.com/nightly/latest)

## 动手
需要在你自己的机器和想通过`ssh`远程连接的机器上都安装`shell integration`
### 自动安装
```bash
curl -L https://iterm2.com/misc/install_shell_integration.sh | bash
```
### 手动安装
1. 下载这个脚本
```bash
curl -L https://iterm2.com/misc/`basename $SHELL`_startup.in >> \
~/.iterm2_shell_integration.`basename $SHELL`
```
2. 在`shell`登陆脚本中添加如下命令 (`.login` for `tcsh`, `.bash_profile` for `bash`, `.zshrc` for `zsh`, or `config.fish` file for `fish`):
```bash
source "${HOME}/.iterm2_shell_integration.`basename $SHELL`"
```

2个机器都安装好后，现在你就可以体验拖拽上传文件和右键下载文件了

## 常见问题
### `Download with scp` 按钮是灰色的
解决方案:
远程机器的`hostname`不对，默认是`localhost`, 需要设置为远程机器的`ip`, 可以在你远程机器的`shell`登录脚本中通过如下命令设置环境变量
```bash
export iterm2_hostname=你远程机器的ip
```
记得这行命令要在
```bash
source "${HOME}/.iterm2_shell_integration.`basename $SHELL`"
```
之前哦!

可以通过设置`Preferences > Profiles > General > Badge`为`Hostname is \(session.hostname)`，来检测`iTerm2`所使用的`hostname`

## 总结
`iTerm2` 必须为`iTerm2 version 3`，可以在`nightly builds`中获取，目前的稳定版`2.1`是没有这个功能的
有两个问题没注意，导致开启这个特色时花了2个多小时
1个问题是只在我本地的机器上安装了`shell integration`, 没有在远程机器上安装`shell integration`
第2个问题是远程机器的`hostname`不正确，导致功能不起作用, `Download with scp`按钮是灰色的

这个比之前使用的的`rz`, `sz`体验要差一点，后者可以通过命令行来操作，更为快捷，但毕竟前者是官方支持，有人维护, 相信前者会越来越完善的

## 参考链接
* [Shell Integration](https://iterm2.com/shell_integration.html)
* [scp not connecting](https://gitlab.com/gnachman/iterm2/wikis/scp-not-connecting)
* [iTerm2 scp discuss](https://groups.google.com/forum/#!topic/iterm2-discuss/e6EKjl0r5ig)
