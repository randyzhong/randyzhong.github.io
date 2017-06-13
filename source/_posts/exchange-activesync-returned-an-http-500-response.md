---
title: Exchange Activesync returned an HTTP 500 response
tags:
  - Activesync
  - Android
  - Exchange
  - Google
  - Nexus
id: 198
categories:
  - Exchange
  - 沟通协作
date: 2013-07-19 13:40:52
---

Exchange Server 2010 SP1, 手机是 Google Nexus (Samsung i9250)，系统是 Android 4.0.4，用户已经启用 ActiveSync, 结果用手机无法创建 Exchange 邮件账户，提示无法连接到服务器。

去到 [TestExchangeConnectivity](https://www.testexchangeconnectivity.com/ "Microsoft Remote Connectivity Analyzer") 网站测试，发现有报错：

Attempting the FolderSync command on the Exchange ActiveSync session.
The test of the FolderSync command failed.

Additional Details
Exchange Activesync returned an HTTP 500 response. (Internal Server Error)

[![500 error](http://winotes.net/wp-content/uploads/FolderSync-Failed.jpg)](http://winotes.net/wp-content/uploads/FolderSync-Failed.jpg)

换个用户测试，发现可以通过，确定问题在用户设置上面。继而查看 Exchange 服务器的日志，发现有 ID:1053 的错误纪录：

[![EventID-1053](http://winotes.net/wp-content/uploads/EventID-1053.jpg)](http://winotes.net/wp-content/uploads/EventID-1053.jpg)

<!--more-->

Exchange ActiveSync doesn't have sufficient permissions to create the "CN=Randy Zhong,OU=IT,DC=company,DC=com" container under Active Directory user "Active Directory operation failed on DC01.company.com. This error is not retriable. Additional information: <span style="color: #ff0000">Access is denied.</span>

Active directory response: 00000005: SecErr: DSID-031521D0, problem 4003 (INSUFF_ACCESS_RIGHTS), data 0".

<span style="color: #ff0000">Make sure the user has inherited permission granted to domain\Exchange Servers</span> to allow List, Create child, Delete child of object type "msExchActiveSyncDevices" and doesn't have any deny permissions that block such operations.

Details:%3

&nbsp;

从上面可以看到，出错原因是 Exchange Servers 没有足够的权限，导致 Access is denied.

解决方法：

1.  在DC上打开Active Directory Users and Computers
2.  在菜单上点击 View, 勾选 Advanced Features
3.  找到该问题用户，鼠标右键点击选中用户，在弹出菜单中选择 properties ，然后去到 Security 标签卡
4.  点击 Advanced，选中 Include inheritable permissions from this object's parent.
[![AdvancedSecurity-Setting](http://winotes.net/wp-content/uploads/AdvancedSecurity-Setting.jpg)](http://winotes.net/wp-content/uploads/AdvancedSecurity-Setting.jpg)

等待 AD 复制完成后，再尝试配置手机，问题解决。

<span style="color: #ff0000">补充：
</span><span style="color: #ff0000">如果碰到某些用户以上操作都不起作用的话，可以尝试在 EMC 中先禁掉用户的 ActiveSync，然后尝试配置手机，当然会提示失败，之后再开启 ActiveSync, 再试一下，多半就可以了。</span>