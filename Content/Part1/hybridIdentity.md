**The chapter below is still work in progress!**

# 1.3 - Hybrid Identity

## Why a Hybrid Identity?

In the previous, rather lengthy chapter, we looked at Azure Active Directory and how it forms the absolute foundation of any Azure landscape. We also discussed the differences between Azure AD and Windows Server Active Directory Domain Services. While such a comparison might have felt redundant for some readers, I consider it crucial for anyone from the Windows Server ecosystem. 
Active Directory (AD), which we often refer to today as WS AD or AD DS, was previewed in 1999 and made publicly available with the release of Windows Server 2000. It provided authentication and authorisation for users and devices on a Windows-based network environment. As a core component of many enterprise-grade business applications including, but not limited to, Microsoft's own Exchange, SharePoint and Dynamics, it quickly became one of the essential services. 
As a result, when a decade later, Microsoft launched Azure, many organisations were long-time users of Active Directory Domain Services. They had, sometimes extensive and complex, deployments of AD that had all the information about their users, group assignments, computers and more. Allowing organisations to bring those identities to their cloud environments made sense. Thankfully, Microsoft was already getting AD DS identities into the cloud for the Business Productivity Online Standard Suite (predecessor of O365).

## Directory Synchronisation

### Introducing Azure AD Connect

After several name changes and several updates, we ended up with a tool called Microsoft Azure Active Directory Connect. AAD Connect is a tool installed locally within your Windows Server ecosystem, either on a dedicated virtual machine or on one of the domain controllers. It will connect to your Azure AD tenant and orchestrate synchronising your WS AD objects to Azure AD. 

The installation process is pretty straightforward. I recommend using the custom settings mode, even if only using a dedicated service account. You can also specify a SQL server if you have a shared database instance that you use for the smaller apps in your environment. 

![Azure AD Connect Custom Settings](Images/azureAdConnectAdvancedOptions.png)

The tool does need an MS SQL Database instance, so if you don't specify a custom one, the tool will install SQL 2019 Express as a default. Be mindful of the capacity consideration, as MS SQL does tend to consume as much memory as possible.

Once the required components are installed, you must make the most crucial choice in your AD Connect deployment - the sign-in method. 

### Three modes of sign-in

Azure AD Connect offers three sing-in modes that provide different options:
- Password Hash Synchronisation (PHS) will store a copy of the password hash (not clear text) in the cloud. Depending on your compliance framework, this might not be a viable option. It is, however, the most convenient one. With PSH, your AzureAD sign-ins don't rely on any on-prem infrastructure, so outages and maintenance won't be a problem. Also, with password write-back, users can change their passwords in AzureAD, which will get synchronised back to WS AD. There is also the Self-Service Password Reset (SSPR) option that you can enable in Azure AD for maximum gains. Depending on the size of your organisation, using SSPR can provide significant savings in support costs. 
- Pass-Through Authentication (PTA) will use the authentication agents installed on-premises for user sign-ins. It's as straightforward in deployment as PHS, but sing-ins will depend on the on-premises infra. That's why you should aim to deploy multiple authentication agents for high availability.
- Federation with either AD FS or PingFederatare. The latter is a relatively new option, and I've never tried using it. There the official documentation will be your best bet. I have, however, used AD FS many times, and unless you already have it or badly need one of the features only supported in this mode, I recommend avoiding it. AD FS requires several on-premises servers for a proper, highly available deployment (two federation servers, two federations proxy servers, and an MS SQL database), so it comes at quite a high cost. Both in terms of infrastructure and the management overhead. 

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
| Support smartcard authentication for my users.4 |   |   | X |
| Display password expiry notifications in the Office Portal and on the Windows desktop. |   |   | X |

You can also choose not to configure an Azure AD Connect sign-in method if you want to handle it via a federated third-party tool. 

Once you've made the critical decision regarding user sing-ins, you'll have to configure a few other settings regarding the synchronisation scope. Choose the domains and OUs and configure filtering. If you want to enable password write-back, you'll find this option in the "Optional Features" section of the setup wizard. 

## Active Directory in Azure

### Extending AD to Azure

- Deploying AD DS Domain Controllers
- Best Practices

### Azure AD Domain Services

- Benefits and limitations of AAD DS
- Coexistence with AD Connect

[ <- 1.2 - Azure Active Directory](azureActiveDirectory.md) | [ 1.4 - Managing Azure -> ](managingAzure.md)