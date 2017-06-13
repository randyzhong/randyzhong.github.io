---
title: HMC 4.5 Update Rollup 5 has been released
tags:
  - Exchange
  - HMC
  - Rollup
id: 130
categories:
  - HMC
  - Hosting
date: 2010-03-02 19:06:18
---

Description of Update Rollup 5 for Hosted Messaging and Collaboration 4.5

[http://support.microsoft.com/kb/977174/en-us](http://support.microsoft.com/kb/977174/en-us "http://support.microsoft.com/kb/977174/en-us")

Microsoft has released an update that is dated October 30, 2009\. This update fixes issues in the following components in Microsoft Solution for Hosted Messaging and Collaboration (HMC) 4.5 and includes all fixes released in previous rollup packages RU1, RU2, RU3 andRU4.

*   ResourceManageDatabase.msi
*   Exchange2007ResourceManagerWS.msi
*   HostedSharePoint2007WS.msi
*   Exchange2007ResourceManagerNS.msi
*   HostedSharePoint2007NS.msi
<!--more-->
<div id="more">This update rollup fixes the following issues that were not previously documented in a Microsoft Knowledge Base article:

*   The SetUMServer procedure in the Unified Messaging 2007 Provider may fail if you specify more than one value in the dialPlans property (it only fails if at least one of the dialPlan values contains a space character.
*   The DeleteResourceRecord procedure in the DNS Provider may run for a long time or time out if there arelots of records in the zone.
*   The GetDNSZone procedure in the DNS Providerreturns the zone properties only The GetDNSZone procedure does not return the Resource Records.
*   The Active Directory Collector job may throw an exception if it finds lots of records to return. This might occur if for example, there are lots of new entries in Active Directory since the PWDB was last updated.
*   The CreateUser procedure in the Hosted Active Directory namespace may return the error " there is no such object on the server" if the Organization is renamed.
*   Documentation Issue: In the deployment walkthrough for Server 2008 the procedure DW08-DWHE.70 states an incorrect autodiscovery URL.
*   SharePoint Service Pointer in AD can be orphaned if a Site Collection Administrator deletes a site or sub-site from the SharePoint Administration Interface instead of using MPS.
*   Exchange2007Resource Manager: Excessive Run Times When Calling FindAutoAllocateOrgMailStores_ With a Large Number of Mail Stores.
*   Exchange2007ResourceManager:Allocate cannot use level algorithm for the first mailbox as specified in the organization Plan.
*   Exchange2007ResourceManager:ReallocateOrganizationMailNoMove cannot reallocate organization correctly when you use the 'level' algorithm for 'not shared' mail stores.
*   Exchange2007ResourceManager:AllocateOrganization fail when you use optional parameter 'shared.'
</div>