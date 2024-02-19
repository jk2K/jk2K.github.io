---
title: PostHog 新手入门，一个用于帮助一流团队打造好产品的工具平台
date: 2024-02-18 20:50:00 +0800
categories: [技术]
tags:
- 介绍
---

## 前言
PostHog 可以自动追踪使用者在网站上的行为，帮助产研团队了解用户以及如何改进产品。他能做到自动捕获点击事件、鼠标运动轨迹，而无需前端工程师手动埋点推送事件。我以腾讯云对象存储为分析案例，来看看他的效果怎么样

## 站点接入
要想将网站接入 PostHog，需要在网站里添加一段 JavaScript 脚本，因为只是个人研究目的，所以我借助浏览器插件来实现
我在 Chrome 浏览器上安装了 Tampermonkey 插件，然后注入网站接入脚本

```javascript
// ==UserScript==
// @name         Tencent COS
// @namespace    http://tampermonkey.net/
// @version      2023-12-29
// @description  try to take over the world!
// @author       You
// @match        https://console.cloud.tencent.com/cos/*
// @icon         https://www.google.com/s2/favicons?sz=64&domain=jd.com
// @grant        none
// ==/UserScript==
(function() {
    'use strict';
    // 创建一个新的 script 元素
    var script = document.createElement('script');
    // 设置 script 元素的 src 属性
    script.textContent = `
    !function(t,e){var o,n,p,r;e.__SV||(window.posthog=e,e._i=[],e.init=function(i,s,a){function g(t,e){var o=e.split(".");2==o.length&&(t=t[o[0]],e=o[1]),t[e]=function(){t.push([e].concat(Array.prototype.slice.call(arguments,0)))}}(p=t.createElement("script")).type="text/javascript",p.async=!0,p.src=s.api_host+"/static/array.js",(r=t.getElementsByTagName("script")[0]).parentNode.insertBefore(p,r);var u=e;for(void 0!==a?u=e[a]=[]:a="posthog",u.people=u.people||[],u.toString=function(t){var e="posthog";return"posthog"!==a&&(e+="."+a),t||(e+=" (stub)"),e},u.people.toString=function(){return u.toString(1)+".people (stub)"},o="capture identify alias people.set people.set_once set_config register register_once unregister opt_out_capturing has_opted_out_capturing opt_in_capturing reset isFeatureEnabled onFeatureFlags getFeatureFlag getFeatureFlagPayload reloadFeatureFlags group updateEarlyAccessFeatureEnrollment getEarlyAccessFeatures getActiveMatchingSurveys getSurveys onSessionId".split(" "),n=0;n<o.length;n++)g(u,o[n]);e._i.push([i,s,a])},e.__SV=1)}(document,window.posthog||[]);
    posthog.init('<密钥>',{api_host:'https://app.posthog.com'})

  fetch('https://console.cloud.tencent.com/cgi/login?action=checkLogin&t=1708350973774&uin=<腾讯云Uin>&ownerUin=0&csrfCode=492421633', {
    credentials: 'include' // 确保跨域请求时发送 Cookie
  })
  .then(response => {
    // 检查响应状态
    if (!response.ok) {
      throw new Error('Network response was not ok ' + response.statusText);
    }
    return response.json();
  })
  .then(data => {
    //console.log(data); // 这里处理你的数据
    posthog.identify(
      data.data.userInfo.uin,
      { fullName: data.data.userInfo.displayName }
    );
  })
  .catch(error => {
    console.error('There was a problem with the fetch operation: ', error);
  });
    `;
    // 将 script 元素添加到文档的 body 中
    document.body.appendChild(script);
})();
```

当使用浏览器访问 https://console.cloud.tencent.com/cos/ 时，上述脚本会捕捉各种浏览器事件，并发往 https://app.posthog.com，

`@match` 对应的值 https://console.cloud.tencent.com/cos/* 代表对 cos 路径下所有网页都生效

## 接入效果
然后随便在腾讯云对象存储里逛逛，一会 PostHog 就会展示收集到的数据了

### 会话回放
![](/assets/img/intro-product-os-posthog/session-replay-demo.gif)

PostHog 可以看到某个人在一段时间内的鼠标运动轨迹，了解这个人是怎么使用产品的

### 按钮点击热力图
打开热力图
![](/assets/img/intro-product-os-posthog/open-Launch-toolbar.png)

可以看到每个按钮的点击次数

![](/assets/img/intro-product-os-posthog/heatmap-demo.png)


### 自动捕获事件
用户在网站上的点击、页面跳转都会生成事件，PostHog 会自动捕捉事件并记录下来

