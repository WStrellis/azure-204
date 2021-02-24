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

Two kinds: 
- Continuous : run continuously
- Triggered : triggered by an event or run on a schedule

Azure Functions
Enables devs to run small snippets of code on automatically provisioned servers. 
- Supports multiple languages
- Pay  for amount of time function runs

WebJobs are preferrable when the code needs to be part of an existing AppService collection or precision control is required over the object that listens for events that trigger the code.