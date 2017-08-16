---
title: HMC Resource Management - Part 1 - PlanManager and ResourceManager - Overall Concept
tags:
  - HMC
categories:
  - Microsoft
  - HMC
date: 2009-10-25 12:07:28
---

This article was original posted by Kip.Ng on http://blogs.technet.com/provtest/archive/2009/07/20/planmanager-and-resourcemanager-part-1.aspx

I believe one of the most mysterious parts of HMC is about the MPS engine and the Resource Management databases. Between last December and January, I blogged about how different products (Exchange, SharePoint and OCS) integrate in a HMC Solution. However, understanding just the integration points without knowing the resource management portion definitely does not paint a complete picture of what HMC is.

Since then, I have received requests to discuss a bit about the resource management in HMC and what those databases that shipped with HMC are storing and being used. So, in my next few entries, I intend to spend some time talking about the following,

1. PlanManager and ResourceManager - Overall Concept
2. HMC Resource Management and Active Directory
3. HMC Resource Management and Exchange
4. HMC Resource Management and Windows SharePoint Services
5.  HMC Resource Management and Office Communication Servers

The aim for this is not to go crazily in-depth nor is it meant to discuss the full database schema and etc. I realize most of our HMC administrators come from an infrastructure background (in fact, mostly from AD and Exchange) and find it difficult to manage the environment (or sometime even fearful to do anything) because there has been very little about how those databases work in the HMC environment.

My objective is to help the HMC administrators to better understand the relationship and hence able to better plan for recovery, database integrity checking, have better process on how to troubleshoot and handling issues and not blindly go into a HMC environment doing things that may potentially mess up the environment. For example, moving mailbox from one database to another using cmdlets, reconnecting a mailbox to another user, re-enabling sip user and etc.

Let's get started. This introduction is going to be something extremely light. I want to talk briefly about the purposes of these databases and what things are being stored in it on a very high level.

When you install HMC 4.5, it creates 5 databases. They are MPFConfig, MPFAudit, MPFTranLogData, PlanManager and ResourceManager databases.

The first 3 are used by the MPF engine and I will talk about that some other time as there are tons to talk about too on those. Some of those databases can be used to better analyze the history of all the transactions, used for better performance analysis and also to configure some advance configurations not shown in the interface.

For now, we will discuss about the last 2 databases, PlanManager and ResourceManager.

**PlanManager Database**

PlanManager used to be called HeCustomerDB in HMC 3.5\. While, the concept hasn't changed much since HMC 3.5, I am going to refer to just the PlanManager which is what HMC 4.5 is using today. There is obviously a big change from HMC 3.5 to HMC 4.5 such as introduction of organization and user plans in HMC 4.5 and also support of different version of the hosted products. So those have resulted in additional categories and features and etc.

PlanManager stores more than just plans (I personally think it should be named something else). On a high level, it has the following,

1.  Active Directory Object reference. It consists of all the references to all the objects created in AD. Such as, OUs, Users, Contacts, Groups, SMTP domains. Something that I will go into greater depth in when I discuss about the Resource Management and Active Directory.
2. Mailbox assignment. It also consists of information of mailbox GUID linkage to the respective AD objects. I will also discuss that further in the Resource Management and Exchange later.
3. Plans information. It consists of all the plan information, including all the features of each plan. For example what mail feature or quota is tied to which plan. Different product tends to be slightly different and I will discuss that accordingly when I discuss about different products.
4. Plan Assignment. It also consists of what plans are those organizations assigned to.
 
I think it should also be noted that you can further 'extend' the capability of the database yourself. Such as you can add additional feature, add in additional customer type or asset type and etc. which your control panel can use if needed.

**ResourceManager Database**

ResourceManager database right now is primarily used by Hosted Exchange only. The complexity and the need of tracking the resource allocation for individual organization, for public folder, for OAB, for individual mailbox user, for different mailbox store created has created the need to track that on a separate database.

As I walk through different products in the next few entries, you may realize that WSS and OCS are not using the ResourceManager database as they are less complex in terms of the needs for resource allocation and management.

ResourceManager, on a high level has the following,

1. Mailbox Stores and their sizes. It consists of all the mailbox stores and their available size.
2. Public Folder Stores and their sizes.
3. OAB Servers.
4. Public Folder allocation for each organization.
5. Organization's OAB assignment
6. Organizations and their allocated size.
7. Organizations and their assigned mail stores.
8. Individual mailbox and the mailbox size assigned to them.

Now, I should also highlight that the reference to AD objects are often using GUID and I also blogged about this earlier this month ([http://blogs.technet.com/provtest/archive/2009/07/15/matching-the-guid-from-ad-with-mps.aspx)](http://blogs.technet.com/provtest/archive/2009/07/15/matching-the-guid-from-ad-with-mps.aspx)

There you go; this is a simple overview of the 2 mysterious databases that has so often make HMC looks more complicated than it really is.  I believe once you understand what's in the databases, you will find that it isn't that bad at all.