---
title: HMC 4.5 Update Rollup 6 has been released
tags:
  - HMC
categories:
  - Microsoft
  - HMC
date: 2010-03-02 19:10:50
---

Description of Update Rollup 6 for Hosted Messaging and Collaboration 4.5

[http://support.microsoft.com/kb/979704](http://support.microsoft.com/kb/979704)

Microsoft has released an update that is dated December 2009\. This update fixes issues in the following components in Microsoft Solution for Hosted Messaging and Collaboration 4.5.

*   Exchange 2007 Resource Manager Namespace
*   Exchange 2007 Resource Manager Web Services
*   Hosted Email 2007 Namespace
*   Hosted Email 2007 Web Services
*   Managed Email 2007 Namespace
*   Managed Email 2007 Web Services
*   Exchange 2007 Mobility Provider
*   Exchange 2007 Provider
*   Unified Messaging 2007 Provider
Additionally, this update also includes native 64-bit versions of the following components:

*   Exchange 2007 Mobility Provider
**In order to get Exchange 2007 SP2 supported, do install this UR.**

This update rollup fixes the following issues that were not previously documented in a Microsoft Knowledge Base article:

*   When a folder is created, the value of folder allocation is not consistent with StorageQuota calculation result.
*   When a public folder is created by using the "-StorageQuota" and "-postStorageQuota", the operation fail.This is because the two parameters was replaced by the parameters "-IssueWarningQuota" and "-ProhibitPostQuota" in the Exchange SP2.
*   An overflow exception occurs when allocating 2Terra bytes or above mailbox space to an organization. This is because the maximum mailbox space that can be allocated to an organization lower than 2Terra bytes.
*   If an organization allocated a "not shared" mailstore, it cannot reallocate on the same mailstore.

#### Prerequisites

*   Hosted Messaging and Collaboration version 4.5 must be installed before you apply this update.
*   The logon user should be a member of the Domain Admins Group.
*   The HMC Deployment Tool must be installed on the first Microsoft Provisioning Server (MPS) Engine server.
*   **Microsoft Exchange Server 2007 Service Pack 2 must be installed before you apply this update.**
Hotfix replacement information
This rollup replaces the previously released RU1, RU2, RU3, RU4 and RU5.