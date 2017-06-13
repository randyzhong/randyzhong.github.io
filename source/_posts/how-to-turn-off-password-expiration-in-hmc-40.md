---
title: 如何设置使 HMC 4.0 创建的用户的密码永不过期
tags:
  - HMC
  - Provisioning
id: 163
categories:
  - HMC
  - Hosting
date: 2007-11-10 12:01:02
---

HMC 4.0 创建的用户，默认的帐户属性里面没有设置密码永不过期，这样就会产生个问题，在用户密码过期后，用户无法通过 OWA 再去修改或者重置密码，将会导致用户无法访问邮箱，为了解决这一问题，我们需要修改 MPS 默认的创建用户的脚本，通过设置 "userAccountControl" 的属性来达到默认就是密码永不过期的目的。

1.  以管理员身份登陆 MPS (MPSSQL)，打开 **Provisioning Manager**
2.  在名称空间 "Hosted Active Directory" 中打开 "CreateUser" 过程的属性，找到更改用户的调用脚本 - Managed Active Directory::ModifyUser: <span style="color: #0000ff">&lt;</span><span style="color: #990000">execute</span><span style="color: #ff0000"> namespace</span><span style="color: #0000ff">="Managed Active Directory"</span><span style="color: #ff0000"> procedure</span><span style="color: #0000ff">="ModifyUser"</span><span style="color: #ff0000"> impersonate</span><span style="color: #0000ff">="1"&gt;</span>
注释或者去掉以下的2句判断语句
<span style="color: #0000ff">&lt;when select="isAdmin" case="1" /&gt;
&lt;when select="isBiz" case="0" /&gt; </span>
3.  改完之后应该类似这样
<div>
<pre class="wrap:true lang:scheme decode:true crayon-selected">&lt;execute namespace="Managed Active Directory" procedure="ModifyUser" impersonate="1"&gt;
&lt;executeData&gt;
&lt;user/&gt;
&lt;properties&gt;
&lt;!-- sets the password to never expire --&gt;
&lt;property name="userAccountControl"&gt;
&lt;value&gt;65536&lt;/value&gt;
&lt;/property&gt;
&lt;/properties&gt;
&lt;/executeData&gt;
&lt;before source="procedureData" sourcePath="path" destination="executeData" destinationPath="user" mode="merge"/&gt;
&lt;before source="data" sourcePath="preferredDomainController" destination="executeData" mode="insert"/&gt;
&lt;/execute&gt;</pre>
&nbsp;

    </div>
4.  点击 "Apply" 保存修改。
<span style="color: blue">解释：userAccountControl 属性中，值设置成 65536 表示设置密码永不过期
</span>
<pre class="lang:default decode:true">&lt;when select="isAdmin" case="1" /&gt;
&lt;when select="isBiz" case="0" /&gt;</pre>
<span style="color: blue">这2句很好理解，就是如果创建的用户是管理员的，密码就永不过期，如果是一般用户，就是默认设置，有密码有效期的</span>

参考的文章：
Microsoft KB:[http://support.microsoft.com/kb/305144](http://support.microsoft.com/kb/305144)
MSDN:User-Account-Control Attribute - [http://msdn2.microsoft.com/en-us/library/ms680832.aspx](http://msdn2.microsoft.com/en-us/library/ms680832.aspx)