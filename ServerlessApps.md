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

*Function Apps* are a collection of Azure Functions which are deployed in the same environment and use the same language.  
*Function Project* - folder which contains all of the code needed for a set of functions when developing in local environment. Same as a `function app` on azure.  

*Bindings* are the input(reads from/triggers) and output( writes to) of a AF.  
`Input` bindings can be a connection to a datasource which provides input to a function or a trigger which causes the function to run.  

*Required Properties for Bindings:*
- Name : defines the parameter name passed to the function
- Type: type of data or service to interact with
- Direction : input or output
- Connection: name of app setting key used for connection string. Only required for some bindings.


*Authorization Level*
Unauthorized requests are blocked. The API key can be included in a query string variable named `code`, or it can be included in an `x-functions-key` HTTP header.  
Function - requires a function-key or host-key in each request. 
Admin - Uses a host-key  only
Anonymous - no key required  

*function key* - Specific  to a function  
*host key* - Apply to all functions inside a function app

Q: How to set app setting binding expressions?
A: wrap parameter name in percent symbols


Q: The container that groups functions into a logical unit for easier management, deployment, and sharing of resources is called?
A: Function App

A function app is a way to organize and collectively manage your functions. A function app is comprised of one or more individual functions that are managed together by Azure App Service. All the functions in a function app share the same pricing plan, continuous deployment, and runtime version.:w


```curl --header "Content-Type: application/json" --header "x-functions-key: <your-function-key>" --request POST --data "{\"name\": \"Azure Function\"}" https://<your-url-here>/api/<function-name> ```

Q: How is the schedule configured for Timer Triggers?
A: Using a cron-formatted schedule parameter

Q: How to change the name of the variable that contains information about an incoming http request for an http trigger?
A: Set the *Request Parameter Name*. the default is `req`


Blob trigger - executes a function when a file is uploaded or updated in blob storage  

Q: Which setting controls which file in blob storage should trigger a function?
A: `path` setting

Blob trigger path:  
my_blob_name/{foo}.jpg

Two parts: blob name and the file to watch for within the blob. words between brackets will become variable names which are passed to the function.

Q: When should the `sql query` parameter be used when using Cosmos DB as an input binding?
A: When you want to get multiple rows of data. Requires additional processing within function. `document parameter name` is better when searching for a single row.

Q: What is the difference between Azure Functions and Durable Functions?
A: AFs are stateless. Data is lost after function returns. DFs are stateful. Data can be retained between function calls.

*Durable Functions*
Three types:  
- Client : entry point to a DF orchestration. Run in response to a trigger. Can be written in any supported AF language.
- Orchestrator : describe how actions are executed and the order in which they run. C# and JS only.
- Activity : The work/action the function performs when triggered.

Always use the `currentUTCDateTime` property of `durable-functions` package to read the time of the request.  

Use `createTimer()` instead of `setInverval` or `setTimer` in Orchestrators.

```
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function* (context) {
    const outputs = [];
    // add 20 seconds to the time of the request
    const deadline = moment.utc(context.df.currentUtcDateTime).add(20, "s");
    const activityTask = context.df.waitForExternalEvent("Approval");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    // use the result of whichever function returns first 
    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        outputs.push(yield context.df.callActivity("Approval", "Approved"));
    }
    else
    {
        outputs.push(yield context.df.callActivity("Escalation", "Head of department"));
    }

    if (!timeoutTask.isCompleted) {
        // All pending timers must be complete or canceled before the function exits.
        timeoutTask.cancel();
    }

    return outputs;
});
```

### Core Tools CLI

Start a new Functions project
```
func init
```

Add a function to the project
```
func add
```

Run the functions locally
```
funct start
```