# 2.3 - Highly Available Deployments

In this section we will cover the following topics:
    - Azure Load Balancer
    - Virtual Machine Scale Sets
    - Azure Batch

### Azure Load Balancer


### Excercise 2.3.1

In the previous section we deployed an entry-level setup which included a file server and a web server. It worked very well, but we had to configure everything manually and we lacked high availability. 
In this excercise, we will try to create a new, more advanced deployment.

1) Create yourself a new Resource Group.
    - You can call it whatever you like, but I will keep referring to it as App2-Network-RG.

2) Deploy a new Virtual Network
    - Deploy it to your new Resource Group, i.e. App2-Network-RG.
    - Use whatever name you like, but I will keep referring to it as App2-vnet.
    - Use an IP range you like, but make sure it's unique within your environment. Use a /16 prefix.
    - Create one subnet - WebServerSubnet
    - Do not deploy any advanced features.

3) Create a new Network Security Group.
    - Call it what you want, but I will refer to it as App2-WebServer-NSG.
    - Make sure that the only allowed traffic is:
        - Inbound - HTTP from the internet
        - Outbound - HTTP and HTTPS towards the Internet.
    - Be sure to associate it with the correct subnet.
    - Try using Application Security Groups.

4) Create yourself a new Resource Group.
    - You can call it whatever you like, but I will keep referring to it as App2-VirtualMachines-RG.

5) Deploy two zone-redundant Windows VMs
    - Use the Portal to explore available options.  
    - After the first one, you can speed things up by using the re-deploy option.
    - Deploy the VMs across two different availability zones.
    - Do not deploy Public IPs or NSGs.
    - Use Standard SSD disks.
    - Use Windows 2022 as the OS.

6) When you Virtual Machines are deployed add the Custom Script Extension each of them. 
    - Use Cloud Shell to run the script provided below:

    ```PowerShell
    Set-AzVMExtension -ResourceGroupName "test-radek" -ExtensionName "IIS" -VMName "testradek" -Location "SwedenCentral" -Publisher Microsoft.Compute -ExtensionType CustomScriptExtension -TypeHandlerVersion 1.8 -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
    ```

7) Deploy a Load Balancer 
    - Configure a Load Balancer for the two VMs.
    - Make it a Public one.
    - Be mindful of the SKU.
    - Load balance HTTP traffic between both VMs.

8) Test
    - Try navigating to the public IP of the Load Balancer with your browser. You should see the IIS splash screen. 


### Virtual Machine Scale Sets


### Azure Batch


### Excercise 2.3.2

The two Virtual Machines you just deployed give us a highly-available and zone-redundant setup - that is quite the upgrade for business continuity. And while you didn't need to interactively log into the the Virtual Machines to configure IIS, you still had to use Cloud Shell to deploy the extensions. 
To take things even further, we will now replace the VMs with a VM Scale Set.

1) Clean up your App2 VMs
    - We won't be needing them any more.
    - How about simply removing the entire Resouce Group?

2) Create a new Resource Group.
    - You can call it whatever you like, but I will keep referring to it as App2-VMSS-RG.

3) Deploy a VM Scale Set
    - Create a new Virtual Machine Scale Set using:
        - Whatever name you like
        - A Linux OS (to stir things up a little)
        - Availability Zones for high-availability
        - The same VNET/Subnet as before. NSGs are still in place, right?
        - Automatic Scaling - set the minimum instances to two. Use the defaults for the rest.
    - Cloud-init to set it up as a web server

    ```Cloud-Init
    #cloud-config
    package_update: true
    package_upgrade: true

    packages:
    - nginx

    runcmd:
    - systemctl start nginx
    - systemctl enable nginx
    ```


[< 2.2 - Azure Virtual Machines](./azureVirtualMachines.md) | [Home - Course Contents](../Contents.md) |  [ Part 3 - Multi-Network Environments >](../Part3/partThreeIndex.md)