### create a resource group and a vm.  
Find images with `az vm image list -f keywords --all`  .
Use the `urn` value in cli commands to specify an image.  
```
az group create --name my-vm-grp --location eastus 
```
```
az vm create \
--resource-group my-vm-grp \
--name demovm \
--image centos \
--admin-username me \
--ssh-key-value ~/.ssh/my_public_key  
```

### create web app service and  integrate with github for cicd
1. declare variables
```
$plan="newplan1000"
$appname="webapp50001"
$repourl="https://github.com/yourname/yourproject"
```

2. Then create the resource group  
`az group create --location westeurope --name web-grp`  

3.  create the App Service Plan  
`az appservice plan create --name $plan --resource-group web-grp --sku B1`

4. create the Azure Web App  
`az webapp create --name $appname --resource-group web-grp --plan $plan`  

5.  configure the deployment settings - require manual deployment  
`az webapp deployment source config --name $appname --resource-group web-grp  --repo-url $repourl --branch master --manual-integration`  

### enable cors through cli
```
az webapp cors add \
-g some-resource-group \
-n my-api-service \
--allowed-origins http://my-web-app.azurewebsites.net
```