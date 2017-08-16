---
title: HMC 4.5 and Exchange 2007 SP1 - Part 1 - Overview and Active Directory
tags:
  - Exchange
  - HMC
categories:
  - Microsoft
  - HMC
date: 2009-05-06 17:06:36
---

转自 http://blogs.technet.com/provtest/archive/2008/12/04/hmc-4-5-and-exchange-2007-sp1-part-1.aspx 很好的文章,贴过来收藏,hehe.

**Introduction**

One of the frequently asked questions about Hosted Messaging &amp; Collaboration (HMC) is this, "What do you do to Exchange to make HMC work?" So, here I will attempt to demystify this.

HMC started long way back but the concept has not deviated much. I want to note that this specific blog focuses mainly on HMC 4.5 and Exchange 2007 SP1 and it should be noted that things discussed here probably is applicable to this specific version only unless mentioned otherwise.

There are quite a few customizations that we set in Exchange 2007 for HMC and it is probably too much to do it in one blog, so, I will split this into a few parts,

*   Part #1: Overview and Active Directory
*   Part #2: Address Lists Segregation
*   Part #3: Offline Address Book generations
*   Part #4: Internal and External Out of Office
*   Part #5: Autodiscover in the Multi-tenancy environment
*   Part #6: Conclusion

**What does HMC mean to Exchange 2007?**

Let me start by asking this, what exactly is HMC? Yes, yes, there are quite a fair bit of things in HMC such as tools for flexible business modeling for service providers, resource management so that service providers can offer a broad range of services that go from basic e-mail up to higher-value services like providing additional storage and hosting personal domains and shared calendars and contacts. However, if I have to use one to two words to explain it, I will say HMC is about **Multi-tenant enablement**.

Yes, I think there is no mystery that Exchange 2003 and Exchange 2007 were not really packaged or designed to cater for multi-tenant. Not that it is not capable to do so, just that it wasn't designed with that as the focus.

What is multi-tenancy? Multitenancy refers to a principle in software architecture where a single instance of the software runs on a software-as-a-service (SaaS) vendor's servers, serving multiple client organizations (tenants).

Think of it like a condominium with hundreds of different individual units living in it, that's what multi-tenant is. Instead of having only one company _(note: I tend to use company and tenant interchangeably)_ to live in one Exchange environment (1:1), multi-tenant enablement for Exchange 2007 means we want to enable the same Exchange environment to cater for multiple individual and separate companies and like a unit in the condominium, each company should have their own space and privacy (m:1)

Let me continue with the condominium analogy. Now that we know we need to build a condominium, we also need to figure out what we need to build each unit in the condominium. We need to make sure that each floor will be partitioned nicely and that the tenants will have their own kitchen, doors, parking space and enjoy their home and privacy. Likewise with Exchange 2007, we need to make sure that each tenant will enjoy the full messaging features and capabilities but yet do not compromise privacy and security.

So what do we need to multi-tenant enable Exchange 2007? Here is what needed to be done,

1.  **Active Directory Partitioning and Permission** - think of it like partitioning each floor to different unit. Every company will have their own separate 'space' and the users in that company should only have access to what they are allowed to. I will discuss more on this later on how we can achieve that.

2.  **Address List Segregation** - access to the contact information in each company should only be limited to users in that company. Other tenants shouldn't know have access to that information.

3.  **Outlook Experience **- Outlook experience should not be compromised. The users in each company should enjoy all the features available in Outlook, such as Outlook Anywhere with cached mode and Offline Address Book, Calendaring and resource management, using Free/busy feature, Internal and External Out of Office feature in Exchange 2007, Autodiscover, Outlook rules, mailbox delegation, mailbox quota and etc. Some of the above mentioned features will required to be tweaked for multi-tenancy and some do not. I will walk through them as we go.

4.  **Shared Components** - as much as we try to make sure everything is segregated, there will be some information and shared components here. Just like living in a condominium, everyone will share the same garage and elevator.

Now that we have listed them down, it doesn't look that bad, does it? In fact, once we walk through how we do this in HMC, you may realize that we aren't doing that much customization to Exchange at all. HMC, however, is more than just doing these customizations, of course. It is also a tool that allows you to better manage your resource and also allow you to automate a number of things, but that discussion will be for a separate blog. I will briefly touch on this at the end of this series.

