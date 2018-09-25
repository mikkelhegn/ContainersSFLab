# Create an Azure Container Registry, and push your container to a registry

**Goal:** The goal of this section is to publish the container image to
a private Azure Container Registry.

## Azure Subscription
If you do not have one, you can get a trial subscription here: https://azure.microsoft.com/en-us/free/

## Process

<table>
<thead>
<tr class="header">
<th><strong>Step</strong></th>
<th><strong>Procedure</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Create a container registry</td>
<td><p>Create a private container registry using the Azure Container Registry service.</p>
<p><strong>Note:</strong> This requires an Azure account</p></td>
</tr>
<tr class="even">
<td>Build, tag and push the container image</td>
<td>Create a production ready container image and push it to the registry.</td>
</tr>
<tr class="odd">
<td>Pull the container image and run it locally</td>
<td>Pull the production ready image from the container registry and run it.</td>
</tr>
</tbody>
</table>

## Create a container registry

In this section we will create a private container registry in Azure
using Azure Container Registry.

<table>
<thead>
<tr class="header">
<th><strong>Step</strong></th>
<th><strong>Action</strong></th>
<th><strong>Result</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>1</td>
<td>Browse to <a href="https://shell.azure.com/" class="uri">https://shell.azure.com/</a> and sign-in to your Azure subscription.</td>
<td>If you do not have one, you can get a trial subscription here: <a href="https://azure.microsoft.com/en-us/free/" class="uri">https://azure.microsoft.com/en-us/free/</a></td>
</tr>
<tr class="even">
<td>2</td>
<td><p>Create a new resource group:</p>
<p><strong>az group create --name <em>[insert rg name]</em> --location eastus</strong></p>
<p><strong>Note:</strong> Substitute the <em><strong>[insert rg name]</strong></em> with the name for your Resource Group</p></td>
<td>az will output information about the resource group.</td>
</tr>
<tr class="odd">
<td>3</td>
<td><p>Create a container registry in that resource group:</p>
<p><strong>az acr create --resource-group <em>[insert rg name]</em></strong> <strong>--name <em>[insert acr name]</em></strong> <strong>--sku Basic --admin-enabled</strong></p>
<p><strong>Note:</strong> Substitute the <em><strong>[insert acr name]</strong></em> with the name for your registry name</p></td>
<td>az will output the information about the newly created acr.</td>
</tr>
<tr class="even">
<td>4</td>
<td><p>Retrieve the credentials to sign-in to the container registry, and note these to be used later:</p>
<p><strong>az acr credential show --name <em>[insert acr name]</em></strong></p>
<p><strong>Note:</strong> Substitute the <em><strong>[insert acr name]</strong></em> with the name for your registry name</p></td>
<td>az will output the passwords and login names for the registry, which we will use later in the lab.</td>
</tr>
</tbody>
</table>

### Completion

In this section of the lab, we’ve created a private container registry
as a repository to store your container images.

##   

## Build, tag and push the container

In this section we will prepare our container image to be uploaded, by
tagging it and upload the image to our registry.

<table>
<thead>
<tr class="header">
<th><strong>Step</strong></th>
<th><strong>Action</strong></th>
<th><strong>Result</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>1</td>
<td><p>Open <strong>PowerShell</strong> and run the following command to tag the image:</p>
<p><strong>docker tag eshopweb:1.0 <em>[insert acr name]</em>.azurecr.io/eshopweb:1.0</strong></p>
<p><strong>Note:</strong></p>
<p>The registry part of the container image tag should match the name of your container registry.</p></td>
<td>This command creates a container image tag with the container registry name and version of the container image.</td>
</tr>
<tr class="even">
<td>2</td>
<td><p>Run the following command to login to your container registry:</p>
<p><strong>docker login <em>[insert acr name]</em>.azurecr.io</strong></p>
<p><strong>Note:</strong></p>
<p>To retrieve the login server, username and password, see above section</p></td>
<td></td>
</tr>
<tr class="odd">
<td>3</td>
<td><p>Push the container image to your registry:</p>
<p><strong>docker push <em>[insert acr name]</em>.azurecr.io/eshopweb:1.0</strong></p></td>
<td>This will start uploading the container image layers to you Azure Container Registry.</td>
</tr>
</tbody>
</table>

### Completion

In this section of the lab, we’ve tagged our container image and
uploaded it to our private container registry, ready to be deployed to
any container host.

## Clean up

Finally, let’s clean-up, by running the following commands in PowerShell in the directory C:\Users\Administrator\Desktop\Lab:

1. git clean -df
1. git reset --hard