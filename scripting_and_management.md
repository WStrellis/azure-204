### Resource Groups
Create a resource group  
`az group create --name <name> --location <location>`  

List all resource groups in table format  
`az group list --output table`  

Filter output with a query  
`az group list --query "[?name == 'my_resource_group']"`  

A resource can have up to 50 tags. key: 512 character limit. value: 256 character limit