Those who has been dealing with Exchange 2007 probably realize that we have a white paper that talks about the similar thing, which is [White Paper: Configuring Virtual Organizations and Address List Segregation in Exchange 2007 ](http://technet.microsoft.com/en-us/exchange/bb936719.aspx "White Paper: Configuring Virtual Organizations and Address List Segregation in Exchange 2007 "). I strongly recommend anyone who wants to know more about the Exchange 2007 HMC customization to also read this white paper and try setting it up in your test environment for your own learning.

The above white paper explained how you can come up with your homebrew version of hosted solution by making a whole list of manual tweaking. It also provides good explanations on some of those customizations executed. HMC, for most parts are doing the same thing but there are a few things that we did quite differently and also we have a Deployment Tool to help you to make those customization instead of having you manually go through those permission setting of various OU containers, removal of address lists and etc.

**Active Directory Partitioning and Permission**

Firstly, let me start with how we do the Active Directory partitioning and permission in HMC for Exchange. The process of deploying a HMC is quite straightforward. Everything is done through the Provisioning Deployment Tool that comes with the HMC Solution ISO file.

(1) List Object Mode (**dsHeuristics**)

This is the first thing that the Deployment Tool will do. This is about controlling the object visibility. By default in Active Directory, we control an object using List Contents permissions on the parent object. The concept is quite simple; it means an object that will only be visible to a user if the user has been granted List Contents permissions on the parent object. When a user has List Contents permission on a parent node, he or she can see and browse all objects that are children of that node without any further selectivity.

Obviously this can be a problem in a Hosted Environment, for example, if in my Active Directory, I have the following structure,

* Fabrikam.com (Windows Domain)
    * Hosting (Hosting Container)
        * ConsolidatedMessenger (Reseller)
             * Company1
                  * [User1@Company1]
                  * [User2@Company1
                  * [User3@Company1]
             * Company2
			   * [User1@Company2]
			   * [User2@Company2]
			   * [User3@Company2]
             * [User1@ConsolidatedMessenger]
             * [User2@ConsolidatedMessenger]

If I grant [User1@Company1](mailto:User1@Company1) List Contents permission say on Compmany1 OU, I will also have to do it on all the parent OUs above it. Without that, it stops someone looking at what lives under the object or container. However, granting it lets whoever has the permission the ability to go through the content, including Company2 OU, which is obviously a bad thing. This is where "List Object" mode comes in.

In this special mode, even if the user has not explicitly been granted or explicitly been denied List Contents permission on the parent object, the object will still be visible to the user if the user is granted List Object permission on both the parent object and the object itself.

The availability and setting of this mode is very important to multi-tenant enablement and this is deployed as part of Core Platform in the Deployment Tool. Essentially, this form the basis of security control and Active Directory partitioning in HMC.

(2) Active Directory Partitioning - Hosting Container

Now, I want to briefly walk through the structure that HMC will be creating in Active Directory. Here are a few things that you should know,

*   HMC does not support deployment in a child domain. While it is technically possible, it hasn't be well tested and hence it is not a supported model.
*   By default in HMC, it will create an OU called Hosting right under the Domain. This can be changed by setting the procedure parameter in _Hosting Platform - Initialize Default Services - Initialize Active Directory_ for Hosting in the Deployment Tool.

That is only the creation of the master hosting container. You will then need to follow the Deployment Walkthrough in the Help File to create the Reseller container and also the Tenant container. From the Exchange perspective, you only need to know the following,

*   **HMC Resellers** - Each Reseller is fully contained within its own Organizational Unit (OU) in Active Directory. The administrator from this OU will have permission to manage the HMC Tenants in it. Example in the Deployment Walkthrough is_ConsolidatedMessenger.com_. The Reseller OU will usually contain the following objects

    *   HMC Tenants
    *   HMC Users
    *   A _Private OU //you should for the most part ignore the contents of this OU its contents are primarily in place to support provisioning actions.
    *   An Admins@&lt;Reseller&gt; group

*   **HMC Tenants** - Each Tenant is fully contain within its own Organizational Unit (OU) in Active Directory, this Organizational Unit is a child of an HMC Reseller. Example in the Deployment Walkthrough is _AlpineSkiHouse.com_. The Tenant OU will usually contain the following objects,

    *   HMC Users
    *   A _Private OU //you should for the most part ignore the contents of this OU its contents are primarily in place to support provisioning actions.
    *   An Admins@&lt;TenantOrg&gt; group // This group contains all of the Tenant Admin
    *   Custom or service specific groups (such as FolderAdmins, FolderUsers for Public Folder service)

*   **HMC Users** - Users objects within AD are the security principal.
*   **HMC Mailboxes** - An HMC mailbox is a user object that has been Exchange Mailbox Enabled.
Here is an example of the Active Directory Hosting Container structure in HMC

*   Fabrikam.com (Domain)

    *   Hosting (Hosting OU)

            *   _Private
        *   ConsolidatedMessenger.com (HMC Reseller)

                    *   _Private
            *   [admins@ConsolidatedMessenger.com](mailto:admins@ConsolidatedMessenger.com) (Admins group for HMC Reseller)
            *   [administrator@ConsolidatedMessenger.com](mailto:administrator@ConsolidatedMessenger.com) (Administrator for the HMC Reseller)
            *   AlpineSkiHouse.com (HMC Tenant)

                            *   _Private
                *   [admins@alpineskihouse.com](mailto:admins@alpineskihouse.com) (Admins group for HMC Tenant)
                *   [johnc@alpineskihouse.com](mailto:johnc@alpineskihouse.com) (HMC Users/Mailboxes)
Obviously, I have simplified quite a few things here, for example, in this whole Active Directory Partitioning process; the HMC Deployment Tool will also create a number of groups (such as AllUsers, AllCustomers, AllCustomers, AdminsGroups and etc.) and properly assign the appropriate membership and permissions in different containers.

Permission Inheritance is not blocked specifically. For example,[admins@ConsolidatedMessenger.com](mailto:admins@ConsolidatedMessenger.com) group is granted with Write, Create and Delete Child Objects to ConsolidatedMessenger.com OU. And that permission is also inherited downward to AlpineSkiHouse.com. This allows users in the[admins@ConsolidatedMessenger.com](mailto:admins@ConsolidatedMessenger.com) group the capability to manage all the HMC Tenants in the ConsolidatedMessenger.com OU.

For the purpose of this blog, I think that's all you need to know about the Active Directory partitioning in HMC for Exchange. If not, this blog may end up to be a novel. :)

(3) User Logon

I want to touch a little bit about User Logon here. Most of you who are familiar with Active Directory will know what a _samAccountName _is. This is the logon name we carried forward from the old NT world. It has to be unique in a Windows domain. Even though we have revolved to allow longer logon name in later version of Windows, we still need to specify a samAccountName, a mandatory attribute. It usually follow the logon name. If my logon name is John, then my samAccountName is also John.

I can login to the environment using either username@domain or DOMAIN\&lt;samAccountName&gt;. This is not a big problem in a normal corporate environment. When it comes to a multi-tenant environment, this maybe a bit of a problem because we may have John in AlpineSkiHouse.com and we may also have John in Contoso.com.

In order to achieve the uniqueness, HMC recommends users to logon using the UPN as a start, which is [username@domain](mailto:username@domain) such as [johnc@alpineskihouse.com](mailto:johnc@alpineskihouse.com). You will notice in HMC allows that by modifying the **upnSuffixes attribute **of the Tenant OU. Now, all these things are done automatically when you create a HMC Tenant through the Microsoft Provisioning System.

With that, it solves the user logon uniqueness problem. However, it does not resolve the samAccountName uniqueness. To ensure that the samAccountName is also unique, HMC appends the domain of the Tenant to the logon name but because samAccountName can't have more than 20 characters, it will get truncated if it is more than 20 characters. For example,

*   Logon Name: [johnc@alpineskihouse.com](mailto:johnc@alpineskihouse.com) will have the samAccountName of johnc_AlpineSkiHouse
*   Logon Name: [bob@alpineskihouse.com](mailto:bob@alpineskihouse.com) will have the samAccountName of bob_AlpineSkiHouse.c
So, hopefully that will help to explain the naming convention of the user object.

(4) Active Directory Attributes

HMC did not introduce any schema change to Active Directory. Sometimes, I wish it does because it would have made things little bit easier but it didn't. Instead, it uses what I would call the poor man's schema extension, the **otherWellknownObjects** attribute.

From the Exchange standpoint, this attribute is not used but for the provisioning process (MPS) to work properly, understanding this attribute is essential. And at times, I also think it is important to know this because it can tell you the Global Address List, Address List, Offline Address List that the specific HMC Tenant is using.

Here is a LDP dump of the otherWellKnownObjects attribute of one of the HMC Tenant.

12&gt; otherWellKnownObjects: B:32:3841BDA6D81C4095B9BBB838808F5A55:CN=Services,CN=_Private,OU=AlpineSkiHouse,OU=ConsolidatedMessenger,OU=Hosting,DC=hmc45,DC=com;**B:32:D22DFCC5B73645E99E16C9AD3D61F34F**:CN=AlpineSkiHouse OAL,CN=Offline Address Lists,CN=Address Lists Container,CN=HMC45,CN=Microsoft Exchange,CN=Services,CN=Configuration,DC=hmc45,DC=com;**B:32:9E444526CB6F4D5C9A59C9A84E26B627**:CN=AlpineSkiHouse AL,CN=All Address Lists,CN=Address Lists Container,CN=HMC45,CN=Microsoft Exchange,CN=Services,CN=Configuration,DC=hmc45,DC=com;**B:32:89FB25B7DF784FC198A493E2E8A0EE7E**:CN=AlpineSkiHouse GAL,CN=All Global Address Lists,CN=Address Lists Container,CN=HMC45,CN=Microsoft Exchange,CN=Services,CN=Configuration,DC=hmc45,DC=com; B:32:7DEF010C6019A1458068D74AD1A3C1FA:CN=FolderUsers@AlpineSkiHouse,OU=AlpineSkiHouse,OU=ConsolidatedMessenger,OU=Hosting,DC=hmc45,DC=com; B:32:4619BE598BF441DB8C9DB0482E62E386:CN=_Private,OU=AlpineSkiHouse,OU=ConsolidatedMessenger,OU=Hosting,DC=hmc45,DC=com; B:32:EA755D448CE64157A20E82B7CCBE14B0:CN=customer,CN=WatOrgTypes,CN=_Private,OU=Hosting,DC=hmc45,DC=com; B:32:3B6FF4FA8AA248039AD8F9493A43B704:CN=CSRAdmins@AlpineSkiHouse,OU=AlpineSkiHouse,OU=ConsolidatedMessenger,OU=Hosting,DC=hmc45,DC=com; B:32:65F37ECB46704F0E9300E1FB48E1096E:CN=Admins@AlpineSkiHouse,OU=AlpineSkiHouse,OU=ConsolidatedMessenger,OU=Hosting,DC=hmc45,DC=com; B:32:58888CFC8F7F430C8183102CD5758D81:OU=ConsolidatedMessenger,OU=Hosting,DC=hmc45,DC=com; B:32:CC016CF08DEF4EA4A05C9C54B198785A:OU=AlpineSkiHouse,OU=ConsolidatedMessenger,OU=Hosting,DC=hmc45,DC=com; B:32:A276E3A170F0C24699770F593818501E:CN=FolderAdmins@AlpineSkiHouse,OU=AlpineSkiHouse,OU=ConsolidatedMessenger,OU=Hosting,DC=hmc45,DC=com;

As you can see from above, we have a unique GUID number to identify a specific category of relationship.

&lt;GUID name="GlobalAddressList"&gt;**89FB25B7DF784FC198A493E2E8A0EE7E**&lt;/GUID&gt;
&lt;GUID name="AddressList"&gt;**9E444526CB6F4D5C9A59C9A84E26B627**&lt;/GUID&gt;
&lt;GUID name="OfflineAddressList"&gt;**D22DFCC5B73645E99E16C9AD3D61F34F**&lt;/GUID&gt;

In addition, here are some of the _Key attributes to Exchange:_

*   **homeMDB**: DN of the users mailbox store - no customizations for HMC.
*   **mailNickname**: This is the HMC Users mailbox alias. - no customizations for HMC.
*   **msExchMailboxFolderSet**: Defines the folders available to the user through OWA, this attribute is controlled by the HMC User Plans. ** **
*   **msExchQueryBaseDN**: Provides a baseDN from which LDAP Searches are performed, in HMC this attribute is set to the DN of the Tenant effectively limiting LDAP searches in OWA, ActiveSynch etc to the Tenant OU.
*   **protocolSettings**: Defines which protocols a user can use to access Exchange, this attribute is controlled by the HMC user Plans
*   **showInAddressBook**: Multi-valued attribute includes the DNs for the Tenant specific GAL and AL as well as the Default GAL, this attribute must be set correctly and the GALs ACL'd correctly in order for a user to logon to Outlook as well as for them to be restricted to the appropriate address list. ** **
*   **msExchUseOAB**: DN of the Tenant specific OAL object, this attribute directs Outlook to the appropriate OAB to download for this user.
Let's briefly talk about the e-mail address stamping here too. In Exchange 2003, we have this process called Recipient Update Services. In Exchange 2007, this whole process has been embedded into the cmdlets and Email Address Policy is being used to determine the e-mail address of those users during the creation. It should be noted that, in HMC, right after it created an email user, a distribution group or an Exchange resource, HMC immediately performs another task to set the **EmailAddressPolicyEnabled** attribute to **False**. This means,

*   It does not rely on the Email Address Policy at all. That's why you will see that HMC will not create any Email Address Policy at all.

*   HMC will use its own mechanism to stamp the SMTP addresses.
So, that's it. It is straightforward so far, eh? We will talk more about other customizations introduced by HMC for Exchange in the new few parts.