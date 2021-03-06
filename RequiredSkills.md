### Serverless Apps
- make azure functions
    + use schedule trigger
    + use storage trigger/input/output
        - blob
        - Cosmos
    + use http trigger/input/output
    + use a queue trigger/input/output

- create Durable Functions
    + Client func
    + Starter func ?
    + Orchestrator func
        - use a schedule to execute an action
    + Action func

- use Azure Core cli tools to create and deploy a project

- create multiple apis and combine them with API Management Service

### Message and Event Services
- Make app with Service Bus Queues
- Make app with Event Hub/Grid

### Data Storage
- make web app which allows users to upload files to blob storage and anyone can download them.

### Resource Management
- Configure policies on a resource group
- Configure RBAC on a resource group

### VM
- create linux vm and ssh to it
- create windows vm and rdp to it
- create container registry
- create container instance from container registry
- create kubernetes cluster

### App Service and Web Apps
- Deploy a web app from git
- Deploy web app from Docker
- Use Deployment Slots with git
- Use Deployment Slots with Docker Containers
- use Web App service to deploy app
- enable logging
- configure continuous deployment from github
    - manual
    - automatic
- custom domain name 
- enable CORS through App Service dashboard and cli
- configure connection strings

### Security
- Store secrets in Key Vault
- Encrypt vm hd with AZ Disk Encryption
- Encrypt DB tables
- Add security classifications to db columns
- Set DB Server-level firewall rules
- Set DB database-level firewall rules
- enable Azure Defender for a SQL db
- enable auditing for a sql db

set a db level rule
```
EXECUTE sp_set_database_firewall_rule N'My Firewall Rule', <start address>, <end address>
GO
```
delete a db level rule
```
EXECUTE sp_delete_database_firewall_rule N'my firewall rule';
GO
```