---
title: The ReportServerVirtualDirectory element is missing
tags:
  - Reporting Service
  - SQL
id: 82
categories:
  - SQL数据库
date: 2009-05-29 15:58:37
---

Install SQL Server 2005 on a Windows Server 2008, when use your browser to access http://localhost/reports, you received below error msg:
"_An error occurred while parsing the configuration file. The ReportServerVirtualDirectory element is missing._ "
After doing some search&amp;research, this is the workaround.

<span style="font-family: Arial;font-size: small">Lookin the RSWebApplication.config for the "&lt;</span>ReportServerVirtualDirectory<span style="font-family: Arial;font-size: small">&gt;"</span>

It will look something like this
<pre class="toolbar:2 show-lang:2 striped:false marking:false ranges:false nums:false nums-toggle:false wrap-toggle:false plain:false show-plain:3 plain-toggle:false copy:false popup:false lang:xhtml decode:true">&lt;ReportServerVirtualDirectory&gt;&lt;/ReportServerVirtualDirectory&gt;</pre>
&nbsp;

change to

<pre class="toolbar:2 show-lang:2 striped:false marking:false ranges:false nums:false nums-toggle:false wrap-toggle:false plain:false show-plain:3 plain-toggle:false copy:false popup:false lang:xhtml decode:true ">&lt;ReportServerVirtualDirectory&gt;ReportServer&lt;/ReportServerVirtualDirectory&gt;</pre>
&nbsp;