---
title: The specified CGI application encountered an error and the server terminated the process
tags:
  - Chrome
  - Cookie
  - Microsoft
  - MSDN
categories:
  - Microsoft
date: 2013-03-10 12:47:37
---

最近老是发现用 Google Chrome 访问微软的 MSDN 或者 Technet 站点时，网页打不开，会显示以下错误

**The specified CGI application encountered an error and the server terminated the process**

而用IE访问则正常，一开始以为是 Google Chrome 的 版本问题，结果升级了 Chrome 的版本也没有用。

而今天在访问 Google Analytics 时，提示 Cookie Mismatch，清空相关的 Cookie 后，Google Analytics恢复正常，于是突然想到会不会也是由于 Cookie 的问题影响了微软相关站点的访问，清除 microsoft.com 的所有相关 Cookies 后，technet 和 MSDN 站点顺利在 Chrome 中打开！

以下是 Chrome 的 Cookie 清除示例
![Remove Chrome Cookies](the-specified-cgi-application-encountered-an-error-and-the-server-terminated-the-process/Remove-Chrome-Cookies.jpg)

在地址栏中输入 **chrome://settings/content** 回车，在 Cookies 下面选择 All cookies and site data... ，在搜索框中输入`microsoft.com` 列出所有 microsoft.com 相关的 cookies，点击 **Remove all** 清除， done 完成！