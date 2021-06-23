---
title: Android 源码管理之二 repo
date: 2021-06-18 14:59:15
categories: 
tags:
- Android
- Tools
comments: true
---

### 概要

<p style="text-indent:2em">repo是Android为了方便管理多个git库而开发的Python脚本。repo的出现，并非为了取代git，而是为了让Android开发者更为有效的利用git。Android源码包含数百个git库，仅仅是下载这么多git库就是一项繁重的任务，所以在下载源码时，Android就引入了repo。 Android官方推荐下载repo的方法是通过Linux curl命令，下载完后，为repo脚本添加可执行权限：</p>

```bash
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

<p style="text-indent:2em">由于国内Google访问受限，所以上述命令不一定能下载成功。其实，我们现在可以从很多第三方渠道找到repo脚本，只需要取下来，确保repo可以正确执行即可。</p>

### 工作原理

<p style="text-indent:2em">repo需要关注当前git库的数量、名称、路径等，有了这些基本信息，才能对这些git库进行操作。通过集中维护所有git库的清单，repo可以方便的从清单中获取git库的信息。 这份清单会随着版本演进升级而产生变化，同时也有一些本地的修改定制需求，所以，repo是通过一个git库来管理项目的清单文件的，这个git库名字叫manifests。</p>

<p style="text-indent:2em">当打开repo这个可执行的python脚本后，发现代码量并不大(不超过1000行)，难道仅这一个脚本就完成了AOSP数百个git库的管理吗？并非如此。 repo是一系列脚本的集合，这些脚本也是通过git库来维护的，这个git库名字叫repo。</p>

<p style="text-indent:2em">在客户端使用repo初始化一个项目时，就会从远程把manifests和repo这两个git库拷贝到本地，但这对于Android开发人员来说，又是近乎无形的(一般通过文件管理器，是无法看到这两个git库的)。 repo将自动化的管理信息都隐藏根目录的.repo子目录中。</p>

### 项目清单库(.repo/manifests)

<p style="text-indent:2em">AOSP项目清单git库下，只有一个文件default.xml，是一个标准的XML，描述了当前repo管理的所有信息。 AOSP的default.xml的文件内容如下：</p>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
  <remote  name="aosp"
           fetch=".."
           review="https://android-review.googlesource.com/" />
  <default revision="master"
           remote="aosp"
           sync-j="4" />
  <project path="build/make" name="platform/build" groups="pdk,tradefed" >
    <copyfile src="core/root.mk" dest="Makefile" />
  </project>
  <project path="art" name="platform/art" groups="pdk" />
    ...
  <project path="tools/studio/translation" name="platform/tools/studio/translation" groups="notdefault,tools" />
  <project path="tools/swt" name="platform/tools/swt" groups="notdefault,tools" />
</manifest>
```

<p style="text-indent:2em"><font color="#00dd00">&ltremote&gt</font>：描述了远程仓库的基本信息。name描述的是一个远程仓库的名称，通常我们看到的命名是origin；fetch用作项目名称的前缘，在构造项目仓库远程地址时使用到；review描述的是用作code review的server地址。</p>

<p style="text-indent:2em"><font color="#00dd00">&ltdefault&gt</font>：default标签中定义的属性，将作为<font color="#00dd00">&ltproject&gt</font>标签的默认属性，在<font color="#00dd00">&ltproject&gt</font>标签中，也可以重写这些属性。属性revision表示当前的版本，也就是我们俗称的分支；属性remote描述的是默认使用的远程仓库名称，即<font color="#00dd00">&ltremote&gt</font>标签中name的属性值；属性sync-j表示在同步远程代码时，并发的任务数量，配置高的机器可以将这个值调大。</p>

<p style="text-indent:2em"><font color="#00dd00">&ltproject&gt</font>：每一个repo管理的git库，就是对应到一个<font color="#00dd00">&ltproject&gt</font>标签，path描述的是项目相对于远程仓库URL的路径，同时将作为对应的git库在本地代码的路径; name用于定义项目名称，命名方式采用的是整个项目URL的相对地址。譬如，AOSP项目的URL为https://android.googlesource.com，命名为platform/build的git库，访问的URL就是https://android.googlesource.com/platform/build。</p>

