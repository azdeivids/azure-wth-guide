# Azure static web apps
-----------------
# Resources
- [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)
- [GitHub Actions Workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)
- [Azure Static Web Apps documentation](https://learn.microsoft.com/en-us/azure/static-web-apps/?WT.mc_id=javascript-76678-cxa)
- [Securing Azure Functions](https://learn.microsoft.com/en-us/azure/azure-functions/security-concepts?WT.mc_id=javascript-76678-cxa&tabs=v4)
- [Authenticate users with Azure Static Web Apps](https://learn.microsoft.com/en-us/training/modules/publish-static-web-app-authentication/)
- [Azure Static Web App Configuration](https://learn.microsoft.com/en-us/azure/static-web-apps/configuration?WT.mc_id=javascript-76678-cxa)
- 
-------------
Something to look back on from when I did the lab on Azure Static Web App's.

--------------

# Walkthrough

1. Clone the GitHub repo to your local machine: [Github Repo](https://github.com/azdeivids/azure-swa.git).

2.  Sign into [Azure Portal](https://portal.azure.com) and create a [static web app](https://portal.azure.com/#create/Microsoft.StaticApp).
	- Select a subscription.
	- Select or create a new resource group.
	- Select a name for the app.
	- Select GitHub as the deployment type.
	- Select the free app plan.
	- Select the location closes to your users.
	- Authenticate against GitHub.
		1. Select the organization.
		2. Select the [repository containing the app](https://github.com/azdeivids/azure-swa.git).
		3. Select the branch.
		4. Select a custom build preset.
		5. In the App location enter /www for the frontend.
		6. In the Api location enter /api for the backend.
		7. In the Output location enter /www since the frontend does not require any build systems to run.
	- Review and create the app.
	- Once the app has been created pull down the repository as azure will have created aditional files within the repo during the deployment.

The added YAML file, that Azure created defines that the GitHUb Action is going to be trigered everytime there is a push on the main branch or every time a Pull Request is opened, synchronized, reopened or closed.

3. In GitHub, go to the static web app repository and click on *Actions* tab to view all workflows. 
![[Pasted image 20221226175138.png]]
4. In [Azure Portal](portal.azure.com) navigate to the newly created resource group and click on the new app. Acquire the URL and verify that the app had been deployed successfully. 

5. Install the Azure Static Web App CLI via npm.  

Azure [SWA CLI](https://github.com/Azure/static-web-apps-cli) serves as a local development tool for Azure Static Web Apps. It provides a similar experiance as to what you get in Azure cloud such as:
- Local static server.
- Proxy to frontend framework development server.
- Proxy to API endpoints.
- Mock authn and authz server.
- Local routes and configuration settings.

```powershell
npm install -g @azure/static-web-apps-cli
```

6. Create a local web server to run the app.

```powershell
swa start www --open
```
A local web server will be started at http://localhost:4280 to run the app.

7. Create Azure Function App in VSCode. 
	- Open the command panel using `Ctrl + Shift + P` and create a new project in Azure Functions.
		- Select the *api* folder. This is where the Function App will be created.
		- Choose JavaScript language to write the function.
		- Select the *HTTP Trigger*.
		- Select *Anonymous* authorization level. 
		- Create an array as a "fake" database in the `index.js` file.
	- Open the `function.json` file in the *tasks-get* folder and configure the endpoints.
		-  Remove the `POST` method in the methods array so that the function can only be called using `GET` request.
		- Change the URL of the function by adding a `route` parameter. 
		- Enter the value for the `route` parameter, such as *tasks*. Now the function is only accessible using `GET /api/tasks` request.
```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get"
      ],
      "route": "tasks"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

8. Use GitHub provider to set up user authentication. 
	- Add a button in the login.html to enable users to login with GitHub. The button should redirect to `/.auth/login/github`.
	- Set up redirect URL to redirect uses to a different page once they successfully manage to login. Add a `?post_login_redirect_uri=/index.html` query param at the end of the URL.

```html
<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="style.css" />
    
	<title>Login</title>
</head>
  
<body>
    <div class="container">
        <a class="login-button" onclick="loginWithGithub()">Login with GitHub</a></li>
    </div>
</body>

</html>

<script>
    function loginWithGithub() {
        window.location = '/.auth/login/github?post_login_redirect_uri=/index.html';
    }
</script>
```

It is evident that once the function was added and changes had been comited to git and pushed to GitHub, a workflow was triggered. The work flow had been triggered due to the YAML file that had been created earlier when we deployed the Azure Static Web App. 
![[Pasted image 20221226220331.png]]

If we look at the YAML file that had been created, we can see that any time there is a push on the main brach, the GitHub Action will be triggered and the website will be re-deployed automatically. 

9. Browse to http://localhost:4280/login.html and click on the button to log in.
	- Once signed in, check that the redirect is working. After successful authenticatio you ought to be taken to `index.html` page.
	- Retrive the user information by fetching the `/.auth/me` url. This should return some JSON containing a clientPrincipal object. If the object is null, the user is not authenticated.
	- You can sign out of the app by navigation to `/.auth/logout` to clear the client principal.

The SWA CLI emulates the security flow implemented in Azure. This can work with multiple security providers, not just GitHub.

10. Complete the getUser() method in app.js to retrieve and display the logged in user information at the top of the web page.
```javascript
async function getUser() {
    // TODO
    
    const response = await fetch('/.auth/me');
    const payload = await response.json();
    username.innerHTML = `Hi, ${payload.clientPrincipal.userDetails}`;
}
```

11. Create a new file `staticwebapp.config.json` in the *www* folder. This will be used to handle HTTP requests.
	- Create the file in *www* folder and restart the CLI (CLI has to be restart everytime change is made in the `staticwebapp.config.json` file).

12. Prevent unatheticated users from calling the API.
```json
{
   "routes": [
      {
          "route": "/api/tasks/*",
          "allowedRoles": [
              "authenticated"
          ]
      }
    ]
}
```

13. Restrict the frontend to authenticated users only. 
```json
{
   "routes": [
      {
          "route": "/",
          "allowedRoles": [
              "authenticated"
          ]
      }
    ]
}
```

All the unathenticated users will now be redirected to 401 (HTTP Unauthorized) web page. Add the `responseOverrides` property to redirect users to a specific web page when an HTTP code is returned. 
```json
     "responseOverrides": {
        "401": {
            "rewrite": "/login.html"
        },
        "404": {
            "rewrite": "/custom-404.html"
}
```

14. Create a `custom-404.html` page within the *www* folder to redirect all the users to this page whenever they enter a URL which does not exists.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>404</title>
</head>
<body>
    What you seek cannot be found!
</body>
</html>
```


15. Set up the dev environment to store the app data in a databse.
	- In the *api* folder, open the `package.json` file and add the dependencies.

```
{
  "name": "api",
  "version": "1.0.0",
  "description": "",
  "scripts": {
    "start": "func start",
    "test": "echo \"No tests yet...\""
  },
  "dependencies": {
    "mongodb": "^4.13.0"
  },
  "devDependencies": {}
}
```

16. Back in the terminal navigate to the `~/dev/project/azure-swa/api/` folder containing the `package.json` file and  type `npm install` to download and isntall the dependencies for the *node_module* folder of the Azure Functions App.

17. Create [Azure Cosmos DB](https://portal.azure.com/?WT.mc_id=javascript-76678-cxa#create/Microsoft.DocumentDB) for the application and select the *Azure Cosmos DB API for MongoDB*.
	- Select the subscription; has to be the same subscription as before.
	- Select the resource group where the swa is deployed in.
	- Enter a unique name to identify the DB
	- Choose the same location to host the DB where the app is hosted.
	- For [capacity mode](https://learn.microsoft.com/en-us/azure/cosmos-db/throughput-serverless) choose the [*provisioned throughput*](https://learn.microsoft.com/en-us/azure/cosmos-db/set-throughput) option.
		- You can set the throughput either manually or you can configure autoscalling. The throughtput is reserved for the container so that it receives the provisioned through put all the time. The throughput is meassured by the [Request Units (RU's)](https://learn.microsoft.com/en-us/azure/cosmos-db/request-units). 
	- Apply for the free tier and limit the account throught to prevent any unexpected charges.
		- You can get the first 1000 RU/s and 25Gb of storage for free in a CosmosDB account.
		- Leave rest as default. 
		- Click on the `Create` button once done.
		- 
![[Pasted image 20221227103400.png]]

18. In VSCode click on the new CosomosDB account and select `Create Databse`.
	- Enter the databse name.
	- Once the db is created, select `Create Collection`; this will be used to store the task
![[Pasted image 20221227110552.png]]

19. Add data to the database.
	- Right click the collection *tasks* and select `Create Document`. 
	- Navigate to the Azure website and authenticate using GitHub.
	- Once signed in, navigate to the */.auth/me* URL and acquire the *"userId"* value; this will be used in the new document as a reference.
	- Enter the following values in the new document in the *tasks* collection; save the changes and upload the file.
```
{
  "_id": {
    "$oid": "63aad6d634a3ed54ac329887"
  },
  "userId": "009238eab0274649ac5acd662e741f43",
  "label": "Create Azure STW.",
  "status": "checked"
}
```

```
{
  "_id": {
    "$oid": "63aad6f634a3ed54ac329888"
  },
  "userId": "009238eab0274649ac5acd662e741f43",
  "label": "Create Azure CosmosDB account.",
  "status": "checked"
}
```

```
{
  "_id": {
    "$oid": "63aad71c34a3ed54ac329889"
  },
  "userId": "009238eab0274649ac5acd662e741f43",
  "label": "Add data to the databse.",
  "status": ""
}
```

Use the moc *"userID"* to test the application locally.

20. Setup the connection string in the */api/local.settings.json* file. The *local.settings.json* files isn't tracked by GitHub due to sensitive information container within the configuration, like the db connection string for example. To configure the app for production, use the Azure Portal.
	- Enter the configuration values.
```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "mongodb://task-swa-db:YcnDusxrviO9LmLMHXdVguuJ5mJMlmPkLn2j7EYhSW70qh8nSj8h6Lsmm6TIPYq8ChjzdedmfZ0rACDbVgsiVQ%3D%3D@task-swa-db.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@task-swa-db@"    
  }
}
```

21. 