![](/assets/img/intro-product-os-posthog/activity-demo.png)

### 功能覆盖率
![](/assets//img/intro-product-os-posthog/feature-usage-1.png)

假设我想知道哪些人在使用`基本使用统计`功能，哪些人在高频使用这个功能

从事件列表里可以查到，点击`基本使用统计`按钮会生成哪个事件
![](/assets//img/intro-product-os-posthog/feature-usage-2.png)

当用户点击`基本使用统计`按钮后，PostHog 会自动创建事件，事件标识为 `phc_PrwtoKZs8ec17JSBdYMcudnNQYoS1qXnd8wxHhCQz6R`

![](/assets//img/intro-product-os-posthog/feature-usage-3.png)

新建一个洞察报告，报告类型先选择 Trends（趋势）

![](/assets//img/intro-product-os-posthog/feature-usage-4.png)

筛选出点击`基本使用统计`按钮对应的事件，用 token `phc_PrwtoKZs8ec17JSBdYMcudnNQYoS1qXnd8wxHhCQz6R` 去筛选自动捕捉（Autocapture）的事件

下方就会展示出对应的报告，统计每个事件发生的次数。Y 轴是事件发生次数

![](/assets//img/intro-product-os-posthog/feature-usage-5.png)

![](/assets//img/intro-product-os-posthog/feature-usage-6.png)

鼠标悬停在折线图某个点上可以查看具体使用人

![](/assets//img/intro-product-os-posthog/feature-usage-7.png)

我们将报告类型切换为 Stickness（粘性）

![](/assets//img/intro-product-os-posthog/feature-usage-8.png)

这样 Y 轴就是用户数量了，X 轴是时间周期里用户触发该事件的天数

![](/assets//img/intro-product-os-posthog/feature-usage-9.png)

比如说时间周期是最近7天，3days 对应的值是 1740，代表最近7天，1740 个人在其中3天触发过该事件，7天有3天都在用，那么这1740个人算是重度活跃用户了


还有个报告类型是 Retention（留存），代表的是指定时间周期里（比如说最近7天）每天会有多少百分比的用户仍在持续触发事件

![](/assets//img/intro-product-os-posthog/feature-usage-10.png)

### 用户反馈
![](/assets//img/intro-product-os-posthog/survey-1.png)

点击右上角 New survey（新建调研）

支持多种类型的调研

![](/assets//img/intro-product-os-posthog/survey-2.png)

新建一个净推荐值调研

![](/assets//img/intro-product-os-posthog/survey-3.png)

保存后，启动该调研

![](/assets//img/intro-product-os-posthog/survey-4.png)

效果如下
![](/assets//img/intro-product-os-posthog/survey-5.png)

数据收集如下


![](/assets//img/intro-product-os-posthog/survey-6.png)


## 商业模式
PostHog 目标是帮助产研团队设计出好产品，在一个平台上提供一切必要的工具。

PostHog 按照每个功能的使用情况收费

比如产品分析按照事件数量收费

![](/assets//img/intro-product-os-posthog/business-mode-1.png)

会话回放按照录制数量收费

![](/assets//img/intro-product-os-posthog/business-mode-2.png)

调研按照调研回收数量收费

![](/assets//img/intro-product-os-posthog/business-mode-3.png)

PostHog 提供了一个价格计算器来帮助客户计算每月花费

![](/assets//img/intro-product-os-posthog/business-mode-4.png)

PostHog 是订阅制收费，开源了他的代码，有些代码是 MIT 协议的，有些代码是需要购买授权的

## 对比其他产品
PostHog 相比谷歌分析。

谷歌分析主要是为营销人员设计的，用于分析落地页、对比营销渠道、计算广告费用的回报率

PostHog 主要是为产研团队设计的，帮助他们理解用户和打造更好的产品，包括用户行为分析和一大堆其他能力，例如会话回放、点击热力图、功能开关、A/B 测试、调研等

PostHog 相比 Hotjar。

Hotjar 主要为营销人员和用户体验设计师设计的，提供会话回放、热力图、调研，是一个用户研究工具

PostHog 是一个用于打造好产品的多合一的一体化平台，包括产品分析、会话回放、功能开关等能力，专为产研团队而设计

## 总结
产品优化这条路上有很多现成的单点工具，但这些工具太分散了，联合在一起使用成本很高，新时代来临，有了新的操作方法，PostHog 选择从 0 开始打造一体化的产品，从会话回放、热力图、用户反馈这些竞争小的功能开始切入，最终实现一个平台解决所有问题。

这种整合多种工具为一个平台的思路，且专门为产研团队而设计，梦想挺大，看他未来能不能被市场接受了