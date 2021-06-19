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

