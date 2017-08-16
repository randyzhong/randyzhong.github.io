---
title: How to add Send On Behalf permission to users with MPS
tags:
  - HMC
categories:
  - Microsoft
  - HMC
date: 2008-11-26 01:11:10
---

Using **Hosted Active Directory::ModifyUser** to set **publicDelegates **property can do this.  
Sample:
```xml
<request xmlns:xsl="http://www.w3.org/1999/XSL/Transform"><procedure xmlns:xsl='http://www.w3.org/1999/XSL/Transform'>
<execute namespace="Hosted Active Directory" procedure=
"ModifyUser" impersonate="1">
<executeData>
<path>LDAP://CN=jack@litware.com,OU=litware.com,OU=OrgA,OU=Hosting,DC=fabrikam,DC=com</path>
<properties>
<property name="publicDelegates">
<value>mike@litware.com,OU=litware.com,OU=OrgA,OU=Hosting,DC=fabrikam,DC=com</value>
</property>
</properties>
<preferredDomainController>ad01.fabrikam.com</preferredDomainController>
</executeData>
</execute>
</procedure>
</request>
```