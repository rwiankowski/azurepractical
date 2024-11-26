# 5.2 - Running Containers in Azure

In this section we will cover the following topics:
- Container Instances
- Azure Kubernetes Service
- Container Registry
- Container Apps

## Container Instances

### Excercise 5.2.1

*Expect to spend 30 minutes on this excercise*

1. Prepare the environment
    - Create a new Resource Group
    - Provision a VNET

2. Deploy a Container Instance
    - Create a new Container Instance
    - For most of it, use the defaults (including the image) 
    - But do connect it to the VNET

3. Deploy the Application Gateway
    - Create a new Azure Application Gateway
    - Go for the WAF_v2 SKU
    - Use your new subnet
    - Use a Public front-end configuration
    - Add your recently deployed ACI as the backend
    - Configure a routing rule from the public front-end to the ACI backed
    - Use unencrypted HTTP for now

4. Test your application

## Azure Kubernetes Service

## Container Registry

## Container Apps

### Excercise 5.2.2

*Expect to spend 30 minutes on this excercise*

1. Create a new Azure Kubernetes Service
    - For most of it, use the default settings
    - Disable Container Monitoring in the "Integrations Section”

2. Deploy a sample app
    - Go to your AKS cluster
    - Add a new workload, use the [example YAML file](https://github.com/Azure-Samples/aks-store-demo/blob/main/aks-store-all-in-one.yaml)

3. Test Around
    - Grab the public IP from the AKS Services
    - Wait a few minutes for the deployment to finish
    - Find the Resource Group that was created and search for your cluster's name in the resource groups section.
    - Look around, check the NSG, for example.

[5.1 - Web Apps and Functions >](./webAppsFunctions.md) | [Home - Course Contents](../Contents.md) |  [Part 6 Index >]()