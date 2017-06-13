---
title: Patches for HMC4.0
tags:
  - HMC
  - Patch
id: 73
categories:
  - HMC
  - Hosting
date: 2008-09-21 10:44:35
---

There are many updates released after HMC4.0 first release version, here is a simple list:
Main rollup update:

1.  Update Rollup 1 for the Hosted SharePoint services
2.  Update Rollup 2 for the Hosted Exchange services
3.  Update Rollup 3 for the Hosted Exchange services (included Rollup2)
4.  Update Rollup 4 for the Hosted Exchange services (included Rollup3)
<!--more-->

Others not included in the rollups above:
<div id="more">

*   [954965](http://support.microsoft.com/kb/954965/en-us) Event ID 50103 is logged in the Application log when you use the Exchange Data Collection tool in Microsoft Solution for Hosted Messaging and Collaboration 4.0
*   [943728](http://support.microsoft.com/kb/943728/en-us) The SMTP Mail Provider does not send usernames or passwords in Microsoft Solution for Hosted Messaging and Collaboration 4.0
*   [951052](http://support.microsoft.com/kb/951052/en-us) During high provisioning activity in Microsoft Solution for Hosted Messaging and Collaboration 4.0, a request may fail
*   [949708](http://support.microsoft.com/kb/949708/en-us) Error message when you call the RenameUser procedure in Hosted Messaging and Collaboration 4.0: "There is no such object on the server"
*   [939216](http://support.microsoft.com/kb/939216/en-us) Some provisioning requests do not work, and you receive a "0xc220140b" error message after you install Windows Server 2003 Service Pack 2 on a server that is running Microsoft Solution for Hosted Messaging and Collaboration
*   [952089](http://support.microsoft.com/kb/952089/en-us) The Provisioning Engine COM+ component stops responding in Hosted Messaging and Collaboration 4.0
*   [945630](http://support.microsoft.com/kb/945630/en-us) Passwords are not updated in the destination directory after you configure Inbound Automatic Synchronization in Solution for Hosted Messaging and Collaboration 4.0
*   [954150](http://support.microsoft.com/kb/954150/en-us) You receive an error response when you call the "EnsureServicePointers" procedure of the ManagedSharePoint2007 namespace in Hosted Messaging and Collaboration 4.0
*   [943864](http://support.microsoft.com/kb/943864/en-us) Hosted users can see other hosted users if they can access the HMC Active Directory by using LDAP tools in Microsoft Solution for Hosted Messaging and Collaboration version 4.0
*   [945629](http://support.microsoft.com/kb/945629/en-us) How to increase the maximum number of offline address books on an offline address book server to 10,000 in Solution for Hosted Messaging and Collaboration version 4.0
*   [947005](http://support.microsoft.com/kb/947005/en-us) You cannot rebuild or replicate offline address books in Hosted Messaging and Collaboration 4.0 Migration Kit
*   [943792](http://support.microsoft.com/kb/943792/en-us) The ManagedPlans::GetAssetsByCustomer procedure returns only one result when multiple results are expected in Solution for Hosted Messaging and Collaboration 4.0
*   [950095](http://support.microsoft.com/kb/950095/en-us) Memory usage increases unexpectedly in high-stress situations when you continuously call Provisioning Web Services to do duplicate tasks in Hosted Messaging and Collaboration 4.0
*   [939560](http://support.microsoft.com/kb/939560/en-us) Users cannot download offline address books in Exchange 2007 when you use Microsoft Solution for Hosted Messaging and Collaboration version 4.0
*   [936159](http://support.microsoft.com/kb/936159/en-us) A newly created recipient may not receive e-mail messages for up to eight hours when you use an Exchange 2007 Edge Transport server in a Microsoft Solution for Hosted Messaging and Collaboration version 4.0 environment
**[Update Rollup 1 for the Hosted SharePoint services](http://support.microsoft.com/kb/952075/en-us) - June 30, 2008 v2.0**
[943837](http://support.microsoft.com/kb/943837/) Description of the update for Hosted Messaging and Collaboration 4.0 for Windows SharePoint Services provisioning components
[939030](http://support.microsoft.com/kb/939030/) You receive an error message from the SharePoint 2007 provider when you try to provision a Windows SharePoint Services 3.0 site by using HMC 4.0
[949629](http://support.microsoft.com/kb/949629/) The Peoplepicker function may not be fully implemented when you use Windows SharePoint Services 3.0 Service Pack 1 together with Microsoft Solution for Hosted Messaging and Collaboration (HMC) 4.0
[946516](http://support.microsoft.com/kb/946516/) Null values may be returned for the Web parameter or for the site parameter after you install update 943837 in Microsoft Solution for Hosted Messaging and Collaboration 4.0

This update rollup also fixes the following issues that were not previously documented in a Microsoft Knowledge Base article:
•    The **GetRoles** method in the Hosted SharePoint 2007 Web services does not return a list of roles.
•    You cannot modify the following site properties:
**•    Title
•    HostHeaderIsSiteName
•    URL
•    Target
•    Theme
**Note This update rollup provides a **ModifyCustomerSite** procedure in the provider namespaces for Managed SharePoint 2007 Namespace, for Hosted SharePoint 2007 Namespace, and for SharePoint 2007 Provider.
•    You receive an error message if you try to create a site that is larger than 4 GB.
•    The **GetHostingOUPath_** procedure in the Managed Helpers namespace does not exist.
**[Update Rollup 2 for the Hosted Exchange services](http://support.microsoft.com/kb/948755/en-us) - April 30, 2008 v3.0
**This update rollup fixes the following issues:
•    [942100](http://support.microsoft.com/kb/942100/) Description of the update for Exchange Server components in Microsoft Solution for Hosted Messaging and Collaboration 4.0
•    [946495](http://support.microsoft.com/kb/946495/) You cannot set the MaxInactivityTimeDeviceLock parameter in Microsoft Solution for Hosted Messaging and Collaboration 4.0

This update rollup fixes the following issues that were not previously documented in a Microsoft Knowledge Base article:
•    The **ReallocateMailbox** method in the Exchange 2007 Resource Manager Web services cannot be used to reduce a mailbox's allocation.
•    The new **CreateResourceMailbox** procedure in the Hosted Email 2007 namespace can illegally accept recipients in another hosted organization. Additionally, the CreateResourceMailbox procedure may not make mailboxes visible to users in the organization.
•    The **ModifyResourceMailbox** procedure in the Hosted Email 2007 namespace incorrectly accepts an e-mail address that does not exist in the environment.
•    The **CreateAddressList** procedure in the Exchange 2007 Provider namespace may return an error if the first MAPI-enabled user is created within several seconds of the organization being created and if there is more than one domain controller in the environment.

**[Update Rollup 3 for the Hosted Exchange services](http://support.microsoft.com/kb/952076/en-us) - May 14, 2008 v1.1**
This update rollup includes the fixes from the following update rollup:
•    [948755](http://support.microsoft.com/kb/948755/) Description of **Update Rollup 2** for the Hosted Exchange services in Microsoft Solution for Hosted Messaging and Collaboration 4.0

This update rollup also fixes the following issues that were not previously documented in a Microsoft Knowledge Base article:
•    The **DeleteMailbox** procedure in the Hosted Email 2007 namespace may fail. You may receive an "A procedure tried to release more resources than are currently allocated to a consumer" error message or a "No allocations were found to update" error message. These errors are caused by a mismatch in the method that the Hosted Email 2007 procedures and the Exchange Resource Manager 2007 procedures use to calculate allocations.
•    By default, all mailboxes that are created have the **ActiveSync** feature enabled. You cannot create a plan in which the ActiveSync feature is disabled.

Note After you install this update, you can use the **ActiveSyncEnabled** parameter to enable or disable the ActiveSync feature.
•    The SetCASMailbox procedure in the Exchange 2007 Provider namespace does not let you control the following new Outlook Web Access features in Exchange 2007 Service Pack 1 (SP1):
•    OWAPublicFoldersEnabled
•    OWARecoverDeletedItemsEnabled
•    OWARulesEnabled
•    OWASMimeEnabled

**[Update Rollup 4 for the Hosted Exchange services](http://support.microsoft.com/kb/952077/en-us) - July 21, 2008 v1.0
**• [952076](http://support.microsoft.com/kb/952076/) Description of **Update Rollup 3** for the Hosted Exchange services in Microsoft Solution for Hosted Messaging and Collaboration 4.0

This update rollup also fixes the following issues that were not previously documented in a Microsoft Knowledge Base article:
•    When an organization owns more than one SMTP domain, Microsoft Exchange ActiveSync mailbox policies may not be applied.
•    When you use the SetUserPolicy procedure in the Hosted Mobility 2007 namespace, a user's **showInAddressBook** attribute may be cleared. When this occurs, the user is removed from address lists.
•    In an environment in which lots of new users are being created in quick succession, a failure may occur. This failure indicates that you tried to create a user by using a proxy address that already exists for another user.
•    The GetSMTPDomain procedure in the Microsoft Exchange Server 2007 provider may fail, and you may receive an "Out Of Memory" error message.
Additionally, this update rollup includes the following tools:
•    A tool to deny Send As and Receive As rights to administrators and to customer administrators
•    A tool to modify the names of existing mobile policies

</div>