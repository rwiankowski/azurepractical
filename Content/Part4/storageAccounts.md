# 4.1 - Storage Accounts

In this section we will cover the following topics:
- Storage Accounts Basics
- BLOB Storage
- Storage Security
- Managing Storage
- Azure Files
- Table Storage

## Storage Accounts Basics

## BLOB Storage

## Storage Security

## Managing Storage

### Excercise 4.1.1

*Expect to spend 30 minutes on this excercise.*

A Storage Account is a fundamental Azure Service for data storage. It offers a variety of services for:
- Containers for object storage (BLOB) accesses via HTTP
- File Shares accessed via SMB 3.0
- Table storage for non-relational data
- Queue service for asynchronous communication
- and it can even be turned into an Azure Data Lake (v2) with the hierarchical namespace option enabled.

That wide spectrum of option makes the Azure Storage Account a good candidate for App1. Let's try out a few things. Storing data in files is the simplest option, so that we will start with basic object storage.

1. Create yourself a new Resource Group.

2. Deploy a new Storage Account
    - Use whatever name you like, but I will keep referring to it as part4storagerawi (we're limited to 24 alphanumerical characters, and the name needs to be globally unique).
    - Use local redundancy for replication, Standard for performance, and stick to the defaults for everything else. 

3. Upload some test data
    - Use the portal to create a container.
    - Upload some test data.

4. Configure Lifecycle Management
    - Configure a lifecycle policy that moves storage to the cool tier and ultimately to the archive tier after 30/180 days.

5. Test Azure Storage Explorer
    - Download the Azure Storage Explorer app for your platform of choice.
    - Generate a Shared Access Signature token that grants access to your test data (make a note of the key used to sign it).
    - Use the SAS token to connect via Storage Explorer.
    - Try to write and read files, troubleshoot if needed.
    - Regenerate the access key which you used to sign the token.
    - Try accessing the data.

## Azure Files

## Table Storage

## Endpoints

### Excercise 4.1.2

*Expect to spend 20 minutes on this excercise*

Using BLOB containers is fairly straightforward. We have a lot of nice options to manage and secure data. But what if the application isn't ready for using BLOB storage? It might, at least in the beginning, require a good, old file hare. While we could deploy a Virtual Machine, which would serve as a file server, the Azure Storage Account seems to have a nice, cloud-native service that could do the job just as well. Let's take a closer look at it.

1. Add a File Share to your Storage account

2. Deploy a Virtual Network
    - Create a subnet for a Virtual Machine.
    - Add Azure Bastion.

3. Deploy a Virtual Machine
    - Use Windows Server as the OS.
    - Choose any settings you like, but I recommend a B2mS SKU and a Standard SSD as the perfect blend of price and performance.

Before proceeding, we will adjust the Firewall setting to improve the security of our storage account. Even though there's no anonymous access, and all requests need to authenticate, we don't want strangers knocking on our door.
    
4. Configure VNET access on the Storage Account

    - Use a Service Endpoint to access only from the client subnet of your new VNET
    - Be sure to create the service endpoint on the subnet.

5. Mount the File Share and test

    - Mount the File Share as a network drive on your VM
    - Add some files
    - Try accessing the share from your browser

**Do not remove any resources just yet, we'll use them for the next excercise.**

### Excercise 4.1.3

*Expect to spend 25 minutes on this excercise*

Mounting the File Share directly on the VM  works nice but has a few disadvantages. First and foremost, we needed to use one of our two almighty access keys. Should we ever rotate the keys (and we should do it regularly), we would need to reconfigure the file share. That's not all that optimal. Also, from a network perspective, if we're using an Azure VM, we can configure a secure, private connection, but should we have an on-premises VM, we would need to put in much more effort. Thankfully, there is a really nice service to help with those challenges. It's called Azure File Sync.
    
1) Clean up some of the old configuration
    - Unmount the file share
    - Remove the subnet from the ACL - leave the Storage Account completely locked from the network perspective

2) Deploy a new VM
    - Use the same settings as previously

3) Configure Azure File Sync
    - Create the Azure File Sync Resource
    - Create a Sync Group, use the Storage Account and File Share from the previous excercise
    - Register both VMs by installing the Azure File Sync Agent
    - Add two Server Endpoints to the Sync Group

4) Test
    - Upload and modify some files
    - Observe how the service synchronises your changes

Azure File Sync is a really nice service, especially if you need to bridge a gap between the legacy world with the cloud-native world. You could even replace DFS replication with it. And on top of that, you'll always have another copy of your data in the cloud... what's not to like about that?

[< Part 4 Index](./partFourIndex.md) | [Home - Course Contents](../Contents.md) |  [4.2 - Databases >](./databases.md)