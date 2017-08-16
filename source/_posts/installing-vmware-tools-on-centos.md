---
title: 在 CentOS 中安装 VMware Tools
tags:
  - CentOS
  - VMware
  - VMware Tools
categories:
  - '*Nix'
date: 2013-04-28 12:26:46
---

不管在什么情况下，我们一般认为安装 VMware Tools 带来的好处要远远大于不安装，所以仅以此文记录下安装步骤。

在 VMware 中挂上 VMware Tools 的 ISO： VM -> Guest -> Install/Upgrade VMware Tools
```bash
mkdir /media/cdrom  #创建挂载点目录
mount /dev/cdrom /media/cdrom #挂载光盘
cp /media/cdrom/VMwareTools*.tar.gz /tmp #复制文件到 /tmp
cd /tmp
tar –xzvf VMwareTools*.tar.gz &amp;nbsp;# 解压缩
cd vm* #注意这里的 vm 是小写
./vmware-install.pl
```
基本上一步步按回车接受默认设置就好了，最后再运行一下命令启用 VMware Tools
```bash
/etc/init.d/networking stop
rmmod pcnet32
rmmod vmxnet
depmod -a
modprobe vmxnet
/etc/init.d/networking start
```