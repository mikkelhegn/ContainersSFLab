# Create an second service to your application

## Goal

The goal of this section is to add a second service to your .net core application and run it in Mesh.

## Process

| **Step**                                         | **Procedure**                                                                                                                                |
| ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Create a second service for our Service Fabric Mesh Application using Visual Studio       | We will use Visual Studio to create a Service Fabric application hosting the container |
| Setup communication between the services | Using HTTPClient to communicate |
| Debug and deploy the services              | We will see it running in Mesh |

## Add a second to the Service Fabric Application

1. In Visual Studio **right-click** the Service Fabric Mesh Application project and choose **Add --> New Service Fabric Service**

1. Choose **ASP.NET Core** and click **Next**

1. Choose the **API** template

1. Open the **ValuesController.cs** file in the **Controllers** folder

1. Substitute the **Get()** method with this method:

```c#
    public String Get()
    {
        return "Hello from the other side";
    }
```

1. Save the file

## Setup communication between the services

1. Open the **About.cshtml.cs** file in the first project you created

1. Add the following code snippet to replace the OnGet method

```c#
    public async Task OnGet()
    {
        HttpClient client = new HttpClient();
        string response = await client.GetStringAsync("http://web1:2008/api/values);

        Message = response;
    }
```

**Note** Make sure to make the URL match the name of the new WebAPI service you created, as well as the port it's listening on. The port, can be found in the service.yaml file in the WebAPI project.

## Deploy the new version to Azure Service Fabric Mesh

1. To publish the updated application to Service Fabric Mesh in Azure, **Right-click** the Service Fabric Mesh application project and choose **Publish**

1. The publish dialog should have stored the information and you can click **Publish**

This will kick-off a rebuild and deployment of the containers. Once finished go to the web site. You can also browse the Azure portal to see the environment variable that the container is running with.