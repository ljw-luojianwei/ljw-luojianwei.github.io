---
title: Android 源码管理之一 AOSP镜像下载
date: 2021-06-17 11:18:48
categories: 
tags:
- Android
- Tools
comments: true
---

<p style="text-indent:2em"><b>关于：</b>&nbsp&nbsp&nbsp&nbsp AOSP(Android Open Source Project)是Google开放的<a href="https://source.android.google.cn/" style="text-decoration:none">Android开源项目</a>。AOSP通俗来讲就是一个Android系统源码项目，通过它可以定制Android操作系统，国内手机厂商都是在此基础上开发的定制系统。因为墙的缘故，无法连接谷歌服务器获取AOSP源码，就从<a href="https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/" style="text-decoration:none">清华大学镜像站</a>或者<a href="https://mirrors.ustc.edu.cn/help/" style="text-decoration:none">中科大镜像</a>。</p>

<p style="text-indent:2em"><b>下载：</b>&nbsp&nbsp&nbsp&nbsp因第一次同步数据量特别大，如果网络不稳定，中间失败就要从头再来了。所以使用镜像站提供的打包的 AOSP 镜像，为一个 tar 包，大约 100G(单文件)，这样就可以通过 HTTP(S) 的方式下载，该方法支持断点续传。初始化 tar 包下载地址：</p>

```bash
# 清大源，每月更新的初始化包
wget -c -t 0 https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-latest.tar   # 使用 wget -c 断点续传进行下载初始化包
wget https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-latest.tar.md5 # 下载初始化包校验值
# or 
# 科大源，定时同步于清大源
wget -c -t 0 https://mirrors.ustc.edu.cn/aosp-monthly/aosp-latest.tar
wget https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-latest.tar.md5
```

<p style="text-indent:2em"><b>解压：</b>&nbsp&nbsp&nbsp&nbsp下载完成后，根据 checksum 的内容校验一下。接着解压：</p>

```bash
tar xvf aosp-latest.tar
```

<p style="text-indent:2em"><b>修改：</b>&nbsp&nbsp&nbsp&nbsp替换已有的 AOSP 源代码的 remote。更改为 
```bash
# 修改 .repo/manifests.git/config
# 将
url = https://android.googlesource.com/platform/manifest
# 更改为清大源
url = https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest
# 或者 科大源
url = git://mirrors.ustc.edu.cn/aosp/platform/manifest
# 如果由于某种原因不能通过 git 协议同步，则可以改为通过 HTTP 协议同步，不推荐使用 HTTP 协议同步，因为 HTTP 服务器不支持 repo sync 的 --depth 选项，可能导致部分仓库同步失败。
url = http://mirrors.ustc.edu.cn/aosp/platform/manifest 
```

<p style="text-indent:2em"><b>同步：</b>&nbsp&nbsp&nbsp&nbsp由于所有代码都是从隐藏的 .repo 目录中 checkout 出来的，所以 tar 包只保留了 .repo 目录。因此解压后只有一个隐藏的 .repo 目录，需要再 repo sync 一遍可得到完整的目录。由于 AOSP 镜像造成CPU/内存负载过重，镜像站限制了并发数量，因此尽量选择流量较小时错峰同步，sync 的时候并发数不宜太高，否则会出现 503 错误，即-j后面的数字建议选择 4,由于 repo sync 命令默认使用 4 个并发连接，因此勿需使用 -j 参数增加并发连接数。
</p>
```bash
cd aosp # 进入解压得到的 AOSP 工程目录，这时 ls 的话什么也看不到，因为只有一个隐藏的 .repo 目录
.repo/repo/repo sync # 正常同步一遍即可得到完整目录
# 或 
.repo/repo/repo sync -l # 仅checkout代码
```

<p style="text-indent:2em"><b>内核：</b>&nbsp&nbsp&nbsp&nbsp AOSP源码中并不包括内核源码，需要单独下载，内核源码有很多版本，比如common是通用的Linux内核，msm是用于使用高通MSM芯片的Android设备，goldfish是用于Android模拟器的内核源码。</p>

```bash
git clone https://aosp.tuna.tsinghua.edu.cn/kernel/goldfish.git
git clone https://aosp.tuna.tsinghua.edu.cn/kernel/msm.git
```

<p style="text-indent:2em"><b>工具：</b>&nbsp&nbsp&nbsp&nbsp Android源码包含数百个git库，光是下载这么多的git库就是一项繁重的任务，所以Google开发了<a href="https://source.android.google.cn/setup/using-repo" style="text-decoration:none">repo</a>，它是用于管理Android版本库的一个工具，使用了Python对git进行了一定的封装，简化了对多个Git版本库的管理。仓库位于：aosp\.repo\repo\.git

<p style="text-indent:2em"><b>含义：</b>&nbsp&nbsp&nbsp&nbsp AOSP代码目录含义

| 目录     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| art      | ART虚拟机做为Dalvik虚拟机的替代,字节码翻译优化成机器码从运行时提早到安装,以空间换时间达到更流畅的体验. |
| bionic   | C/C++运行时库,在NDK程序中很大一部分调用就是这里的程序        |
| bootable | 启动引导相关代码,用于Android装载和启动程序,其中就包括bootloader和recovery.bootloader是Android中唯一在Linux内核之前执行的程序.通过这段程序可以初始化硬件,建立内存控件的映射图等,总之,bootloader就是为Linux内核准备合适的运行环境. |
| build    | 存放系统编译规则及generic等基础开发包配置,用于编译Android源代码以及构建system.img，ramdisk.img等文件的工具. |
|          |                                                              |
|          |                                                              |
|          |                                                              |
|          |                                                              |



