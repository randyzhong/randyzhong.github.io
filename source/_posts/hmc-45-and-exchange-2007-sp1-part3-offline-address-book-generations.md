---
title: 'HMC 4.5 and Exchange 2007 SP1 - Part #3 - Offline Address Book Generations'
id: 136
categories:
  - HMC
  - Hosting
date: 2009-05-07 17:38:47
tags:
---

转自 http://blogs.technet.com/provtest/archive/2008/12/18/hmc-4-5-and-exchange-2007-sp1-part-3-offline-address-book-generations.aspx 很好的文章,贴过来收藏,hehe.

** **

**Introduction**

In the last part, we discussed about Address List segregation, which is an important concept to understand in Hosted Exchange environment. It is my belief that if you understand that clearly, you have understood 70% of the Hosted Exchange concept in HMC. The rest will be just easy. :)

In this part, we will continue to discuss about Offline Address Book (OAB) Generations in a HMC environment. I don't intend to talk about the OAB generation process in Exchange 2007 because I think Dave Goldman's web log has covered most of them ([http://blogs.msdn.com/dgoldman/default.aspx](http://blogs.msdn.com/dgoldman/default.aspx)). Now, for those who are interested to know more, I think the following is a good starting point (although the content is mainly Exchange 2003, the whole OAB generation process hasn't changed much)

Overview of the OABgen process
[http://blogs.msdn.com/dgoldman/archive/2005/03/31/Overview-of-the-OABgen-process.aspx](http://blogs.msdn.com/dgoldman/archive/2005/03/31/Overview-of-the-OABgen-process.aspx)

My primary objective in this blog is to discuss about the customization of OAB components in Exchange 2007 SP1 done by HMC.

<!--more-->
<div>

**What has been introduced by HMC?**

HMC does not change the OAB component in Exchange 2007 SP1 much. It does make a few changes in terms of how it works to make sure that things will run better in a service provider environment. So, let's take a closer look,

<span style="text-decoration: underline;">(1) OAB Generation Process</span>

This is probably one of the parts in HMC that confuses messaging engineers the most. Typically, when an Exchange engineer troubleshoots the OAB generation (OABgen) process, they will look at the event log and also look at the OAB generation schedule. What they will see is that in a HMC environment is that the schedules for the all the OALs have been set to nothing, meaning Never Run.

Instead of using the normal OABgen schedule which will scan through all OABs and update each one of them (which in a Hosted environment, you could be talking about 30-50k OABs, that can take a long time), HMC changes things a little bit here by making the process to be triggered by changes instead of by time. Meaning, if there is no changes made to users or contacts in the company, no offline address book update will be called or triggered for that company.

To do that, HMC does the following,

*   **No Schedule **- When creating an Offline Address List object, HMC will set the schedule of that Offline Address List object to Never Run.
*   **HmOABUpdate** - HMC introduces a separate components called Hosted E-mail Offline Address Book (OAB) Update Batch Application (HmOABUpdate), installed on one of the Microsoft Provisioning Servers (MPS).
This application will query the MPS SQL Server (which keeps track of all the creations, deletions, modifications and changes made to the environment through HMC) and based on those changes; it will call the Update-OfflineAddressBook only on the specific company that has been changed.

The application will also generate a set of logs to log any success of failures in a log folder in the same directory of the application, by default, which is &lt;drive&gt;:\Program Files\Microsoft Hosting\Provisioning\ Exchange OAB Update\log folder.

Now, for each Offline Address List object, there are a couple of interesting items to look at,

*   The Address Lists - Generally it should be pointing to the company address list (NOT the company global address list).
*   PublicFolderDistributionEnabled - Depending on the Organization Plan (&lt;features/ outlookClient&gt;) you use to create the company/tenant, this may be True or False.
*   PublicFolderDatabase - This will determine which Exchange server will be performing the OAB generation. If the distribution is primarily on Public Folder, then it is important to make sure that the number of OAB hosted by the server does not exceed the MaxPageSize specified in the Active Directory LDAP Policies. If it does, you may have OAB generation issues. I will cover this in the troubleshooting section later.
If all things are well, the generation process should be just like any other corporate Exchange environment, except it is being triggered from another application.

<span style="text-decoration: underline;">(2) OAB Limit and Distribution</span>

There are a few unique challenges that a Hosted Exchange environment like HMC may face that are generally not faced by a corporate environment.

*   **High number of OABs**, when will you see a corporate environment have more than 1000 OABs? Almost never. However, this could be common in a HMC environment. When Microsoft released HMC 4.0, it provided a recommendation that each Microsoft Exchange Server is limited to supporting a maximum of 1000 Exchange Server Offline Address Lists ([http://technet.microsoft.com/en-us/library/cc539031.aspx](http://technet.microsoft.com/en-us/library/cc539031.aspx)).
> In HMC 4.5, we have raised it to 10,000 per server instead. The Knowledge Base article KB 945629 updates the sizing guidance for Exchange Offline Address Book (OAB) servers. This ten-fold increase compared to Hosted Messaging and Collaboration version 4.0 allows service providers to save on hardware, software licensing, and maintenance cost. However, it should be noted that after raising the limit in an environment, service providers should monitor Active Directory performance carefully to ensure the change has no negative impact. Service providers should also ensure the existing hardware for the OAB servers are capable of handling the increase to ensure that customers are still able to quickly download updates to their OABs, even during peak working hours.

*   **Dedicated OAB Distribution Server**, depending on what version of Outlook Client you intend to support. If you intend to support anything below Outlook 2007, then you have to have Public Folder as the distribution point and the recommendation is that, you should have dedicated OAB servers and each server should not have more than 10,000 OABs in it (as mentioned above).
If you have only Outlook 2007 Clients, you may be using primarily just the web distribution method, it doesn't mean you have no issue too because it means the load of download will be shifted to the CAS server and you should also monitor the performance of the CAS to see if there is a need to have dedicated CAS just to be a OAB distribution point (Deployment Walkthrough will walk you through on how to setup a dedicated CAS for Web Distribution).

*   **Other behaviour that you should take note**, OAB generation process actually has some interesting implication that is not generally encountered by a corporate environment.

    *   OAB public folder expiration. This actually trace back to Exchange Server 2003, Update to modify the OAB public folder expiration in Exchange Server 2003 ([http://support.microsoft.com/kb/832761](http://support.microsoft.com/kb/832761)).
> > Because HMC moved the generation process out to an application and only touches the changed OAB, you may potentially have an OAB that may not have any change for a while. OABgen sets the default expiration time of 30 days. So what's going to happen is that, when it expires, it will remove the content in those folders and the user will not be able to download the OAB. The trick is to assign a value of 0 (zero) to the "OAL Folder Lifetime" registry entry, messages in the public folder do not expire. If you set this registry entry to a value other than zero, messages in the public folder expire in the number of days that correspond to the value that you assign. After you create this registry value, every time that OABgen runs, OABgen reads the registry entry and stamps it on the public folders that it uses.
> > 
> > HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\MSExchangeSA\Parameters
> > 
> > Value name: OAL Folder Lifetime (days)
> > Value type: REG_DWORD
> > Value data: 0

*   OABScan task is not required. This article should explain it. CPU usage by the Exchange System Attendant service on offline address list servers in a hosting environment increases to 50 percent, [http://support.microsoft.com/?id=834315](http://support.microsoft.com/?id=834315). While the article is written specifically for Exchange 2003, it is also applicable to Exchange 2007 if Public Folder OAB distribution is heavily used.
&nbsp;

<span style="text-decoration: underline;">(3) OAB Creation and Maintenance</span>

Even though HMC puts in tweaks here and there, creation of the Offline Address List is nothing more than calling the New-OfflineAddressBook cmdlet. Here, I just want to highlight one thing,

_SkipPublicFolderInitialization_ parameter. This parameter was introduced in Exchange 2007 SP1 and the appropriate namespace and procedures have been updated to allow for this parameter. If you are creating an OAB that uses public folder distribution, use the_SkipPublicFolderInitialization_ parameter to skip the immediate creation of the OAB public folders. The OAB will not be available for download until the next site folder maintenance cycle has completed. You do not have to specify a value with the_SkipPublicFolderInitialization_ parameter. If you do not skip this initialization, it may cause the task to pause while it contacts the responsible public folder server to create the necessary public folders. If the server is presently unreachable, or is otherwise costly to contact, the pause could be significant.

So, it is a decision of the service providers whether they want to immediately create the OAB root folder or they want to wait till the next day.

<span style="text-decoration: underline;">(4) How does user know which OAB to use?</span>

Now that we understand the OAB generation process is triggered from a separate HMC application and also that there are some tweaks required to make things work more suitable for a service provider environment, the next thing we need to sort out is that how Exchange knows which of those 10,000 OABs  should it send the user to retrieve. The answer lies in one of the user object Active Directory attributes that we mentioned in part #1,

**msExchUseOAB**: DN of the Tenant specific OAL object, this attribute directs Outlook to the appropriate OAB to download for this user.

This attribute is populated when the mailbox user is created in HMC.

<span style="text-decoration: underline;">(5) A Web-based Offline Address Book (OAB) Server</span>

&nbsp;

By far, the discussions have been mostly around the Public Folder distribution for OAB. In HMC 4.0, the only method of distribution of OAB is through Public Folder even though Exchange 2007 has the capacity to do both Public Folder and Web Distribution.

&nbsp;

HMC 4.5 has lifted that limitation, allowing service providers to deploy web distribution for OAB. Of course, introduction of this new feature requires some additional configurations.

&nbsp;

In order to enable a specific Offline Address List to be distributed through web, each OAB has to be enabled for Web-based distribution and that it must have at least one OAB distribution virtual directory specified. Hence, it means from the MPS perspective, when MPS needs to create a new OAB, there must be some logic to assign the appropriate CAS server as the OAB distribution point. To do that, HMC introduces the concept of CAS Pool.

&nbsp;

The concept is quite straightforward. What we really need to do is to create a pool of CAS servers that will be used as distribution server. The steps are documented in the Deployment Walkthrough. We must first create the pool by using CreateOABCASPool.xml. Next, we have to add each OAB Web Distribution server into the pool using AddOABCAS.xml.

&nbsp;

Then when create the mailbox, we have to specify the CAS Pool that will be used by the company or tenant. I like to note that this is during the process of create mailbox rather than create organization or email plan subscription. The reason is that the creation of the OAB really happens on the first MAPI user creation. That's why the specification of the CAS pool is on creating mailbox.

&nbsp;

Everything sounds simple enough so far, now what will happen when we decided to add a new OAB web distribution server to the CAS pool? You may realize that only the new company may get all the web distribution servers listed in the OAL whereas the OAL created in the past may not have that updated. This is where HMC 4.5 also introduces a new tool called**UpdateOABProvision.exe**.

&nbsp;

The **UpdateOABProvision.exe** tool is used to update all existing Offline Address Books (OAB's) when the list of Web-based OAB CAS servers that exist in an OAB CAS Pool has changed.

&nbsp;

For example, if a new server is added to the OAB CAS Pool, the orchestration logic will need to update all OAB's associated with that pool so that they have an accurate list of distribution points, including the new server. Similarly, if an OAB CAS server is removed from the pool, all OAB's associated with that pool should be updated so that the removed server is no longer set as a distribution point.

&nbsp;

The command-line syntax for running the UpdateOABProvision.exe tool is:

&nbsp;

_UpdateOABProvision.EXE -OABCASPool -Errorlog -MaxError_

&nbsp;

I also like to highlight one thing here which is in HMC 4.5, it really uses just the default CAS Pool. Each CAS Pool can cater for up to 128 servers, which is quite a big number, consider the fact that there is no 1,000 OAB limit like what we have when we are using Public Folder OAB distribution.

&nbsp;

&nbsp;

**Troubleshooting OAB download Issues in HMC**

Problem downloading OABs is one of the most common issues raised to us for newly setup HMC environment. Most of the time, the problems can be categorized into 2 types,

<span style="text-decoration: underline;">(1) OAB Generation Issues</span>

This is probably the first thing you want to find out, that is if the OAB generation is successful. I will generally start with the following,

*   Confirm that the company OAB root folder exists. If not, you need to find out,

    *   If you are looking at the right Public Folder Server, if you are not, please try to locate the right Public Folder Server
    *   If you can find the root folder on some of the OAB Public Folder Server but not on others, then you may need to find out if you have a Public Folder Replication issue.
    *   If you can't find the root folder anywhere, you may want to find out how the OAB was created in the first place.

*   If you can find the company OAB root folder, confirm that the OAB Version folders are there and that the contents are there. You can do that by using MFCMAPI, OWA or the Exchange Management Console.
*   HmOABUpdate application isn't working because it was not setup properly. This could be due to permission not properly setup, problem connecting to the SQL server and etc. I would first review the HMOBUpdate log to find out what's going on to get started.
*   Actual OAB Generation Issues, this could be due to various reasons. I will try some of the steps in this article to start with, How to troubleshoot the OAB Generation process,[http://blogs.msdn.com/dgoldman/archive/2005/07/16/How-to-troubleshoot-the-OAB-Generation-process.aspx](http://blogs.msdn.com/dgoldman/archive/2005/07/16/How-to-troubleshoot-the-OAB-Generation-process.aspx). Turning up the Diagnostic logging will usually lead you to the source of the problem.
If you can confirm that the OAB is there (that is it has been properly generated) and that the hierarchy has been properly replicated, then you problem will be more on locating the OABs.

<span style="text-decoration: underline;">(2) Locating OABs</span>

I will start with this, Outlook 2003 and 2007 clients receive error 0x8004010f when downloading the Offline Address Book
[http://blogs.msdn.com/dgoldman/archive/2007/04/26/outlook-clients-receive-error-0x8004010f-when-downloading-the-offline-address-book.aspx](http://blogs.msdn.com/dgoldman/archive/2007/04/26/outlook-clients-receive-error-0x8004010f-when-downloading-the-offline-address-book.aspx)

In addition to this, I also like to take note that Outlook 2007 uses Autodiscover to find out where the OAB is. If the Autodiscover isn't working well, Outlook 2007 may get the same common 0x8004010f error as well. So, if you can't download OAB using Outlook 2007 but you can do it through Outlook 2003, I would start looking at Autodiscover.

I will also cover how Autodiscover work in the HMC environment in part #5.

</div>