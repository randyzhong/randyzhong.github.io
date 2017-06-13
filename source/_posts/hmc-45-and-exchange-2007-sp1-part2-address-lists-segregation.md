---
title: 'HMC 4.5 and Exchange 2007 SP1 - Part #2 - Address Lists Segregation'
tags:
  - Exchange
  - HMC
id: 135
categories:
  - HMC
  - Hosting
date: 2009-05-07 17:32:58
---

转自 http://blogs.technet.com/provtest/archive/2008/12/10/hmc-4-5-and-exchange-2007-sp1-part-2.aspx 很好的文章,贴过来收藏,hehe.

**Introduction**

In part #1, I spent a fair bit of time explaining how HMC was introduced into Exchange as part of the multi-tenant enablement process for service provider. I also spent some time discussing about how the Active Directory was partitioned and what the primary attributes that are essential to the Exchange 2007 SP1 multi-tenant enablement.

So far, there wasn't anything complicated about it. Now, I am going to move on to the 2nd part of the customizations, which is probably one of the more important ones, that is the Address List Segregation.

Let's look at what address lists we need to segregate and why. Address list is about contact information which is very important because it makes managing and finding your contacts (internal as well as external) easier and hence makes communication easier. There are a few places where you will use Outlook to locate the contact information,

<!--more-->
<div id="more">

*   **Contacts folder** - this folder is created within the mailbox itself if you have an Exchange account. Think of this folder like a normal Inbox or Calendar folder in your mailbox. This is a private folder. If you don't use a Microsoft Exchange Server e-mail account, Outlook stores your Contacts with the rest of your Outlook data in a Personal Folders file that has a PST file extension.
&nbsp;

*   **Personal Address Book (PAB)** - Outlook also supports Personal Address Books (PAB). Like Contacts, a Personal Address Book can store a contact's name, address, e-mail address, phone, and other information. Outlook stores the Personal Address Book in a file with a PAB file extension. The PAB is completely separate from your other Outlook data stored in your PST file (or in an Exchange Server store). You can add more than one PAB to an Outlook profile.
&nbsp;

*   **Server-side Address Lists **- in Exchange 2007 environment, you have 2 types of address lists,

    *   _Global Address List (GAL)_ - The GAL contains information for <span style="text-decoration: underline;">all</span> email users, distribution groups, and Exchange resources. Outlook needs this to work and Outlook can only see one GAL at a time.
&nbsp;

*   _Other Address Lists _- Sometimes, Exchange Server administrator might create other address lists to organize Exchange users by department, surname, or other criteria. These additional address lists show up under the **All Address Lists** group in the **Show Names from the** drop-down list in the Outlook Address Book.
From the above, I think it is pretty obvious that Contacts folder is a private folder, like the Inbox folder in a mailbox, is only accessible to the mailbox owner. PAB is really an offline file that store contact information. There is really nothing required to be done on these contact stores for a multi-tenant environment because those are 'not shared' and are private by default.

Server-side address lists however are slightly different. The address lists are shared and because of how we designed Exchange and Outlook, the address lists in Exchange, out of the box is accessible by everyone in the environment. That obviously is a problem for multi-tenancy because it means users in company A will be able to see the users in company B. So, let's take a closer look at what HMC will change in order to segregate the address lists so that every company will only see what they are supposed to see.

Before that, I think it is important to highlight that address list in Exchange is really nothing more than an Active Directory object containing query logic or filter that specify what mail enabled objects in Active Directory should be included in that list. It is not an actual list or a table or store that consists of all the contact information. It is a filter or I like to call it query logic. That's not all, what is more important to understand is that this query logic is only essential when creating the mailbox, but not so much for the actual Outlook query. As we walk through the blog, the statement above will become clearer to you and you will why HMC does what it does.

**What is HMC trying to do? **

So, let's look at what HMC intends to achieve here,

*   **Global Address List** - By default when you install Exchange 2007, it assumes that it is only meant for one company; hence it comes with a Default Global Address List which lists of <span style="text-decoration: underline;">all</span> email users, distribution groups, and Exchange resources in the whole environment. That is not something a multi-tenant environment would like to have. Therefore, the primary objective here is NOT to have one GAL that will display everyone in the environment but different GALs for different companies.
&nbsp;

*   **Other Address List** - If there is any other address list being used by a company in the environment, those address lists have to be only accessible and visible to users in that company only.
So, with the above objectives in mind, let's take a closer look.

**Global Address List Segregation**

