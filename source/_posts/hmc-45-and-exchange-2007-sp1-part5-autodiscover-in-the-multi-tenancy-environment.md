---
title: >-
  HMC 4.5 and Exchange 2007 SP1 - Part #5 - Autodiscover in the Multi-tenancy
  environment
tags:
  - Autodiscover
  - Exchange
  - HMC
  - MultiTenancy
id: 138
categories:
  - HMC
  - Hosting
date: 2009-05-07 18:52:04
---

转自 http://blogs.technet.com/provtest/archive/2009/01/01/hmc-4-5-and-exchange-2007-sp1-part-5-autodiscover-in-the-multi-tenancy-environment.aspx 很好的文章,贴过来收藏,hehe.

**Introduction**

So, we have covered quite a fair bit by now. In part #1, we talked about how we can properly partition the Active Directory so that each company or tenant can coexist in the same environment but yet remain isolated and segregated. In part #2, we talked about how we have tweaked the address list so that each tenant will only see what they are supposed to see. In part #3, we further explore how Offline Address Book needs to be tweaked in to work in a multi-tenant environment and in part #4, we discussed about how Out-of-Office doesn't work completely out of the box in a hosted Exchange environment and what HMC has introduced to make it work better.

So, what's next?
<div id="more">

**Shared Infrastructure in HMC?**

<!--more-->

Now that we have most of the things well setup at the service provider side, the HMC environment is ready to serve clients. For Outlook Web Access (OWA), it is easy; the users only need a web browser, the OWA URL, username and password. Most people can manage that without a problem.

For Outlook client, unfortunately it is a little bit more complicated than we like. Outlook has come a long way and it still carries what I will call requirements for some 'legacy' parameter that cause the configuration of Outlook more complicated than desired.

For example, to configure Outlook to access an Exchange mailbox, you have to create an Outlook profile and in that Outlook profile, you have to specify the Exchange Server name (which most users do not know), you have to specify username and password. That's not all, because almost all the users in the HMC environment comes from Internet, the Exchange Server Name is not resolvable and that we have to provide the configuration of RPC over HTTP so that it can utilize the RPC Proxy to access the Exchange server from Internet.

If that doesn't sound complicated enough, Outlook 2007 and Exchange 2007 enhanced some of the features by introducing querying and accessing web services for some of the features such as Availability Services, Out-of-Office service and Unified Messaging service. OAB web distribution was also introduced to replace the old OAB distribution method through Public Folder. All these features mean Outlook 2007 now has to know more URLs or configurations to access those services.

Now, this is only for Outlook, what about my Windows Mobile Devices? What about Entourage clients? What about devices that can utilize ActiveSync other than Windows Mobile, such as iPhone?

While the above sounded complicated, we do have some good news for you.

*   Firstly, those shared infrastructure such as Exchange Web Services for Availability Services, Out-of-Office, Unified Messaging services DO NOT need any isolation or segregation. So no tweaking is required. Out of the box, those services enforce authentication (making sure that only authorized users can access the shared infrastructure) and it knows what security context to use when retrieving information or setting information (making sure that users can make changes or retrieve information ONLY if they have been granted the permission).
*   Secondly, for all the complications introduced by Outlook 2007, we now have a feature called Autodiscover that hide those complications and make things easier.
The not good news is this, Autodiscover doesn't just work out of the box in HMC environment, it has to be configured for each domain introduced by the tenant and it may not work in some situations. We will explore that in this blog.

<span style="text-decoration: underline;">(1) Outlook 2003</span>

Outlook has come a long way. However, if you look at Outlook profile creation and configurations, we haven't changed much. It looks almost the same since the birth of Outlook.

The Good: Once you have configured the Outlook client, it is pretty self-contained; all it needs is to access the mailbox server, it will then know where to locate the free/busy, set the OOF, and find the public folder and OABs. It doesn't need to access any other URLs (such as Availability Services, Web Distribution OABs and etc.).

The Bad: The client has no logic built in to automatically detect the client configuration needed to access the mailbox. In addition, it also does not have access to some of the new features introduced in Exchange 2007 like Internal and External OOF, UM and enjoy the real-time free/busy capability from Availability Services.

Solutions for easier profile configurations: Install the RPC over HTTP Client Configuration Web site. This site will allow the user to authenticate and then download their PRF file that has been populated with all the information they need to configure their Outlook.

<span style="text-decoration: underline;">(2) Outlook 2007</span>

Outlook 2007 becomes slightly more complicated. The fact that it introduces the concept of Exchange Web services being accessed by Outlook directly means that now Outlook 2007 needs to know how to get to those web services. The answer to all these is a feature called Autodiscover.

