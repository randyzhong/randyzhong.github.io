---
title: The ReportServerVirtualDirectory element is missing
tags:
  - Reporting Service
  - MSSQL
categories:
  - Microsoft
  - SQL数据库
date: 2009-05-29 15:58:37
---

Install SQL Server 2005 on a Windows Server 2008, when use your browser to access http://localhost/reports, you received below error msg:
> "_An error occurred while parsing the configuration file. The ReportServerVirtualDirectory element is missing._ "

After doing some search&amp;research, this is the workaround.

Lookin the RSWebApplication.config for the
```xml
<ReportServerVirtualDirectory>
```
It will look something like this
```xml
<ReportServerVirtualDirectory>
</ReportServerVirtualDirectory>
```
change to
```xml
<ReportServerVirtualDirectory>ReportServer</ReportServerVirtualDirectory>
```