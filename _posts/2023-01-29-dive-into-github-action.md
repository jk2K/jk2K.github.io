---
title: 深入研究 GitHub Action：解决了什么问题，优势，产品分析？
date: 2023-01-29 19:50:00 +0800
categories: [技术]
tags:
- 思考
---

GitHub Actions 是 GitHub 推出的持续集成和持续交付平台（CI/CD 平台），允许开发者实现自动化的编译、测试、部署，于 2019 年 11 月全面可用，今天就来深入拆解下 GitHub Actions

![](/assets/img/dive-into-github-action/DL-V2-LinkedIn_FB.webp)

## 解决了什么问题？
![](/assets/img/dive-into-github-action/cicd-history.png)

CI/CD 平台（流水线引擎）发展历史中大概有 3 波浪潮

1. 第一波浪潮以 `Jenkins` 为代表，让大家广泛的接受了持续集成和持续交付的概念，解决了自动编译、测试、部署代码的问题
2. 第二波浪潮以 `GitLab CI` 为代表，以代码仓库为入口，设计理念是流水线即代码，开箱即用，使用体验极好，好比大家从 Eclipse 切换到 IntelliJ IDEA，解决了流水线的管理问题
3. 第三波浪潮以 `GitHub Actions` 为代表，设计理念是将可复用的能完成复杂但是高频任务的脚本封装为原子（Actions），原子和编程语言的函数一样，有入参、出参。解决了流水线里脚本复用难的问题

GitHub 发现了很多流水线里任务是类似的，可以共享出来，故提出了原子的概念，支持使用 JavaScript、yml、Docker 镜像等方式开发原子，提倡通过 GitHub Marketplace（市场）分发原子，降低了大家使用流水线的成本，最终带来了 GitHub Actions 生态的繁荣，目前 GitHub 市场里有 1.7 万个原子，涉及开发工作流的方方面面，这是世界上最大的流水线生态

## 优势
GitHub Actions 优势主要有以下几点
1. SaaS 软件，运行稳定，承载能力高，能应对企业级复杂流水线的编排，支持混合云部署（可以在客户自己的服务器环境里执行任务）
2. 简单的按需付费定价模式，根据硬件配置和使用分钟数收费。开源仓库免费使用，私有仓库每月有一定的免费配额
3. 生态繁荣和强大，市场每月新增 400+ 原子
4. 支持丰富的 GitHub 事件，扩大了流水线的使用场景


## 产品分析
简单介绍下 GitHub Actions

GitHub Actions 把流水线配置存在 YAML 文件里，可以通过代码仓库来管理 YAML，流水线的结构类似下图

![](/assets/img/dive-into-github-action/overview-actions-simple.png)

GitHub Actions 通过[语法规范](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions) 实现了串行、并行、矩阵编译、指定在哪些机器上执行任务、监听 GitHub 事件、审批、原子引用、流水线复用等流水线功能

几个基础概念简单介绍下

1. Event（事件），用于触发流水线
2. Step（步骤），最小的可执行代码单元，能够引用原子
3. Job（任务），包含一串顺序执行的 Step。job 之间可以顺序执行也可以并行执行
4. Runner（执行任务的服务器），通过标签来区分，Job 可以在具有同一标签的任何服务器上执行

### 功能脑图
把功能梳理下可得到如下脑图

![](/assets/img/dive-into-github-action/feature-list.png)


### 业务流程
核心业务流程如下

![](/assets/img/dive-into-github-action/business-process.png)

### 产品架构
![](/assets/img/dive-into-github-action/product-architecture.png)

GitHub Actions 不仅仅能解决 DevOps 领域的问题，更广义的来讲，他可以作为自动化引擎来使用。例如如果你上班早上总是忘记打卡，可以让他每天上午10点定时运行一下，
先请求打卡数据 api 判断你有没有打卡，如果发现没有打卡，就给你发送企业微信通知提醒你打卡


## 思考题 🤔
如果让你上线一个 CI/CD 平台，你应该怎么做？

### 先分析下市场
现在 CI/CD 已经是红海了，领先的一体化 DevOps 平台如 GitHub、极狐、腾讯云 CODING、阿里云 云效、华为云 CodeArts 都提供了流水线能力，
如果再做一个通用型的 CI/CD 平台，很难有大的差异化

