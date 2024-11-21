# 3.2 - Databases

In this section we will cover the following topics:
    - Azure SQL
    - Cosmos DB

## Azure SQL

### Excercise 4.2.1

*Expect to spend 45 minutes on this excercise*

Azure offers a wide variety of choices regarding running databases in Azure, both relational and NoSQL ones. Given that SQL is still a dominant choice, we will focus on those for now. 

While we have managed PaaS variants of open-source databases like MySQL and PostgreSQL, our development team, working on App1, is more familiar with Microsoft SQL. Therefore, we will take a closer look at the options for running some variant of this popular database engine. Microsoft is trying very hard to Azure a great place to run a SQL database, no matter the underlying requirements. Even when running MS SQL inside a Virtual Machine, we get nice options that help us reduce the management overhead. And, as much as we would like to use managed services, sometimes we need to deploy a SQL VM. We might be constrained to using a specific version of the engine, might be working with a vendor that refuses to support cloud-native solutions or have more technical requirements like SSAS or SSRS installed on the same system. So let's start our journey with this traditional way of running a SQL database in Azure.

1. Prepare your environment
    - If you haven't cleaned up after working with storage accounts, you can use the same Resource Group and the same Virtual Network.
    - If you did remove those resources, make sure you have a Resource Group, a VNET with at least one client subnet and Azure Bastion ready./

2. Deploy the VM
    - In the Azure Marketplace, find "Microsoft SQL Server 20xx on Windows 20xx".
    - Create a new Virtual Machine Using this image
    - I traditionally recommend the B2mS SKU and Standard SSD disks 

3. Restore a sample database

    - Connect to your new Virtual Machine
    - If you're curious inspect how the MS SQL instance is configured 
    - Use SQL Management Studio (SSMS) to restore a sample database. I recommend [this one github](https://github.com/microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Full.bak)/

4. Explore the Azure-specific options
    - In your Resource Group, you should find an additional resource of type "SQL Virtual Machine". Take a closer look at it and explore the options. 

Even with those cool features, however, we're still running a Virtual Machine, and that is something that we would ultimately like to avoid. Let's see if we can find a managed service that doesn't force us to take care of infrastructure components. The next step in this quest is often the SQL Managed Instance. It runs a fully functional version of the Microsoft SQL Server, but we're constrained to the latest stable version, and we don't get to deploy SSAS, SSRS or SSIS. It's a great option for customers who still cannot use the true PaaS offering but don't want to manage VMs. We will, however, go directly for the truly cloud-native option. 

Azure SQL itself comes with a wide variety of choices that one must make before deploying a database. When it comes to pricing, we must decide between the Database Throughput Units (DTUs) or virtual CPU cores (vCore). On top of that, we also can choose between Standard/General Purpose, Premium/Business Critical and Hyperscale. Although each fundamentally provides the same functionality, they rely on different underlying architectures, impacting reliability and performance. But let's just try it out.

We will first need to prepare an Azure SQL Server. The server is a logical container for Azure SQL databases that will share a single management plane but will still use dedicated compute resources. If that sounds confusing, try to search for more confirmation about the single database and the elastic pool consumption models.

5. Prepare the Azure SQL Server
    - Deploy an Azure SQL Server (it might seem harder than it seems at first)
    - Once it's deployed, enable VNET integration but this time use a Private Endpoint
    - Be sure to add yourself as the Azure AD admin

6) Migrate the sample database to Azure SQL
    - Go back to your SQL VM and try to migrate the sample database to Azure SQL PaaS.There are multiple ways of doing it. Some are very easy!

## Cosmos DB

[< 4.1 - Storage Accounts](./storageAccounts.md) | [Home - Course Contents](../Contents.md) |  [4.3 - Integrating Applications >](./integratingApplications.md)