# ContainersSFLab - Service Fabric Mesh application

The instructions below walk you through working with ASP.NET for Azure Service Fabric Mesh.

For more info about Service Fabric Mesh look here: https://docs.microsoft.com/en-us/azure/service-fabric-mesh

Requirements for running the lab are documented here: https://docs.microsoft.com/en-us/azure/service-fabric-mesh/service-fabric-mesh-howto-setup-developer-environment-sdk

You'll also need an Azure subscription. If you do not have one, you can get a trial subscription here: https://azure.microsoft.com/en-us/free/

## Lab content

1. Build a Docker container and upload it to Azure Container Registry
    1. [Build a Docker container for an existing asp.net application](./1buildacontainer.md)
    1. [Create an Azure Container Registry and push the container](./2createacr.md)
1. Azure Service Fabric Mesh 101
    1. [Deploy a container to Azure Service Fabric Mesh](./3meshhelloworld.md)
1. Build an application for Azure Service Fabric Mesh
    1. [Use Visual Studio to create and ASP.NET Core application for Mesh](./4createappvs.md)
    1. [Add more services to your application](./5addservice.md)

If you are already familiar with building Docker containers and using ACR, you can skip the first part of the lab.