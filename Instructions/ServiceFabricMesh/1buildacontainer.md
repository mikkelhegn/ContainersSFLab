 
# Build a Docker container for Windows, using an existing ASP.NET web application

**Goal:** The goal of this section is to have an ASP.NET application running in a Docker container on your Windows 10 developer machine.

## Process

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
| 6 | In PowerShell run the command <br> **“docker build . -t eshopweb:1.0”**<br> in the directory **“C:\\Users\\Administrator\\Desktop\\<br>Lab\\eShopLegacyWebFormsSolution\\src\\eShopLegacyWebForms”** | This command will tell the Docker host to build a container image and tag it eshopweb:1.0, using the instructions in the Dockerfile. **Note:** Make sure Docker is running and on Windows containers mode
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