Outlook needs GAL to work and as we know, Outlook can only see one GAL at a time. In order to provide each company their own Global Address List, we need to create different GAL objects for different companies, having specific query logic that scope to just users, distribution groups and resources in the company.

Once we have different GALs for different companies, we need to address the following,

<span style="text-decoration: underline;">(1) What's the limitation of Global Address List?</span>

The default configuration of the global address list (GAL) class object allows only 1000 address lists. This presents a clear problem because we do expect most hosters to have at least more than that. To make sure that the environment can support more than 1000, you need to use the **MakeGalLinked** tool to extend this limit.

This is documented in the deployment walkthrough. I am not going to spend time to cover that as I am quite sure you can find other blogs cover this better.

<span style="text-decoration: underline;">(2) How to make sure that Outlook clients access the correct global address list if there is more than one global address list?</span>

If you refer to the following article, [http://support.microsoft.com/kb/312287](http://support.microsoft.com/kb/312287), you will realize that Outlook displays the global address list that meets the following criteria:

*   The user has permissions to access this global address list.
*   The user is a member of this global address list.
*   This global address list is the largest of all of the other global address lists.
The above Outlook logic means that the Default Global Address List will always be used because all the users created will always be a member of this GAL and it is obviously the largest GAL of all because it consists of everyone in the environment.

Because of the above Outlook logic, we need to either make sure the user is not a member of the Default GAL or we make sure that the user has no permission to it. HMC has selected the latter. We deny almost everyone except Exchange Servers and Domain Admins permission to Open Address List for Default GAL. You may notice that this isn't very different as compared to the steps documented in [White Paper: Configuring Virtual Organizations and Address List Segregation in Exchange 2007](http://technet.microsoft.com/en-us/exchange/bb936719.aspx "White Paper: Configuring Virtual Organizations and Address List Segregation in Exchange 2007 ").

With the default GAL not accessible to all the users, we need to now create different GAL for different companies so that Outlook will actually work.

What HMC did is this, when you create a company and the user with Outlook plan, it will automatically create a company GAL. HMC also ensures that only users (AllUsers@&lt;company&gt;) in that company/tenant will have the Open Address Lists permission their GAL. I am not going to list down all the permissions that we set in there but it shouldn't stop you from finding out yourself, right? If you want to know the exact permissions being granted, just run Get-ADPermission cmdlet, for example,

_Get-ADPermission "AlpineSkiHouse GAL" | FL_

<span style="text-decoration: underline;">(3) What query logic or filter should we use for the company GAL?</span>

In [White Paper: Configuring Virtual Organizations and Address List Segregation in Exchange 2007 ](http://technet.microsoft.com/en-us/exchange/bb936719.aspx "White Paper: Configuring Virtual Organizations and Address List Segregation in Exchange 2007 "), the article uses **customattribute1** and they make sure that mailboxes or resources created must have that attribute, if not, it will not be included in that GAL.

This is where HMC 4.5 does things slightly differently. If you perform a Get-GlobalAddressList on one of the GALs created by HMC, you will find the query logic/filter, which is the **RecipientFilter** and **LdapRecipientFilter** attributes are empty.

These attributes are essential if you want to make sure a newly created mailbox will be included as a member of this GAL. Only if the user is a member of the GAL, it can access the GAL and that it can show up in the GAL. These attributes are being read during the creation of the mailboxes and if the mailbox falls under the query logic, the appropriate membership will be stamped.

So, why does HMC not have any value on those attributes then? The answers are,

*   HMC mechanism - HMC has its own mechanism to retrieve the company GAL and perform the membership stamping. How? Remember the otherWellKnownObjects attribute I talked about in part #1?
*   It is faster. Just imagine if the environment hosts 20,000 domains in the environment, how long do you think it will take to walk through those query each time I create a mailbox? Having HMC to accurately retrieve the correct GAL will be more effective.
You may ask, what about when I query this from Outlook? Does it use those query logic? The answer is no. When you query from Outlook, it retrieves the objects based on Address List Membership (showInAddressBook) attribute in each objects. In short, the query logic in the GAL itself is only used for stamping the membership during mailbox creation.

There you go, Global Address List segregation, not really a difficult concept but can be confusing sometimes.

**Address List Segregation**

Address List Segregation isn't very different as compared to the GAL Segregation. However, there are a few things worth mentioning here.

Firstly, out of the box, Exchange 2007 installation comes with a few default address lists, All Contacts, All Groups, All Rooms, All Users, and Public Folders. Those will be removed during HMC 4.5 deployment. So, a clean HMC deployment will have an empty All Address Lists container.

Next, like the GAL segregation, the permission of the All Address Lists container has been properly locked down. Now, in theory, we don't really need to be part of any Address Lists for Outlook to work. Being part of the GAL is more essential because if you are not, you can't even create an Outlook profile for that user.

However, some processes in Exchange, such as the Offline Address Book generation prefers using Address List than Global Address List when generating the OAB (I will cover the OAB generation process in my next part). For those reasons, we need to make sure each company will also have company Address List where every mail enabled objects in the company will be part of that address list (yes, there is no difference compared to GAL).

Like the GAL membership stamping process, the Address List membership stamping process is the same, it is done by HMC mechanism. Hence you will see the query logic/filter for all the Address Lists created by HMC are also empty.

**Wait the minute, what about Outlook Web Access?**

So far, we have been discussing about the Outlook experience of the Global Address List and Address Lists. I think it is important to highlight that things work a little bit differently in Outlook Web Access.

There is some permission that applies only to specific clients. For example, Outlook Web Access does not incorporate user permission sets when doing searches. The permissions set on OUs do not prevent search results from including other segregated group's recipients, due to how OWA works. This is where attribute**msExchQueryBaseDN** comes in.

In order for us to control or restrict the search results to include only the members of the appropriate address list in Outlook Web Access, we must set the attribute**msExchQueryBaseDN** on each user object to the _distinguishedname_ (DN) of the OU or an address list containing the correct group of users. In the case of HMC, when a mailbox user is created, HMC will also populate this attribute with the OU of the company/tenant.

**What about LDAP? I have clients needing this access.**

If I have a group of users using other clients and needs LDAP access so that they can query the users of in their company/tenant, what happen then? Well, the way I look at it is this, as much as possible, don't.

However if you have to do it, make sure that the client has to at least go through some form of VPN or something because you really don't want to put your Active Directory server out and exposed them to the Internet just like that.

Also, if you are having HMC 4.0, you should look at this article, Hosted users can see other hosted users if they can access the HMC Active Directory by using LDAP tools in Microsoft Solution for Hosted Messaging and Collaboration version 4.0 ([http://support.microsoft.com/kb/943864/en-us](http://support.microsoft.com/kb/943864/en-us)), to properly lock it down before allow your users to use it.

**What are some of the usual problems that you may face with Address Lists?**

<span style="text-decoration: underline;">(1) Incorrect Address List membership</span>

The most common issue that I have seen with address lists issues are membership problem. Based on the above, you kind of understand how some of the things could have gone wrong. The fact that HMC does not use the **RecipientFilter** and **LdapRecipientFilter** means that you can't run Update-GlobalAddressList or Update-AddressList because, That two cmdlets will rebuild (there is no update process in Exchange 2007, only rebuild) the address list, which means when it finds that it is empty, it may reset those accounts that currently are supposed to be in the address list.

When an account lost the GAL or AL membership, you may notice that you may not be able to create an Outlook profile for that user, and the user may have other problems too.

To resolve that, you can either use ADSIEdit to manually slot those membership back or you can use the hosted Email 2007::ModifyMailbox to reset this attribute to its correct values without having to know the address list DNs. This procedure resets the showInAddressBook attribute on each call.

Here is a sample, assuming that you supply the original values for &lt;emailAddresses&gt;  and &lt;alias&gt; the procedure will only reset the showInAddressBook value without modifying any other attributes.

&lt;request&gt;
&lt;data&gt;
&lt;preferredDomainController&gt;AD02.Fabrikam.Com&lt;/preferredDomainController&gt;
&lt;user&gt;LDAP://CN=User1,OU=AlpineSkiHouse,OU=ConsolidatedMessenger,OU=Hosting,DC=fabrikam,DC=com &lt;/user&gt;
&lt;alias&gt;User1&lt;/alias&gt;
&lt;emailAddresses&gt;
&lt;value&gt;User1@alpineskihouse.com &lt;/value&gt;
&lt;/emailAddresses&gt;
&lt;/data&gt;
&lt;procedure&gt;
&lt;execute namespace="Hosted Email 2007" procedure="ModifyMailbox" impersonate="1" &gt;
&lt;before source="data" destination="executeData" mode="merge" /&gt;
&lt;after source="executeData" destination="data" mode="merge" /&gt;
&lt;/execute&gt;
&lt;/procedure&gt;
&lt;/request&gt;

This functionality was added to account for the fact that the Exchange Cmdlets like to reset showInAddressBook value during most mailbox operations.

</div>