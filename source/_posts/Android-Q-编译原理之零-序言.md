---
title: Android Q 编译原理之零 序言
date: 2021-06-15 17:20:06
categories:
- Compiler
tags:
- Android
comments: true
---

<p style="text-indent:2em">Android 的版本一直快速的进行迭代着，从我们以前最常见的 Android 4.4 一直发展到了今天的 Android 13.0 版本(即 Android K 到 Android T)，Android 版本的快速迭代对于消费者来说是一件好事，但是对于开发者来说各种适配各种改造有时候就很痛苦了。而对于 Android 版本的适配和各种改造的第一步就是从编译 Android 源码开始，可是不幸的是随着 Android 版本的迭代连编译 Android 源码的相关流程都发生了翻天覆地的变化。本系列基于 Android 10.0 进行编译系统的体系的一个梳理。</p>

Android 各个版本的对应关系如下:

| 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   | 12   | 13   |      |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| K    | L    | M    | N    | O    | P    | Q    | R    | S    | T    |      |

#### 目录

- [00.序言](https://ljw-luojianwei.github.io/2021/06/15/Android-Q-编译原理之零-序言)
- [01.编译系统入门篇](https://ljw-luojianwei.github.io/2021/06/15/Android-Q-编译原理之一-编译系统入门篇)
- [02.编译环境初始化](https://ljw-luojianwei.github.io/2021/06/16/Android-Q-编译原理之二-编译环境初始化)
- [03.make编译过程](https://ljw-luojianwei.github.io/2021/07/08/Android-Q-编译原理之三-make编译过程/)
- [04.Image打包流程](https://ljw-luojianwei.github.io/2021/07/08/Android-Q-编译系统之四-Image打包流程/)
- [05.Kati详解](https://ljw-luojianwei.github.io/2021/07/08/Android-Q-编译原理之五-Kati详解)
- [06.Blueprint简介](https://ljw-luojianwei.github.io/2021/07/08/Android-Q-编译原理之六-Blueprint简介/)
- [07.Blueprint代码详细分析](https://ljw-luojianwei.github.io/2021/07/08/Android-Q-编译原理之七-Blueprint代码详细分析/)
- [08.Android.bp 语法浅析](https://ljw-luojianwei.github.io/2021/07/08/Android-Q-编译原理之八-Android-bp-语法浅析/)
- [09.Ninja简介](https://ljw-luojianwei.github.io/2021/07/08/Android-Q-编译原理之九-Ninja简介/)
- [10.Ninja提升编译速度的方法](https://ljw-luojianwei.github.io/2021/07/08/Android-Q-编译原理之十-Ninja提升编译速度的方法/)
- [11.Soong 编译系统](https://ljw-luojianwei.github.io/2021/07/08/Android-Q-编译原理十一-Soong-编译系统/)

