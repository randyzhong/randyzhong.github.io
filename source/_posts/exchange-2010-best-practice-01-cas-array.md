---
title: Exchange 2010 最佳实践01 - CAS Array
tags:
  - 最佳实践
  - Exchange
  - Kerberos
categories:
  - Microsoft
  - Exchange
date: 2013-12-06 09:44:56
---

CAS Array 的概念是第一次在 Exchange 2010 中被引入，其主要目的是在于统一客户端访问名称，便于不管连接哪台服务器均能够使用同样一个名字访问，因此在不建立NLB的情况下使用CAS ARRAY能够统一访问名称。

实际的部署 CAS Array 的过程我就不说了，网上很多教程。这里要提出来的三点建议，也许对你的环境会有帮助。  

**CAS Array 的内外部 FQDN 不要用一样的，说更清楚点，内部 CAS Array 的 FQDN 不要能被外部 DNS 解析。**

Why? 就算我内外FQDN一样，我也还是能正常访问 Exchange 啊。对，你确实还是能够访问，但是却增加了 Outlook 的连接时间，在某些慢速网络下，甚至会导致连接超时。我们下面详细来看看各种场景下的连接情况。

a)  内部 Outlook 用户 (TCP连接)。
* Outlook会通过DNS去查询内部 FQDN 对应的 IP，比如说outlook.internal.local -&gt; 192.168.1.30 (内网IP)。
* Outlook 会认为自己在内网，于是尝试使用DCE/RPC 也就是135 端口去连接 192.168.1.30。
* 连接建立。

b)  外部 Outlook 用户 （CAS Array内部FQDN可以从公网解析）

* Outlook会通过DNS去查询内部 FQDN 对应的 IP，比如说outlook.internal.local -&gt; 123.234.213.213 (公网IP)。
* Outlook会认为自己在内网，于是尝试使用DCE/RPC 也就是135 端口去连接123.234.213.213。
* 而大多数情况下，防火墙是不会允许公网的 135 端口通信的，所以得等待TCP连接超时，这其中还有几次重试的时间。
* 当 Outlook 发现 TCP/135 的端口无法连接的时候，才发现，哦，原来我不在内网啊，那就只能尝试使用 https协议去连接 443 端口。
默认情况下， 在快速网络环境下，Outlook 会使用 TCP 先行尝试连接，不成功的情况下才会使用 Http/Https ，这里TCP连接浪费的时间，短则10来秒，最长可达30秒钟以上，所以，Outlook Anywhere用户体验会变得糟糕。

当然，你可以改成无论何时都是先用http/https 连接，对于 Outlook Anywhere 用户，很适合，不过这会对内网用户造成困扰，甚至无法登陆，因为内外网证书很可能不一致，导致设定的 FQDN 名称和证书的 Common Name 不匹配，会总提示输入用户名和密码，即便你输入的是正确的，还是会不停地跳出来。

c)  外部 Outlook 用户 （CAS Array内部FQDN不能从公网解析）
* Outlook会通过DNS去查询内部 FQDN 对应的 IP，但此时从 DNS 并不能返回查询结果。
* Outlook 知悉，哦，地址无法解析，我在外网，于是直接使用 Https 去建立连接。

** CAS Array **的场景下，客户端最好能做** Kerberos **认证 （Exchange 2010 SP1）。

在 Exchange 2010 中，所有的域客户端的 MAPI 访问实际上都是通过 CAS 来走，而客户端的认证会使用协商认证，即negotiate authentication，也就是会先使用 Kerberos 认证。失败之后再尝试 NTLM 认证。（注：Outlook Anywhere 不适用）

而在配置了 CAS Array 的环境下，Kerberos 认证会失败的，因为 Kerberos 认证的时候，发起的service ticket中的authenticator 使用的是 CAS Array FQDN的 SPN，而不是 CAS Server 的 SPN，所以会认证失败。具体的实现步骤可以参考这里：[http://blogs.technet.com/b/exchange/archive/2011/04/15/recommendation-enabling-kerberos-authentication-for-mapi-clients.aspx](http://blogs.technet.com/b/exchange/archive/2011/04/15/recommendation-enabling-kerberos-authentication-for-mapi-clients.aspx)

那么，开启 Kerberos 认证有啥好处呢？除了可以满足一些企业的安全需求外，还能减少 CAS 服务器和 AD 的负载，这对大型企业中的性能优化很有帮助。

**即便你只有一个 CAS 服务器，也建议配置 CAS Array **。