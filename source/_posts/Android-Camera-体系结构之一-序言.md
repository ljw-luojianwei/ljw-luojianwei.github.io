---
title: Android Camera 体系结构之一 序言
date: 2021-06-11 14:42:37
categories:
- Camera
tags:
- Android
---

<p style="text-indent:2em">Android系统自2007年底被Google推出问世以来，已经走过14个春夏秋冬，历经多次的大大小小的迭代重构、架构调整，虽然时代年轮依旧滚滚，虽然每年技术依然在不断地推陈出新，但是到目前为止，依然可以窥见其接口与实现相分离的核心设计理念，所以其架构设计的优越性可见一斑，另外，随着智能手机的快速普及，面对这一庞大终端市场，作为Android系统中最重要的几个组件之一的相机系统也必定会作为主要战场在手机市场中与其它厂商展开竞争。近几年，谷歌针对相机框架体系进行了多次迭代优化，就而今的相机框架而言，整体架构设计十分优秀，作为一个相机系统开发者，个人感觉很有必要针对整个框架进行一次完整的梳理总结，所以便促使我写下该系列文章，以整个框架的梳理为出发点，深入介绍下Android 相机框架。</p>

### 文章目录

[01.序言](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之一-序言)
[02.相机简史](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之二-相机简史/)
[03.架构概览](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之三-架构概览/)
[04.应用层](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之四-相机应用层)
[05.服务层](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之五-相机服务层/)
[06.硬件抽象层](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之六-相机硬件抽象层/)
[07.硬件抽象层实现](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之七-相机硬件抽象层实现/)
[08.驱动层之V4L2框架](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之八-相机驱动层–V4L2框架解析/)
[09.驱动层之高通KMD](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之九-相机驱动层–高通KMD框架详解/)
[10.硬件层](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构之十-相机硬件层/)
[11.总结](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构十一-安卓相机架构总结/)
[12.手机相机的未来与发展](https://ljw-luojianwei.github.io/2021/06/11/Android-Camera-体系结构十二-手机相机的未来与发展/)
