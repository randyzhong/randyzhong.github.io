---
title: MSCRM 4.0 Email Router - 404 not Found
tags:
  - MSCRM
categories:
  - Microsoft
  - MSCRM
date: 2009-08-20 15:19:02
---

Here is a good article regarding to CRM 4.0 Email Router issues:

[http://blogs.msdn.com/crm/archive/2009/01/08/troubleshooting-the-microsoft-dynamics-crm-e-mail-router.aspx](http://blogs.msdn.com/crm/archive/2009/01/08/troubleshooting-the-microsoft-dynamics-crm-e-mail-router.aspx)

But what I encounterd is not listed.

**If you have an error type HTTP 404 (Not Found)**

The E-mail Router Configuration Manager was unable to retrieve user and queue information from the Dyamics CRM server. Verify the url 'http://servername:5555/orgname'  is correct. The request failed with HTTP status 404:**Not Found**."

Workaround:

Check your CRM deployment properties, make sure these 2 record are not "LocalHost", better use its netbios servername, ip address or FQDN.

**AD App Root Domain, ****AD SDK Root Domain**

How to check:
```sql
SELECT Id, ColumnName, NvarCharColumn From DeploymentProperties
WHERE ColumnName IN ('ADSdkRootDomain','ADWebApplicationRootDomain')
```
Post tasks:

1. Restart IIS.

2. Restart Microsoft Dynamics CRM Asynchronous Processing Services.