title: IPV6 环境下用 iPhone 访问 windows 10 远程桌面
date: 2018-12-01 14:43:19
tags:
- Windows
---

## 前言
最近意外发现家里用的移动宽带已经获取到了 IPV6 地址，然后又意外发现移动 4G 也是可以访问 IPV6 的

Windows 网络情况

{% asset_img "computer-network.png" [computer network] %}

iOS 网络情况

{% asset_img "ios-network.png" [ios network] %}


2010 年开始在校园网用 IPV6, 到现在已经过去 9 年了，现在终于开始普及了

## 需求
我们有时候需要访问家中的电脑，如果没有公网 IP 的话，实现起来就比较麻烦

在 IPV4 环境下，IP 数量比较少，很多运营商不会给设备分配独立的 IP，而是共用一个出口 IP，这种情况下一般需要通过额外的软件(例如 [frp](https://github.com/fatedier/frp))去搭建隧道来实现

IPV6 则没有这个烦恼，它可以给地球上的每粒沙子都分配一个 IP 地址，既然现在我电脑已经有了独立的 公网 IPV6 地址，那现在是时候采用更简单的方式去访问指定电脑了

## 操作步骤
### 允许远程连接到此计算机
点击 **远程桌面** 的**显示设置**

{% asset_img "change-remote-desktop-settings.png" [change remote desktop settings] %}

选择`允许远程连接到此计算机`

{% asset_img "allow-remote-desktop.png" [allow remote desktop] %}

这个选中后，我看防火墙会自动放行相关连接，不用再单独去防火墙里允许远程桌面的程序通过了

> 电脑防火墙如果不放行的话，是无法连接到该电脑的

### 查看电脑 IPV6 地址
去 **设置 -> 网络和 Internet -> 更改适配器选项** 查看 `以太网` 的状态

{% asset_img "computer-network-settings.png" [computer network settings] %}

点击详细信息，查看电脑的 IPV6 地址

{% asset_img "computer-network-details.png" [computer network details] %}

这个临时 IPV6 地址就是你电脑的 公网 IPV6 地址了，记下它


### 使用 RD Client 访问 windows 10 远程桌面
下载 Microsoft 官方出品的 RD Client

{% asset_img "app-store-microsoft-remote-desktop.png" [App Store microsoft remote desktop] %}

填好电脑的公网 IPV6 地址

{% asset_img "rdclient-new-connection.png" [rdclient new connection] %}

使用移动 4G 访问这个连接，正常情况下你就可以访问到你的电脑了


## 进阶技巧
### 设置域名解析到 IPV6 地址
如果你有域名的话，可以考虑把域名解析到这个 IPV6 地址，然后以后就不用记那么长的 IPV6 地址了，通过域名就可以访问你的电脑

PS. 运营商一般会屏蔽 80 端口，所以即使配了域名也无法直接用来访问自己电脑上的网站哦