<p style="text-indent:2em">如果需要新增或替换一些git库，可以通过修改default.xml来实现，repo会根据配置信息，自动化管理。但直接对default.xml的定制，可能会导致下一次更新项目清单时，与远程default.xml发生冲突。 因此，repo提供了一个种更为灵活的定制方式local_manifests：所有的定制是遵循default.xml规范的，文件名可以自定义，譬如local_manifest.xml, another_local_manifest.xml等， 将定制的XML放在新建的.repo/local_manifests子目录即可。repo会遍历.repo/local_manifests目录下的所有*.xml文件，最终与default.xml合并成一个总的项目清单文件manifest.xml。</p>

<p style="text-indent:2em">local_manifests的修改示例如下：</p>

```bash
$ ls .repo/local_manifests
local_manifest.xml
another_local_manifest.xml

$ cat .repo/local_manifests/local_manifest.xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
    <project path="manifest" name="tools/manifest" />
    <project path="platform-manifest" name="platform/manifest" />
</manifest>
```

### repo脚本库(.repo/repo)

<p style="text-indent:2em">repo对git命令进行了封装，提供了一套repo的命令集(包括init, sync等)，所有repo管理的自动化实现也都包含在这个git库中。 在第一次初始化的时候，repo会从远程把这个git库下载到本地。</p>

### 仓库目录和工作目录

<p style="text-indent:2em">仓库目录保存的是历史信息和修改记录，工作目录保存的是当前版本的信息。一般来说，一个项目的Git仓库目录(默认为.git目录)是位于工作目录下面的，但是Git支持将一个项目的Git仓库目录和工作目录分开来存放。 对于repo管理而言，既有分开存放，也有位于工作目录存放的:</p>

- manifests： 仓库目录有两份拷贝，一份位于工作目录(.repo/manifests)的.git目录下，另一份独立存放于.repo/manifests.git

- repo：仓库目录位于工作目录(.repo/repo)的.git目录下

- project：所有被管理git库的仓库目录都是分开存放的，位于.repo/projects目录下。同时，也会保留工作目录的.git，但里面所有的文件都是到.repo的链接。这样，即做到了分开存放，也兼容了在工作目录下的所有git命令。

<p style="text-indent:2em">既然.repo目录下保存了项目的所有信息，所有要拷贝一个项目时，只是需要拷贝这个目录就可以了。repo支持从本地已有的.repo中恢复原有的项目。</p>

### 使用介绍

<p style="text-indent:2em">repo命令的使用格式如下所示：</p>

```bash
$ repo <COMMAND> <OPTIONS>
```

<p style="text-indent:2em">可选的的有：help、init、sync、upload、diff、download、forall、prune、start、status，每一个命令都有实际的使用场景， 下面我们先对这些命令做一个简要的介绍：</p>





[Android源码下载+编译+调试框架层代码 - SegmentFault 思否](https://segmentfault.com/a/1190000017317513)

[(5条消息) Android源码下载+编译+调试框架层代码_oldbalck的博客-CSDN博客](https://blog.csdn.net/weixin_34326179/article/details/88694499)

[(5条消息) Android源码编译环境搭建_w2064004678的博客-CSDN博客_安卓编译环境](https://blog.csdn.net/w2064004678/article/details/104813771/)

[Android源码下载 编译 刷机 (juejin.cn)](https://juejin.cn/post/6844903833739493390)

[Android Q源码下载，源码编译 - 简书 (jianshu.com)](https://www.jianshu.com/p/ce0392233289/)

[Android源码下载并编译 - SegmentFault 思否](https://segmentfault.com/a/1190000022865500)

[Android Studio 调试 Android Framework 层代码 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/295363172)

[Repo 命令参考资料  | Android 开源项目  | Android Open Source Project](https://source.android.com/source/using-repo)

[搭建安卓源码服务器，repo+gerrit+git环境，代码审核 - 简书 (jianshu.com)](https://www.jianshu.com/p/03d24ddb22de)
