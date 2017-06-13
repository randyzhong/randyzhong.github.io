---
title: >-
  HMC Resource Management - Part #2 - HMC Resource Management and Active
  Directory
tags:
  - HMC
  - Resource Management
id: 140
categories:
  - HMC
  - Hosting
date: 2009-10-25 12:37:24
---

<span style="font-family: Calibri; font-size: medium;">Things have been busy for me and i haven't had the time to complete this series in a timely fashion. In </span>[<span style="font-family: Calibri; font-size: medium;">part #1</span>](http://blogs.technet.com/provtest/archive/2009/07/20/planmanager-and-resourcemanager-part-1.aspx)<span style="font-size: medium;"><span style="font-family: Calibri;">, we briefly talked about what's in the PlanManager and ResourceManager databases from a very high level. In the next few parts, we will go in a little bit further to discuss how HMC manage the resource allocation in Exchange, SharePoint and also OCS.</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">Before we get into that, I think it is essential to talk about how HMC database, PlanManager is linked with the Active Directory. It is important to understand that it isn't the purpose of this article to discuss about the database schema but I will discuss briefly how they are linked.</span></span>
<div>

<span style="font-size: medium;"><span style="font-family: Calibri;">This article was original posted by Kip.Ng on [http://blogs.technet.com/provtest/archive/2009/09/28/hmc-resource-management-part-2-hmc-resource-management-and-active-directory.aspx](http://blogs.technet.com/provtest/archive/2009/09/28/hmc-resource-management-part-2-hmc-resource-management-and-active-directory.aspx "http://blogs.technet.com/provtest/archive/2009/09/28/hmc-resource-management-part-2-hmc-resource-management-and-active-directory.aspx")
</span></span>

<!--more-->

<span style="font-size: medium;"><span style="font-family: Calibri;">The best way to explain this, in my humble opinion is to walk through the provisioning process. As we walk through them, I will explain how the data is being stored in the database and then I will provide some examples and discuss about some of the tables and their purpose.</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">As you all know, the first thing you need to do before mailbox enable a user, or enabling the user for OCS or creating the appropriate SharePoint site for the organization is to create the organization itself and the appropriate users for that organization. In the HMC world, an Organization means a company and all the users and groups in that organization are grouped under an Active Directory container or Organizational Unit (OU).</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">So, let's take a closer look and see what is being inserted into the database. Note:</span></span>

**<span style="font-size: medium;"><span style="font-family: Calibri;">Creating Organization</span></span>**

_<span style="font-size: medium;"><span style="font-family: Calibri;">Procedure: To create an organization, you called Hosted Active Directory::CreateOrganization.</span></span>_

1.  <span style="font-family: Calibri; font-size: medium;">1.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">What is created in Active Directory? When this procedure is called,</span></span>

    1.  <span style="font-family: Calibri; font-size: medium;">a.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">It creates the Organization OU container in the Hosting container.</span></span>
    2.  <span style="font-family: Calibri; font-size: medium;">b.</span>      <span style="font-size: medium;"><span style="font-family: Calibri;">it will also create some of the system container and objects such as,</span></span>
<span style="font-size: medium;"><span style="font-family: Calibri;">_Private</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">Allusers@&lt;org&gt;</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">Admins@&lt;org&gt;</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">CSRAdmins@&lt;org&gt;</span></span>

1.  <span style="font-family: Calibri; font-size: medium;">c.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">AD object creation is one thing; the procedure will also stamp the appropriate permissions to ensure that only users in this Org should have access to objects underneath this OU.</span></span>
<span style="font-family: Calibri; font-size: medium;"> </span>

1.  <span style="font-family: Calibri; font-size: medium;">2.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">What you should know about otherWellKnownObjects? OtherWellKnownObjects are heavily used in HMC to 'extend' the Active Directory schema to link with other objects. For example, MPS will use otherWellKnownObjects to link the _Private folder, the Admins, the CSRAdmins, the parent folder to this organization. This is important to know because a number of the procedures in MPS will use this reference.</span></span>
_<span style="font-size: medium;"><span style="font-family: Calibri;">Note: You should know that however Allusers@&lt;org&gt; are not 'linked' in this otherWellKnownObjects. This is one of the common issues where renaming of Org will cause problem.</span></span>_

<span style="font-family: Calibri; font-size: medium;"> </span>

1.  <span style="font-family: Calibri; font-size: medium;">3.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">What is in the database then? There aren't many things being put into the database during Org creation. Because otherWellKnownObjects are used to link most of the things like Admins, CSRAdmins and _Private folder, there is no reason to keep any one of those in the database. Hence, you won't any record of those admins account in the database.</span></span>
<span style="font-size: medium;"><span style="font-family: Calibri;">PlanManager is the only database that you need to look at this point. When an organization is created, it will create a record in the Customers table in the PlanManager database. Say if you create a new Organization called TestOrg, if you perform a query like this,</span></span>

_<span style="font-size: medium;"><span style="font-family: Calibri;">SELECT * FROM [PlanManager].[dbo].[Customers] WHERE CommonName = 'TestOrg'</span></span>_

<span style="font-size: medium;"><span style="font-family: Calibri;">You will one record being retrieved showing you the following fields,</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">CustomerID -&gt; This will match it to the objectGUID of the OU container or the object.</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">ParentID -&gt; This will match it to the object GUID of the Parent OU or holding OU</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">StatusTypeCode -&gt; This will tell you if this Org or object is still active or enabled.</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">CustomerTypeCode -&gt; This will tell you the type of Object is this. For example,</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">        - BU is business user</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">        - BZ is business organization</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">        - CT is contact object</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">        - GP is group object</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">        - CU is consumer user</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">        - RO is Reseller organization</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">        - IR is Information Worker</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">CommonName -&gt; This stores the name of the organization. If it is a user, it will store the user email or contact email for Contact object or group name if it is a group object.</span></span>

_<span style="font-size: medium;"><span style="font-family: Calibri;">Note: You should know that Customer table does not just contain the Organization OU object. It consists of almost everything that is created in the Active Directory, such as OU, User, Contact, Distribution Groups.</span></span>_

<span style="font-size: medium;"><span style="font-family: Calibri;">Also, the database has been designed to cater for customization and extension. You can define your own type if necessary and you can find out all the types that are available from the CustomerTypes table.</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">The above doesn't cover in detail everything is being executed by the procedure and it also does not cover some of the hidden things written into the database. For example, there is difference between creating a reseller organization and a normal business organization. A consumer organization is also different compared to a business organization. You can see they are being differentiated by the CustomerTypeCode. You should also know when you create a Business Org for example, it will also subscribe the Organization to a BusinessPlan which is a default plan for Business.</span></span>

<span style="font-size: medium;"><span style="font-family: Calibri;">In short, the mapping is by GUID and it creates only the OU record and subscribed the OU to a business plan if it is a business Org. Now, in the later parts, I will also go a little bit deeper into the plans in the database and how each service like Exchange has Org plan as well as user plan. For now, from Active Directory standpoint, it is quite straightforward and it won't touch ResourceManager database at all.</span></span>

**<span style="font-size: medium;"><span style="font-family: Calibri;">Creating a User</span></span>**

1.  <span style="font-family: Calibri; font-size: medium;">1.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">What is created in Active Directory? When this procedure is called,</span></span>

    1.  <span style="font-family: Calibri; font-size: medium;">a.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">It creates the user object in the Organization OU. Obviously prior to executing the creation, it will perform some checks such as whether the path being passed in is correct or not.</span></span>
    2.  <span style="font-family: Calibri; font-size: medium;">b.</span>      <span style="font-size: medium;"><span style="font-family: Calibri;">It doesn't create anything else other than the user object but what it does do is that will insert this user to the appropriate security groups such as the AllUsers group created during the Org creation.</span></span>
    3.  <span style="font-family: Calibri; font-size: medium;">c.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">It will then perform the appropriate permissions hardening.</span></span>
<span style="font-family: Calibri; font-size: medium;"> </span>

1.  <span style="font-family: Calibri; font-size: medium;">2.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">What is in the database then? Creation of the AD user object will create a record on the Customer table in the PlanManager database.</span></span>
<span style="font-family: Calibri; font-size: medium;"> </span>

**<span style="font-size: medium;"><span style="font-family: Calibri;">Creating a Group</span></span>**

1.  <span style="font-family: Calibri; font-size: medium;">1.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">What is created in Active Directory? When this procedure is called,</span></span>

    1.  <span style="font-family: Calibri; font-size: medium;">a.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">It creates the group object in the Organization OU. Obviously prior to executing the creation, it will perform some checks such as whether the path being passed in is correct or not.</span></span>
    2.  <span style="font-family: Calibri; font-size: medium;">b.</span>      <span style="font-size: medium;"><span style="font-family: Calibri;">It will create a group object in the Active Directory.</span></span>
    3.  <span style="font-family: Calibri; font-size: medium;">c.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">It will then perform the appropriate permissions hardening.</span></span>

2.  <span style="font-family: Calibri; font-size: medium;">2.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">What is in the database then? Creation of the AD group object will create a record on the Customer table in the PlanManager database.</span></span>
**<span style="font-family: Calibri; font-size: medium;"> </span>**

**<span style="font-size: medium;"><span style="font-family: Calibri;">Creating a Contact</span></span>**

1.  <span style="font-family: Calibri; font-size: medium;">1.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">What is created in Active Directory? When this procedure is called,</span></span>

    1.  <span style="font-family: Calibri; font-size: medium;">a.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">It creates the contact object in the Organization OU. Obviously prior to executing the creation, it will perform some checks such as whether the path being passed in is correct or not.</span></span>
    2.  <span style="font-family: Calibri; font-size: medium;">b.</span>      <span style="font-size: medium;"><span style="font-family: Calibri;">It doesn't create anything else other than the contact object but what it does do is that will insert this user to the appropriate security groups such as the AllUsers group created during the Org creation.</span></span>
    3.  <span style="font-family: Calibri; font-size: medium;">c.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">It will then perform the appropriate permissions hardening.</span></span>
<span style="font-family: Calibri; font-size: medium;"> </span>

1.  <span style="font-family: Calibri; font-size: medium;">2.</span>       <span style="font-size: medium;"><span style="font-family: Calibri;">What is in the database then? Creation of the AD contact object will create a record on the Customer table in the PlanManager database.</span></span>
<span style="font-family: Calibri; font-size: medium;"> </span>

<span style="font-size: medium;"><span style="font-family: Calibri;">So, there you go. I think it is pretty straightforward. As you can see, creation of objects in Active Directory, from MPS standpoint does not create anything outside of PlanManager database. It does not touch the ResourceManager database at all. You should also see that all the records created, uses the Active Directory object GUID as the primary reference in the MPS's PlanManager database.</span></span>

</div>