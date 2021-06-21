---
title: Android Q 编译原理之零 序言
date: 2021-06-15 17:20:06
categories:
- Compiler
tags:
- Android
comments: true
---

<p style="text-indent:2em">Android的版本一直快速的进行迭代着，从我们以前最常见的Android 4.4一直发展到了今天的Android 12.0版本(即Android K到Android S)，Android版本的快速迭代对于消费者来说是一件好事，但是对于开发者来说各种适配各种改造有时候吃翔的心情都有了。而对于Android版本的适配和各种改造的第一步就是从编译Android源码开始，可是不幸的是随着Android版本的迭代连编译Android源码的相关流程都发生了翻天覆地的变化。本系列基于Android 10.0 进行编译系统的体系的一个梳理。</p>

Android各个版本的对应关系如下:

| 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   | 12   |      |      |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| K    | L    | M    | N    | O    | P    | Q    | R    | S    |      |      |

#### 目录

- [00.序言](https://ljw-luojianwei.github.io/2021/06/15/Android-Q-编译原理之零-序言)
- [01.编译系统入门篇](https://ljw-luojianwei.github.io/2021/06/15/Android-Q-编译原理之一-编译系统入门篇)
- [02.编译环境初始化](https://ljw-luojianwei.github.io/2021/06/16/Android-Q-编译原理之二-编译环境初始化)
- [03.make编译过程](https://ljw-luojianwei.github.io/2021/06/17/Android-Q-编译系统之三-make编译过程/)
- [04.Image打包流程](https://luojianwei.top/2021/06/17/Android-Q-编译系统之四-Image打包流程/)

