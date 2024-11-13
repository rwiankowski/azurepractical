# 3.1 - Multi-Network Environments

In this section we will cover the following topics:
- VNET Peering
- Azure Firewall
- Route Tables

## VNET Peering

### Excercise 3.1.1

We now have two Virtual Networks - one with a pair of Web Server VMs and a File Server, and another one with a VM Scale Set. However, without any inter-VNET connections, our environment is still suboptimal. The VM scale set cannot use the file server, and we cannot connect to the VMSS nodes using Azure Bastion. We could create a VNET peering between the two neworks to solve these challenges, but should we add more networks, things will quickly start getting unruly. Therefore, we will start building our Hub-Spoke Network Topology.

1. Remove Azure Bastion from the App1 VNET
    - Remove the service and the subnet
    - You can leave the NSG rules in place for now

2. Create yourself a new Resource Group.
    - You can call it whatever you like, but I will keep referring to it as Hub-Network-RG.

3. Deploy a new Virtual Network.
    - Deploy it to your new Resource Group, i.e. Hub-Network-RG.
    - Use whatever name you like, but I will keep referring to it as Hub-Vnet.
    - Use whatever IP rage you like, but don't overlap with the VNETs used by App1 and App2.
    - Make sure it has two subnets - AzureFirewallSubnet and AzureBastionSubnet.
    - Do not deploy any advanced features.

3. Configure VNET Peering1
    - Set up a peering between the App1 Vnet and the Hub Vnet
    - Set up a peering between the App2 Vnet and the Hub Vnet

4. Deploy Azure Bastion
    - Deploy a new Azure Bastion instance to your Hub Vnet
    - Adjust the Network Security Groups of your App1 subnets - make sure that RDP traffic is only allowed via the Bastion
    - Adjust the Network Security Groups of your App2 subnet - make sure that SSH traffic is only allowed via the Bastion
    
5. Test Connectivity
    - Try connecting to your Virtual Machines using Azure Bastion - use RDP for Windows and SSH for Linux

## Azure Firewall

## Route Tables

### Excercise 3.1.2

If everything went well, you were able to use the single instance of Azure Bastion to access instaces hosting both App1 and App2. But we sill cannot use the file server of App1 from the App2 nodes. That is because VNET peerings are non-transitive, and so we need a routing device within the hub network. Let's add it!

1. Deploy Azure Firewall
    - Create a new instance of the Azure Firewall in your hub VNET
    - As a good practice, I recommend always making your firewall zone-redundant
    - Use the Standard SKU
    - Use Firewall Manager for configuring the rules.

2. Configure monitoring for the Firewall
    - Deploy a Log Analytics Workspace to your Part2-Hub-RG
    - Configure Diagnostic Settings for your Azure Firewall, which send all logs and metrics to that workspace
    
3. Deploy a Route Table for App1
    - Create a new Route Table in your App1 Network Resource Group
    - Disable Gateway Route propagation
    - Add a default route pointing to the private IP of your firewall
    - Be sure to associate the Route Table with both subnets in your App1 VNET.

8. Test Connectivity
    - Log into both of your Virtual Machines and try accessing the Internet, for example.
    - Take a look at your Firewall logs. Explore Application Rule logs and Network Rule logs.
    - If you're feeling explorative, add some rules to your Firewall and test a bit more. See if the connection between the File Server and the VMSS nodes works.
    - Don't forget the NSGs!

[< Part 3 Index](./partThreeIndex.md) | [Home - Course Contents](../Contents.md) |  [3.2 - Hybrid Networks >](./hybridNetworks.md)