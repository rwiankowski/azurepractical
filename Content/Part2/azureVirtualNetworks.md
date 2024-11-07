# 2.1 - Azure Virtual Networks

In this section we will cover the following topics:
- Virtual Network Basics
- Controlling Network Access
- Private DNS

## Virtual Network Basics

### Introduction

Compute resources are the core component of every application, but processing information becomes pointless if we cannot communicate with other systems to exchange said information. While some PaaS components like App Service have a default public endpoint and do not require a private dedicated network, others, like Virtual Machines, have such a requirement. Additionally, the public-by-default services can be made much more secure when connected to a private network.

### The Virtual Network

The Virtual Network, or the VNET in short, is what we use to create a private dedicated network in Azure. It is a software-defined network that spans the entire region to which it is deployed. It needs a continuous IP address space (for example, 10.0.0.0/16) and is subdivided into subnets (we need at least one subnet). 

IP address allocation, switching, routing, DNS, and a default Internet breakout for VMs are all provided out-of-the-box and don't require additional setup. Still, operators can configure those settings to meet their requirements. In a way, the VNET is like the device we get from a home Internet provider - all we need to do is connect the copper/fibre cable and power it on - we'll be ready to browse the online Azure documentation within a few minutes. 

*IMPORTANT - the default outbound Internet access for Azure VMs will be retired on Septerber 30th 2025. Please see the [official announcement](https://azure.microsoft.com/en-us/updates/default-outbound-access-for-vms-in-azure-will-be-retired-transition-to-a-new-method-of-internet-access/) for details.*

The Virtual Network is a free resource which does not have a monthly cost associated with it, but data egressing from your Azure VNET will be billed. Ingress is free.

### Exercise 2.1.1

Imagine that your company, Contoso, wants to start using that amazing Azure environment that you started setting up for them. One of the development teams is working on a new, ground-breaking application called App1 which has a straightforward architecture - just a Web Front End Server and a File Server. They asked you set up the environment. To get going, we will deploy a Virtual Network.

1) Create a new Resource Group.
    - You can call it whatever you like, but I will keep referring to it as App1-Network-RG.

2) Deploy a new Virtual Network.
    - Deploy it to your new Resource Group, i.e. App1-Network-RG.
    - Use whatever name you like, but I will keep referring to it as App1-Vnet.
    - Use whatever IP rage you like
    - Make sure it has two subnets - a WebFrontEndSubnet and a FileStoreSubnet.
    - Do not deploy any advanced features.

##  Controlling Network Access

### Excercise 2.1.2

Having a VNET is all nice, but by default, it's not really secure. Let's add some, at least basic, ACLs.

1) Create a new Network Security Group for the WebFrontEndSubnet.
    - Call it what you want, but I will refer to it as App1-FrontEnd-NSG
    - Make sure that the only allowed traffic is:
        - Inbound - HTTP from the internet, RDP from the FileStoreSubnet
        - Outbound - HTTP and HTTPS towards the Internet
    - Be sure to associate it with the correct subnet

2) Create another Network Security Group for the FileStoreSubnet.
    - Call it what you want, but I will refer to it as App1-FileStore-NSG
    - Make sure that the only allowed traffic is:
        - Inbound - RDP from your home IP and your office IP
        - Outbound - RDP towards the FrontEndSubnet
    - Be sure to associate it with the correct subnet

## Private DNS

### Excercise 2.1.3

Let us now deploy a Private DNS zone which the developers of App1 can use for internal name resolution.

1) Create a new Private DNS Zone
    - Pick any name you like, i.e. app1.internal.
    - Link the new DNS Zone to your VNET.
    - Be sure to enable auto-registration.

**IMPORTANT - Do not remove the resource created in the excercises above. You will need them in the next section!**

[< Part 2 Index](./partTwoIndex.md) | [Home - Course Contents](../Contents.md) |  [2.2 - Azure Virtual Machines >](./azureVirtualMachines.md)