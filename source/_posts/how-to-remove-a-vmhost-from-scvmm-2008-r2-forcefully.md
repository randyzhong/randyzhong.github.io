---
title: How to remove a VMHost from SCVMM 2008 R2 forcefully
tags:
  - SCVMM
  - VMHost
id: 107
categories:
  - 虚拟化
date: 2010-04-13 22:17:03
---

这几天碰到个问题，有个服务器原来安装的是 2008 Server Core系统,上面跑 Hyper-V，已经将其导入 SCVMM 2008 R2 来管理。最近实在是嫌 Server Core 管理太不方便，于是想把系统换成带图形界面的，也没在 SCVMM 里面把主机删除，直接就重装了，IP和计算机名什么的都没变，结果问题来了，再打开 SCVMM 的控制台，发现这台主机联系不上(当然，因为重装的没有了 Agent)，就想删掉再重加，结果删除过程报错：
> Error (801)
> 
> VMM cannot find ISO object .
> 
> Recommended Action
> 
> Ensure the library object is valid, and then try the operation again.
移除任务的进度一直停留在99% 而主机就是保持在 Pending 状态，结果就是旧的删不掉，新的加不了。后来试过用 PowerShell 来删:
Get-VMHost -Computername Hostname | Remove-VMHost -Force
一样的提示。
一番研究和搜索之后，终于发现直接操作 SCVMM 的数据库可以搞定此问题。
<!--more-->
<div id="more">步骤：
1\. 停止 VMMService 服务；
2\. 用 SQL Management Studio 连接到 SCVMM 的数据库 (默认应该是 SQL Express 的实例:**MICROSOFT$VMM$**，数据库名称是 **VirtualManagerDB**)，保险起见，先备份；
3\. 查询 tbl_ADHC_Host， 可以看到原来这台旧的 Hyper-V 主机的记录，记下其对应的 HostID, 比如 '68899616-fdc8-4f85-a702-979a4251f766'；
4\. 打开查询窗口，执行以下 SQL 语句:</div>
<div>
<pre class="lang:tsql decode:true ">DELETE FROM tbl_ADHC_HostNetworkAdapter WHERE (HostID='68899616-fdc8-4f85-a702-979a4251f766')
DELETE FROM tbl_ADHC_VirtualNetwork WHERE (HostID = '68899616-fdc8-4f85-a702-979a4251f766')
DELETE FROM tbl_ADHC_HostVolume WHERE (HostID = '68899616-fdc8-4f85-a702-979a4251f766')
DELETE FROM tbl_ADHC_HostDisk WHERE (HostID = '68899616-fdc8-4f85-a702-979a4251f766')
DELETE FROM tbl_WLC_PhysicalObject WHERE (HostId = '68899616-fdc8-4f85-a702-979a4251f766')
DELETE FROM tbl_WLC_VObject WHERE (HostId = '68899616-fdc8-4f85-a702-979a4251f766')
DELETE FROM tbl_ADHC_Host WHERE (HostID = '68899616-fdc8-4f85-a702-979a4251f766')</pre>
5\. 再查看表 tbl_ADHC_AgentServerRelation 和 tbl_ADHC_AgentServer，如果有对应的主机记录也相应删除

</div>
<div>6\. 如果是 Hyper-V 群集的话，每个节点都要跑一次上面的删除语句，还得删除 Cluster 的记录
<pre class="lang:tsql decode:true ">DELETE FROM tbl_ADHC_ClusterDisk WHERE (ClusterID = '1149953c-cde3-45d2-82ee-b79a259d6a79')
DELETE FROM tbl_ADHC_HostCluster WHERE (ClusterID = '1149953c-cde3-45d2-82ee-b79a259d6a79')</pre>
&nbsp;

PS. 用下面这条命令可以重置 Failed Job 状态
<pre class="toolbar:2 show-plain:3 lang:tsql decode:true ">UPDATE tbl_TR_TaskTrail SET IsVisible = 0 WHERE TaskState = 'Failed'</pre>
&nbsp;

</div>