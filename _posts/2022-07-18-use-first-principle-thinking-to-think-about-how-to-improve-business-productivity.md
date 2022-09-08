---
title: 使用第一性原理思维思考如何打造提高生产力的平台
date: 2022-09-03 11:00:00 +0800
categories: [技术]
tags:
- 思考
---

## 引言

现在全社会都在搞`数字化转型`，从政府到企业，那么为什么要进行数字化转型呢？本质上还是社会治理和企业经营难度变得更大了。

以企业来说，转型的目标是为了实现有质量的活着，比如能赚更多的钱或者持续保持稳健运营，转型的核心是期望借助数字化技术构建一个管理体系，以应对外部环境动荡、企业竞争变化和技术更新发展带来的不确定性。

数字化转型会带来大量的研发需求，如何更好更快的交付这些需求成为一个突出问题，该怎么打造一个平台去解决该问题？能不能用第一性原理思维去推导出发展方向？

## 什么是第一性原理
`第一性原理`指的是，将问题拆分成最基本的事实或规律，根据这些已知信息，不断推演和计算，从而找到解决问题最优路径的方法。

该思维比较出名的应用例子有
1. 埃隆·马斯克降低火箭发射成本的故事。将火箭发射成本进行细分，发展火箭可重复使用发射技术，简化设计和改进工艺以降低火箭制造成本
2. 亨利·福特降低汽车制造成本的故事。将汽车拆解为最基本的部件，利用装配线和流水线工人批量制造汽车
3. 埃隆·马斯克降低汽车电池组成本的故事。分析电池的原材料市场价格，不断逼近这个价格极限，采用自建工厂、改进电池设计、改进生产过程等措施来降低成本
4. 乔布斯去掉手机物理键盘的故事。分析手机的组成部件，通过去掉物理键盘来扩大触摸屏区域，带来更好的体验和更低的制造成本

## 如果采用类推思维去思考
类推思维是指借助自己的感觉和经验积累知识的方法

以国内外做得好的 3 个平台为例，分别是微软的 [Power Platform](https://powerplatform.microsoft.com/zh-cn/what-is-power-platform/)、创业公司[轻流](https://qingflow.com/)、华为的[应用魔方](https://www.huaweicloud.com/product/appcube.html)。这些平台都以帮助企业快速开发应用、应对市场发展为目的

### 微软 Power Platform
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/powerplatform.png)

除了上述 5 大产品外，还具有以下功能
- Dataverse：可以进行数据的管理，相当于是个更易于使用的数据库
- 连接器：可以和大量的 API、服务、系统进行通信
- Common Data Model：标准化和可扩展的数据模型，能方便数据的流转和利用，由微软和行业合作伙伴多年积累而成
- 生态系统：Power Platform 可以连接到微软的生态系统，例如 Azure（全球第二大云计算平台）、Microsoft 365（包含 Office 办公套件和 Teams）、Dynamics 365（包含 ERP 和 CRM 功能）
- Power Fx：低代码语言，类似 Excel 的公式，相比专业的编程语言更易于使用

Power Platform 的产品理念是让没有技术背景的人、开发者都能轻松高效的解决业务问题。产品架构上以 Dataverse 为核心，内置数据模型（Common Data Model）降低业务建模成本，可视化的 UI 编辑器（Power Apps、Power Pages）可降低应用开发成本，报表制作工具（Power BI）可挖掘出数据的价值，效率工具（Power Automate 和 Power Virtual Agents）可将员工从枯燥重复的任务中解放出来，这一整套组合拳下来，期望帮助企业更好的发展业务，节省运营成本

微软这么做了，我们是不是也要这么做呢？即
1. 开发一套非专业开发者也能学会使用的数据库
2. 开发可视化的 UI 编辑器，支持移动应用、门户、报表的制作
3. 开发效率工具，实现任务的自动化，支持桌面软件、Web、即时聊天窗口
4. 开发一种非专业开发者也能学会使用的编程语言

