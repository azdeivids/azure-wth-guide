# Resources

- [Azure Function Core Tools](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=v4%2Cwindows%2Ccsharp%2Cportal%2Cbash)
- [Azure Functions Overview](https://learn.microsoft.com/en-us/azure/azure-functions/functions-overview)
- [Create C# function is vscode](https://learn.microsoft.com/en-us/azure/azure-functions/create-first-function-vs-code-csharp?tabs=in-process)
- [Create Azure Cosmos DB Account](https://learn.microsoft.com/en-us/azure/cosmos-db/how-to-manage-database-account)
- [Getting started with SQL queries (NoSQL)](https://learn.microsoft.com/en-us/azure/cosmos-db/nosql/query/getting-started)
- [Azure Event Grid](https://learn.microsoft.com/en-us/azure/event-grid/concepts)
- [Computer Vision](https://learn.microsoft.com/en-us/azure/cognitive-services/computer-vision/)
- 

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

2. Create a storage account in the same RG. Refer to it as `INIT`.
![[Pasted image 20221229214759.png]]

- Within the storage account, create a container named `images`.
- Within the same storage account, create a second container named `exports`.

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

- 