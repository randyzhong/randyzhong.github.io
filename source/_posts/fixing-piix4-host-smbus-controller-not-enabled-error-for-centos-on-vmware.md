---
title: 修复安装于VMware上的CentOS启动时出现 piix4_smbus Host SMBus controller not enabled 的错误
tags:
  - CentOS
  - VMware
categories:
  - '*Nix'
date: 2013-05-01 23:45:49
---

如果你的 CentOS 6.4 是在 VMware 的虚拟机上安装的，那么在启动的时候，你可能会看到这样一个错误：

![piix4_smbus 0000:00:007.3: Host SMBus controller not enabled](fixing-piix4-host-smbus-controller-not-enabled-error-for-centos-on-vmware/piix4_smbus_not_enabled.jpg) 

**piix4_smbus 0000:00:007.3: Host SMBus controller not enabled**
> "The PIIX4 (properly known as the 82371AB) is an Intel chip with a lot of functionality. Among other things, it implements the PCI bus. One of its minor functions is implementing a System Management Bus. This is a true SMBus - you can not access it on I2C levels. The good news is that it natively understands SMBus commands and you do not have to worry about timing problems. The bad news is that non-SMBus devices connected to it can confuse it..."

从内核的说明文档来看，这个 piix4 实际上是 Intel 82371AB 南桥芯片，多功能总线控制器，而在 VMware 里面并没有这个真实的芯片组，但在启动时最会尝试载入这个驱动模块，所以会报错，虽然对系统没有任何影响，但如果你看着不爽想移除掉的话，也是可以的：
```bash
vi /etc/modprobe.d/blacklist.conf
#加入以下两行
#intel piix4 driver
blacklist i2c_piix4
```