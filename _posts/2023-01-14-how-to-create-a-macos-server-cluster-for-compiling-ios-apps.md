---
title: 如何建设一个用于编译 iOS App 的 macOS 云服务器集群？
date: 2023-01-14 19:50:00 +0800
categories: [技术]
tags:
- 思考
---

现代软件开发一般会借助 CI/CD 来提升代码质量、加快发版速度、自动化重复的事情，iOS App 只能在 mac 机器上编译，CI/CD 工具因此需要有一个 macOS 云服务器集群来执行 iOS App 的编译。
今天就来谈谈如何建设 macOS 云服务器集群

![](/assets/img/how-to-create-a-macos-server-cluster-for-compiling-ios-apps/1.jpeg)

## 购买 mac mini / Mac Studio 机器
最简单的方式就是购买一批 mac 机器，买它个 1000 台，买回来后放入机房，把其中一台机器作为管理机，申请办公网访问这台机器的网络权限，每次要维护这批机器里的软件时，先从办公网进入管理机，然后从管理机在远程连接到其他机器。

## 虚拟化 macOS 集群
借助 macOS 官方的 `Hypervisor.framework`，可以分钟级创建 macOS 虚拟机，这种方式创建的虚拟机性能、稳定性、安全性都很高，可以持续采用更新、更快、更高级的 mac 硬件，最终实现更快的 App 编译速度。

在企业内部申请 macOS 机器，需要经历申请预算、领导审批、采购、运输、部署至机房等繁琐步骤，现在可通过购买高配置的 mac 机器，然后再将其按需配置成多台虚拟机，减少了硬件维护成本，提高了扩容效率。这方面代表性产品有
- https://github.com/cirruslabs/tart
- https://www.macstadium.com/anka

移动端 CI/CD 平台 bitrise 采用的是这种方式，只不过他们做了很多自研工作，打造了 [Gen2](https://bitrise.io/blog/post/introducing-gen2 ) 编译基础设施，宣称能让客户更快、更频繁的发布 App


## 云端管理 macOS 服务器
自行购买并维护 mac 机器还是不太方便，成本很高，如果能云端管理、按需付费就更好了。亚马逊的 AWS 解决了这个问题，他家的 [Amazon EC2 Mac Instances](https://aws.amazon.com/ec2/instance-types/mac/) 能让我们按需申请 macOS 服务器，如果能接受云端服务的话，其实这种方式最省事。与这种服务类似的产品有
- https://flow.swiss/mac-bare-metal
- https://www.scaleway.com/en/hello-m1/

## 在 linux 里运行 macOS 虚拟机
[sickcodes/Docker-OSX](https://github.com/sickcodes/Docker-OSX) 是最近流行的一个项目。
- 借助 KVM 技术，实现了在 linux 里运行 macOS 虚拟机
- 借助 docker，简化了 macOS 虚拟机的部署

这个项目具有很多优势，例如
1. 不依赖专门的 Apple 硬件，能够运行 KVM 的 x86_64 机器就行
2. 能够通过 docker 容器来部署 macOS 虚拟机。未来借助 k8s，能够实现大规模 macOS 服务器的管理，轻易实现扩缩容

也有风险
1. 不太合规，macOS 正常只能在 Apple 硬件上运行
2. 在 KVM 里运行 macOS，兼容性、性能、稳定性需要评估。如果出现问题需要付费找项目维护者解决

我在裸金属上利用 Docker-OSX 项目确实跑起来了 macOS 虚拟机。运行该项目有以下几点要注意
1. linux 宿主机需要支持嵌套虚拟化（Nested Hardware Virtualization）。裸金属和物理机正常都支持
2. 对 Linux 内核有要求，操作系统最好比较新，CentOS 8 可以，CentOS 7 不行

## 在 Apple 硬件上用 k8s 来调度 macOS
有没有可能在私有云里实现 aws 这种 mac 管理体验呢？[MacStadium](
https://www.macstadium.com/orka) 家的  Orka 平台做到了。Orka 有以下好处

**1 - 降低了 mac 集群的管理成本**

花时间更少，例如管理服务器、在服务器上安装软件

利用 Kubernetes 调度 mac 服务器，这与 Linux 服务器的管理保持一致，方便融入公司已有技术栈

**2 - 在真正的 Apple 硬件上运行 mac 虚拟机**

不用担心兼容性、稳定性、性能问题

**3 - 用心为开发者设计**

提供多种操作方式（命令行、REST API）来管理 mac 虚拟机

对于企业来说，可以考虑购买 Orka 平台，在私有云里面搭建 macOS 云服务器集群。GitLab SaaS 版也是使用这种方式来提供托管的 macOS Runner 的

## 总结
我讲了建设 macOS 云服务器集群的 5 种方式，这些方式各有利弊，总的来说：
- 如果能接受使用云服务器，建议直接购买 AWS 的 Amazon EC2 Mac Instances，方便、成本低
- 自建机房，如果计划建设的 macOS 集群规模很大，建议购买 MacStadium 的 Orka 平台来建设，专业服务，避免踩坑，省时省力
- 自建机房，如果 macOS 集群规模比较小，对自动化要求不高，建议购买高配的 apple 硬件，然后通过  `Hypervisor.framework` 虚拟化出多台 macOS 机器，整体架构比较简单，也能解决问题