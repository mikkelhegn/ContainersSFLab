# Deploy a container to Mesh with CLI

## Goal
 The goal of this section is to deploy a container to the Mesh environment in Azure and access the application running in Azure.

 ![Hello world app in the browser][sfm-app-browser]

## Steps
In this exercise, you will complete the following steps 
* Install the Azure Mesh CLI
* Create a resource group for your application
* Deploy a prepackaged container using an ARM template which contains the application
* Access the application


| **Step** | **Action** | **Result** |
| -------- | ---------- | ---------- |
| 1        | Open Azure cloud shell or a local terminal if Azure CLI is installed and [install the Azure Mesh CLI extension](service-fabric-mesh-howto-setup-cli.md).                                             | In your terminal you will be able to run `az mesh -h` to view available Mesh CLI commands                                      |
| 2        | Sign into Azure  <br>`az login`      <br> `az account set --subscription "<subscriptionID>"`                                                                                    | In the terminal, you will see details of the subscription such as the name and id in json format                              |
| 3        | Create a resource group <br> `az group create --name meshAppRg --location eastus`                                                                                                          | The newly created resource group details will appear in the terminal with "provisioningState" : "Succeeded"                                                    |
| 4        | Deploy the application with the following command <br> `az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json` <br> Enter `eastus` when prompted for a location                                                                                                             | The terminal will indicate the application is deploying. Once successfully finished, it will display the IP address to access the application                                                                                               |
| 5        | Navigate to the IP address from the step above in your browser | You will see the webpage from the image at the top of this exercise with the Mesh logo|
| 6        | Run the show command to view more information about your Mesh application. This application's name is `helloWorldApp` <br> `az mesh app show --resource-group myResourceGroup --name helloWorldApp`                                                                                                                                            | Additional information about your application will appear including the services, status, description, resourceId etc. 

<!-- Images -->
[sfm-app-browser]: ./HelloWorld.png

