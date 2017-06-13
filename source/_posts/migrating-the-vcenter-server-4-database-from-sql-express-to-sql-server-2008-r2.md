---
title: VMWare vCenter 4.x 数据库由SQL Express 迁移至SQL Server 2008 R2
tags:
  - Database
  - Migration
  - SQL
  - vCenter
  - VMware
id: 212
categories:
  - VMware
  - 虚拟化
date: 2013-11-28 16:38:52
---

默认安装下的 VMware vCenter 4.x 会在本地安装 SQL Express 2005 版本，如果你有需要迁移至 Full SQL Server版本比如 SQL Server 2008 R2的话，可以参考以下步骤：

1.  停止 VirtualCenter Server service服务，4.x 的服务名是 VMware VirtualCenter Server。
2.  对现有的 SQL Express 数据库实例上的 vCenter 数据库做一次完全备份。
3.  停止现有 SQL Express 的 SQL 服务。
4.  在新的 SQL Server 2008 R2 服务器上面创建新数据库，并将之前备份的文件还原至改新建数据库。为了确保还原正确，最好还原的时候去到 Options ，勾选 Overwrite existing database with replace 选项以保证完全覆盖还原。
5.  在 SQL Server 2008 R2 上面创建 vCenter 的 SQL 用户，给其分配 DB Owner 和 dbo 角色。
6.  修改 vCenter 服务器上面的 System DSN，默认名称是 VMware VirtualCenter，将其连接改至新服务器。
7.  修改 vCenter 的注册表,

    1.  **HKEY_LOCAL_MACHINE **&gt; **SOFTWARE **&gt; **VMware Inc, VMware VirtualCenter **&gt; **DB**
    2.  确认键值 1 是 System DSN 中指定的名字，比如 VMware VirtualCenter
    3.  确认键值 2 用来连接新数据库的SQL用户名，比如 vCenter_User

8.  更改注册表中所存储的SQL用户密码，也就是在上一步中的键值 3
C:\Program Files\VMware\Infrastructure\VirtualCenter Server\vpxd.exe -p
9.  重建 rollup jobs。参考 http://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&amp;docType=kc&amp;docTypeID=DT_KB_1_1&amp;externalId=1029824
10.  重启 vCenter 服务全文参考 [VMWare KB - Migrating the vCenter Server database from SQL Express to full SQL Server (1028601)](http://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&amp;docType=kc&amp;externalId=1028601&amp;sliceId=1&amp;docTypeID=DT_KB_1_1&amp;dialogID=100018749&amp;stateId=0%200%20111449579)