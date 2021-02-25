Four services availabe to build serverless applications
- Logic App
- Power Automate
- WebJobs
- Azure Functions

### Design First Services
Logic Apps and Power Automate are *design first* services which can be created using only the GUI.

Logic Apps
- Supports GUI and editing of the underlying code

Power Automate
- GUI only
- Built on Logic Apps, supports the same connections and actions

Four workflows available in Power Automate:  
- Automated : triggered by an event
- Button : click a button in the ui to trigger an event
- Scheduled : executes on a schedule
- Business Process : Models business processes such as ordering goods or sending notifications

### Code First Services

`WebJobs` can be be written in several different languages.
- Pricing is per VM or App Service Plan that hosts the job.
- The only service that allows customization of retry policies when a function fails

Two kinds: 
- Continuous : run continuously
- Triggered : triggered by an event or run on a schedule

WebJobs are preferrable over Azure Functions when the code needs to be part of an existing AppService collection or precision control is required over the object that listens for events that trigger the code.

**Azure Functions**
Enables devs to run small snippets of code on automatically provisioned servers. 
- Supports multiple languages
- Pay  for amount of time function runs
- Only run when triggered. Can only be triggered by one event.
- must be linked to a storage account. Storage account will be used for logging and managing execution triggers. on consumption plan, also where function code and config file stored.

AF's must complete their execution within a limited period of time( timeout). The default timeout is 5 minutes and can be configured up to a maximum of 10 minutes.If the service is initiated by a HTTP request the maximum timeout is 2.5 minutes. *Durable Functions* are an extension of AFs that can run without a timeout.

If an AF runs continuously it may be cheaper to run it on a dedicated vm.

Two plans available:
- Consumption
    + automatic scaling
    + billed on execution time
    + configurable timeouts on execution of functions
- App Service Plan
    + no timeouts
    + runs on a vm defined by owner
    + Owner is responsible for managing
    + technically not serverless

When scaling only one functon app instance can be created every 10 seconds, up to a max of 200 instances. 
There is no limit to how much traffic a single instance can handle.

*Bindings* are the input(reads from/triggers) and output( writes to) of a AF. 

*Authorization Level*
Unauthorized requests are blocked. The API key can be included in a query string variable named `code`, or it can be included in an `x-functions-key` HTTP header.  
Function - requires a function-specific key in each request. 
Admin - Uses a global key  
Anonymous - no key required  


Q: The container that groups functions into a logical unit for easier management, deployment, and sharing of resources is called?
A: Function App

A function app is a way to organize and collectively manage your functions. A function app is comprised of one or more individual functions that are managed together by Azure App Service. All the functions in a function app share the same pricing plan, continuous deployment, and runtime version.:w


```curl --header "Content-Type: application/json" --header "x-functions-key: <your-function-key>" --request POST --data "{\"name\": \"Azure Function\"}" https://<your-url-here>/api/<function-name> ```


