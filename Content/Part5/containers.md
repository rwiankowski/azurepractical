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
    - Add a new workload, use the provided YAML file 

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
        env:
        - name: ALLOW_EMPTY_PASSWORD
          value: "yes"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front

```

3. Test Around
    - Grab the public IP from the AKS Services
    - Wait a few minutes for the deployment to finish
    - Find the Resource Group that was created and search for your cluster's name in the resource groups section.
    - Look around, check the NSG, for example.

[5.1 - Web Apps and Functions >](./webAppsFunctions.md) | [Home - Course Contents](../Contents.md) |  [Part 6 Index >]()