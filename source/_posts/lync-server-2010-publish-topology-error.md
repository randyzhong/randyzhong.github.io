---
title: Lync Server 2010 发布架构报错
tags:
  - Lync
  - OCS
  - SQL
id: 41
categories:
  - OCS/Lync
  - 沟通协作
  - 网站相关
date: 2010-12-24 10:36:47
---

昨天想试试在现有的 OCS 2007 R2 的基础上部署 Lync Server 2010 试试看，装好 Topology Builder 之后，新建了一个拓扑，然后把该填的都填了，发布的时候老是报SQL错，很是郁闷，后来终于在log中找到了错误原因:
<pre class="toolbar:2 show-lang:2 nums:false nums-toggle:false wrap:true wrap-toggle:false show-plain:3 copy:false popup:false lang:default decode:true crayon-selected">Script result: ERROR_VALIDATE_BAD_SQL_VERSION

Error: Script failed (code "ERROR_VALIDATE_BAD_SQL_VERSION") when installing "CentralMgmtStore" on "LyncServer01.mail2any.local". For details, see the following log file: "C:\Users\Administrator.Mail2any\AppData\Local\Temp\Create-CentralMgmtStore-LyncServer01.mail2any.local-[2010_12_22][16_11_55].log"

Error: An error occurred: "Microsoft.Rtc.Common.Data.SqlConnectionException" "Cannot open database "xds" requested by the login. The login failed.
Login failed for user 'Mail2Any\Administrator'."</pre>
&nbsp;

&nbsp;

看到这个 Bad Version 版本错误提示，我才想起来原来部署 OCS 2007 R2 的时候，后端数据库用的是 SQL Server 2008，而 Lync Server 2010需要至少 SQL Server 2008 SP1 才行，于是乎打上 SP1 补丁，顺利通过发布。SQL Server的版本需求:

*   Standard or Enterprise Edition of SQL Server 2005 with Service Pack 3
*   Standard or Enterprise Edition of SQL Server 2008 with Service Pack 1
*   Standard or Enterprise Edition of SQL Server 2008 R2