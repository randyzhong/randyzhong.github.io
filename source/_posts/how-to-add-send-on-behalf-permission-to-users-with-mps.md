---
title: How to add Send On Behalf permission to users with MPS
tags:
  - MPS
id: 71
categories:
  - HMC
  - Hosting
date: 2008-11-26 01:11:10
---

Using **Hosted Active Directory::ModifyUser** to set **publicDelegates **property can do this.  Sample:
<pre class="lang:xhtml decode:true ">&lt;request xmlns:xsl="http://www.w3.org/1999/XSL/Transform"&gt;
&lt;procedure xmlns:xsl='http://www.w3.org/1999/XSL/Transform'&gt;
&lt;execute namespace="Hosted Active Directory" procedure=
"ModifyUser" impersonate="1"&gt;
&lt;executeData&gt;
&lt;path&gt;LDAP://CN=jack@litware.com,OU=litware.com,OU=OrgA,OU=Hosting,DC=fabrikam,DC=com&lt;/path&gt;
&lt;properties&gt;
&lt;property name="publicDelegates"&gt;
&lt;value&gt;mike@litware.com,OU=litware.com,OU=OrgA,OU=Hosting,DC=fabrikam,DC=com&lt;/value&gt;
&lt;/property&gt;
&lt;/properties&gt;
&lt;preferredDomainController&gt;ad01.fabrikam.com
&lt;/preferredDomainController&gt;
&lt;/executeData&gt;
&lt;/execute&gt;
&lt;/procedure&gt;
&lt;/request&gt;</pre>
&nbsp;