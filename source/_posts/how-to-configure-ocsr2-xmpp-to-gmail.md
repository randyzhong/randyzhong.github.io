---
title: 配置OCS XMPP网关以支持OC和Gmail联系人的通讯
id: 108
categories:
  - OCS/Lync
  - 沟通协作
date: 2010-06-23 16:15:45
tags:
---

是的，您没有看错，我也没有写错！现在通过安装配置 XMPP 网关，您可以在 Office Communicator 中添加 Gmail （Google Talk）用户作为联系人，查看对方的忙/闲状态，并即时聊天。

微软的OCS团队早在去年就在其团队博客上面发布了配置指南，请移步[http://communicationsserverteam.com/archive/2009/10/01/599.aspx](http://communicationsserverteam.com/archive/2009/10/01/599.aspx) 查看。

另外本文也参考了MVP- [Jeff Schertz](http://blogs.pointbridge.com/Blogs/schertz_jeff) 的博文

[http://blogs.pointbridge.com/Blogs/schertz_jeff/Pages/Post.aspx?_ID=91](http://blogs.pointbridge.com/Blogs/schertz_jeff/Pages/Post.aspx?_ID=91)

基本上，您需要在现有运行良好的 OCS 2007 R2环境中添置至少一台 XMPP 网关服务器，通过配置其相关设定，来支持和Gmail用户的连接。

<!--more-->

下面我也说说我自己的经验和步骤:

**一、拓扑图**

我们先来看看微软官方文档中的一幅图：

[![Fig_1](http://winotes.net/wp-content/uploads/Fig_1-300x164.jpg)](http://winotes.net/wp-content/uploads/Fig_1.jpg)

&nbsp;

*   **Gmail**
> o Gmail 云部分是由Google提供的XMPP网关服务器，目前有5个不同的Gtalk主机名，其名称和IP如下所示。

*   xmpp-server.l.google.com,  74.125.45.125
*   xmpp-server1.l.google.com, 74.125.155.125
*   xmpp-server2.l.google.com, 74.125.47.125
*   xmpp-server3.l.google.com, 74.125.45.125
*   xmpp-server4.l.google.com, 74.125.45.125
<div id="more">

*   **OCS边缘服务器**
> o 一台运行良好的 OCS 2007 R2 Edge 服务器

*   **XMPP 网关**
> o 一台运行在 Windows Server 上面的 XMPP 网关服务器。 一般来说，单网卡就够了，可以同时处理来自外部（Gmail）和内部（OCS边缘）的请求。如果根据您的实际情况需要配置两片网卡，当然也是支持的。
> o 支持网络地址转换 (NAT)，在本例中我们用的就是NAT。如果你有单独的公网地址能直接分配给XMPP的话,当然更好。
好了，说了这么久，那你也许会问，什么是 XMPP 呢？

**XMPP **是 E**x**tensible **M**essaging and **P**resence Protocol 的简称，是一种以 XML 为基础的分布式和开放式即时通讯协议，听起来很熟悉？没错，其前身就是 **Jabber**。**XMPP **经由 IETF 小组制订，后因为被 Google Talk 采用而被大家所接触。

Jabber / XMPP 历史

Jeremie Miller于1998年开始了Jabber这个项目。第一个公开版本于2000年5月发行。这个项目的主要产品是jabberd，XMPP的服务器端软件。它既可以创建私人的XMPP网络，也可以加入全球的公共XMPP网络。 XMPP的关键特色是，分散式的即时通讯系统，以及使用XML流。
Jabber Logo
标准化的Jabber已经由IETF XMPP协议（RFC3920）。
Jabber是一个开放源码形式组织产生的网络即时通信协议。 XMPP原本是为Instant Messenger而量身定制，但由于XML Stanza本身是XML元素，在基于XML灵活发展的特性下，使得XMPP也可以适用其他方面，已经得到了IETF的批准。 XMPP与IMPP、PRIM、SIP（SIMPLE）合称四大IM协议主流，在此4大协议中，XMPP是最灵活的。
2005年，Google发布了Google Talk，这是一个IP电话及即时通讯的服务，即时通讯功能采用了开放的XMPP。预计这将对XMPP社区起很大的推动作用。初期此服务不支持服务器到服务器的通讯功能，所以未能完全发挥XMPP的分散式特色；虽然任何XMPP客户端都能连接到Google Talk，但是用户想要登录Google Talk，必须拥有Google Talk帐号（即Gmail帐号），而用户也无法与公共XMPP网络的用户通信。自2006年1月17日起，Google宣布支持服务器到服务器的通讯，Google Talk用户可与其他XMPP公共网络的用户聊天。
2007年，年的Jabber软件基金会已经成为XMPP标准基金会。

而微软在去年10月发布的 Office Communications Server 2007 R2 XMPP 网关实现了 Office Communications Server 2007 R2 与指定的基于 XMPP 的 IM 和状态提供程序间的 IM 和状态通信，这里指定的 IM 就是指 Jabber XCP 和 Google Talk。本文先讨论和 Gmail 通讯的部分， Jabber XCP 放到下篇中再讲。

**二、****XMPP ****网关服务器安装**

系统需求和 OCS Edge 服务器类似，只是说 XMPP 不能和 Edge 安装在一起。

软件需求：
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="242">.NET Framework</td>
<td valign="top" width="254">3.5</td>
</tr>
<tr>
<td valign="top" width="242">操作系统</td>
<td valign="top" width="254">Windows Server 2003 SP2 64位标准版/企业版Windows Server 2008 64位标准版/企业版Windows Server 2008 R2 标准版/企业版*</td>
</tr>
<tr>
<td valign="top" width="242">MMC控制台</td>
<td valign="top" width="254">3.0</td>
</tr>
<tr>
<td valign="top" width="242">Microsoft Visual C ++® 2005 Redistributable or Visual C++ 2008 Redistributable</td>
<td valign="top" width="254"></td>
</tr>
<tr>
<td valign="top" width="242">Unified Communications Managed API (UCMA)</td>
<td valign="top" width="254">安装补丁**<span style="color: #ff0000;">KB968802</span>**</td>
</tr>
</tbody>
</table>
* 微软OCS团队的博文上面说 Windows 2008 R2 暂不支持，但是我测试了，**<span style="color: #ff0000;">2008 R2</span>**是支持的

支持的服务端

· Microsoft Office Communications Server 2007 R2

· Microsoft Office Communications Server 2007

· Jabber XCP Server version 5.4

· Google Talk

支持的客户端

· Microsoft Office Communicator 2007 R2

· Microsoft Office Communicator 2007

· Microsoft Office Communicator 2005

· Jabber MomentIM Client version 5.2.1

· Google Talk

下载地址：

1\. Microsoft Office Communications Server 2007 R2 XMPP Gateway

[http://www.microsoft.com/downloads/details.aspx?displaylang=zh-cn&amp;FamilyID=aa560bfe-9960-473a-bfb8-53bff678cec4](http://www.microsoft.com/downloads/details.aspx?displaylang=zh-cn&amp;FamilyID=aa560bfe-9960-473a-bfb8-53bff678cec4)

2\. Hotfix

[http://www.microsoft.com/downloads/details.aspx?familyid=80B4E42D-6548-46F4-87B1-7FA0FAF80B82&amp;displaylang=en](http://www.microsoft.com/downloads/details.aspx?familyid=80B4E42D-6548-46F4-87B1-7FA0FAF80B82&amp;displaylang=en)

安装过程，典型的 Next to Next，不过这还只是解压：

[![Fig_2](http://winotes.net/wp-content/uploads/Fig_2-300x238.jpg)](http://winotes.net/wp-content/uploads/Fig_2.jpg) [![Fig_3](http://winotes.net/wp-content/uploads/Fig_3-300x238.jpg)](http://winotes.net/wp-content/uploads/Fig_3.jpg) [![Fig_4](http://winotes.net/wp-content/uploads/Fig_4-300x238.jpg)](http://winotes.net/wp-content/uploads/Fig_4.jpg) [![Fig_5](http://winotes.net/wp-content/uploads/Fig_5-300x238.jpg)](http://winotes.net/wp-content/uploads/Fig_5.jpg)

这里安装程序会把安装文件解压至 C:\Program Files\Microsoft Office Communications Server 2007 R2\XMPP Gateway Installer 目录，运行 setup.exe

[![Fig_6](http://winotes.net/wp-content/uploads/Fig_6-300x227.jpg)](http://winotes.net/wp-content/uploads/Fig_6.jpg)

&nbsp;

点 Install 进行安装，完毕后我们开始配置。

**需要注意的是****,****一开始你会看到**** Install Files for XMPP Gateway****这一步是正常的****,****但是当你退出后下次再进入该页面时，你也许会看到如下提示，尤其是在**** Windows 2008 R2****中安装时，那么你还需要安装这个更新**** ****（包含在**** KB968802****中）：**

**http://download.microsoft.com/download/D/9/6/D967F77F-D0BD-43FB-987B-6FBF7C82251B/UcmaRedist.msp**

[![Fig_7](http://winotes.net/wp-content/uploads/Fig_7-300x220.jpg)](http://winotes.net/wp-content/uploads/Fig_7.jpg)

&nbsp;

**三、****XMPP ****网关配置**

**A. ****要使**** XMPP ****网关能正常和****Gtalk****通讯，得满足下面四个条件：**

1\. 你的环境中的 OCS 2007 R2 运行良好。

2\. 你的 OCS 有安装边缘 Edge 角色。

3\. 一张用来和内部边缘服务器通讯的证书，私有或者商业的都行（和Gmail的通信，私有就够了）。

4\. 需要在你的公网域名下面创建一条 A 记录 和 SRV 记录。

接下来我们再来详细看看 Gmail 和 OCS 的通讯过程

[![Fig_8](http://winotes.net/wp-content/uploads/Fig_8-300x155.jpg)](http://winotes.net/wp-content/uploads/Fig_8.jpg)

&nbsp;

看看一个 Gtalk 用户如何发送一个即时消息到 contoso.com 这个域中的OCS 用户:

1\. GoogleTalk 服务器场中的任意一个 XMPP 服务器向 DNS 请求查询 contoso.com 的 XMPP 服务记录 **__xmpp-server._tcp.contoso.com_**_。_ 这条记录应该指向 contoso.com 组织中 XMPP 网关服务器的公网 A 记录的FQDN地址，A记录的名字任意取，比如 **_xmpp.contoso.com_**_。_

2\. 得到A记录的地址后，GoogleTalk 服务器会使用标准 TCP 连接去和 **_xmpp.contoso.com_**的**5269 **端口发送请求。这个过程是非加密的，所以在这里我们并不需要证书。

3\. 在 contoso 组织的防火墙接到 Gtalk 的请求后，会将其路由给 XMPP 网关的，当然，在这之前，contoso 的管理员需要把 TCP **5269** 端口映射给 **XMPP**。在上面的图例中，XMPP 网关的内部接口地址是 10.11.12.40。 这一步也是非加密的。

4\. 当 XMPP 网关接到请求后，它会查看自己的 SIP 配置来确定下一跳怎么走，那么在我们的例子中，这个下一跳就是 OCS 的边缘访问服务器了 (_sip.contoso.com_)。要注意的是，这里是使用的 Edge 的外部FQDN地址，所以当你的 XMPP 网关和 Edge 在同一个防火墙后面时，也许 XMPP 会得到 Edge 的公网地址，从而连不到 Edge（除非在防火墙上开启了 DNS 别名- DNS Alias or DNS Doctor）。所以这时你需要在 XMPP 网关的本地 Hosts 文件中添加 Edge 的外部接口的内网地址 (10.11.12.33)。多啰嗦一句，在 Edge 的经典配置中，外部接口指的是 Edge 用来监听外部 SIP 通讯和联盟的那张网卡，不是用来和 OCS Pool 通信的那个内部接口。

5\. 在与边缘访问的 Edge 服务器的联盟监听端口 **5061 **建立连接后，XMPP 网关需要一张证书来做 MTLS 的加密通讯。 同样的，XMPP 必须要连接到 Edge 的外部接口上的联盟监听端口。很多朋友在此碰到的问题大多是把 XMPP 网关去连 Edge 的内部接口地址。到这一步，Gtalk的消息就发到了OCS用户那里。

6\. OCS 用户回消息给 Gtalk 用户过程 - Edge 服务器收到来自 OCS 前端服务器的请求后，会将 gmail.com 当做一个联盟的域来处理，就和OCS连接 Yahoo/MSN 等一样，通讯请求将会把去到 gmail.com 的通讯交给 Gmail 的联盟服务器 - XMPP 网关去处理，在上面的图例中就是**_xmpp.jds.local_**

这里有几个重点要注意下:

· 第一，在 XMPP 网关上的证书一定要安装配置好。

o 该证书只是用在 XMPP 和 Edge 服务器的内部通信中间，并不用来和 Google Talk 服务器通信，所以我们可以使用自建 CA 来颁发证书。证书的SN名称应该和 XMPP 网关的内部 FQDN 名称一致，另外，自建 CA 的根证书链应该要导入到 XMPP 网关的计算机账户下受信任的根证书颁发机构中。

o 证书的申请，你可以通过边缘服务器 Edge 上面的证书向导来做（因为 XMPP 上面没有这个向导），或者使用 lcscmd.exe。注意的是，该证书的导出和导入必须要包含私钥（Private Key）。

· 第二， 证书的必须颁发给 XMPP 服务器的 FQDN 名称。

o 因为 XMPP 网关服务器和 Edge 一样，没有加入域，工作组环境，所以我们需要添加相应的 DNS 后缀，比如 **jds.local**。

o XMPP 网关服务器的 FQDN， 证书的 CN (或者 SAN 记录) 以及在 Edge 服务器中的 Gmail 联盟服务器的允许列表中，设定的都应该是**真实的**** FQDN ****名称**，并且要保持**一致**。

· 最后, 防火墙规则的设定:

o XMPP 网关的 TCP **5061**，需要和 Edge 的 TCP **5061** 端口进行双向通信

o XMPP 网关的 TCP **5269**，需要接受来自外部的 Google Talk 的请求

**B.  DNS ****记录的创建**

· 创建 A 记录：**xmpp.contoso.com** - 12.34.56.78

· 创建 SRV 记录：**__xmpp-server._tcp.contoso.com_** - **xmpp.contoso.com****，**** Port 5269**

参照微软的图

[![Fig_9](http://winotes.net/wp-content/uploads/Fig_9-300x109.jpg)](http://winotes.net/wp-content/uploads/Fig_9.jpg)

&nbsp;

**C.****说了很多，下面开始配置**** XMPP :**

· 修改 **TGWConsoleGUI.dll.config** 文件

默认位置在 **C:\Program Files\Microsoft Office Communications Server 2007 R2\XMPP Gateway** 下面，这里需要改到的部分是 _SipIP_ 和 _XmppIP _两行，这里一定要改，本例中 SipIP 和 XmppIP 都是用的 XMPP 的内部地址。这里如果改的不对或者没有改而留空的话，会导致 XMPP Gateway 服务一启动就马上自动停止!

[![Fig_10](http://winotes.net/wp-content/uploads/Fig_10-300x157.jpg)](http://winotes.net/wp-content/uploads/Fig_10.jpg)

&nbsp;

· SIP 配置

在 XMPP Gateway 的配置界面中点击 SIP Configuration:

1. **Domain** 标签卡, Domain 一栏指定你的域名，本例中我们用 **litwareinc.com**。

2. **Host Name** 一栏，填上 Edge 边缘服务器的外部 FQDN 名称，本例中我们用**srv.litewareinc.com **。

[![Fig_11](http://winotes.net/wp-content/uploads/Fig_11-300x208.jpg)](http://winotes.net/wp-content/uploads/Fig_11.jpg)

3.**TLS Certificate**，选择之前通过自建 CA 申请的内部证书

[![Fig_12](http://winotes.net/wp-content/uploads/Fig_12-300x208.jpg)](http://winotes.net/wp-content/uploads/Fig_12.jpg)

&nbsp;

4. **Validate Connection** 在下拉列表中选择 **litwareinc.com**，验证是否配置正确。

· XMPP 配置

选择 **XMPP Configuration** 节点

1\. 在 **Allow List** 标签卡中, 点击 **Add **添加。

2\. 在弹出的 **Federated XMPP Domain names** 对话框中,  **Domain Name** 一栏填_gmail.com__，_选择 **TCP Dialback** (required)，其它留空，确定保存。

[![Fig_13](http://winotes.net/wp-content/uploads/Fig_13-300x208.jpg)](http://winotes.net/wp-content/uploads/Fig_13.jpg)

&nbsp;

3.之前提到过，Gmail不使用加密连接，所以这里不需要配置证书。

4\. 验证 Gmail。

· 启动XMPP 服务

[![Fig_14](http://winotes.net/wp-content/uploads/Fig_14-300x220.jpg)](http://winotes.net/wp-content/uploads/Fig_14.jpg)

&nbsp;

**D. Edge ****服务器的配置**

这里非常简单，就是把 gmail.com 作为联盟域添加:

1\. 打开 OCS Edge 的属性配置对话框。

[![Fig_15](http://winotes.net/wp-content/uploads/Fig_15-263x300.jpg)](http://winotes.net/wp-content/uploads/Fig_15.jpg)

&nbsp;

2\. 转到 **Allow** 标签页, 点击 **Add**, 按图中所示添加。

Federated partner domain name - **_gmail.com_**,

Federated partner Access Edge Server - **srv_xmpp.litewareinc.com**

3\. 为了保证 OCS Edge 服务器能和 XMPP 网关正常通信，你需要在内部 DNS 中添加 XMPP 的记录或者是在本地 Hosts 文件中指定，比如

**192.168.1.20 srv_xmpp.litwareinc.com**

四、添加 Gmail 用户并验证

添加 Gmail 用户之前，得保证你的 OCS 用户策略里面有启用联盟

[![Fig_16](http://winotes.net/wp-content/uploads/Fig_16-300x171.jpg)](http://winotes.net/wp-content/uploads/Fig_16.jpg)

&nbsp;

成功!

[![Fig_17](http://winotes.net/wp-content/uploads/Fig_17-180x300.jpg)](http://winotes.net/wp-content/uploads/Fig_17.jpg)[![Fig_18](http://winotes.net/wp-content/uploads/Fig_18-228x300.jpg)](http://winotes.net/wp-content/uploads/Fig_18.jpg)

</div>