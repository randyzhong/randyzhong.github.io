---
title: HMC 4.5 UR 2 has been released.
tags:
  - HMC
  - Rollup
categories:
  - Microsoft
  - HMC
date: 2009-02-18 22:53:47
---

**Description of Update Rollup 2 for Hosted Messaging and Collaboration 4.5**

[http://support.microsoft.com/?id=957584](http://support.microsoft.com/?id=957584)

### Issues that the update rollup fixes

This update rollup fixes the following issues:

[957583](http://support.microsoft.com/kb/957583/) Description of Update Rollup 1 for Hosted Messaging and Collaboration 4.5

[957003 ](http://support.microsoft.com/kb/957003/) The RenameUser method does not change the samAccountName attribute in Solution for Hosted Messaging and Collaboration 4.0


This update rollup fixes the following issues that were not previously documented in a Microsoft Knowledge Base article:

*   You receive the following failure exception when you attempt to create a Resource Mailbox:
* The recipient '&lt;UPN of Recipient&gt;' does not have access permission to the specified resource mailbox.
*   The ChangeOrganizationPlan
*   Assignment procedure in the Hosted Email 2007 Namespace fails with an error message that resembles the following:
Unable to create 'cn=FolderAdmins@Hostedorg...', object already exists

This error may occur if the hosted organization uses Public Folders.
*   The CreateMailbox procedure in the Hosted Email 2007 Namespace fails with an error message that resembles the following:

`E_BLOCKMODEL_NO_RANKED_RESOURCES_FOUND`

*   The NewUMDialPlan procedure in the Managed Unified Messaging 2007 Namespace restricts the value of the **NumberOfDigitsInExtension** parameter to the range of 3 to 7\. The correct range is 1 to 20.
*   A failure in a CreateMailbox procedure may result in inaccurate data in Exchange Resource Manager.
*   The SmtpDomainCache task logs a success (0x0) status in Scheduled Tasks even if it fails.
*   Some of the memory that the Exchange 2007 Provider consumes is not released as quickly as it should be.
*   The health of a server may not be accurately updated by the Hosted Service Monitor procedures.