### 轻流
All-in-One 应用开发平台
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/qingliu-all-in-one.png)

专有轻流，面向多角色的企业级平台产品
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/qingliu-dedicated.png)

轻流是从表单+流程管理这种高频场景切入的，面向的是业务人员而不是开发者，随着行业案例的积累，客户的增多，
逐步发展为快速解决业务问题的应用开发平台和面向中大客户的企业级平台这两个套餐分级迭代。
值得一提的是轻流和神策一样，也采用了订阅制收费模式，这样保证了不管是公有云还是私有化部署都能以高迭代速度去服务客户

轻流这么做了，我们是不是也要这么做呢？即
1. 开发一套通过表单来进行数据建模的工具，支持流程管理，支持自动化任务
2. 开发可视化的 UI 编辑器，支持移动应用、门户、报表的制作
3. 提供插件能力，方便 IT 人员进行系统集成和功能扩展
4. 持续集成外部主流系统和业务能力，丰富平台生态

### 华为应用魔方 AppCube
AppCube 介绍
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/huawei-intro.jpeg)

AppCube 在华为内部的应用
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/huawei-best-practice.jpeg)

AppCube 提供了全场景的开发能力：
1. 对于开发者，可以用低代码开发能力开发应用
2. 对于业务人员，可以用零代码能力，通过一些拖拽和配置构建出应用
3. 对于面向专业领域的专业问题，如复杂业务算法，开发者可以使用 Java、Python 等语言开发，以微服务的方式发布到 AppCube 低代码平台进行托管和调用

AppCube 的做法类似 Power Platform，对于无代码技术路线，提供可视化的学习成本低的开发方式；对于低代码技术路线，
提供模拟软件开发过程的组件或服务去方便开发者使用；对于代码技术路线，提供应用的部署和运维能力

### 思考
- 这些平台都提供了`数据建模`能力，一种是类似数据库的建模过程，一种是表单的建模过程
- 这些平台都提供了 `UI 编辑器`，可以进行前端页面的可视化搭建，支持多平台（移动端、Web 端）
- 这些平台都提供了`代码开发`能力，可以进行代码级别的逻辑编写
- 这些平台都提供了`扩展`机制，可以集成外部系统和业务能力

看来，如果要做一个生产力平台，就必须提供这四大功能

## 采用第一性原理思维去思考
企业经营就是解决客户问题的过程。从以往的经验来看，有两类人解决问题时，需要数字化的解决方案
1. 还没有验证过某个问题存在客户需求，想要尽快验证
2. 已经验证过客户需求的存在，需要尽快上线

第一类人，如果采用代码开发的方式实现需求，成本是非常高的，可以想办法通过其他方式去低成本验证，比如美团外卖刚上线时都是客服人工处理外卖订单，Stripe 早期是手动填写交易信息发送到银行

第二类人，客户需求已经验证过了，就差个程序员了，这个时候沟通成本、人员开发成本、时间成本占大头

如何解决第二类人的问题呢？

> 要想提高生产力，最好是让有问题的人能尽快解决掉问题

举三个例子
1. 比如我们碰到某个数学题不会解，提高生产力的方式是有个老师在旁边教我们解。
2. 比如我们想去某个景点玩却发现路途遥远，提高生产力的方式是有辆车可以载我们过去。
3. 比如我们饿了肚子呱呱叫，家里没有任何食材和厨具，提高生产力的方式是去最近的餐馆或者叫外卖，而不是自己做饭

从这条定律出发，我们就要思考如何能让有问题的人尽快解决问题。

理想情况，最快解决问题的方式就是花钱请专业的人解决问题，将问题外包出去，效果最好，性能最好。

除了这种方式外还有其他第二好的方式吗？

> 保证问题解决速度的同时降低工具的使用难度

就像以前点火需要钻木取火，后来打火石点火、火柴点火，到现在的打火机点火，越来越方便，使用难度越来越低，点火越来越快，这样生产力才得到了巨大的提升

