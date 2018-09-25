# ContainersSFLab - Service Fabric Native application

The instructions below walk you through packaging an existing ASP.NET application for Service Fabric clusters, using the Service Fabric Native application model.

For more info about Service Fabric application models, look here: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-technical-overview#application-and-service-concepts

Requirements for running the lab are

- Windows 10 Enterprise or Professional Edition or Windows Server 2016
- Docker for Windows
- Visual Studio 2017 15.7
- Service Fabric SDK 3.1 and Service Fabric Runtime 6.2
- An Azure subscription: If you do not have one, you can get a trial subscription here:https://azure.microsoft.com/en-us/free/

## Containerize an existing ASP.NET application using Visual Studio and Service Fabric

### Lab Overview

In this lab, you will be guided through the following tasks:

1. Build a Docker container for Windows, with an existing ASP.NET web
    application

1. Create an Azure Container Registry, and push your container to a
    registry

1. Create an Azure Service Fabric application with your container

1. Deploy your application to Azure Service Fabric
  
## Build a Docker container for Windows, with an existing ASP.NET web application

**Goal:** The goal of this section is to have an ASP.NET application running in a Docker container on your Windows 10 developer machine.

### Process

| **Step**                           | **Procedure**                                                                                          |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Run the application                | Run the existing ASP.NET application to verify its functionality, using Visual Studio and IIS Express. |
| Create a Docker container          | Create a docker container.                                                                             |
| Run the application in a container | Run and debug the application running in a container, using Visual Studio.                             |

## Run the Application

In this section we will test the ASP.NET application using Visual Studio
and IIS
Express.

| **Step** | **Action** | **Result** |
| -------- | ---------- | ---------- |
| 1        | Open the solution **eShopLegacyWebForms.sln**, which you will find in the **\\Lab\\eShopLegacyWebFormsSolution** folder on the desktop. <br><br> **Note** <br> Visual Studio will ask for login, choose **Not now** and choose a theme.                                                 | Visual Studio will open, and you will see one project with an ASP.NET MVC application in Visual Studio.                                      |
| 2        | Expand the **Default.aspx** file in the **Solution Explorer**, then open the **Default.aspx.cs** file in Visual Studio and set a breakpoint at line **25**.                                                                                                | This will break the application once a browser requests the home page.                                                                       |
| 3        | Change the **Solution Configuration** to **Debug** and press **F5** to start debugging the application.                                                                                                                                       | The application will now build, and Edge will open with the application when it’s ready.                                                     |
| 4        | When the breakpoint is hit, click **F5** to continue code execution.                                                                                                                   | The applications home page shows up in Edge.                                                                                                 |
| 5        | The application simulates a simple web shop administrative interface. Feel free to browse around the application to see details of the various products and some of the functionality. | **Note:** The application is setup to use mock data loaded from source files, so all changes will be reverted once the debug session closes. |
| 6        | Press **Shift+F5** to stop the debugging                                                                                                                                               | Debugging stops.                                                                                                                             |

### Completion

You’ve now complete debugging an ASP.NET application using Visual Studio
and IIS Express.

## Create a Docker container

In this section we will create a docker container image to run the
ASP.NET application eShopLegacyWebForms, which requires Internet
Information Server (IIS) to be running in the container.

| **Step** | **Action** | **Result** |
| -------- | ---------- | ---------- |
| 1 | Publish eShopLegacyWebForms application to a folder from Visual Studio by: <br><br> 1.  **Right-Clicking** the **eShopLegacyWebForms** project and choose **Publish** <br> 2.  Choose **Folder** <br> 3.  Click **Publish** | The web application is now published to a folder and ready to be deployed in to a container image. |
| 2 | Open **PowerShell** and change directory to **“C:\\Users\\Administrator\\Desktop\\<br>Lab\\eShopLegacyWebFormsSolution\\src\\eShopLegacyWebForms”** | |
| 3|Create a new DockerFile (no file extension) and open it in Notepad, by running the following commands: <br><br> 1. New-Item Dockerfile <br> 2. Notepad Dockerfile <br> 3. A blank Notepad will show up.||
| 4| Add the following content: <br><br> FROM microsoft/aspnet:4.7.1-windowsservercore-ltsc2016 <br> COPY bin/Release/Publish /inetpub/wwwroot|
| 5 | **Save and close** the file| The Dockerfile contains instructions for the docker engine on how to build a container, when running the “docker build” command in this directory.|
| 6 | In PowerShell run the command <br> **“docker build . -t eshopweb:1.0”**<br> in the directory **“C:\\Users\\Administrator\\Desktop\\<br>Lab\\eShopLegacyWebFormsSolution\\src\\eShopLegacyWebForms”** | This command will tell the Docker host to build a container image and tag it eshopweb:1.0, using the instructions in the Dockerfile.
| 7 | Run the command **docker images** to see the container images cached on your machine | You should see an image with the repository: eshopweb and the tag: 1.0. |

