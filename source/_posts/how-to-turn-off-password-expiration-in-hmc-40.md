---
title: 如何设置使 HMC 4.0 创建的用户的密码永不过期
tags:
  - HMC
categories:
  - Microsoft
  - HMC
date: 2007-11-10 12:01:02
---

HMC 4.0 创建的用户，默认的帐户属性里面没有设置密码永不过期，这样就会产生个问题，在用户密码过期后，用户无法通过 OWA 再去修改或者重置密码，将会导致用户无法访问邮箱，为了解决这一问题，我们需要修改 MPS 默认的创建用户的脚本，通过设置 `userAccountControl` 的属性来达到默认就是密码永不过期的目的。

1.  以管理员身份登陆 MPS (MPSSQL)，打开 **Provisioning Manager**
2.  在名称空间 "Hosted Active Directory" 中打开  **CreateUser** 过程的属性，找到更改用户的调用脚本 - Managed Active Directory::ModifyUser: 
```xml
<execute namespace="Managed Active Directory" procedure="ModifyUser" impersonate="1">
```
注释或者去掉以下的2句判断语句
```xml
<when select="isAdmin" case="1" />
<when select="isBiz" case="0" />
```
3.  改完之后应该类似这样
```xml
<execute namespace="Managed Active Directory" procedure="ModifyUser" impersonate="1">
<executeData>
<user/>
<properties>
<!-- sets the password to never expire -->
<property name="userAccountControl">
<value>65536</value>
</property>
</properties>
</executeData>
<before source="procedureData" sourcePath="path" destination="executeData" destinationPath="user" mode="merge"/>
<before source="data" sourcePath="preferredDomainController" destination="executeData" mode="insert"/>
</execute>
```
4.  点击 "Apply" 保存修改。
> 解释：`userAccountControl` 属性中，值设置成 65536 表示设置密码永不过期

```xml
<when select="isAdmin" case="1" />
<when select="isBiz" case="0" />
```
这2句很好理解，就是如果创建的用户是管理员的，密码就永不过期，如果是一般用户，就是默认设置，有密码有效期的。
***
参考的文章：
* Microsoft KB:[http://support.microsoft.com/kb/305144](http://support.microsoft.com/kb/305144)
* MSDN:User-Account-Control Attribute - [http://msdn2.microsoft.com/en-us/library/ms680832.aspx](http://msdn2.microsoft.com/en-us/library/ms680832.aspx)