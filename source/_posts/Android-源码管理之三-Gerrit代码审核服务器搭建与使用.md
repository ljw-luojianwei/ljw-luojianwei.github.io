---
title: Android 源码管理之三 Gerrit代码审核服务器搭建与使用
date: 2021-06-18 17:05:10
categories: 
tags:
- Android
- Tools
comments: true
---

### 前言

<p style="text-indent:2em">Google 的 AOSP 在 Git 的使用上有两个重要的创新，一个是为多版本库协同而引入的 repo，另外一个重要的创新就是 Gerrit —— 代码审核服务器。Gerrit 为 git 引入的代码审核是强制性的，就是说除非特别的授权设置，向 Git 版本库的推送(Push)必须要经过 Gerrit 服务器，修订必须经过代码审核的一套工作流之后，才可能经批准并纳入正式代码库中。</p>

### Gerrit简介

<p style="text-indent:2em">Gerrit，一种免费、开放源代码的代码审查软件，使用网页界面。利用网页浏览器，同一个团队的软件程序员，可以相互审阅彼此修改后的程序代码，决定是否能够提交，退回或者继续修改。它使用Git作为底层版本控制系统。它的作者为Google公司的Shawn Pearce，原先是为了管理Android计划而产生，它的名称来自于荷兰设计师赫里特·里特费尔德(Gerrit Rietveld)。最早它是由Python写成，在第二版后，改成用Java与SQL。使用Google Web Toolkit来产生前端的Java。</p>

### Gerrit工作原理和流程

<p style="text-indent:2em">首先贡献者的代码通过 git 命令(或git review封装)推送到 Gerrit 管理下的 Git 版本库，推送的提交转化为一个一个的代码审核任务，审核任务可以通过 refs/changes/下的引用访问到。代码审核者可以通过 Web 界面查看审核任务、代码变更，通过 Web 界面做出通过代码审核或者打回等决定。测试者也可以通过 refs/changes/引用获取(fetch)修订对其进行测试，如果测试通过就可以将该评审任务设置为校验通过(verified)。最后经过了审核和校验的修订可以通过 Gerrit 界面中提交动作合并到版本库对应的分支中。更详细的流程描述见下图所示： </p>

![这里写图片描述](Android-源码管理之三-Gerrit代码审核服务器搭建与使用/20151029180320488)

### Gerrit安装与配置

[雪梦科技 - 知乎 (zhihu.com)](https://www.zhihu.com/column/c_1019890899806896128)

[如何在 Ubuntu 20.04 上安装 Apache - ITCoder](https://www.itcoder.tech/posts/how-to-install-apache-on-ubuntu-20-04/)

[APACHE2 服务器配置 （二） 默认端口*** - 江召伟 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jiangzhaowei/p/7911764.html)

[技术|如何在 Ubuntu 上安装和优化 Apache (linux.cn)](https://linux.cn/article-9679-1.html)

[Ubuntu之apache2安装 - 简书 (jianshu.com)](https://www.jianshu.com/p/adeff478493e)

[(5条消息) Gerrit代码评审服务器搭建指南_云江游子-CSDN博客](https://blog.csdn.net/omfalio1101/article/details/80038663)

[(5条消息) Gerrit代码审核服务器搭建全过程_ganshuyu的专栏-CSDN博客_gerrit服务器搭建](https://blog.csdn.net/ganshuyu/article/details/8978614)

[Ubuntu下搭建本地Gerrit代码审核服务器 - 尚码园 (shangmayuan.com)](https://www.shangmayuan.com/a/bf593bf309194dabad9251cd.html)

[CentOS7搭建gerrit 代码审查服务方法 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1722078)

[(5条消息) Gerrit代码审核服务器搭建与使用_rungong123的专栏-CSDN博客](https://blog.csdn.net/rungong123/article/details/88420119)

[使用Gerrit进行代码评审 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/32833929)

[(5条消息) CentOS7搭建gerrit 代码审查服务_会飞的鸵鸟-CSDN博客](https://blog.csdn.net/u013207966/article/details/79112740)

[[原创\]Gerrit代码审查服务搭建及使用简介 (sohu.com)](https://www.sohu.com/a/115723456_494937)

[Gerrit代码审核服务器搭建全过程 - 莫水千流 - 博客园 (cnblogs.com)](https://www.cnblogs.com/zhoug2020/p/6477421.html)

[(5条消息) Gerrit代码审核服务器搭建与使用_rungong123的专栏-CSDN博客](https://blog.csdn.net/rungong123/article/details/88420119)

[(5条消息) Gerrit代码审核服务器搭建全过程_tq08g2z的专栏-CSDN博客_gerrit搭建](https://blog.csdn.net/tq08g2z/article/details/78627653?depth_1-)

[(5条消息) Gerrit代码审核服务器搭建全过程_jsd2root的博客-CSDN博客](https://blog.csdn.net/jsd2honey/article/details/94434655)

[Gerrit 代码审查安装 | IT工程师的生活足迹 (cn-blogs.cn)](https://cn-blogs.cn/archives/7016.html)





[Ubuntu16.04安装Docker - SegmentFault 思否](https://segmentfault.com/a/1190000014066388)
