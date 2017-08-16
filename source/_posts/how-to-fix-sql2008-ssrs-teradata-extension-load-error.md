---
title: 解决 SQL 2008 Reporting Service 的 TeraData 扩展错误
tags:
  - MSSQL
  - SSRS
  - TeraData
categories:
  - Microsoft
  - MSSQL
date: 2010-07-02 16:29:24
---

新装的一台 SQL Server 2008 SP1， 上面有跑数据库引擎和报表服务，新装后不久在事件管理器中频繁出现以下错误：
> Log Name:      Application
> Source:        Report Server (MSSQLSERVER)
> Date:          7/2/2010 12:48:21 AM
> Event ID:      108
> Task Category: Extension
> Level:         Error
> Keywords:      Classic
> User:          N/A
> Computer:      M2AHKCRMWEB01.mail2any.local
> Description:
> Report Server (MSSQLSERVER) cannot load the TERADATA extension.

发生这个的原因是在2008的报表服务中,默认开启了 TeraData 的扩展，而2008的安装文件是不包含这个扩展的文件的，所以就会找不到扩展所需要的文件。

##### 解决方案：
大多数情况下应该是没有用到 TeraData 的，所以可以打开 Reporting Service  所在的目录下的 RSReportServer.config 文件，注释掉 TeraData 相关部分即可。
比如 C:\Program Files\Microsoft SQL Server\MSRS10.MSSQLSERVER\Reporting Services\ReportServer\RSReportServer.config 文件
```xml
<!-- <Extension Name="TERADATA" Type="Microsoft.ReportingServices.DataExtensions.TeradataConnectionWrapper,Microsoft.ReportingServices.DataExtensions" /> -->
<!-- <Extension Name="TERADATA" Type="Microsoft.ReportingServices.SemanticQueryEngine.Sql.Teradata.TdSqlSQCommand,Microsoft.ReportingServices.SemanticQueryEngine">
<Configuration>
<EnableMathOpCasting>True</EnableMathOpCasting>
<ReplaceFunctionName>oREPLACE</ReplaceFunctionName>
</Configuration>
</Extension> -->
```

第二：如果有用到 TeraData，那就去其网站下载 TeraData .Net Data Provider，注意要安装版本12以上的，比如 http://www.teradata.com/DownloadCenter/Topic9541-152-1.aspx