### Completion

You’ve now published the ASP.NET application eShopLegacyWebForms and
built a docker container image with the application.

## Run the application in a container

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
<td><p>Create an instance of the container and run it, by running the following command in PowerShell:</p>
<p><strong>docker run -p 80:80 -d --name eshoptest eshopweb:1.0</strong></p></td>
<td>This creates and starts a container, exposing port 80 on the local host.</td>
</tr>
<tr class="even">
<td>2</td>
<td>Once the command return, open Edge and browse to <strong>http://localhost.</strong></td>
<td>You should now see the application being served from the container.</td>
</tr>
<tr class="odd">
<td>3</td>
<td>Stop the container by running the command <strong>docker stop eshoptest</strong> in PowerShell</td>
<td>This stops the container and persists the state and configuration. You can then restart the container at a later point.</td>
</tr>
<tr class="even">
<td>4</td>
<td>Remove the container by running the command <strong>docker rm eshoptest</strong> in PowerShell</td>
<td>This command will remove the container state and configuration. We will do this as in the next step we create a new instance of the container with a different configuration.</td>
</tr>
<tr class="odd">
<td>5</td>
<td><p>The application has a feature to add an environment variable to the front-page title.</p>
<p>Create a new configuration of the container image, by running the following command in PowerShell:</p>
<p><strong>docker run -p 80:80 -d --name eshoptest -e eShopTitle=MyTitle eshopweb:1.0</strong></p>
<p><strong>Note:</strong></p>
<p>This feature is implemented in the <strong>Site.Master.cs</strong> file.</p></td>
<td>Environment variables is a common way of passing configurations to containers. The -e parameter is used with docker to pass environment variables to containers.</td>
</tr>
<tr class="even">
<td>6</td>
<td>Once the command returns, open Edge and browse to <strong>http://localhost.</strong></td>
<td>You should now see the application title using your environment variable.</td>
</tr>
<tr class="odd">
<td>7</td>
<td><p>You can run commands inside a running container’. Output the environment variables from the container by running the following commands in PowerShell:</p>
<p><strong>docker exec eshoptest cmd.exe /C SET</strong></p></td>
<td>You will see all the environment variables in the container being output.</td>
</tr>
<tr class="even">
<td>8</td>
<td><p>Let’s stop and remove the container:</p>
<ol type="1">
<li><p><strong>docker stop eshoptest</strong></p></li>
<li><p><strong>docker rm eshoptest</strong></p></li>
</ol></td>
<td></td>
</tr>
</tbody>
</table>

### Completion

In this section of the lab, we created a container image with our
application, and ran it. Finally, we used environment variables to
configure the application running in the
container.

## Create an Azure Container Registry, and push your container to a registry

**Goal:** The goal of this section is to publish the container image to
a private Azure Container Registry.

### Azure Subscription
If you do not have one, you can get a trial subscription here: https://azure.microsoft.com/en-us/free/

### Process

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

## Pull the container image and run it locally

In this section we’ll do a final validation of being able to pull down
the container image.

