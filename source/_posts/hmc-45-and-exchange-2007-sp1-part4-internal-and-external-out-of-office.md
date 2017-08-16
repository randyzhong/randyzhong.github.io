---
title: HMC 4.5 and Exchange 2007 SP1 - Part 4 Internal and External Out of Office
tags:
  - Exchange
  - HMC
categories:
  - Microsoft
  - HMC
date: 2009-05-07 18:50:18
---

转自 https://blogs.technet.microsoft.com/provtest/2008/12/26/hmc-4-5-and-exchange-2007-sp1-part-4-internal-and-external-out-of-office/ 很好的文章,贴过来收藏,hehe.

***

**Internal and External OOF in HMC Environment**

So, the 4th main customization that HMC introduces to Exchange 2007 is the OOF Transport Agent. What is OOF? Here is the text book definition, "The Out-of-Office (OOF) feature is commonly used by end-users to let other people know when they are not available to respond to e-mail." Now, I don't intend to go into the working of OOF in Exchange 2007 because I think there are better articles out there that talk about it.

Here, I want to briefly explain how Exchange 2007 OOF, out of the box isn't really working complete in a HMC environment and how we make it work.

**What's not working, really?**

Exchange 2007 introduces the concept of Internal and External OOF. This is something that a lot of our customers have been waiting for. Now, we all know Exchange 2007 wasn't packaged with multi-tenancy enabled out of the box. The problem is this, the identification of what is considered internal and what is external.

If a mail is sent from one user to another (even though they are of different tenants) within the same Exchange Organization, it is considered internal. In another word, a hoster may hold both company A and company B, however, Exchange 2007 believes that both company A and company B are internal, and hence company A will never use External OOF to company B. This breaks the concept of internal and external OOF notification as in Exchange 2007 in HMC environment.

For example,

1.  Hoster A has a HMC environment that hosts both company A and company B.
2.  John from company A set an internal and external OOF. The internal OOF is only intended for users in company A.
3.  Alice from company B sends an e-mail to John from company A. Instead of getting John's external OOF, she got his internal OOF.
**How do we make it work then?**

To support Out of Office (OOF) feature for tenants among different organizations within the same Windows domain, HMC introduces a customized OOF transport agent on all Exchange Hub Transport servers. As highlighted over here ([http://technet.microsoft.com/en-us/library/cc545931.aspx](http://technet.microsoft.com/en-us/library/cc545931.aspx)), the OOF agent includes two utilities:

*   **Categorizer Override Agent**: A transport agent to override the categorizer behavior on Hub Transport servers. This agent sits in Submission Queue and to be more specific, OnSubmittedMessage in the Exchange 2007 Transport pipeline.

*   **SMTP Domain Cache Task**: A scheduled task to generate cache files for categorizer override agent to filter the OOF messages. Based on the deployment document, the interval recommended is 60 minutes.
So, how do these utilities work? It is quite simple as a matter of fact. Let's talk about the Categorizer Override Agent first. When a mail comes into the Hub Transport server, it hits the OnSubmittedMessage; it fires up the Categorizer Override Agent. The agent checks to see if the mail is sent using an external OOF template, if yes, then it expands the recipients to check and <span style="text-decoration: underline;">bypass</span> suppression of the external OOF message for external and inter-company recipients.

Next it checks to see if it is an internal OOF template, if it is, it will then expand the recipients and suppress the internal OOF message for external and inter-company recipients.

For performance reason, that's where the SMTP Domain cache task comes in. The task read from the MPS SQL server for all the domains that have been provisioned and generate a .cache file with all the domains hosted by the environment periodically.

This file is used by the categorizer override agent to find out if it is part of this Exchange organization. Having it cached as a local text file, is obviously a big performance gain compared to going to Active Directory to make the query every time especially if the environment has large number of hosted domains.

So, there you go, with the above, it overrides the logic of Internal and External OOF when dealing with different companies in the same Exchange organization.

The OOF agent was first introduced in HMC 4.5 and Microsoft has also back ported this agent to HMC 4.0 environment, ([http://blogs.technet.com/provtest/archive/2008/11/19/oof-agent-is-now-available-in-hmc-4-0.aspx](http://blogs.technet.com/provtest/archive/2008/11/19/oof-agent-is-now-available-in-hmc-4-0.aspx))
**What are some of the usual problems that you may face while installing this OOF agent?**

The installation of the OOF agent is clearly documented [here](http://technet.microsoft.com/en-us/library/cc545931.aspx "Install OOF Aent"). However, there are a few areas that you may encounter issues,

The OOF agent included in the deployment ISO is a 32-bit agent. Hence, the default path, instead of the documented, C:\Program Files\Microsoft\Exchange Server\TransportRoles\Agents\CategorizerOverrideAgent, it will be C:\Program Files (x86)\Microsoft\Exchange Server\TransportRoles\Agents\CategorizerOverrideAgent instead.

Another thing you should know is that, the Microsoft Exchange Transport service runs as a NT_AUTHORITY\NetworkService account. Hence, you need to make sure whichever directory that you are installing this agent in, the NT_AUTHORITY\NetworkService must have permission to it.

Also, it is my recommendation to Install HMC 4.5 Rollup 1 instead as this rollup includes a 64-bit binaries.

Another common mistake that most people may have is not setting the path correctly in SmtpDomainCacheTask.exe.config file. A simple mistake there may, like removing a '&amp;' may result in failure with strange error.

**More about OOF?**

In case you want to know more about the working of OOF, here are 2 articles that you may want to read,

Exchange Server 2007 Out of Office (OOF)
[http://msexchangeteam.com/archive/2006/10/06/429115.aspx](http://msexchangeteam.com/archive/2006/10/06/429115.aspx)

Legacy client and Out of Office (OOF) interoperability
[http://msexchangeteam.com/archive/2007/04/04/437544.aspx](http://msexchangeteam.com/archive/2007/04/04/437544.aspx)