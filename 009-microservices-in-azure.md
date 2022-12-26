
## Pre-requisites

1. Set up a new app service plan.
```
az appservice plan create --name asp-microservices --resource-group $rg --is-linux --location $loc --sku B1 --number-of-workers 1
```

2. Create a new web app.
```
az webapp create --resource-group $rg --name asp-microservices-web --plan asp-discovery -i microservicesdiscovery/travel-web
```

3. Configure the new web app settings.
```
az webapp config appsettings set  --resource-group $rg --name asp-microservices-web --settings DataAccountName=$cosmoAccountName DataAccountPassword=$cosmosPrimaryKey ApplicationInsights__InstrumentationKey=$appkey DataServiceUrl="http://$dataServiceUri/" ItineraryServiceUrl="http://$itineraryServiceUri/"
```

## [Chalange 1](https://github.com/microsoft/WhatTheHack/blob/master/009-MicroservicesInAzure/Student/Challenge-01.md)

1. Find the nearest azure data center to your location and put it into a variable
```
# pwsh

$loc="uksouth"
```

2. Create a resource group into the closest data center
```
# pwsh

# create a varibale for the resource group name
$rg="*resource-group-name*"

# az cli

az group create -n $rg -l $loc
```

## [Challange 2](https://github.com/microsoft/WhatTheHack/blob/master/009-MicroservicesInAzure/Student/Challenge-02.md)

1. Deploy the provided ARM template to create app insight resource
	- Application insights is an extension of Azure Monitor and provides application performance monitoring (APM) features.
	-  [Application Insights Overview](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview?tabs=net)
```
# az cli

az group deployment create \
-n microservices-app-insights \
--resource-group $rg \
--template-file appinsights.json \
--parameters 'name=microservices-app-insights' \
			 'location=uksouth'
```

2. Wait for the deployment to finish; once done, copy the new `InstrumentationKey` and put it into a variable.
```
# pwsh

$appkey="673704b7-be25-4a79-ba0f-5084f2f3f181"
```
## [Challange 3](https://github.com/microsoft/WhatTheHack/blob/master/009-MicroservicesInAzure/Student/Challenge-03.md)

1. Create a CosmoDB account.
	- Azure CosmoDB is a fully managed NoSQL and relational database for modern app development. 
	- [CosmoDB Overview](https://learn.microsoft.com/en-us/azure/cosmos-db/introduction)
2. Copy the `PrimaryMasterKey` and store it within a variable; think of this as a password
```
# pwsh

$primarymasterkey="Ov77wszVZjhYrSgkGSdYSGbyxCXgABUqalZ5gnrrtGFNMW6xTNov3agrwoME5zvKzvEiwjVUCXnBACDbQMUY1Q=="
```
3. Copy the CosmosDB account name into variable as well; think of this as username
```
# pwsh

$cosmosdbaccname="microservices-data"
```
## [Challange 4](https://github.com/microsoft/WhatTheHack/blob/master/009-MicroservicesInAzure/Student/Challenge-04.md)

- Deploy three different containers as Azure Container Instances
	- Data API: `microservicesdiscovery/travel-data-service`
	- Itinerary API: `microservicesdiscovery/travel-itinierary-service`
	- DataLoader utility: `microservicesdiscovery/travel-dataloader`

1. Deploy the Data API: `microservicesdiscovery/travel-data-service` Azure container instance. The container need to be accessible by the Web Site. 
```
# az cli

az container create --resource-group $rg --dns-name-label deividsegle --image microservicesdiscovery/travel-data-service --name "data-api-aci" --environment-variables DataAccountName=$CosmosDbAccountName DataAccountPassword=$PrimaryMasterKey ApplicationINsights__IntrumentationKey=$InstrumentationKey
```

2. Once the container is deployed, extract the ACI IP address and store it into a variable
```
# pwsh

$dataServiceUri=az container show --resource-group $rg --name data-api-aci --query "ipAddress.fqdn"
```

3. Deploy the Itinierary API: `microservicesdiscovery/travel-itinierary-service` Azure container instance. The container needs to be accessible from the Web Site.
```
az container create --resource-group $rg --dns-name-label deividsegle2  --image microservicesdiscovery/travel-itinerary-service --name "itinierary-api-aci" --environment-variables DataAccountName=$cosmosDbAccountName DataAccountPassword=$PrimaryMasterKey ApplicationInsights__InstrumentationKey=$appkey
```

4. Once the container is deployed, extract the FQDN and store it into a itineraryServiceUri
```
$itineraryServiceUri=az container show -g $rg -n itinierary-api-aci --query "ipAddress.fqdn" 
```

5.  Set up the travel-dataloader Azure container instance
```
az container create --resource-group $rg --name dataloader-service --image microservicesdiscovery/travel-dataloader --environment-variables DataAccountName=$cosmosDbAccountName DataAccountPassword=$cosmosPrimaryKey ApplicationInsights__InstrumentationKey=$appkey --restart-policy OnFailure
```

6. Verify that all the containers are running and check the container instace log's.
```
az container logs --resource-group $rg --name data-api-aci
```


## [Challange 5](https://github.com/microsoft/WhatTheHack/blob/master/009-MicroservicesInAzure/Student/Challenge-05.md)

- Deploy the web front end into Azure App Service.

1. Create a Standard Linux App Service Plan (asp).
```
az appservice plan create -g $rg -n microservices-asp --is-linux --number-of-workers 1 --sku B1 --location $loc
```

2. Create a Web App and set the microservicesdiscovery/travel-web as the container image for the Web app.
```
az webapp create -g $rg --plan microservices-asp --name travel-web --image microservicesdiscovery/travel-web
```

3.Add the following Applicateion settings to the newly created *travel-web* app.
- `DataAccountName`: Name of the CosmosDB Account - $cosmosdbaccname
- `DataAccountPassword`: Primary key of the CosmosDB Account - $primarymasterkey
- `ApplicationInsights_InstumentationKey`: Insturmentation key of the App Insights Resource - $appkey
- `DataServiceUri`: The URL to the Data Service - $dataServiceUri
- `ItineraryServiceUri`: The URL to the Itinerary Service - $itineraryServiceUri