Create a storage account
```
az storage account create \
  --name $STORAGE_ACCOUNT_NAME \
  --resource-group learn-911c9833-df08-4546-b2b5-8c402b1d7357 \
  --kind StorageV2 \
  --sku Standard_LRS
```

Create Azure DB account
```
az cosmosdb create  \
  --name msl-sigr-cosmos-$(openssl rand -hex 5) \
  --resource-group learn-911c9833-df08-4546-b2b5-8c402b1d7357
```

Create SignalR account
```
SIGNALR_SERVICE_NAME=msl-sigr-signalr$(openssl rand -hex 5)
az signalr create \
  --name $SIGNALR_SERVICE_NAME \
  --resource-group learn-5cb775a2-882c-4da8-a9ca-5e6b7b0e892f \
  --sku Free_DS2 \
  --unit-count 1
```

Configure SignalR. Set to "serverless" mode.
```
az resource update \
  --resource-type Microsoft.SignalRService/SignalR \
  --name $SIGNALR_SERVICE_NAME \
  --resource-group learn-5cb775a2-882c-4da8-a9ca-5e6b7b0e892f \
  --set properties.features[flag=ServiceMode].value=Serverless
```

Get connection strings
```
STORAGE_CONNECTION_STRING=$(az storage account show-connection-string \
--name $(az storage account list \
  --resource-group learn-5cb775a2-882c-4da8-a9ca-5e6b7b0e892f \
  --query [0].name -o tsv) \
--resource-group learn-5cb775a2-882c-4da8-a9ca-5e6b7b0e892f \
--query "connectionString" -o tsv)

COSMOSDB_ACCOUNT_NAME=$(az cosmosdb list \
    --resource-group learn-5cb775a2-882c-4da8-a9ca-5e6b7b0e892f \
    --query [0].name -o tsv)

COSMOSDB_CONNECTION_STRING=$(az cosmosdb list-connection-strings  \
  --name $COSMOSDB_ACCOUNT_NAME \
  --resource-group learn-5cb775a2-882c-4da8-a9ca-5e6b7b0e892f \
  --query "connectionStrings[?description=='Primary SQL Connection String'].connectionString" -o tsv)

COSMOSDB_MASTER_KEY=$(az cosmosdb list-keys \
--name $COSMOSDB_ACCOUNT_NAME \
--resource-group learn-5cb775a2-882c-4da8-a9ca-5e6b7b0e892f \
--query primaryMasterKey -o tsv)

SIGNALR_CONNECTION_STRING=$(az signalr key list \
  --name $(az signalr list \
    --resource-group learn-5cb775a2-882c-4da8-a9ca-5e6b7b0e892f \
    --query [0].name -o tsv) \
  --resource-group learn-5cb775a2-882c-4da8-a9ca-5e6b7b0e892f \
  --query primaryConnectionString -o tsv)

printf "\n\nReplace <STORAGE_CONNECTION_STRING> with:\n$STORAGE_CONNECTION_STRING\n\nReplace <COSMOSDB_CONNECTION_STRING> with:\n$COSMOSDB_CONNECTION_STRING\n\nReplace <COSMOSDB_MASTER_KEY> with:\n$COSMOSDB_MASTER_KEY\n\nReplace <SIGNALR_CONNECTION_STRING> with:\n$SIGNALR_CONNECTION_STRING\n\n"
```

Update `local.settings.json` with connection strings.
```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<STORAGE_CONNECTION_STRING>", 
    "AzureCosmosDBConnectionString": "<COSMOSDB_CONNECTIONS_STRING>",
       "AzureCosmosDBMasterKey":"<COSMOSDB_MASTER_KEY>",
    "AzureSignalRConnectionString": "<SIGNALR_CONNECTION_STRING>",
    "FUNCTIONS_WORKER_RUNTIME": "node"
  },
  "Host" : {
    "LocalHttpPort": 7071,
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

### Service Bus
Get connection string and access key
```
az servicebus namespace authorization-rule keys list \
    --resource-group <resource-group-name> \
    --name RootManageSharedAccessKey \
    --query primaryConnectionString \
    --output tsv \
    --namespace-name <namespace-name>
```