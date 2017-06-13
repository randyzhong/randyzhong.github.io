---
title: 'Exception from HRESULT: 0x800A03EC'
tags:
  - DCOM
  - Excel
  - System
id: 43
categories:
  - 操作系统
date: 2009-08-20 14:09:45
---

我们新的生产环境, Windows 2008 64-bit, 自己写的一个 Windows Service, 用到了 Excel 的 DCOM 组件, 服务的执行账户是 **SYSTEM** 但是在执行中报出了一下错误:
> Exception from HRESULT: **0x800A03EC
> **   at Microsoft.Office.Interop.Excel.Workbooks.Open(String Filename, Object UpdateLinks, Object ReadOnly, Object Format, Object Password, Object WriteResPassword, Object IgnoreReadOnlyRecommended, Object Origin, Object Delimiter, Object Editable, Object Notify, Object Converter, Object AddToMru, Object Local, Object CorruptLoad)
<!--more-->

Google 了大半天, 总算是找到了问题所在:

[http://social.msdn.microsoft.com/Forums/en-US/innovateonoffice/thread/b81a3c4e-62db-488b-af06-44421818ef91?prof=required](http://social.msdn.microsoft.com/Forums/en-US/innovateonoffice/thread/b81a3c4e-62db-488b-af06-44421818ef91?prof=required)

在 Windows 2008 中, 如果以 SYSTEM 用户跑, 系统会去寻找 SYSTEM 这个用户的 Profile 目录, 而这个用户是不能按照交互用户登录的,而且在处理 Excel 文件的过程中, 一个 for SYSTEM 账户 "Desktop" 文件夹似乎是必须的

・Windows 2008 Server x64

在 "C:\Windows\**SysWOW64**\config\systemprofile" 下创建 "Desktop" 目录

C:\Windows\SysWOW64\config\systemprofile\Desktop

・Windows 2008 Server x86

在 "C:\Windows\config\systemprofile" 下创建 "Desktop" 目录

C:\Windows\System32\config\systemprofile\Desktop

注意 64-bit 的要在 **SysWOW64** 下面