Autodiscover has changed things quite a fair bit and for those are interested to know more, you can read this [White Paper: Exchange 2007 Autodiscover Service](http://technet.microsoft.com/en-us/library/bb332063.aspx) as I don't intend to spend much time talking about the working which has been well covered by this white paper.

In a corporate environment, Microsoft Office Outlook 2007 clients locate an Autodiscover service running on a Client Access Server by directly querying the Active Directory directory service and locating relevant Service Connection Points (SCPs). However, as we know most of the users in the HMC environment aren't part of the HMC Windows domain and also they are accessing the service from the Internet.

For Internet users, Outlook 2007 will attempt to locate and connect to an Autodiscover service based on the e-mail domain of the user. For example, for the user johnc@alpineskihouse.com, Outlook 2007 will automatically try to connect to the following URLs in turn:

*   [https://alpineskihouse.com/autodiscover/autodiscover.xml](https://alpineskihouse.com/autodiscover/autodiscover.xml)
*   [https://autodiscover.alpineskihouse.com/autodiscover/autodiscover.xml](https://autodiscover.alpineskihouse.com/autodiscover/autodiscover.xml)
*   [http://autodiscover.alpineskihouse.com/autodiscover/autodiscover.xml](http://autodiscover.alpineskihouse.com/autodiscover/autodiscover.xml)
*   And if all those failed, it will try to use DNS SRV records. However, this is only if the Outlook Clients have been updated with the appropriate rollup. A new feature is available that enables Outlook 2007 to use DNS Service Location (SRV) records to locate the Exchange Autodiscover service [http://support.microsoft.com/kb/940881](http://support.microsoft.com/kb/940881)
The logic to form the URLs above is built into Outlook 2007, we can't really change that and it has to go according to that sequence. Now, obviously that can present a problem in a HMC environment. Why?

The first 2 URLs are SSL. When SSL is required, we must make sure that a valid security certificate is installed for the Web site that matches the site name. So, this can be a problem for HMC environment because it will be madness and totally impractical to have 10,000 unique valid security certificate for all the 10,000 domains hosted by your environment. Not to mention you may need many IP addresses and website just to cater for that.

This is where the 3rd URL, which is only a HTTP redirection, comes in. It redirects the Outlook to another HTTPS Autodiscover URL. That's the reason why you see in the Deployment Guide, we have to create an additional Web site in IIS just to do this redirection.

There are some customers who attempted to use CNAME rather than the redirect, meaning just point autodiscover.alpineskihouse.com to autodiscover.consolidatedmessenger.com, this will fail because the security certificate cannot be properly validated. A redirection is a must.

In order for Outlook to go to[http://autodiscover.alpineskihouse.com/autodiscover/autodiscover.xml](http://autodiscover.alpineskihouse.com/autodiscover/autodiscover.xml) and get redirected, you must do 2 things,

*   **DNS** - Configure autodiscover.alpineskihouse to point to the HTTP redirect website or IP address.
*   **Autodiscover.xml** - your HTTP redirect website must be able to respond to /autodiscover/autodiscover.xml and then redirect them to actual HMC autodiscover HTTPS website.
Of course, you can also do the SRV record. The only concerns for this is that the Outlook 2007 has to be properly updated before it can enjoy this new features and also we have seen many customers do not do the SRV record correctly. I kind of like the idea that you put both the HTTP redirect as well as the DNS SRV record there so that in the event where the HTTP redirect website is down, Outlook 2007 could continue to function well.

One thing you should also know is that Autodiscover is not only used during the profile setup, it is also being used each time you query any of the web services, such as availability services or OOF.

<span style="text-decoration: underline;">(3) Other Clients</span>

What about other clients? As you can see, the deployment of the Autodiscover functions very much depends on the client. If the client does not support, say HTTP redirect or the SRV record, then Autodiscover in HMC environment will fail. One example is iPhone. IPhone does not support HTTP redirect or the SRV record. Hence, the auto account configuration may not work in those clients until they have updated the clients.

**Troubleshooting tips for Autodiscover in Multi-tenancy environment**

I find that most of the Autodiscover problems in HMC can be categorized into the following,

*   Deployment problem - the Autodiscover not being configured correctly. It is extremely important that you set this correctly such as what the externalurl, the HTTP redirect and etc. Do run through the configurations again.
*   Certificates problem - it is important to understand the usage of SSL certificate in Autodiscover and also other Exchange web services. Currently, the best reference is this, [White Paper: Exchange 2007 Autodiscover Service](http://technet.microsoft.com/en-us/library/bb332063.aspx)
*   DNS configuration issue - because Outlook depends on DNS to locate the Autodiscover service, DNS needs to be configured correctly.
*   Firewall or Network Connectivity Issues - Publishing the SSL, the Autodiscover service and etc. can be a challenge too sometimes.
</div>