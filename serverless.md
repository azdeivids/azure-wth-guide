# Resources

- [Azure Function Core Tools](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=v4%2Cwindows%2Ccsharp%2Cportal%2Cbash)
	- [Work with Azure Function Core tools](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=v4%2Cwindows%2Ccsharp%2Cportal%2Cbash)
- [Azure Functions Overview](https://learn.microsoft.com/en-us/azure/azure-functions/functions-overview)
	- [Create C# function is vscode](https://learn.microsoft.com/en-us/azure/azure-functions/create-first-function-vs-code-csharp?tabs=in-process)
- [Create Azure Cosmos DB Account](https://learn.microsoft.com/en-us/azure/cosmos-db/how-to-manage-database-account)
	- [Getting started with SQL queries (NoSQL)](https://learn.microsoft.com/en-us/azure/cosmos-db/nosql/query/getting-started)
- [Azure Event Grid](https://learn.microsoft.com/en-us/azure/event-grid/concepts)
	- [Work with Event Grid triggers and binding](https://learn.microsoft.com/en-us/azure/azure-functions/event-grid-how-tos?tabs=v2%2Cportal#create-a-subscription)
- [Computer Vision](https://learn.microsoft.com/en-us/azure/cognitive-services/computer-vision/)
	- [Optical Character Recognition for Images](https://learn.microsoft.com/en-us/azure/cognitive-services/computer-vision/concept-ocr)
- [Manage Function App](https://learn.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings?tabs=portal)
- [Managed Identities](https://learn.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)
- [Store unstructured data using Azure Functions and Azure Cosmos DB](https://learn.microsoft.com/en-us/azure/azure-functions/functions-integrate-store-unstructured-data-cosmosdb?tabs=csharp)
- [Application Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview?tabs=net)
	- [Monitor Azure Functions](https://learn.microsoft.com/en-us/azure/azure-functions/functions-monitoring)
	- [Live Metric Stream](https://learn.microsoft.com/en-us/azure/azure-monitor/app/live-stream)
	- [Configure Monitoring](https://learn.microsoft.com/en-us/azure/azure-functions/configure-monitoring?tabs=v2#configure-log-levels)
- [Azure Logic Apps](https://learn.microsoft.com/en-us/azure/logic-apps/logic-apps-overview)
	- [Azure Functions Workflows](https://learn.microsoft.com/en-us/azure/logic-apps/logic-apps-azure-functions?tabs=consumption#call-azure-functions-from-logic-apps)
-------------------------------------------
## Review Later

- [Get Started using Az Dev CLI (Preview)](https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/get-started?tabs=baremetal&pivots=programming-language-nodejs)

-------------------------------
# Serverless

## Run the function locally.

1. Set up the lab environment.
- Download and install [Azure Function](https://go.microsoft.com/fwlink/?linkid=2174087) and [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases/tag/4.0.4865)
	- Azure function core tools enable us to develope and test our functions on the local machine using windows terminal. The functions can connect to Azure services, and can be debuged on local machine using full Function runtime.
- Open a new `wt.exe` instance and verify that the tools have been installed successfully.
```powershell
func --version
```

- Download and install Node.js 8+
- Verify the installation
```powershell
node --version
npm --version
```

- Download and install [.Net 6 SDK](https://dotnet.microsoft.com/en-us/download)
- Verify the installation
```powershell
dotnet --version
```

3. Create a "Hello World" Azure Function in [VSCode](https://learn.microsoft.com/en-us/azure/azure-functions/create-first-function-vs-code-csharp?tabs=in-process).
- Open VSCode and click on the Azure Tools extension.
- In the left nav, click on the `+` icon next to *WORKSPACE Local* to create the first function.
![[Pasted image 20221227160711.png]]

- Select `C#` as the language and `.NET 6 Isolated` as the runtime.
	- Isolated worker process allows to run .NET class library functions on a version of .NET that is different from the version used by the Functions host process.  This will allow you to develop functions that use current .NET releases not natively supported by the functions runtime.
	- [How to run functions in an isolated worker process](https://learn.microsoft.com/en-us/azure/azure-functions/dotnet-isolated-process-guide)
- Choos anonymous as the Authz level.
	-  Anonymous enables anyone to call the function endpoint.
	- [Authorization Keys.](https://learn.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys)
-  Leave rest as the default.

4. Once the function is created, in the left nav bar select `Run and Debug (Ctrl+Shift+D)` and click the drop down menu to select *Attach to .NET function*.
![[Pasted image 20221229184647.png]]
- Now hit the green play button or `F5` on the keyboard to *call the function*.
- You  can see the app starting in the terminal panel. This will also show the endpoint URL running locally.
![[Pasted image 20221229185454.png]]

5.In the left nav, go back to the *WORKSPACE Local* and click on the refresh button. A *Local Project* folder should appear now.
- Expand the functions and right click the http_trigger (the name of the trigger that was defined earlier).
- Click on the `Execute Function Now...` button
![[Pasted image 20221229185906.png]]

- Enter your name in the *Enter Request Body* and press `Enter` to sen the request.
- You can see the information about the function in the terminal panel in vscode.
- We can also visit the URL given in the terminal to see that the function executed successfully.
![[Pasted image 20221229190959.png]]

- We can add a *Name* parameter to the URL in to receive a customised message
![[Pasted image 20221229195240.png]]

- We can also use different tools like [Postman](https://www.postman.com/) to POST request to the function with a request body defined in JSON object like so:
```json
{ "name": "YourName" }
```

- In the following example I use Thunder Client extension in VSCode to make a POST request to the URL.
![[Pasted image 20221229200207.png]]


## Provision Azure Resources

1. Provision resources in Azure to start developing the solution.
- Create the [Azure CosmosDB](https://learn.microsoft.com/en-us/azure/cosmos-db/how-to-manage-database-account) account using [NoSQL API](https://azure.microsoft.com/en-us/updates/generally-available-azure-cosmos-db-core-sql-api-query-engine-improvements/).
	- Azure Cosmos DB account name has to be unique since it is appended to *documents.azure.com*.
	- In regards to Location, you should select the location that will be closes to your users to give the least amount of latency possible, therefor improving their time it takes to access the data.
	- Capacity modes:
		- [Provisioned Throughput](https://learn.microsoft.com/en-us/azure/cosmos-db/set-throughput)
		- [Serverless](https://learn.microsoft.com/en-us/azure/cosmos-db/serverless)
	- You can have up to one free tier Azure Cosmos DB account per subscription.
![[Pasted image 20221229203624.png]]

- Create a container: `Processed`
![[Pasted image 20221229211955.png]]

- Create the second container using the same datanase id: `NeedsManualReview`
![[Pasted image 20221229214210.png]]

- Once finished create Cosmos DB account and containers, navigate to the `Keys` blade in the left navigation pane.
	- Copy the `PRIMARY KEY` for later use: i3evVzDHc4vzRHrH5Ygf58s710PidBw3bx6lUNxVDTtGgmHpY3XsctM4j1VQB82xFJYtm25lBVVoACDbrd8YLA==

>[!CAUTION] Key's used in Production shouldn't be stored out in open.

2. Create a storage account in the same RG. Refer to it as `INIT`.
![[Pasted image 20221229214759.png]]

- Within the storage account, create a container named `images`.
- Within the same storage account, create a second container named `exports`.
- Navigate to the `Access Keys` blade in the left side navigate pane.
	-  Copy the `Connection string` for later use: DefaultEndpointsProtocol=https;AccountName=cosdbuks001init001;AccountKey=4qa5GAnHrlR1FyV6P6O51xit4tLz+q1jWT/E8yxEAHGqI5f6X570sOCfo3xhCBbA4oWK4wgU8Cxy+AStzIq6/g==;EndpointSuffix=core.windows.net

>[!CAUTION] Key's used in Production shouldn't be stored out in open.

3. Create [Azure Function](https://learn.microsoft.com/en-us/azure/azure-functions/functions-create-function-app-portal) app with the following settings:
- Put *App* in the name.
![[Pasted image 20221229215535.png]]

- Under `Hosting` tab, create a new storage account: `azfunst001tollbooth001`.
- Under `Monitoring` tab, disable the *Application Insights*.

4. Create another [Azure Function](https://learn.microsoft.com/en-us/azure/azure-functions/functions-create-function-app-portal) app with the following settings:
- Put *Events* in the name.
- This time, select the Node,js runtime stack.
![[Pasted image 20221229221943.png]]

- Under `Hosting` tab create a new storage accont: `azfunst002events001`
- Under `Monitoring` tab disable the *Application Insights*.

5. Create an Event Grid Topic (leave schema as Event Grid schema).
- [Event Grid Topic](https://learn.microsoft.com/en-us/azure/event-grid/concepts#topics) provides an endpoint where the source sends events.
![[Pasted image 20221229233720.png]]

- Once the deployment is complete, navigate to the `Access keys` blade in the left navigation pain and copy the `Key 1` for later use.

>[!CAUTION] Key's used in Production shouldn't be stored out in open.

6. Create a Computer Vision API service (SKU: S1).
- This will be used to extract the data from our [iamges](https://learn.microsoft.com/en-us/azure/cognitive-services/computer-vision/concept-ocr) to be sent to the Event Grid we deployed earlier.
![[Pasted image 20221230000229.png]]


 > [!NOTE] You can scale the pricing tier after the initial deployment.

- Once the deployment is completed navigate to the `Keys and Endpoint` blade from the left navigation pane.
	- From here, copy `KEY 1` value for later use: 8b7068a46f724f9ab5548045922d3a17

>[!CAUTION] Key's used in Production shouldn't be stored out in open.

7. Create Azure Key vault to store the secrets for the Tollbooth app.
- Once the KeyVault has been created, navigate to the `Secrets` blade in the left navigation pane and create the following secrets:
	- computerVisionAPIkey
	- eventGridTopicKey
	- cosmosDBAuthorizationKey
	- blobStorageConnectionKet

## App Configuration

1. Add the [application settings](https://learn.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings?tabs=portal) to the *tollbooth-azfun-app* that we created earlier.
- In the left navigation pane under *Settings* section, clik on the `Configuration` blade.
- Add the computerVisionApiURL and append **vision/v2.0/ocr** at the end of the URL
![[Pasted image 20230102112744.png]]

- Add the **computerVisionAPIkey** from the [Key Vault to the function app.](https://learn.microsoft.com/en-us/azure/app-service/app-service-key-vault-references?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=azure-cli)
>[!NOTE] You must reference the key vault uri like so: **@Microsoft.KeyVault(SecretUri=`<enter-uri-here>`**)

- Add the **eventGridTopicEndpoint** to the function app.
- Add the **eventGridTopicKey** from the Key Vault to the function app.
- Add the **cosmosDBEndpointUrl** to the function app.
- Add the **cosmosDBAuthorizationKey** from the Key Vault to the function app.
- Add the **cosmosDBDatabaseId (LicensePlates)** from the `cosdb-serverless-dev` account to the function app.
- Add the **cosmosDBCollectionId (Processed)** from the `cosdb-serverless-dev` account to the function app.
- Add the **exportCsvContainerName** from the `cosdbuks001init001` storage account.
- Once all the settings are added click on `Save` button in the top toolbar.

2. Once the seetings are saved, there are some error with they keys passed from the Key Vault.
![[Pasted image 20230102134636.png]]
If we expanded the settings, it is evident that the error is related to [`Managed Identity`](https://learn.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview). 
![[Pasted image 20230102134809.png]]

What this means is that the app does not have the right permission to access the key baul, therefor we must create a system assigned identity and grant the access to the key vault so thatt the app can retrieve the keys.

- To configure the identity, click on the `Identity` blade in the left side navigation and flick the status to `On`. 
![[Pasted image 20230102135025.png]]

This will now create  a managed identity in Azure Active Directory that the app can now use to connect to resources that support Azure AD authentication. This is secure way to grant access to secrets, keys and tokens wihtout having to handle any credentials. In our scenario we created a system assigned managed identity so we can be sure that once the resource (tollbooth-azfun-app) is the deleted, the managed identiy will be deleted together with the resource.

- Once the Identity is created, we must assign a relevant role that will allow the app to interact with the Key Vault resource. We will grant the **Key Vault Secret User** role to this identity which will allow it to only read the secrets stored in the `ocr-keyv-tollbooth-app` Key Vault.

> [!NOTE] This only works with Key Vault tas use RBAC.

![[Pasted image 20230102135827.png]]

- Now once the role assignment is created navigate to the `ocr-keyv-tollbooth-app` Key Vault and click on the `Access Configuration` blade in the left navigation pane.
	- Change the permission mode to Azure roll-based access control (RBAC).
	- Click on Apply at the bottom of the screen.
![[Pasted image 20230102140837.png]]

- In the top right you will see that it has successfully updated.
![[Pasted image 20230102140917.png]]

- Now back in the `Configuration blade` in the `tollbooth-azfun-app` we can see that the app has succssfully retrieved the keys.
![[Pasted image 20230102141111.png]]

3. Now open the tollbooth app resources in VSCode and click on the TODO extentions.#
![[Pasted image 20230102141240.png]]

4. Complete the relevant [challanges](https://github.com/microsoft/WhatTheHack/blob/master/015-Serverless/Student/Challenge-04.md) and finishe the app.
- TODO1: Add the code listed below. the *licensePlateImage* in should represent the name of the partition key from the license plate databse in Azure Cosmos DB account.

Code:
```C#
licensePlateText = await new FindLicensePlateText(log, _client).GetLicensePlate(licensePlateImage);
```

Partition key:
![[Pasted image 20230102144911.png]]

- TODO2: Pupulate the variables with the correct app settings. This will allow the app to contact the Computer Vision API and extract the license plate text from the photo using the OCR.

```C#
var uriBase = Environment.GetEnvironmentVariable("computerVisionApiUrl");
var apiKey = Environment.GetEnvironmentVariable("computerVisionApiKey");
```

- TODO3: Add the event type and licensplate data to be sent to the Ecent Grid. Copy the first parmeter in the send method for later use.
	- `savePlateData`
	- `queuePlateForManualCheckup`

```C#
await Send("savePlateData", "TollBooth/CustomerService", data);
await Send("queuePlateForManualCheckup", "TollBooth/CustomerService", data);
```

- Once the TODO list is complete run dotnet build to see if the app builds and doesn't return any error.
```powershell
dotnet build
```

![[Pasted image 20230102152718.png]] 

## Deploy the app to Azure

1. [Deploy](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=v4%2Cwindows%2Ccsharp%2Cportal%2Cbash#before-you-publish) the local app to the `tollbooth-azfun-app` in Azure, that we had created earlier, using the following [command](https://learn.microsoft.com/en-us/azure/azure-functions/functions-core-tools-reference?tabs=v2#func-azure-functionapp-publish): 

```powershell
func azure functionapp publish tollbooth-azfun-app
```

Here is our published app
![[Pasted image 20230102155459.png]]

We can also see the functions in the portal under the `Function` blade. 
![[Pasted image 20230102155701.png]]
If we click on the app URL we can also confirm that the app is up and running.
![[Pasted image 20230102160707.png]]

2. If the app has deployed successfully, proceed to create **[Event Subscription](https://learn.microsoft.com/en-us/azure/azure-functions/event-grid-how-tos?tabs=v2%2Cportal#event-subscriptions)** in the `cosdbuks001init001` storage account
- On the laft navigate pane click the `Event` blade and click on ` + Event Subscription` in the top toolbar.
- Create the following:
![[Pasted image 20230102162308.png]]
A subscription is required to receive events raised by the event grid. Here we specify the **ProcessImage** as the endpoint that will triger the event

- Wait for the deployment to finish.
![[Pasted image 20230102162409.png]]

## Create Node.js Function app in the Portal

1. Create the first function that will be triggered by event grid.
- Navigate to the `tollbooth-azfun-events` function app that was created earlier.
- Click on the `Functions` blade and select `Create`. 
	- Select **Develop in Portal**
	- Choose the **Azure Event Grid trigger**
	- Enter the name: **SavePlateData**
	- Click on `Create`
	- Once created, click on `Code + Test` blade and enter the following code:

```javascript
module.exports = function(context, eventGridEvent) {
context.log(typeof eventGridEvent);
context.log(eventGridEvent);

context.bindings.outputDocument = {
    fileName: eventGridEvent.data['fileName'],
    licensePlateText: eventGridEvent.data['licensePlateText'],
    timeStamp: eventGridEvent.data['timeStamp'],
    exported: false
};

context.done();
};
```

- In Azure Portal search for **Event Grid Topic** and select the last event grid created: `tollbooth-evgt`
- Click on `+ Event Subscription` and create the following:
![[Pasted image 20230102164920.png]]

- Go back to the `tollbooth-azfun-events` app and select the `SavePlateData` that we just created and add the Cosmos DB output.
- Select the `Integration` blade and click on `+ Add Output`.
- Create the following:
	- **Document parameter name** can be the name that refers to the CosmosDB object in code.
	- **If true, creates the Azure Cosmos DB databse and collection** can be left off since we alreay created the db and collection in previous challange.
![[Pasted image 20230102165546.png]]

2. Create the second function in `tollbooth-azfun-event` resource that will be triggered by event grid simialr to how before.
- Click on the `Functions` blade and select `Create`. 
	- Select **Develop in Portal**
	- Choose the **Azure Event Grid trigger**
	- Enter the name: **QueuePlateForManualCheckup**
	- Click on `Create`
	- Once created cllick on the `Code + Test` blade and enter the following:

```javascript
module.exports = async function(context, eventGridEvent) {
context.log(typeof eventGridEvent);
context.log(eventGridEvent);

context.bindings.outputDocument = {
    fileName: eventGridEvent.data['fileName'],
    licensePlateText: '',
    timeStamp: eventGridEvent.data['timeStamp'],
    resolved: false
};

context.done();
};
```

- In the Azure Portal search for the **Event Ggrid Topic** and select the last event grid created: `tollbooth-evgt`.
- Click on `+ Event Subscription` and create the following:
![[Pasted image 20230102170613.png]]

- Go back to the `tollbooth-azfun-events` resource and follow the same steps as above to add the Cosmos DB Output to the `QueuePlateForManualCheckup` function.
![[Pasted image 20230102170935.png]]

- We can run  a test on our function to see if they have deployed successfully. You wold expect  to receive HTTP Response code 202.

## Monitor the Azure Function App

1. Create **[Application Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview?tabs=net)** resource in Azure Portal.

Application insights can be integrated with the azure function apps by providing a valid insturmentation key to establish a connection between the resources. This can help you monitor the function executions and collect log, performance, and error data, to help more easily diagnose issues and  understand how the app is performing.

- Create the resource.
- Add application insights to both of the *tollbooth-azfun* apps.
	- Navigate to either of the apps to start with.
	- Click on the `Application Insights` blade an select an exisitring resource, the one that we just created.
	- Click on `Apply`.
	- Do the same for the other *tollbooth-azfun* app.
	- Once this is done, we can also see that two new settings have been added to the app configuration.
![[Pasted image 20230102181646.png]]

The instrumentation key is require to connect to application insights resource. They key should be added to the settings as you create function app resource.
If the key is not there, you must add it [manually](https://learn.microsoft.com/en-us/azure/azure-functions/configure-monitoring?tabs=v2#enable-application-insights-integration). They key can be obtained from the Application Insights Overview.

2. Navigate to the Application Insight resource and select the `Live Metrics` blade.

> [!CAUTION]  If you receive an error message, you need to check that your firewall isn't blocking traffic to the following URLs.
> 	`*.livediagnostics.monitor.azure.com` </b>
> 	`*.monitor.azure.com`
> >  The same can apply to Ad-Blocker or any other tool that intercepts web traffic.

3. In the `..\TollBooth\UplaodImages` folder, start a new instance of **UploadImages.csproj** in Visual Studio.
- Wait for visual studio to load. Then click on the `Start` button.
![[Pasted image 20230102191209.png]]

- A new shell promt should appear asking for a connection string.

5. Acquire the storage account connection string.
- Go to the init storage account, `cosdbuks001init001` and under the `Access Key` blade acquire the Connection String.
- Pass the connection string in to the terminal.
![[Pasted image 20230102190010.png]]

> [!CAUTION]  Connection string are a sensitive information and should not be exposed to the public. All my resources will be deleted prior to me publishing this.

> [!NOTE] If the keys due get leaked, you can always roate them: [Manage Access Keys](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-keys-manage?tabs=azure-portal)

6. Back in the Azure Portal, we can start seeing some live telemetry from the images that we just uploaded.

- Telemetry data after we have just uploaded 10 images.

We can see that there are currently 5 servers at rest since there is nothing to process.
![[Pasted image 20230104192918.png]]

- Telemetry data as we start the uplaod of 1000 images. 

We can see the metrisc as they start to rise once the data starts coming in and we have now gone from 5 server to 8 servers and their capacitry varies due to the processing of the incoming images.

![[Pasted image 20230104193831.png]]

- Telemetry data after the upload of 1000 images

We can see here how the metrics have decreased after the upload of 1000 images and the server are being scaled in since there is no longer no incoming images to process. However, the server are still on stand by anticipating any further incoming data. 

![[Pasted image 20230104194106.png]]

- After the cooldown period there will be no server left.
- We can also see the output of our date in Azure Cosmos DB LicensePlate database.
![[Pasted image 20230104203339.png]]

## Data Export Workflow

1. Create a Logic App resource.
- Name the app `tollbooth-logic`.
- Ensure that log analytics is turned off.
- Select the consumption based plan.
![[Pasted image 20230104235542.png]]

