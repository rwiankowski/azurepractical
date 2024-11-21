# 5.1 - Web Apps and Functions

In this section we will cover the following topics:
- App Service
- Function Apps
- Logic Apps
- Application Load Balancers and CDN

## App Service

### Excercise 5.1.1

*Expect to spend 45 minutes on this excercise*

1. Create a new Resource Group.

2. Deploy an App Service Plan
    - Create an Azure App Service Plan
    - Use Linux OS and the Standard SKU

3. Configure autoscaling for the App Service Plan
    - Scale-out when CPU saturation goes above 70%
    - Remember to create a scale-in rule

4. Create an Azure Web App for Containers
    - Create a new Web App
    - Deploy it to the App Service Plan from the previous step of this excercise
    - Use Linux OS and Docker Container for Publish
    - Select the default quickstart image or any other image from the docker hub

5. Test your new app
    - Find the URL that you can use
    - Remember that it might be on port 80, not 443!

6. Create an Azure Web App for Code
    - Create a new Web App
    - Deploy it to the App Service Plan from the previous step of this lab
    - Use Linux OS, Code for Publish, and the latest PHP as the runtime stack

7. Deploy a sample application
    - Fork this repo https://github.com/rwiankowski/php-docs-hello-world/php into your GitHub account (or use something of your own if you have a PHP app for demo purpose)
    - In your new App Service, create a new Deployment Slot. I will be calling it Staging.
    - In the Staging Slot, use the Deployment Centre to set up continuous deployment from the forked GH repo.
    - Select the "App Service Build Service" (or KUDU) for the build provider

8. Test the App
    - Once the app is deployed to the staging slot, browse it
    - Then take a look at the production slot
    - Change slots, check again
    - It GitHub, make some changes to the code
    - Browse the production slot again
    - Browse the staging slot again

## Function Apps

## Logic Apps

## Application Load Balancers and CDN

### Excercise 5.1.2

*Expect to spend 30 minutes on this excercise*

1. Prepare your Dev Environment
    - If you don't have it yet, install VS Code
    - Install the Azure Functions extension
    - Log into Azure using the extension

2. Create a new Function App
    - Use the Azure extension to create a new Function App in your subscription
    - Use Python 3.11,

3. Add a function to the Function App
    - Create a new Function Project
    - Select Python, v2 programming model and an HTTP trigger
    - Use whatever authentication you prefer, Anonymous is OK

4. Deploy the Function
    - Explore the function_app.py file, ensure the HTTP trigger function is there
    - If yes, use the extension to deploy the App to the resource you created in step 3
    - Once deployed, find the URL to call your function and test it.

[< Part 5 Index](./partFiveIndex.md) | [Home - Course Contents](../Contents.md) |  [5.2 - Running Containers in Azure >](./webAppsFunctions.md)
