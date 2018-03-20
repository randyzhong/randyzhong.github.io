---
title: 升级到 Exchange 2010 SP1 后出现的2个小问题
tags:
  - Exchange
  - PowerShell
categories:
  - Microsoft
  - Exchange
date: 2010-09-12 11:46:31
---

今天有空把所有的 Exchange 服务器从 2010 RTM 升级到了 2010 SP1 了， 升级完了后发现了2个小问题：

1.  OWA打开正常，但是点到 ECP 的时候报 403 访问拒绝错误
2.  某台 Exchange 打开 EMS 时报错如下：

```
[server.mydomain.local] Connecting to remote server failed with the following error message :
The client cannot connect to the destination specified in the request. Verify that the
service on the destination is running and is accepting requests. Consult the logs and
documentation for the WS-Management service running on the destination, most commonly IIS
or WinRM. If the destination is the WinRM service, run the following command on the
destination to analyze and configure the WinRM service: "winrm quickconfig". For more
information, see the about_Remote_Troubleshooting Help topic.
+ CategoryInfo : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [], PSRemotingTransportException
+ FullyQualifiedErrorId : PSSessionOpenFailed
```
1. 这个错误在经过仔细排查后发现， 取消在 IIS 虚拟目录里面ecp的 "Require SSL" 就可以了，原来是之前为了做 SSL Offloading，把这个SSL的要求取消了，但是 Exchange 2010 SP1的安装程序在升级的时候又给加上去了，所以就报错了。

2. 这个错误在网上查了好久，都没发现有用的答案，包括重装 WinRM IIS 扩展，重新配置 PowerShell 虚拟目录，运行 WinRm QuickConfig 等等，都没有用，最后发现问题还是出在IIS上面，Default Web Site 一开始给禁用了，而且默认的端口也从80改到了8080，EMS在打开的时候需要访问 Default Web Site 下面 PowerShell 的虚拟目录，找不到，报错，改回去80，错误解决。
