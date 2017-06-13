---
title: >-
  Solved: The Security Database on the Server Does Not Have a Computer Account
  for This Workstation Trust Relationship
tags:
  - Domain
  - FQDN
  - servicePrincipalName
  - SPN
  - WINS
id: 194
categories:
  - 操作系统
date: 2013-07-09 12:54:20
---

用户计算机录域的时候出现如下错误：

Error: The security database on the server does not have a computer account for this workstation trust relationship

[![Domain relationship error](http://winotes.net/wp-content/uploads/Domain-relationship-error.jpg)](http://winotes.net/wp-content/uploads/Domain-relationship-error.jpg)

退出域重新加域，问题依旧。

最终找到了原因，在用户计算机的属性SPN里面，只有 WINS 的主机名记录，没有 FQDN 的

servicePrincipalName:
HOST/SRV1
<span style="color: #ff0000">HOST/srv1.mydomainname.com</span>
RestrictedKrbHost/SRV1
<span style="color: #ff0000">RestrictedKrbHost/srv1.mydomainname.com</span>
TERMSRV/SRV1
<span style="color: #ff0000">TERMSRV/srv1.mydomainname.com</span>

缺少红字部分，用 SetSPN 或者 ADSIEdit 来添加相对应的 FQDN 即可

&nbsp;