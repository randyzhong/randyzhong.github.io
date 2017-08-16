---
title: 'Solved: The Security Database on the Server Does Not Have a Computer Account for This Workstation Trust Relationship - Part2'
tags:
  - Active Directory
categories:
  - Microsoft
  - 活动目录
date: 2013-10-21 10:12:38
---

在 Part1 里面，我们用 ADSIEdit.msc 解决了用户登录域的问题，在 Part2，我们来看看可能的另外一个原因：

用户计算机录域的时候出现如下错误：

Error: The security database on the server does not have a computer account for this workstation trust relationship

![Domain relationship error](http://winotes.net/wp-content/uploads/Domain-relationship-error.jpg)](http://winotes.net/wp-content/uploads/Domain-relationship-error.jpg)

退出域重新加域，问题解决，可是第二天早上又在出现。

检查 AD 的日志，发现有  Event 5722 NETLOGON 错误：

**Event 5722**: The session setup from the computer ComputerName failed to authenticate. The name(s) of the account(s) referenced in the security database is ComputerName$. The following error occurred:
<span style="color: #ff0000">Access is denied.</span>

在用户端的电脑上，也有Event 3210 NETLOGON 报错:

**Event 3210:** This computer could not authenticate with \\DC01.contoso.com, a Windows domain controller for domain Contoso, and therefore this computer might deny logon requests. This inability to authenticate might be caused by <span style="color: #ff0000">**another computer on the same network using the same name** or the password for this computer account is not recognized</span>. If this message appears again, contact your system administrator.

注意红色加粗标注部分，实际上，计算机账户的密码，也就是所说的 Secure Channel 安全通道，这个密码是由客户端发起的，一般不存在无法识别的问题，所以很有可能是在网络中有同名的计算机存在。

后来经过排查，发现有两台计算机重名，两台在碰到无法登陆域的情况下都有做退域重新加域的动作，哪一台后加域，另外一台就会出现无法登陆域的情况，因为在加域的时候，会重置计算机账户密码，所以对于原来已经加域的计算机来讲，密码就不一致了，导致安全通道被破坏，无法认证，最终不能登录域。

解决方法：将重名的计算机退域，改名，再重新加域，保险起见，用 netdom 重置下计算机账户密码 (重建 Secure Channel)
