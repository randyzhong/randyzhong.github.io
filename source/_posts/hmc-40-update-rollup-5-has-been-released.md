---
title: HMC 4.0 Update Rollup 5 has been released
tags:
  - HMC
  - Rollup
categories:
  - Microsoft
  - HMC
date: 2009-05-27 12:46:53
---

This has been released a while ago but in case you missed this.

[http://support.microsoft.com/?id=961991](http://support.microsoft.com/?id=961991)

Please take note that, this hotfix, although it is named as an rollup, it does not include RU4\. Hence, you will need to first deploy RU4 before you deploy this hotfix.

This update rollup fixes the following issues that were not previously documented in a Microsoft Knowledge Base article:

*   An exception is thrown when a new user mailbox is created: An arithmetic overflow occurred, the resource capacity specified was too large.This issue occurs if the ResourceManager Allocation Algorithm is set to **dynamic**, and if the mailstore that is selected has insufficient free space.
*   OAB generation fails if the Domain Controller that is identified in the config file becomes unavailable. This fix removes the setting from the config file and adds automatic Domain Controller discovery to the functionality of the HeOABUpdate.exe tool.
*   This update rollup updates SharePoint Web Service code to catch SPException errors when one content database is down or is failed over to another node in the cluster. In this case, all other site collections in other content databases can still be access by the provisioning system.
*   The Microsoft Provisioning Server (MPS) DNS provider client does not run on a 64-bit operating system.
*   If a Mailbox Store on an Exchange back-end server is unavailable, you cannot set only this store to **not provisionable** in Resource Manager. Only the complete server can be disabled.
*   A memory leak occurs in the AddCustomerSite and ModifyCustomerSite procedures. When you create and modify many SharePoint Sites by using MPS, the SharePoint2007 Provider develops a slow memory leak that slowly depletes resources on the MPS Engine Server.
*   When you move a mailbox by using the MoveMailbox API on the Exchange 2007 provider, the ShowInAddressBook property can be reset to the Exchange default values. This can prevent the user from seeing the global address list (GAL) or the Offline AddressBook of the organization.