---
layout: post
title:  "Mac(Intel)装Windows 11"
date:   2022-09-22 21:28:28 +0800
categories: Apple MacOS
---

起因是买了个新MacBook，旧的没什么用于是打算干脆刷成Windows。毕竟新的MacBook都是用M系列ARM架构的芯片了，无法在物理机上装基于x86架构的Windows。之前我是用WinToGo整了个Win10来着，但是每次打开都要插硬盘+重启，很麻烦，反正旧电脑的MacOS也不用了，不如直接装双系统然后只用Windows这样方便。正好巨硬更新了Win11，所以这次打算体验一把新系统。

开始试了直接用BootCamp装Win11的iso，但是报错该PC无法安装Win11。所以上网搜索了一下，发现了[如下参考视频](https://www.youtube.com/embed/ISlalQsrWfk)。我按照他的步骤做没有出现问题，所以分享出来。

嫌太长或者看不到视频的可以看下面我总结的文字版。(2026注：视频已下架)

1. 可直接安装官方Win11的Mac型号：
- MacBook (Retina，12 吋，2017)
- MacBook Air (13 吋，2017)
- MacBook Pro (15 吋，2016)
- MacBook Pro (13 吋，2016，四個 Thunderbolt 3 埠)
- MacBook Pro (13 吋，2016，二個 Thunderbolt 3 埠)
- Mac mini (2018)
- iMac Pro (2017)
- iMac (Retina 5K，27 吋，2017)
- iMac (Retina 4K，21.5 吋，2017)
- iMac (21.5 吋，2017)
- Mac Pro (2019)
其余型号，比如我的MacBook Pro（13寸，2017，2个雷雳3接口），需按下文步骤操作
2. 下载[Win10](https://www.microsoft.com/EN-US/software-download/windows10ISO)和[Win11](https://www.microsoft.com/en-us/software-download/windows11)的ISO镜像，下文称两个文件为`旧Win10`和`旧Win11`
3. 安装可用来更改ISO文件的工具`wimlib`，安装工具homebrew如果没有可以先装一下
   ```shell
   brew install wimlib
   ```
4. 新建文件夹`新Win11`
5. 将`旧Win10.iso`里所有文件复制粘贴到`新Win11`里
6. 删除`/新Win11/sources/install.wim`文件
7. 把`/旧Win11/sources/install.wim`从`旧Win11.iso`里拿出来
8. cd到上面这个文件所在目录并对其进行如下操作
   ```shell
   wimlib-imagex split install.wim install.swm 3500
   ```
   得到`install.swm`和`install2.swm`
9.  把上面这俩货复制粘贴到`/新Win11/sources`里
10.  打开自带的`Disk Utility`app
11. 点左上角"File"-"New Image"-"Image From Folder..."，选`新Win11`文件夹，加密选无，格式选"DVD/CD master"，得到`新Win11.cdr`
12. cd到相应目录，对新生成的文件进行如下操作
    ```shell
     hdiutil makehybrid -iso -joliet -o Win11.iso Win11.cdr
    ```
    得到`新Win11.iso`
13. 用Intel版Mac自带的`Boot Camp Assistant`app打开`新Win11.iso`，按提示一步步走就好了。

![结果](/assets/images/windows11-on-intel-mac/result.jpg)