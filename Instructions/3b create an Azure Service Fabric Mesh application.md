# Create an Azure Service Fabric Mesh application

## Goal

The goal of this section is to create a new .net core application and run it in Mesh.

## Process

| **Step**                                         | **Procedure**                                                                                                                                |
| ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Create a Service Fabric Mesh Application using Visual Studio       | We will use Visual Studio to create a Service Fabric application hosting the container |
| Deploy the application to Service Fabric Mesh | Using Visual Studio and Azure Container Registry |
| Add configuration to the service              | We will apply configuration to the service using the Mesh model |

## Create a Service Fabric Application

In this section we will create a Service Fabric application with the
container as a service, and configure it.

1. Open **Visual Studio**

1. Create a new **Service Fabric Mesh Application** project

1. Choose **ASP.NET Core**

1. Choose the **Web Application** template

Notice that two projects are created. The Mesh Application project holds the yaml files, which described shared resources for the application. The asp.net core web project is a regular asp.net core project, which runs anywhere. But the project also has a Dockerfile and a service.yaml file to describe how to run it in Mesh.

1. Press **F5** to debug the application

Once you hit F5, Visual Studio will help setup a local Service Fabric cluster to host the Mesh application in containers.

Once completed, the application is shown in your browser.

1. In Visual Studio stop the debug session by pressing **Shift+F5**

## Publish the application to Azure Service Fabric Mesh

1. To publish the application to Service Fabric Mesh in Azure, **Right-click** the Service Fabric Mesh application project and choose **Publish**

1. Sign in to your Azure account and provide the information needed for the Publish dialog.

Visual Studio will pull a container image to complete the container build. This may take a few minutes to complete. The Visual Studio output window will show the progress.

Once the deployment is done. Visual Studio will output the IP address of the application running in Mesh.

1. Open the Azure Portal **https://portal.azure.com**

1. Browse to the resource group you just created

1. Open the Mesh Application and browse through Services - Replica 0 - Code Package and choose **Logs**

This will show you the stdout container logs form the container deployed to Mesh

## Add configuration

1. In Visual Studio, open the **service.yaml** file under the **Service Resources** folder in the Web project.

1. In **line 22 and 23** add another environment variable with the name **MyEnv** and a random value

1. Save the file

1. Open the **About.cshtml** file in the **Pages** folder

1. Add the following line to the file:

``` cshtml
<p>MyVar value: @Environment.GetEnvironmentVariable("MyVar")</p>
```

1. Press **F5** to debug the application, once the web site comes up, go to the **About** page and you should see the value from the environment variable show up.

1. Without stopping the debugger, you can make changes to the About.cshtml file and reload the browser to see you changes. There is no need to redeploy the container and app to quickly see changes that do not require recompiling the code.

1. Stop the debug session by pressing **Shift+F5**

## Deploy the new version to Azure Service Fabric Mesh

1. To publish the updated application to Service Fabric Mesh in Azure, **Right-click** the Service Fabric Mesh application project and choose **Publish**

1. The publish dialog should have stored the information and you can click **Publish**

This will kick-off a rebuild and deployment of the containers. Once finished go to the web site. You can also browse the Azure portal to see the environment variable that the container is running with.

### Completion

In this section of the lab, we’ve build a new asp.net core service and deployed it to Azure Service Fabric Mesh