**Note:** If you don’t want to wait for the image to be pushed to the
registry, skip step 1 and 2 in this section.

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
<td><p>Since we already have the image locally, let’s start by removing the local copy. Still in PowerShell, run this command:</p>
<p><strong>docker rmi <em>[insert acr name]</em>.azurecr.io/eshopweb:1.0</strong></p></td>
<td><p>The output of the command should read: <strong>Untagged: mycontainerregistry.azurecr.io\eshopweb:1.0</strong></p>
<p>Since this is another tag on top of an existing image, the image is not being deleted, just the tag.</p></td>
</tr>
<tr class="even">
<td>2</td>
<td><p>To pull the container run this command:</p>
<p><strong>docker pull <em>[insert acr name]</em>.azurecr.io/eshopweb:1.0</strong></p></td>
<td>The pull will complete instantaneously, as the image files, which this tag refers to are already on disc.</td>
</tr>
<tr class="odd">
<td>3</td>
<td><p>Let’s run the container and validate it works:</p>
<p><strong>docker run -p 80:80 --name eshopweb -e eShopTitle=MyTitle <em>[insert acr name]</em>.azurecr.io/eshopweb:1.0</strong></p></td>
<td></td>
</tr>
<tr class="even">
<td>4</td>
<td>Once the command returns, open Edge and browse to <strong>http://localhost.</strong></td>
<td>You should now see the application.</td>
</tr>
<tr class="odd">
<td>5</td>
<td><p>Finally, let’s clean-up, by running the following commands in PowerShell in the directory C:\Users\Administrator\Desktop\Lab:</p>
<ol type="1">
<li><p>by stopping the container: <strong>docker stop eshopweb</strong></p></li>
<li><p>Remove the container: <strong>docker rm eshopweb</strong></p></li>
<li><p>Reset the repository</p>
<ol type="a">
<li><p>git clean -df</p></li>
<li><p>git reset --hard</p></li>
</ol></li>
</ol>
<p><strong>Note:</strong> You need to do the clean-up to continue with the lab</p></td>
<td>All cleaned-up and good to go</td>
</tr>
</tbody>
</table>

### Completion

In this section of the lab, we’ve pulled down the container image form
our private container registry, simulating a deployment, and validated
the container can be run and the application works.

## Create an Azure Service Fabric application with your container

**Goal:** The goal of this section is to have the container running as a
service in a Service Fabric
application.

### Process

| **Step**                                         | **Procedure**                                                                                                                                |
| ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Create a Service Fabric Application              | We will use Visual Studio to create a Service Fabric application hosting the container.                                                      |
| Run the container as a service in Service Fabric | We will set up a local development cluster and deploy the containerized application to the local cluster.                                    |
| Apply container policies and upgrade             | We will apply container resource policies and parameters for configuration. Finally, we will upgrade the running application in the cluster. |

## Create a Service Fabric Application

In this section we will create a Service Fabric application with the
container as a service, and configure it.

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
<td>Open the solution <strong>eShopLegacyWebForms.sln</strong>, which you will find in the <strong>\Lab\eShopLegacyWebFormsSolution</strong> folder on the desktop.</td>
<td>The application code will open in Visual Studio 2017.</td>
</tr>
<tr class="even">
<td>2</td>
<td><strong>Right-Click</strong> the project file and choose <strong>Add -&gt; Container Orchestrator Supprt</strong>. Choose <strong>Service Fabric</strong>.</td>
<td>Visual Studio 2017 will now create the required Dockerfile for your container image, as well as a Service Fabric application project in the solution in Visual Studio.</td>
</tr>
<tr class="odd">
<td>3</td>
<td><p>To instruct Service Fabric to open port 80 for the service (our container), like we did in the previous docker commands, we must specify the port to use in the ServiceManifest.xml file.</p>
<ol type="1">
<li><p>The ServiceManifest.xml file should already be open</p></li>
<li><p>Specify the port to use for the endpoint in the &lt;Endpoint&gt; element:</p></li>
</ol>
<blockquote>
<p>&lt;Endpoint Name=&quot;eShopLegacyWebFormsTypeEndpoint&quot; Port=&quot;80&quot;/&gt;</p>
</blockquote>
<ol start="3" type="1">
<li><p>Save the file</p></li>
</ol>
<p><strong>Note:</strong></p>
<p>If no endpoint is specified, Service Fabric will assign a random endpoint from a predefined pool of ports.</p></td>
<td>The endpoint is bound to the service, which will host the container in Service Fabric. This configuration will ensure that Service Fabric attaches a port to the service process when it’s running.</td>
</tr>
<tr class="even">
<td>4</td>
<td><p>To instruct Service Fabric to map port 80 of the container to the endpoint we specified above, we must create a PortBinding configuration in the ApplicationManifest.xml file.</p>
<ol type="1">
<li><p>In the ApplicationPackageRoot folder under the Application project, open the ApplicationManifest.xml file</p></li>
<li><p>Verify the following configuration, as part of the &lt; ContainerHostPolicies &gt; element:</p></li>
</ol>
<blockquote>
<p>&lt;PortBinding ContainerPort=&quot;80&quot; EndpointRef=&quot;eShopLegacyWebFormsTypeEndpoint&quot; /&gt;</p>
</blockquote>
<ol start="3" type="1">
<li><p>Save the file</p></li>
</ol></td>
<td>This configuration ensures that the port exposed by the container, will be mapped to the endpoint of the service. The end result is similar to the <strong>-p 80:80</strong> command we used when running the container previous.</td>
</tr>
<tr class="odd">
<td>5</td>
<td><p>Finally, we’ll specify an environment variable to pass to the container.</p>
<ol type="1">
<li><p>In the ServiceManifest.xml file, add the following element in the CodePackage element to pass an environment variable to the container:</p></li>
</ol>
<blockquote>
<p>&lt;EnvironmentVariables&gt;</p>
<p>&lt;EnvironmentVariable Name=&quot;eShopTitle&quot; Value=&quot;on SF!&quot;/&gt;</p>
<p>&lt;/EnvironmentVariables&gt;</p>
</blockquote>
<ol start="2" type="1">
<li><p>Save the file</p></li>
</ol>
<p><strong>Note:</strong></p>
<p>If the environment variable value you put in is too long a string, it will cause the web site to not show any title – so be kind</p></td>
<td>This environment variable configuration will be used for the front page of the web application.</td>
</tr>
</tbody>
</table>

