---
title: 解决 VMWare ESXi 4.0 在 VC 中主机硬件状态无法监控的问题
tags:
  - Virtual Center
  - VMware
id: 39
categories:
  - VMware
  - 虚拟化
date: 2010-06-15 15:15:52
---

最近 VMWare 释出了 VSphere 4.0 Update2 的更新,忍不住就把目前在跑的几台 Dell PE1950 给升级到了 Update2,别的都没啥问题,就是发现在 VC Client 中无法监测到主机的硬件状态了, 一点 Hardware Status,就出现
<div>Error Message: "Hardware monitoring service not responding, the host is not powered on"</div>
<div></div>
<div>重启服务,重启主机都试过了,问题依旧,后来在Google大神的帮助下,终于找到了解决方法:</div>
<div>在 VC 中删掉原来的主机再重新添加,问题就解决了.</div>