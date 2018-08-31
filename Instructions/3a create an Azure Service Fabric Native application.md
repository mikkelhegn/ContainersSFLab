# Create an Azure Service Fabric Native application with your container

**Goal:** The goal of this section is to have the container running as a
service in a Service Fabric
application.

## Process

| **Step**                                         | **Procedure**                                                                                                                                |
| ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Create a Service Fabric Application              | We will use Visual Studio to create a Service Fabric application hosting the container.                                                      |
| Run the container as a service in Service Fabric | We will set up a local development cluster and deploy the containerized application to the local cluster.                                    |
| Apply container policies and upgrade             | We will apply container resource policies and parameters for configuration. Finally, we will upgrade the running application in the cluster. |

##   

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

##   

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

##   

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