### Completion

In this section of the lab, we’ve created a Service Fabric application
and configured the container to run in a Service Fabric cluster.

## Run the container as a service in Service Fabric

In this section we will run the container in Service Fabric on our
developer machine.

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
<td><p>Service Fabric cluster can run on Windows 10, and the SDK provides cluster configuration scripts which supports setting up clusters of one node for development purposes. Let’s start by creating a one node cluster.</p>
<p><strong>Right-click</strong> the <strong>Service Fabric Local Cluster Manager</strong> in the task bar and select <strong>Start Local Cluster</strong></p></td>
<td><p>A Service Fabric cluster will now be created on your developer machine.</p>
<p>We will be using a one node cluster in this lab, as additional nodes will only add overhead to the developer experience and is usually only needed for debugging failover scenarios.</p></td>
</tr>
<tr class="even">
<td>2</td>
<td>Open <strong>Service Fabric Explorer</strong> using the desktop link and choose <strong>Connect to localhost</strong></td>
<td><p>Service Fabric Explorer is your management UI for any Service Fabric cluster, locally, in Azure or on-premises.</p>
<p>Service Fabric explorer let you get information about your cluster, application and services and do administrative commands against the cluster.</p></td>
</tr>
<tr class="odd">
<td>3</td>
<td><p>Next go back to Visual Studio 2017 to publish the application:</p>
<ol type="1">
<li><p><strong>Right-click</strong> the Service Fabric application project and select <strong>Publish</strong> to publish the application to the local Service Fabric cluster.</p></li>
<li><p>Choose <strong>PublishProfiles\Local.1Node.xml</strong> as the Target profile.</p></li>
<li><p>Click <strong>Publish</strong></p></li>
</ol></td>
<td>Visual Studio will build the container and deploy the application to Service Fabric. The Service Fabric cluster will then pull the container image form the local cache and run it as a Service in Service Fabric.</td>
</tr>
<tr class="even">
<td>4</td>
<td>Once the deployment is completed, you can browse to <strong>http://localhost</strong> to see the website running in SF.</td>
<td>In this scenario we are just publishing the container, however you can also debug the application code in a container running on Service Fabric, simply by pressing F5.</td>
</tr>
<tr class="odd">
<td>5</td>
<td>Go to <strong>Service Fabric Explorer</strong>, which will now show you one Application deployed in the cluster.</td>
<td></td>
</tr>
</tbody>
</table>

### Completion

In this section of the lab, we’ve created a local Service Fabric cluster
and deployed our application in a container to the cluster.

## Apply container policies and upgrade the application

