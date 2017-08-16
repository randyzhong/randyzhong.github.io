---
title: KB974571 Crypto-API Update may break Office Communications Server 2007 R2 installations
tags:
  - Communicator
categories:
  - 沟通协作
date: 2009-10-15 12:15:20
---

Security updates are important. And as we're currently an evaluation setup for OCS 2007 R2, i've decided to install todays batch of security updates on these lesser important machines first. And after a reboot, OCS 2007 R2 Front End Service is not running anymore.

Below are error message from event log:
> Event source: OCS Server
> Event id: 12290
> Event text: The evaluation period for Microsoft Office Communications Server 2007 R2 has expired. Please upgrade from the evaluation version to the full released version of the product.

check this [article](http://blogs.technet.com/dodeitte/archive/2009/10/13/do-not-apply-kb974571-to-lcs-ocs-servers.aspx)
Uninstall KB [974571 ](http://support.microsoft.com/kb/974571/ "http://support.microsoft.com/kb/974571/")and OCS works again.