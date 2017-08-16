---
title: 解决 WSUS 下载速度慢的问题
tags:
  - BITS
  - Microsoft
  - WSUS
categories:
  - Microsoft
date: 2014-06-10 14:40:11
---

解决 WSUS 下载速度慢的问题:

新建的 WSUS 3.0 SP2 服务器，操作系统为 2008 R2，更新补丁非常慢，总共几十个G的补丁文件，下载了三天了才2G。

出现这样的问题，大部分情况是因为 WSUS 通过后台下载补丁文件所导致，也就是我们所说的 BITS 服务，可以试着将后台下载改为前台下载。

解决方案：

1.  如果你的 WSUS 用的是WID（Windows Internal Database）默认数据库：
```cmd
%programfiles%\Update Services\Setup\ExecuteSQL.exe -S %Computername%\MICROSOFT##SSEE -d "SUSDB" -Q "update tbConfigurationC set BitsDownloadPriorityForeground=1"
```

2. 如果你使用 SQL 数据库：
```cmd
%programfiles%\Update Services\Setup\ExecuteSQL.exe -S %Computername% -d "SUSDB" -Q "update tbConfigurationC set BitsDownloadPriorityForeground=1"
```
最后重启WSUS服务生效。

注：**BITS** (后台智能传送服务 Background Intelligent Transfer Service) 是一个 Windows 组件，它可以在前台或后台异步传输文件，为保证其他网络应用程序获得响应而调整传输速度，并在重新启动计算机或重新建立网络连接之后自动恢复文件传输。