如果专注于某个垂直领域，如移动端 CI/CD 平台，这块代表产品是 Bitrise，也做的很好了，除非能再找到一个足够大的垂直领域，否则很难脱颖而出

如果不从商业角度考量，只是内部项目有需要，可以基于开源的产品二次开发

### 再分析下必须要实现的功能
假设我们来自研一个 CI/CD 平台，那么我们应该怎么做，既能满足客户的诉求，又能保持世界领先水平呢？

涉及角色
1. 平台提供方
2. 客户
3. 第三方系统

**客户想要开箱即用的体验，只想完成任务，不想关注流水线如何设计**

比如说客户只想快速完成 Java、Go 项目的编译、部署、灰度发布，这种情况下平台提供方应该提前设计好流水线，准备好所需的流水线运行环境，以可视化界面或者 API 的方式提供给客户使用，客户无需了解流水线设计的细节

**客户有自定义流水线的需求**

先解决问题，允许客户以 YAML 文件的形式设计流水线，并且提供原子开发和引入能力，后续有这方面需求的客户多了，平台提供方还可以花成本来建设可视化的编辑器，降低用户学习成本和实施成本

**第三方系统想要接入 CI/CD 平台**

比如说第三方系统想要将自己的安全检测、真机测试等能力接入 CI/CD 平台，使用这些能力的任务是异步执行的，即任务实际上在第三方系统自己的服务器环境里执行，这种情况下平台提供方应该如何提供接入机制呢？

方案1: 任务里选择使用安全检测原子，runner 原子执行时，先向第三方系统发起任务开始请求，然后不停轮询第三方系统的 API 检查任务是否执行完成，原子执行完毕即代表任务执行完成

![](/assets/img/dive-into-github-action/solution1.png)

缺点：占用平台提供方的 Runner 资源

方案2：任务里选择使用安全检测原子，任务开启异步执行开关，原子执行时，向第三方系统发起任务开始请求，然后就释放 Runner 资源，但是 Job 仍处于运行中状态，直到第三方系统异步回调平台

![](/assets/img/dive-into-github-action/solution2.png)

缺点：异步回调容易丢失消息，如果要做到稳定不丢失消息，流水线引擎的复杂度会变高

方案3：任务里选择使用安全检测原子，任务选择只在标签为 A 的 Runner 上运行，第三方系统在自己的服务器环境里部署 Runner，标签注册为 A，第三方系统定期启动一次性的 Runner 会话，当流水线引擎发现了标签为 A 且空闲的 Runner 后，就将任务分发至该 Runner 运行，原子执行时，同步等待或者轮询检查安全检测是否结束，原子执行完毕即代表任务执行完成

![](/assets/img/dive-into-github-action/solution3.png)

缺点：第三方系统需要维护 Runner 集群，增加了第三方系统的接入成本

方案1最简单可靠，故选择方案1，Runner 资源可以通过 Serverless 实现 Runner 的弹性伸缩，这样成本也比较可控

**平台需要建立原子市场**

想办法复用 GitHub Actions 的原子生态，允许流水线能够使用 GitHub Actions 的原子，这是多赢的方案。平台提供方可以覆盖更多使用场景，第三方系统能节省接入平台的成本，客户能获得更多的产品价值

**如何管理 Runner 集群，支持 Windows、macOS、Linux 这 3 种操作系统？**

1. 可以通过容器技术在 Linux、Windows 操作系统里运行 Runner，Kubernetes 能调度容器，最终实现 Runner 集群弹性伸缩的效果
2. macOS Runner 集群可以借助 [Orka Platform](https://www.macstadium.com/orka) 或 [Tart](https://tart.run/) 或 [Docker-OSX](https://github.com/sickcodes/Docker-OSX) 来搭建，macOS 弹性伸缩实现难度比较大，人工扩缩容也是可以的

## 总结
我介绍了 GitHub Actions 相比其他 CI/CD 平台的优势和创新点，对 GitHub Actions 进行了全面的产品分析，并思考了自研一个 CI/CD 平台应该怎么着手


## 参考资料
- https://blog.codecentric.de/github-actions-nextgen-cicd