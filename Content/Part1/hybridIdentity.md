# 1.3 - Hybrid Identity

In this section will will cover the following topics:
- [Why a Hydrid Identity](#why-a-hybrid-identity)
- [Directory Synchronisation](#directory-synchronisation)
- [Active Directory in Azure](#active-directory-in-azure)

## Why a Hybrid Identity?

In the previous, rather lengthy chapter, we looked at Microsoft Entra ID and how it forms the absolute foundation of any Azure landscape. We also discussed the differences between Entra and Windows Server Active Directory Domain Services. While such a comparison might have felt redundant for some readers, I consider it crucial for anyone from the Windows Server ecosystem. 

Active Directory (AD), which we often refer to as WS AD or AD DS, was previewed in 1999 and made publicly available with the release of Windows Server 2000. It provided authentication and authorisation for users and devices in a Windows-based network environment. As a core component of many enterprise-grade business applications including, but not limited to, Microsoft's own Exchange, SharePoint and Dynamics, it quickly became one of the essential IT services. 

As a result, when a decade later, Microsoft launched Azure, many organisations were long-time users of Active Directory Domain Services. They had, sometimes extensive and complex, deployments of AD that included all the information about their users, group assignments, computers and more. Allowing organisations to bring those identities to their cloud environments made sense. Thankfully, Microsoft was already getting AD DS identities into the cloud for the Business Productivity Online Standard Suite, the predecessor of Office 365.

*IMPORTANT - Please keep in mind that until the farily recent name change, Entra ID was called Azure AD. While the change is now fully solidified in the documentation, it might not yet be the case with software components. Don't be surprised if some components still refer to Azure AD or AAD.*

## Directory Synchronisation

### Microsoft Entra Connect 

After several name changes and several updates, we ended up with a tool called Microsoft Entra Connect. It is a tool installed locally within your Windows Server ecosystem, either on a dedicated virtual machine or on one of the domain controllers. It will connect to both your Entra ID tenant (outbound HTTPS, so no reverse proxy or natting is needed) and your local AD DS and orchestrate the replication of objects. The synchronisation will, however, be one-directional only - from AD DS to Entra. 

The installation process is pretty straightforward. I recommend using the custom settings mode, even if only to set up a dedicated service account. You can also specify a SQL server if you have a shared database instance that you use for the smaller apps in your environment. 

![Entra ID Connect Custom Settings](Images/azureAdConnectAdvancedOptions.png)

The tool does need an MS SQL Database instance, so if you don't specify one, the installation wizard o will configure SQL 2019 Express as a default. Be mindful of the capacity consideration, as MS SQL does tend to consume as much memory as possible.

Once the required components are installed, you must make the most crucial choice in your Entra Connect deployment - the sign-in method. 

### Three modes of sign-in

Entra ID Connect offers three sing-in modes that provide different options:
- **Password Hash Synchronisation** (PHS) will store a copy of the password hash (not clear text) in the cloud. Because of that, this might not be a viable option depending on your compliance framework. It is, however, the most convenient one. With PSH, your Entra ID sign-ins don't rely on any on-premises infrastructure, so outages and maintenance won't be a problem. Also, with password write-back, users can change their passwords in Entra ID, which will get synchronised back to WS AD. There is also the Self-Service Password Reset (SSPR) option that you can enable in Entra ID for maximum gains. Depending on the size of your organisation, using SSPR combined with write-back can provide significant savings in support costs. 
- **Pass-Through Authentication** (PTA) will use the authentication agents installed on-premises for user sign-ins. It's as straightforward in deployment as PHS. Still, password information will not be stored in the cloud, and all authentication requests will be routed towards the on-premises infrastructure. Due to the dependency on the local infrastructure, you should aim to deploy multiple authentication agents for high availability.
- **Federation** with either AD FS or PingFederatare. The latter is a relatively new option, and I've never tried using it. There the official documentation will be your best bet. I have, however, used AD FS many times, and unless you already have it or badly need one of the features only supported in this mode, I recommend avoiding it. AD FS requires several on-premises servers for a proper, highly available deployment (two federation servers, two federations proxy servers, and an MS SQL database), so it comes at quite a high cost. Both in terms of infrastructure and the management overhead. 

The table below offers a comparison of features between the three options:

| Option | PHS and SSO | PTA and SSO | AD FS |
| --- | --- | --- | --- |
| Sync new user, contact, and group accounts created in my on-premises Active Directory to the cloud automatically. | X | X | X |
| Set up my tenant for Office 365 hybrid scenarios. | X | X | X |
| Enable my users to sign in and access cloud services using their on-premises password. | X | X | X |
| Implement single sign-on using corporate credentials. | X | X | X |
| Ensure no password hashes are stored in the cloud. |   | X | X |
| Enable cloud-based multi-factor authentication solutions. | X | X | X |
| Enable on-premises multi-factor authentication solutions. |   |   | X |
| Support smartcard authentication for users. |   |   | X |
| Display password expiry notifications in the Office Portal and on the Windows desktop. |   |   | X |

You can also choose not to configure an Entra ID Connect sign-in method if you want to handle it via a federated third-party tool. 

Once you've made the critical decision regarding user sing-ins, you'll have to configure a few other settings regarding the synchronisation scope. Choose the domains and OUs and configure filtering. If you want to enable password write-back, you'll find this option in the "Optional Features" section of the setup wizard. 

### Entra ID Connect Health

Directory synchronisation can be a critical component of your hybrid cloud environment. But even if you don't find yourself overly reliant on Entra ID Connect, monitoring synchronisation for any issues is a good idea. Entra Connect Health is a service designed just for that. You can find it in the Azure Portal under the Azure Active Directory blade in the Entra ID Connect section. 

![Entra ID Connect Health](images/azureAdConnecHealth.png)

The tool will provide rich insights into alerts, performance and usage metrics. To install and configure the monitoring agents, [follow the official documentation](https://learn.microsoft.com/en-us/azure/active-directory/hybrid/connect/how-to-connect-health-agent-install#install-the-agent-for-ad-fs). 

### Entra ID Connect Cloud Sync

In 2019 Microsoft released a more modern replacement for Entra ID Connect - Entra Cloud Sync. Since then, the tool has been under development, and at the time of writing, it almost offers functional parity compared to its predecessor. 

The new service moves the synchronisation engine from on-premises to a cloud-based service (as the name might have indicated). As a customer, you still need to install an agent application, but Microsoft describes it as a "lightweight agent". No more MS SQL is required, not even the express edition. Besides that, the new version also provides support for:
- Connecting to multiple disconnected on-premises AD forests
- Deploying multiple active agents for high availability

![Synchronisation flow with Entra Connect Cloud Sync](Images/azureAdConnectCloudSync.png)

Some features of Entra ID Connect are still not there, so check the [Entra ID Connect Cloud Sync documentation](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-cloud-sync) before choosing. For example, Pass-Through Authentication is no longer available, so you will have to synchronise password hashes or use the federated model.

However, the two tools can coexist in a single environment, so you can mix and match based on what you need. The limitation is that both services cannot synchronise the same AD object, so you will need a domain- or OU-level scoping. 
Also, please keep in mind that once the new tool catches up on the missing functionality, Microsoft will start phasing out Entra ID Connect.

## Active Directory in Azure

Getting our Active Directory users and groups synchronised to Entra ID is handy. Still, it is only one of the two sides to hybrid identities in Azure. More often than not, especially when working with applications designed for the  Windows Server ecosystem, you will need traditional Active Directory Domain Services in your Azure environment. 

When that's the case, you have two options:
- Extend your existing AD DS to Azure
- Deploy Entra ID Domain Services

We'll look at both to understand the better choice in your circumstances.

### Extending AD to Azure

The typical case is deploying Active Directory Domain Controllers (DCs) to your Azure environment. If you're new to Active Directory, the DC is the primary component, and you will need at least one, although the best practice tells us to have at least two. When you deploy additional Domain Controllers, the service will handle replication and high availability automatically, but there are a few things to keep in mind:
- Domain Controllers need a direct line of sight between each other. That means a local/private network connection. You don't need a full mesh topology if you have many DCs, but any domain controller will need a replication partner. So, if you're extending your AD to Azure, you must **configure a site-to-site VPN or an ExpressRoute** to provide that level of connectivity. 
- Domain Controllers are also used as name resolution servers (AD DS is tightly coupled to DNS), so remember to **set static private IP addresses on those virtual machines**. Also design your overall internal name resolution solution carefully - expect a somewhat complex chain of DNS request forwarding (the challenge will become more obvious later in the course).
- Active Directory operates with the concept of sites, representing physical network locations where you want the service available. The mechanism dates back to when we had slow and low-throughput connectivity, but it helps clients identify the closest DC. **Configure at least one new site for your Azure environment. You will usually want two domain controllers per site for HA.** 
- Another essential concept in AD is the Global Catalogue (GC) role, which stores information regarding all objects in the Active Directory Forest. **Ensure at least one (but ideally two) DC(s) per site has the GC role installed.**
- **Ensure all controllers in your site have their connectivity requirements for replication met**, and provide the same towards another site with a GC role installed. [See the documentation for detailed information](https://learn.microsoft.com/en-us/troubleshoot/windows-server/identity/config-firewall-for-ad-domains-and-trusts).
- Finally, configure the Domain Controllers in Azure to store their database, logs and sysvol on a data disk, not the OS disk, and **ensure the disk has the "Host Cache Preference" setting on the data disk to "None"**.

*IMPORTANT - the above information is an abridged and condensed extract. Active Directory is a vast topic as the service has many advanced features and options. My goal is to give you enough to get you going and ensure you won't shoot yourself in the foot. You'll find more information [here](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/identity/adds-extend-domain), but if AD DS is a complete unknown, I recommend you seek support from someone with experience.* 

The resulting topology of extending one's Active Directory to Azure can be complex and costly. The figure below provides a good overview. If the icons and names still make little sense to you, worry not — we will go into each of them in detail in the coming chapters. 

![Example topology of AD DS extended to Azure](Images/adDsExtendedToAzure.png)

### Entra ID Domain Services

If setting up and managing the entire stack of resources needed to extend your Active Directory to Azure seems too much, there is a managed service that does the same thing. In a way.

If Entra ID Connect synchronises Active Directory with your Entra ID tenant, Entra ID Domain Services sets up an opposite data flow. The service will deploy Domain Controllers, configure Active Directory Domain Services and set up the synchronisation from your Entra ID tenant towards the Active Directory domain.
As a result, you get a managed Active Directory Domain Services environment connected to the Azure Virtual Network(s) of your choice. You can deploy multiple instances, or replica sets, even across multiple Azure regions. Each replica set will consist of two Domain Controllers configured in a zone-redundant topology (if the region supports Availability Zones).

![Example multi-region deployment of Entra ID Domain Services with two replica sets](Images/azureAdDomainServices.png)

If it's starting to sound too good to be true, you're onto something. Although Entra DS is a fantastic service, several limitations will dictate whether the managed AD offering is for you or you will be forced to go the traditional route.

The most critical limitations include the following:
- You cannot connect to the Domain Controllers via Remote Desktop -they are managed for you by the platform and unavailable. To manage your AD domain, you must install the "Users and Computers" MMC snap-in on any domain-joined VM and use it for remote management.
- Entra ID Domain Services does not give you access to the Domain Administrator or the Enterprise Administrator privileges. Upon deployment, a new Entra ID security group will be created - Entra ID DC Administrators and members of that group will have (limited) administrative permissions in the managed domain.
- As a result, you cannot perform any schema updates.
And you get confined to a handful of Organisational Units in the managed domain - AADDC Users, AADDC Computers, AADDCDomainAdmin,AADDCDomainConfig.
- Finally, Entra DS doesn't support guest users invited via Entra ID B2b collaboration. While the user's accounts will get synchronised from Entra ID to Entra ID Domain Services, the passwords for those users are always stored in their home directories. Therefore, the hashes cannot be synchronised to Entra DS.

There are a few other limitations, but if you're interested, I'll have to send you to the official documentation [here](https://learn.microsoft.com/en-us/azure/active-directory-domain-services/faqs) and [here](https://learn.microsoft.com/en-us/azure/active-directory-domain-services/compare-identity-solutions).

If none of the features unavailable in the managed Active Directory Domain Services offering is crucial to your use case, I recommend seriously considering Entra ID DS. While you can get a better deal, looking purely at consumption cost, with a pair of traditional Domain Controllers, the cost of managing, securing, updating and protecting those VMs cannot be underestimated. More on those topics in part 3.

**IMPORTANT - When you deploy an Entra ID Domain Services instance, you must provide a subnet of a Virtual Network that the managed service will use to deploy Domain Controllers. The Network Security Group, which acts as the subnet's Access Control List, requires particular rules. Unfortunately, some of them cannot be configured via the Azure Portal. To work around this issue, allow the Entra DS wizard to create the subnet or use a programmatic deployment for the NSG. Even though it might sound like back magic right now, please keep it in mind. It will all make perfect sense soon.**

[< 1.2 - Microsoft Entra ID](./entraId.md) | [Home - Course Contents](../Contents.md) |  [1.4 - Azure Governance >](./managingAzure.md))