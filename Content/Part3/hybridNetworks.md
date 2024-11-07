# 3.2 - Hybrid Neworks

In this section we will cover the following topics:
    - Azure VPN
    - Azure Express Route

## Azure VPN

### Excercise 3.2.1

Normally, we would set up redundant Express Route Circuits for our hybrid connectivity in a customer-facing scenario. However, the problem with Express Route is that it's close to impossible to use in a lab environment. So, instead, we will set up a VPN connection.

1) Create a new Resource Group.
    - You can call it whatever you like, but I will keep referring to it as Remote-Network-RG.

2) Deploy a new Virtual Network.
    - Deploy it to your new Resource Group, i.e. Remote-Network-RG.
    - Use whatever name you like, but I will keep referring to it as Remote-Vnet.
    - Use whatever IP rage you like, but don't overlap with the other VNETs.
    - Make sure it has two subnets - ManagementSubnet and GatewaySubnet.
    - Do not deploy any advanced features.

3) Deploy a VPN Gateway in the Remote VNET
    - Add a Virtual Network Gateway, type VPN, to your Remote VNET
    - Use Route-based, Basic (to save cost) and Generation 1
    - Let's not complicate things too much for now, and avoid using Active-Active and BGP

4) Deploy a VPN Gateway in the Hub VNET
    - Use the same settings as for the gateway in the Remote VNET

5) Deploy a Local Network Gateway representing the Hub VNET
    - Use the public IP of the Hub VNET VPN Gateway
    - Use the local IP ranges connected to the Hub VNET.

6) Deploy a Local Network Gateway representing the Remote VNET
    - Use the public IP of the Hub Remote VPN Gateway
    - Use the local IP ranges connected to the Remote VNET

7) Connect the VNETs
    - Create two VPN Connections that connect the VPN Gateways and Local Network Gateways across
    - Use Site-to-Site (IPSEC) type of a connection

8) Deploy a Route Table
    - Create a new Route Table in your Hub Resource Group
    - This time, do not disable Gateway Route propagation
    - Add routes that ensure all traffic destined to the subnets on this side of the tunnel towards the Firewall private IP.
    - Be sure to associate the Route Table with the GatewaySubnet of the Hub VNET.

9) Deploy a new Virtual Machine in the Remote VNET
    - Make use of the ManagementSubnet.

10) Connect and test
    -  Use Bastion to connect to the Management VM
    - Try connecting from the Management VM to your Web Server and File Server via RDP.
    - Adjust the NSGs and Firewall Rules accordingly

## Azure Express Route


[< 3.1 - Multi-Network Environments](./multiNetworkEnvironments.md) | [Home - Course Contents](../Contents.md) |  [3.3 - Integrated Network Services >](./integratedNetworkServices.md)