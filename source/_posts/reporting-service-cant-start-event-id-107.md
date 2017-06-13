---
title: Reporting Service can't start - Event ID 107
tags:
  - Reporting Service
  - SQL
id: 67
categories:
  - SQL数据库
date: 2009-12-11 20:55:31
---

Today, one of my clients reported that, the reporting service cannot started.
They didn't change anything and database service is running fine.

Event Type: Error
Event Source: Report Server Windows Service (MSSQLSERVER)
Event Category: Management
Event ID: 107
Date: 12/10/2009
Time: 11:53:21 PM
User: N/A
Computer: ABC
Description:
Report Server Windows Service (MSSQLSERVER) cannot connect to the report server database.
<!--more-->
<div>Event Type: Error
Event Source: Service Control Manager
Event Category: None
Event ID: 7000
Date: 7/27/2008
Time: 7:26:24 PM
User: N/A
Computer: PRODB
Description:
The SQL Server Reporting Services (MSSQLSERVER) service failed to start due to the following error:
The service did not respond to the start or control request in a timely fashion.For more information, see Help and Support Center at http://go.microsoft.com/fwlink/events.asp.

</div>
The Reporting Services logs mostly just repeat that "The Report Server Windows service 'ReportServer' is not running." ...
<div>

This issue was caused by a windows hotfix - **KB948109**, it changed Windows Internal Database (WYukon) security settings that could make the service start timeout.

There are 2 ways can make it resolved.

**Solution A**: Use Add/Remove to uninstall this hotfix **KB948109**.

**Solution B**: Change the registry to increase the timeout value.

We suggest **Solution B.**

&nbsp;

To increase the timeout value in the registry, follow these steps:

1.  Start Registry Editor (Regedit.exe).
2.  To change the value data for the **ServicesPipeTimeout DWORD** value to 60000 in the**Control** key, follow these steps:

    1.  Locate and then click the following registry key:
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet

1.  Click the **Control** subkey
2.  Right-click the **ServicesPipeTimeout DWORD** value, and then click **Modify**.
3.  Click **Decimal**.
4.  Type 60000, and then click **OK**.

*   If the **ServicesPipeTimeout** value is _not_ available, add the new **DWORD** value, and then set its value data to 60000 in the **Control** key. To do so, follow these steps:

    1.  Locate and then click the following registry key:
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet

1.  Click the **Control** subkey.
2.  On the **Edit** menu, point to **New**, and then click **DWORD Value**.
3.  Type ServicesPipeTimeout, and then press ENTER.
4.  Right-click the **ServicesPipeTimeout DWORD** value, and then click **Modify**.
5.  Click **Decimal**.
6.  Type a value of 60000, and then click **OK**.The value is 60000 milliseconds and is equivalent to 60 seconds or to one minute.
**Note **This change does not take effect until the computer is restarted

</div>