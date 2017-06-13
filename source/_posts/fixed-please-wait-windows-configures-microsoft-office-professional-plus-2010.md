---
title: >-
  Fixed: Please wait while Windows configures Microsoft Office Professional Plus
  2010
tags:
  - Microsoft
  - MSI
  - Office
  - Windows
id: 238
categories:
  - Office
date: 2014-04-28 12:02:35
---

上周五通过 .msi 安装包部署了一个软件，今天发现每次启动 Office 2010 的组件—不管是 Word, Excel, PowerPoint 还是 Outlook，都会出现这个提示：

[![Windows configures microsoft office ](http://winotes.net/wp-content/uploads/Windows-configures-microsoft-office.jpg)](http://winotes.net/wp-content/uploads/Windows-configures-microsoft-office.jpg)

Please wait while windows configures Microsoft Office Professional Plus 2010.

而且是每次都会有，不厌其烦。微软官方也给出了解决方案：

请参考KB2528748: [http://support.microsoft.com/kb/2528748](http://support.microsoft.com/kb/2528748 "Microsoft Office 2010 Professional Plus configures each time you start")

运气好的话，说不定问题就能解决，但如果你和我这个倒霉蛋一样，这个方案根本没用的时候，不妨再检查你的日志：

<!--more-->

[![EventID-1004](http://winotes.net/wp-content/uploads/EventID-1004.jpg)](http://winotes.net/wp-content/uploads/EventID-1004.jpg)

The resource 'HKEY_CLASSES_ROOT\.aw\' does not exist. 其实 .aw 这个键值不是不存在，而是由于某种原因，权限丢失了，只要将其所有者改为 SYSTEM 账号，然后赋予如下权限即可。

[![Permissions-aw](http://winotes.net/wp-content/uploads/Permissions-aw.jpg)](http://winotes.net/wp-content/uploads/Permissions-aw.jpg)

By the way, 这个方法适用于所有 Office 系列。