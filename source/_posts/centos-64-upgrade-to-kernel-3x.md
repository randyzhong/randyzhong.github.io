---
title: CentOS 6.4 升级到 3.x Kernel
tags:
  - CentOS
  - Kernel
  - VMware
categories:
  - '*Nix'
date: 2013-04-28 12:12:57
---

#### 1.前言

好吧，虽然这是一个关于微软平台的技术日志，但是并不限于Win，最近手头有些事情需要用到 Linux/Unix 等非 Win 系统，所以还是在这里开个分类目录记录下来，有备无患嘛。

#### 2.准备工作

CentOS 6.4 发布出来有一段时间了，最近才用上，可是发现内核还是 2.6.32，是不是也太旧了点，于是乎打算将其内核升级到 3.x。

以下是准备工作：

2.1. 安装组件:
```bash
yum groupinstall "Development Tools" #这样做会确定你拥有编译时所需的一切工具。
yum install ncurses-devel #你必须这样才能让 make *config 这个指令正确地执行。
yum install qt-devel #如果你没有 X 环境，这一条可以不用。
yum install hmaccalc zlib-devel binutils-devel elfutils-libelf-devel #创建 CentOS-6 内核时需要它们。
```
2.2. 安装 wget
```bash
yum -y install wget #安装 wget 下载工具
```
2.3. 下载 Kernel 内核源码包，这里用的是北京交大的镜像，你也可以直接去 www.kernel.org 获取
```bash
wget http://mirror.bjtu.edu.cn/kernel/linux/kernel/v3.x/linux-3.2.21.tar.xz
```

#### 3.编译/安装内核

3.1. 解压缩源码包
```bash
xz -d linux-3.2.21.tar.xz
tar xvf linux-3.2.21.tar
```
3.2. 配置内核选项
```bash
cd linux-3.2.21
make oldconfig
make menuconfig
```
在其中选择你想取消或者启动的选项，但注意别精简得太多，否则很容易出现各种错误甚至无法引导系统，比如来个 `kernel panic not syncing attempted to kill init` 之类的错误，这里提供我的一份 .config 文件

{% asset_link config-3.2.21-vmware1.zip Kernel 3.2.21 的.config 文件 %} 点击下载

下载后注意改名，另外还应该对照你自己的环境做些修改。（这个适用于搭建在 Intel CPU上面的 VMware 虚拟机）

3.3. 开始编译
```bash
make bzImage modules modules_install
make install #安装
```
编辑 GRUB引导菜单，将 default=1 改成 default=0
`vi /etc/grub.conf` 

你还可以将 hiddenmenu 前面用#注释掉，这样开始会显示引导菜单

`reboot` #重启

至此，CentOS 6.4 升级到 3.x Kernel 完成。

#### 4.补充

如果你的 CentOS 是安装在 VMware 上的话，在最后 make install 这一步的时候有可能会看到这样的警告：

<span style="color: #0000ff">ERROR: modinfo: could not find module vmware_balloon</span>

从字面上来看，是找不到 vmware_balloon 模块，这个 vmware_balloon 实际上被称之为内存释放或者气球膨胀（参见[这篇文章](http://delxu.blog.51cto.com/975660/288682/ "图解VMware内存机制")），可能的原因有2个：

首先，确认你的 .config 文件里面有  **CONFIG_VMWARE_BALLOON=m **这一行，或者你可以用 make menuconfig 进去内核编译菜单，选中 Device Drivers -&gt; MISC devices -&gt; VMware Balloon Driver 为 M 或者 *

其次，而这个模块在后面的版本中，已经更名为 vmw_balloon，所以 可以用下面的命令来解决：
```bash
cd /lib/modules/3.x.xx/kernel/drivers/misc #将版本号改成你自己的
ln -s vmw_balloon.ko vmware_balloon.ko #建立软连接
```
**Tips:**

如果你最后 make install 的时候有很多 could not find module xxxx 的错误，而你又不知道其对应的是 .config 中哪一项的时候，可以用这条命令：
`grep -R --include=Makefile '\bNAME\.o\b'`