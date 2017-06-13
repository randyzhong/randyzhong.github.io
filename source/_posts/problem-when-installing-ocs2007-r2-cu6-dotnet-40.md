---
title: 小心安装 OCS 2007 R2 Cumulative Update 6 (CU6) 和 .NET 4.0
id: 55
categories:
  - OCS/Lync
  - 沟通协作
date: 2010-08-15 09:25:18
tags:
---

今天发现有新的 OCS 2007 R2 的更新，于是就跑去把几台 OCS 2007 R2 的服务器都给升级了，为了偷懒，用了那个一体化更新包ServerUpdateInstaller.exe，这个东西好是好，就是下载后没有点版本信息可以从文件名看出来，每次都一样，我只好自己在后面加个尾巴标注。更新完了重启，发现 Front-End 服务死活都起不来，检查系统日志，发现
**Event ID**: 30968**Source**: Live Communications User Services**Details**: The component Live Communications User Services reported a critical error: code C3EE78F8 (Enterprise Edition Server successfully registered with the back-end, but a stored procedure version mismatch was detected. The service will not start until this problem is resolved. Cause: The database schema and the Enterprise Edition Server were updated by different installation packages. Resolution: Ensure both the Enterprise Edition Server and back-end were installed or modified by the same installation package. The service has to stop.

最后搞了半天才发现是要先升级数据库才行，OCS2009-DBUpgrade.msi 否则会因为前端服务的版本和后端数据库版本不匹配而导致服务启动失败。唉，这种事情也不早说出来，ServerUpdateInstaller.exe 没法升级数据库，只好手动做咯
msiexec /i OCS2009-DBUpdate.msi POOLNAME=xxx
打完了数据库补丁后，启动 Front-End 服务正常。

另外还有个问题是关于.NET 4.0 Framework 的，如果你是新装的服务器，在安装OCS之前装了 .NET 4，你会发现安装程序老是会说 .NET 3.5x 没有安装，即便你已经装过，怎么办呢？只有先行卸载.NET 4.0 Framework, 安装 OCS，再安装 .NET 4.0 才行。
我一向的原则都是新东西等等再装，因为说不准更新完后会出什么问题，先等等，大家都说没问题了，再装，这样安全。