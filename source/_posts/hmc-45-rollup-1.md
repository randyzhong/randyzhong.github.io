---
title: HMC 4.5 Rollup 1
tags:
  - HMC
  - Rollup
categories:
  - Microsoft
  - HMC
date: 2008-11-20 21:31:10
---

[http://support.microsoft.com/kb/957583](http://support.microsoft.com/kb/957583)

Microsoft has released an update that is dated October 8, 2008\. This update fixes issues in the following components in Microsoft Solution for Hosted Messaging and Collaboration 4.5.

*   ActiveDirectoryProvider.msi
*   Exchange2007Provider.msi
*   DNSProvider.msi
*   HostedActiveDirectoryNS.msi
*   HostedActiveDirectoryWS.msi
*   RPCHttpConfigTool.msi
*   pwdb40ssis.msi
*   DNSClient.msi

Additionally, this update also includes native 64-bit versions of the following components:

*   CategorizerOverrideAgent-x64.msi
*   SmtpDomainCacheTask-x64.msi

### Issues that the update rollup fixes

This update rollup fixes the following issues that were not previously documented in a Microsoft Knowledge Base article:

*   When more than one user has the same common name (CN) attribute, a server error is returned to the end-user from the RPCHTTPConfig Web site.
*   An OutOfMemoryException exception is returned from the Exchange2007Provider component when you run a **Get-AcceptedDomain** command.
*   Multiple issues occur with the Microsoft Exchange 2007 Out Of Office (OOF) agent.
*   When you run an "Import Data from PlanManager to PWDB40" SQL agent job, some error messages are logged on the Log File viewer.
*   When you try to create a TXT record by using the HMC DNS Provider, the HMC DNS Provider does not pass the **DescriptiveText** property that is required to create a Microsoft DNS_TXT Type..
*   The Hosted Active Directory Web Service could not set the &lt;properties&gt; element of the CreateUser component through the Web Service class library.
*   A Hosted Active Directory Web Service exception is thrown when the process reads the response of the CreateIIWResellerOrganization component by using the Web service proxy class.