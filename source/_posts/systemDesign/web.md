---
layout: post
title: "生产就绪应用程序架构的高层次概述"
categories: 持续集成,系统设计
tags: 持续集成,系统设计
keywords: 持续集成, 持续部署, 负载均衡器, 反向代理, 数据存储, 外部 API, 监控, 日志记录, 警报服务, 生产环境调试
date: 2023/11/26 19:28:25
---
在你使用的每个完美应用程序背后，都有一整套的架构、测试、监控和安全措施。今天，让我们来看看一个生产就绪应用程序的非常高层次的架构。

## CI/CD 管道

我们的第一个关键领域是持续集成和持续部署——CI/CD 管道。

这确保我们的代码从存储库经过一系列测试和管道检查，无需任何手动干预就进入生产服务器。

![1*DIPdJHlAKsQero5qiyM7NQ.png](/uploads/1*DIPdJHlAKsQero5qiyM7NQ.png)

它配置了像 Jenkins 或 GitHub Actions 这样的平台，用于自动化我们的部署流程。

<!--more-->

## 与服务器的交互

一旦我们的应用程序投入生产，它就必须处理大量用户请求。这由我们的负载均衡器和反向代理（如 Nginx）管理。

![1*Gm7GMJvVh-dVdT6C07eMsw.png](/uploads/1*Gm7GMJvVh-dVdT6C07eMsw.png)

它们确保用户请求均匀分布在多个服务器上，即使在流量激增期间也能保持平稳的用户体验。

## 骨干：数据存储和外部 API

我们的服务器还需要存储数据。为此，我们还有一个不运行在相同生产服务器上的外部存储服务器。相反，它通过网络连接。

![1*OHiyw0UFHWRnsQ5XubDTXg.png](/uploads/1*OHiyw0UFHWRnsQ5XubDTXg.png)

我们的服务器可能还与其他服务器通信。而且我们可以有多个这样的服务，不仅仅是一个。

![1*K0zq-pfcKDYdJCvvEOx4Ow.png](/uploads/1*K0zq-pfcKDYdJCvvEOx4Ow.png)

## 监控、日志和警报：默默的保护者

为了确保一切运行顺利，我们有日志记录和监控系统，对每个微观交互保持敏锐的关注，存储日志并分析数据。

![1*Gz8f6IeZRgPT1AE-fFhzcw.png](/uploads/1*Gz8f6IeZRgPT1AE-fFhzcw.png)

将日志存储在外部服务上是一种标准做法，通常不在我们的主要生产服务器上。

对于后端，像 PM2 这样的工具可以用于日志记录和监控。对于前端，像 Sentry 这样的平台可以用于实时捕获和报告错误。

![1*PZ0wV0VYw8EI1zFVmMPn8w.png](/uploads/1*PZ0wV0VYw8EI1zFVmMPn8w.png)

## 警报服务

当事情不按计划进行时，也就是我们的日志系统检测到失败的请求或异常时？

首先，它通知我们的警报服务。之后，推送通知被发送，以保持用户的知情。从一般的“出现问题了”到具体的“支付失败”，有效的沟通确保用户不会被置于黑暗中，培养了信任和可靠性。

![1*dbccl16Pm4c4SpKS_D3dCg.png](/uploads/1*dbccl16Pm4c4SpKS_D3dCg.png)

现代做法是将这些警报直接集成到我们常用的平台中，如 Slack。

![1*iJ0jseZ7PLHyqGC2EgVb0Q.png](/uploads/1*iJ0jseZ7PLHyqGC2EgVb0Q.png)

想象一下一个专门的 Slack 频道，警报在问题出现的瞬间弹出。这使开发人员几乎可以立即采取行动，在问题升级之前解决根本原因。

## 在生产环境中调试

之后，开发人员必须调试问题。

**日志查看：**首先，需要识别问题。我们之前提到的那些日志？它们是我们首选的工具。开发人员通过它们筛选，寻找可能指向问题源的模式或异常。

![1*vECE_pDLSK_BNBTb1nUiZA.png](/uploads/1*vECE_pDLSK_BNBTb1nUiZA.png)

**在安全环境中复制：**黄金法则是——*永远不要直接在生产环境中调试。*相反，开发人员在‘staging’或‘test’环境中重新创建问题。这确保用户不会受到调试过程的影响。

![1*0PgaONmKlvOJpC9RY3rYPQ.png](/uploads/1*0PgaONmKlvOJpC9RY3rYPQ.png)

开发人员使用工具来查看运行中的应用程序并开始调试。

**热修复：**一旦错误修复，就会推出‘hotfix’。这是一个快速的、临时的修复，旨在让事情再次运行起来。这就像在更永久的解决方案可以实施之前的一个补丁。

![1*3OMXgA7h80Q4hkMNoPkwzw.png](/uploads/1*3OMXgA7h80Q4hkMNoPkwzw.png)


## 系统设计概念系列文章

[计算机的层次化架构](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488478&idx=1&sn=351b322c2089d8fd7124cf801e0a0524&chksm=fb7c9d29cc0b143facd8d62fb15812d6d28e22749e1183c48d6946dbd13d2e206cd9826bfb20&scene=21#wechat_redirect)

[每个开发者都应该知道的7个原则](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488411&idx=1&sn=2520f364a171f0d747a855600c99cc4b&chksm=fb7c9d6ccc0b147a44627a8eef07b5772acbda406ce566afe306ed2b3c98f665e05c12b6ad36&scene=21#wechat_redirect)

[6个系统设计的基本概念](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488409&idx=1&sn=a02b97d71d301be4200ab25e4ffad0bc&chksm=fb7c9d6ecc0b1478f85986554735e307e2c92d45236f241dec44a529e9c799c521d5448f9f9a&scene=21#wechat_redirect)

[数据库：系统设计的核心](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488407&idx=1&sn=6072efba48ed2f9f1968594a1055a3f6&chksm=fb7c9d60cc0b14766e394d5c04f33d5b1c63987d6cf5b66732887e9ffedbbfc576e5a6617891&scene=21#wechat_redirect)


## 图解系列

[系统设计中的缓存技术：完整指南](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488260&idx=1&sn=89701a38ab8af1c670b432b5e7bedf2d&chksm=fb7c9df3cc0b14e52a043999e59b5c1251c3183a61b8c9d35777f237d1478c07b7d01352346e&scene=21#wechat_redirect)

[关系数据库的全景图](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488165&idx=1&sn=2c4aaad53e6ca87f61e23dfba2fd8f9e&chksm=fb7c9c52cc0b1544266bb7e008f55bfbaa9b2acdb512eb07beece554e9f766e80f12699208b7&scene=21#wechat_redirect)

[Redis 全景解析](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247487961&idx=1&sn=f1437b130e1828f4c1c0c45806e4922f&chksm=fb7c9f2ecc0b1638cf34322599d4bd42c002bf2e37c642d9dbc596366f91f237f410b4e90fc5&scene=21#wechat_redirect)

当然架构设计、全景图解系列还有很多,快来关注一起学习吧~
