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

| 目录             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| art              | 全新的ART运行环境，ART虚拟机做为Dalvik虚拟机的替代,字节码翻译优化成机器码从运行时提早到安装,以空间换时间达到更流畅的体验. |
| bionic           | C/C++运行时库,在NDK程序中很大一部分调用就是这里的程序        |
| bootable         | 启动引导相关代码,用于Android装载和启动程序,其中就包括bootloader和recovery.bootloader是Android中唯一在Linux内核之前执行的程序.通过这段程序可以初始化硬件,建立内存控件的映射图等,总之,bootloader就是为Linux内核准备合适的运行环境. |
| build            | 存放系统编译规则及generic等基础开发包配置,用于编译Android源代码以及构建system.img，ramdisk.img等文件的工具. |
| compatibility    | Android兼容性计划                                            |
| cts              | Android兼容性测试套件标准                                    |
| dalvik           | dalvik JAVA虚拟机                                            |
| developers       | 开发者目录,展现了当前版本的新特性                            |
| development      | 应用程序开发相关,示例以及开发工具 主要是给系统开发者使用     |
| device           | 设备相关配置,各品牌手机在硬件上会有差别，厂商会对源码进行定制 修改它的某些部分来配合自家硬件的特性 |
| external         | android使用的一些开源模组相关文件                            |
| frameworks       | 应用程序核心框架，Android系统核心部分，由Java和C++编写       |
| hardware         | 主要是硬件抽象层的代码，部分厂家开源的硬解适配层HAL代码      |
| kernel           | Android Linux 内核，，不过Android默认不提供，需要单独下载    |
| libcore          | Java核心库相关文件，包括java api的源码                       |
| libnativehelper  | 动态库，实现JNI库的基础                                      |
| ndk              | NDK相关代码，帮助开发人员在应用程序中嵌入C/C++代码           |
| out              | 编译完成后代码输出在此目录                                   |
| packages         | 应用程序包                                                   |
| pdk              | Plug Development Kit 的缩写，本地开发套件，google减小碎片化的东西 |
| platform_testing | 平台测试                                                     |
| prebuilts        | x86和arm架构下预编译好的一些资源，包括模拟器,内核文件        |
| sdk              | 在开发环境中使用的工具，如ddms，draw9path，sdkmanager，sdk和模拟器 |
| system           | 底层文件系统库、应用和组件 构成 Android的基本系统            |
| test             |                                                              |
| toolchain        | 工具链文件                                                   |
| tools            | 工具文件                                                     |
| vendor           |                                                              |
| Android.bp       | Android7.0开始代替Android.mk文件，它是告诉ndk将jni代码编译成动态库的一个脚本 |
| Makefile         | 全局Makefile文件，用来定义编译规则                           |

##### 应用层packages部分

应用层位于整个Android系统的最上层，开发者开发的应用程序以及系统内置的应用程序都是在应用层。源码根目录中的packages目录对应着系统应用层。它的目录结构：

| packages目录 | 描述           |
| ------------ | -------------- |
| apps         | 核心应用程序   |
| experimental | 第三方应用程序 |
| inputmethods | 输入法目录     |
| modules      |                |
| providers    | 内容提供者目录 |
| screensavers | 屏幕保护       |
| services     | 通信服务       |
| wallpapers   | 墙纸           |

## 应用框架层

应用框架层是系统的核心部分，一方面向上提供接口给应用层调用，另一方面向下与C/C++程序库以及硬件抽象层等进行衔接。其中目录结构如下：

- av：多媒体框架
- base：Android源码的主要核心目录
- compile：编译相关
- ex：文件解析器
- hardware：硬件适配接口
- layoutlib：布局相关
- minikin：Android原生字体，连体字效果
- ml：机器学习
- multidex：多dex加载器
- native：native实现
- opt：一些软件
- rs：Render Script，可创建3D接口
- support：framework支持文件
- wilhelm：基于Khronos的OpenSL ES/OpenMAX AL的audio/multimedia实现

其中base目录中是应用框架层的主要核心代码，目录结构如下：

- apct-tests：性能优化测试
- api：android应用框架层声明类、属性和资源
- cmds：android系统启动时用到的commands
- core：framework的核心框架组件
- data：android下的资源(字体、声音、视频、软盘等)
- docs：android项目说明
- drm：实现权限管理，数字内容解密等模块的工作
- graphics：图像渲染模块
- keystore：秘钥库
- libs：库信息(界面、存储、USB)
- location：位置信息
- media：手机媒体管理(音频、视频等)
- native：本地方法实现(传感器、输入、界面、窗体)
- nfc-extras：近场通讯
- obex：蓝牙
- opengl：2D和3D图形绘制
- packages：框架层的实现(界面、服务、存储)
- proto：协议框架
- rs：资源框架
- samples：例子程序
- sax：xml解析器
- services：各种服务程序
- telecomm：telecomm通信框架
- telephony：电话通讯框架
- tests：各种测试
- vr：虚拟现实相关
- wifi：wifi模块