那么对于开发数字化的解决方案，怎么做才能既快速解决问题使用难度又低呢？

数字化解决方案一般需要经历数据的获取、处理、存储、展示这四个过程。我们依次来看

**数据的获取**

当前普及率最高的数据获取方式是 Excel，表单的出现让数据填写体验更好，但是当操作大量数据时，Excel 的效率是最高的

**数据的处理**

数据的处理逻辑，Excel 提供的方式是公式，开发者提供的方式是代码，使用难度最低的是配置化（在配置界面勾勾选选）、用例化（复用已存在的逻辑），不需要去记忆复杂的公式用法，不需要去练习如何写代码，这里的难点在于当逻辑复杂后如何保持使用难度不指数级增长

**数据的存储**

格式化的数据（如信用卡号码、电话号码、家庭住址、商品名称）一般存在 Excel 或者数据库里，非格式化的数据（如 word、音频、视频）一般直接存在硬盘上。使用难度最低的是 Excel，一个个格子，把数据填进去就行

**数据的展示**

目前的展示终端太丰富了，手机 APP、网站、小程序、电脑软件、电视大屏等等，这个也是最难降低使用难度的部分，个性化需求太多了，要想做到速度最快使用难度最低，需要一个使用难度很低的 UI 编辑器，最好能像 [Markdown](https://baike.baidu.com/item/markdown/3245829) 那样简单，只关注内容，样式啥的自动渲染，PPT 对于普通人来说都太难了

综上，
- A：对于数据的获取和存储，我们需要一种类似 Excel 的方案，既能存储数据，使用难度又低，使用效率还很高
- B：对于数据的处理，我们需要一种配置化、用例化的方案，能应对从简单到复杂的各种逻辑，且保持使用简单
- C：对于数据的展示，我们需要一种只关注内容，自动匹配好样式的方案，支持多种屏幕和终端

解法
- 对于 A，我们可以参考国外的 [Airtable](https://airtable.com/)、国内飞书的[多维表格](https://bitable.feishu.cn/)、腾讯文档[在线表格](https://docs.qq.com/home/product#excel)，支持多人协作，支持大批量数据的操作，支持多种数据类型

Airtable 截图
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/airtable-screenshot.png)

多维表格截图
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/bittable-screenshot.gif)

- 对于 B，我们可以参考苹果的[捷径](https://support.apple.com/guide/shortcuts/welcome/ios)，国外的 [Zapier](https://zapier.com/)，这些工具可编排逻辑，可通过引入更多应用和操作来扩展使用场景，在使用难度和逻辑可维护性上做到了一个好的平衡

苹果捷径截图
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/mac-shortcut-screenshot.png)

Zapier 截图
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/zapier-screenshot.png)

- 对于 C，我们可以参考国外的 [Notion](https://www.notion.so/)、国内的[息流](https://flowus.cn/)，提供让用户更聚焦内容的工具，通过 AI 或者模版化的方式自动匹配好样式

Notion 截图
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/notion-screenshot.png)

Markdown 截图
![](/assets/img/use-first-principle-thinking-to-think-about-how-to-improve-business-productivity/markdown-screenshot.gif)

## 总结
如果想打造一个能提升生产力的平台，最好方式就是用最快速度帮助遇到问题的人解决掉问题

如果采用类推思维去思考，我们可能得去做非常大而全的事情，比如
- 建设`数据建模`能力
- 建设 `UI 编辑器`能力
- 建设`代码开发`能力
- 建设`扩展`机制

如果采用第一性原理思维，我们就可以将生产力划分为数据的获取、处理、存储、展示四个阶段，运用既快且使用难度低的工具去处理，即
- 建设`新一代表格`能力，支持在线多人协作，支持多种数据类型，支持大批量数据
- 建设`逻辑编排`能力，类似捷径和 Zapier，实现逻辑的顺序编排和扩展
- 建设更聚焦内容的`编辑器`能力，用户只需编写内容，通过 AI 和模版等方法自动匹配好样式