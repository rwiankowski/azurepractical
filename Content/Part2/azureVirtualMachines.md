# 2.2 - Azure Virtual Machines

In this section we will cover the following topics:
- Azure Compute Options
- Virtual Machine Basics
- VM Availability Options
- Disk Encryption
- VM Extensions
- Azure Dedicated Hosts
- Azure Bastion

### Virtual Machine Basics


### VM Availability Options

### Exercise 2.2.1

Deploy two Virtual Machines to the Virtual Network you set up in the previous section.

1. Create a new Resource Group.
- You can call it whatever you like, but I will keep referring to it as App1-WebServer-RG.

2. Deploy a new WebServer Virtual Machine.
- Deploy it to your new Resource Group, i.e. App1-WebServer-RG.
- Use the following parameters:
    - B2M_S SKU
    - Windows Server 2022 OS
    - Use the existing Virtual Network and the appropriate subnet.
    - Include a Public IP address, but don't create a new NSG.
    - Standard SSD OS Disk
    - Disable Boot Diagnostics
    - Ensure auto-shutdown is enabled
- Explore other settings while configuring the Virtual Machine, take the time to read up. 

3. Repeat steps 1 and 2, but this time create a File Server. Put it in a dedicated Resource Group. 

4. Test connectivity
- Connect to your File Server VM via RDP it's Public IP address.
- Use the File Server as a jump box to connect to your Web Server.
- Try using the DNS name, i.e. WebServer.app1.internal.
- Install a web server on the Web Server VM (add IIS role). 
- Try browsing it.

### Azure Bastion

### Exercise 2.2.2

In the previous exercise we connected to the File Server Virtual Machine using a public IP address. While it is a working solution, it is not the safest implementation of remote access. To make our desing a bit cleaner we will deploy Azure Bastion.

1. Create a new Resource Group.
    - You can call it whatever you like, but I will keep referring to it as App1-Bastion-RG.

2. Adjust your virtual network to have a dedicated subnet of Azure Bastion
    - add the AzureBastionSubnet
    - not not associate a Network Security Group with the subnet

3. Deploy Azure Bastion
    - use the Resource Group you just created - App1-Bastion-RG.
    - use the subnet you just created - AzureBastionSubnet

4. Test Connectivity
    - adjust the FileSeverSubnet NSG to disallow RDP traffic from your public IP
    - adjust the FileSeverSubnet NSG to allow RDP traffic from the AzureBastionSubnet
    - adjust the WebSeverSubnet NSG to disallow RDP traffic from the FileSeverSubnet
    - adjust the WebSeverSubnet NSG to allow RDP traffic from the AzureBastionSubnet
    - try connecting to both VMs using Azure Bastion

### Disk Encryption


### VM Extensions


### Azure Dedicated Hosts


[< 2.1 - Azure Virtual Networks](./azureVirtualNetworks.md) | [Home - Course Contents](../Contents.md) |  [ 2.3 Highly-Available Deployments>](./highlyAvailableDeployments.md)