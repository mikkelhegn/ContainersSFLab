# Deploy a container to Mesh with CLI

## Goal
 The goal of this section is to deploy a container to the Mesh environment in Azure and access the application running in Azure.

## Steps
In this exercise, you will complete the following steps 
* Install the Azure Mesh CLI
* Create a resource group for your application
* Deploy a prepackaged container using an ARM template which contains the application
* Access the application


| **Step** | **Action** | **Result** |
| -------- | ---------- | ---------- |
| 1        | Open Azure cloud shell (https://shell.azure.com) |               |
| 2        | run `az extension add --name mesh` to add the Mesh extension to Azure CLI         | In the terminal, you will see details of the subscription such as the name and id in json format |
| 3        | Create a resource group <br> `az group create --name meshAppRg --location eastus`                                                                                     | The newly created resource group details will appear in the terminal with "provisioningState" : "Succeeded" |
| 4        | Deploy the application with the following command <br> `az mesh deployment create --resource-group meshAppRg --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json` <br> Enter `eastus`, `westus` or `westeurope` when prompted for a location. If it doesn't prompt you, append `--parameters "{'location': {'value': 'eastus'}}"` to the command.                                                                                                           | The terminal will indicate the application is deploying. Once successfully finished, it will display the IP address to access the application                                                                                               |
| 5        | Navigate to the IP address from the step above in your browser | You will see the webpage from the image at the top of this exercise with the Mesh logo|
| 6        | Run the show command to view more information about your Mesh application. This application's name is `helloWorldApp` <br> `az mesh app show --resource-group meshAppRg --name helloWorldApp`                                                                                                                                            | Additional information about your application will appear including the services, status, description, resourceId etc. |
| 7        | Open `http://portal.azure.com` and navigate the resource group and Mesh service | |
| 8        | Keep navigating through the service, replica and finally the `helloWorldCode' Code Package. Here you can see the logs emitted by the container to stdout | Services in Mesh can have multiple code packages, which are always deployed together and share ip |
| 9        | Let's scale up the service, by adding another replica, let's start with downloading the WRM template: `curl -o mesh_rp.linux.json https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json` | This will save the file to the cloud shell storage |
| 10       | Open the file in VS Code, in cloud shell `code mesh_rp.linux.json` | |
| 11       | In line 80, change the replicaCount to `2` | This is the number of replicas of a service you want to run. Remember at this services features two code packages, the consumption will be twice the sum of the code package resources defined. |
| 12       | Save the file and close the editor ctrl + s, ctrl + q | There a small ellipse icon in the top right corner to get to the menu |
| 13       | Let's update the deployment by running `az mesh deployment create --resource-group meshAppRg --template-file mesh_rp.linux.json` | ARM uses incremental deployment per default, so only changes will be applied to the deployment |
| 14       | Head over to the Azure Portal to see a second replica of he service now running |

## Clean up

To clean up simple delete the resource group `az group delete --name meshAppRG -y` this will delete without confirmation.

## Conclusion

This concludes you first experience with Mesh - congratulations!

<!-- Images -->
[sfm-app-browser]: ./HelloWorld.png