In this section we will enable parameterization of our application
configuration and apply resource policies to the container. We will also
roll-out an upgrade to the Service Fabric application.

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
<td><p>Docker and Service Fabric can control the amount of resources containers can use in a cluster. To specify the amount of memory and CPU for our container do the following:</p>
<ol type="1">
<li><p>In the <strong>ApplicationPackageRoot</strong> folder under the Application project, open the <strong>ApplicationManifest.xml</strong> file</p></li>
<li><p>Insert the following configuration, as part of the <strong>&lt; Policies &gt;</strong> element:</p></li>
</ol>
<p>&lt;ServicePackageResourceGovernancePolicy CpuCores=&quot;1&quot; MemoryInMB=&quot;1024&quot; /&gt;</p>
<ol start="3" type="1">
<li><p><strong>Save</strong> the file</p></li>
</ol></td>
<td>This will make sure this container gets a single CPU core and 1GB of memory assigned in the cluster. Service Fabric uses this enforcement to place containers in the cluster.</td>
</tr>
<tr class="even">
<td>2</td>
<td><p>We also want to have a single configuration file for changing configuration for our container across environments. In Service Fabric we will use the ApplicationParameter files to do this. To parameterize the environment variable, do the following:</p>
<ol type="1">
<li><p>Open the <strong>ApplicationManifest.xml</strong> file</p></li>
<li><p>Insert the following to override the environment variable in the ServiceManifest.xml file as part of the <strong>&lt;ServiceManifestImport&gt;</strong> element:</p></li>
</ol>
<p>&lt;EnvironmentOverrides CodePackageRef=&quot;Code&quot;&gt;</p>
<p>&lt;EnvironmentVariable Name=&quot;eShopTitle&quot; Value=&quot;[eShopLegacyWebForms_eShopTitle]&quot; /&gt;</p>
<p>&lt;/EnvironmentOverrides&gt;</p>
<ol start="3" type="1">
<li><p>The value specified in square bracket `[]` is a reference to a parameter, which we will also have to add to the file, as part of the <strong>&lt;Parameters&gt;</strong> element. We will set the default value to be an empty string.</p></li>
</ol>
<p>&lt;Parameter Name=&quot;eShopLegacyWebForms_eShopTitle&quot; DefaultValue=&quot;&quot; /&gt;</p>
<ol start="4" type="1">
<li><p><strong>Save</strong> the file</p></li>
</ol></td>
<td></td>
</tr>
<tr class="odd">
<td>3</td>
<td><p>Now that we have defined the environment variable to be overridden by a parameter, let’s specify the parameter:</p>
<ol type="1">
<li><p>Open the <strong>ApplicationParameters</strong> folder and the file <strong>Local.1Node.xml</strong>. This is the file which will be used when we publish to our local cluster.</p></li>
<li><p>Add the following to the <strong>&lt;Parameters&gt;</strong> element:</p></li>
</ol>
<p>&lt;Parameter Name=&quot;eShopLegacyWebForms_eShopTitle&quot; Value=&quot;One&quot; /&gt;</p>
<ol start="3" type="1">
<li><p><strong>Save</strong> the file</p></li>
</ol></td>
<td>Parameter files is a concept understood by Visual Studio and VSTS. If you will be using PowerShell or other means to deploy, Service Fabric accepts parameters as a hash table.</td>
</tr>
<tr class="even">
<td>4</td>
<td><p>Let’s go ahead and upgrade the application in the local cluster:</p>
<ol type="1">
<li><p><strong>Right-click</strong> the Service Fabric application project and select <strong>Publish</strong> to publish the application to the local Service Fabric cluster.</p></li>
<li><p>Choose <strong>PublishProfiles\Local.1Node.xml</strong> as the Target profile.</p></li>
<li><p>Check <strong>Upgrade the application</strong></p></li>
<li><p>Click <strong>Manifest Version</strong></p></li>
<li><p>Change the version of the <strong>ApplicationType</strong> to 2.0 (New Version)</p></li>
<li><p>Click <strong>Save</strong></p></li>
<li><p>Click <strong>Publish</strong></p></li>
</ol></td>
<td>All parts of a Service Fabric application are versioned. This enables the platform to only upgrade the specific parts of the application which are being changed as part of an upgrade.</td>
</tr>
<tr class="odd">
<td>5</td>
<td><p>Open <strong>Service Fabric Explorer</strong> using the desktop link and choose <strong>Connect to localhost.</strong></p>
<ol type="1">
<li><p>Click <strong>Applications</strong> and notice the application is now version 2.0.</p></li>
<li><p>Click <strong>Cluster</strong> and <strong>Metrics</strong> and notice the resource capacity overview in the cluster.</p></li>
</ol></td>
<td>If the cluster had consisted of multiple nodes, the upgrade would have taken a while as each node gets updated to completion, and the upgrade would only move forward if no errors occurred. If an error occurred, the upgrade would automatically roll-back, leaving the application up and running.</td>
</tr>
<tr class="even">
<td>6</td>
<td>Open Edge and browse to <strong>http://localhost</strong> to see the changes.</td>
<td></td>
</tr>
</tbody>
</table>

### Completion

In this section of the lab, we’ve parameterized our configuration,
applied resource governance and upgraded our application in the Service
Fabric cluster.
